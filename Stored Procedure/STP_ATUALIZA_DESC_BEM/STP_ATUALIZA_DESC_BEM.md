create or replace PROCEDURE "STP_ATUALIZA_DESC_BEM" (
       P_CODUSU NUMBER,        -- Código do usuário logado
       P_IDSESSAO VARCHAR2,    -- Identificador da execução. Serve para buscar informações dos parâmetros/campos da execução.
       P_QTDLINHAS NUMBER,     -- Informa a quantidade de registros selecionados no momento da execução.
       P_MENSAGEM OUT VARCHAR2 -- Caso seja passada uma mensagem aqui, ela será exibida como uma informação ao usuário.
) AS
       PARAM_P_NDESCRICAO VARCHAR2(4000);
       FIELD_CODBEM VARCHAR2(4000);
       FIELD_CODPROD NUMBER;
BEGIN
/*******************************************************************************
   É possível obter o valor dos campos através das Functions:

  EVP_GET_CAMPO_DTA(P_IDSESSAO, 'NOMECAMPO') -- PARA CAMPOS DE DATA
  EVP_GET_CAMPO_INT(P_IDSESSAO, 'NOMECAMPO') -- PARA CAMPOS NUMÉRICOS INTEIROS
  EVP_GET_CAMPO_DEC(P_IDSESSAO, 'NOMECAMPO') -- PARA CAMPOS NUMÉRICOS DECIMAIS
  EVP_GET_CAMPO_TEXTO(P_IDSESSAO, 'NOMECAMPO')   -- PARA CAMPOS TEXTO

  O primeiro argumento é uma chave para esta execução. O segundo é o nome do campo.

  Para os eventos BEFORE UPDATE, BEFORE INSERT e AFTER DELETE todos os campos estarão disponíveis.
  Para os demais, somente os campos que pertencem à PK

  * Os campos CLOB/TEXT serão enviados convertidos para VARCHAR(4000)

  Também é possível alterar o valor de um campo através das Stored procedures:

  EVP_SET_CAMPO_DTA(P_IDSESSAO,  'NOMECAMPO', VALOR) -- VALOR DEVE SER UMA DATA
  EVP_SET_CAMPO_INT(P_IDSESSAO,  'NOMECAMPO', VALOR) -- VALOR DEVE SER UM NÚMERO INTEIRO
  EVP_SET_CAMPO_DEC(P_IDSESSAO,  'NOMECAMPO', VALOR) -- VALOR DEVE SER UM NÚMERO DECIMAL
  EVP_SET_CAMPO_TEXTO(P_IDSESSAO,  'NOMECAMPO', VALOR) -- VALOR DEVE SER UM TEXTO
********************************************************************************/

/*     IF P_TIPOEVENTO = BEFORE_INSERT THEN
             --DESCOMENTE ESTE BLOCO PARA PROGRAMAR O "BEFORE INSERT"
       END IF;*/
   /* RAISE_APPLICATION_ERROR(-20000, 'P_DECRETO: ' || P_DECRETO || ' P_CODPARC: ' || P_CODPARC); */
     

       PARAM_P_NDESCRICAO := ACT_TXT_PARAM(P_IDSESSAO, 'P_NDESCRICAO');

       FOR I IN 1..P_QTDLINHAS -- Este loop permite obter o valor de campos dos registros envolvidos na execução.
       LOOP                    -- A variável "I" representa o registro corrente.
        FIELD_CODBEM := ACT_TXT_FIELD(P_IDSESSAO, I, 'CODBEM');
        FIELD_CODPROD := ACT_INT_FIELD(P_IDSESSAO, I, 'CODPROD');

UPDATE TCIBEM SET DESCRABREV = PARAM_P_NDESCRICAO WHERE CODBEM = FIELD_CODBEM and CODPROD = FIELD_CODPROD;
COMMIT; 
 --RAISE_APPLICATION_ERROR(-20000, 'TEXTO ' || PARAM_P_NDESCRICAO || ' CODBEM: ' || FIELD_CODBEM || ' CODPROD: ' || FIELD_CODPROD);
END LOOP;
p_mensagem := 'Descrição Abreviada Alterada para: '||PARAM_P_NDESCRICAO;
END;
