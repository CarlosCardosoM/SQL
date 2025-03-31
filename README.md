# Projeto de Banco de Dados: INTUITIVE

Este projeto tem como objetivo a criação e manipulação de dados relacionados a operadoras de saúde e demonstrações contábeis. O banco de dados foi projetado para armazenar informações sobre operadoras, dados financeiros e relatórios de despesas, permitindo consultas detalhadas sobre as maiores despesas e outros dados financeiros.

## Estrutura do Banco de Dados

### Tabela `OPERADORAS`
Esta tabela armazena os dados das operadoras de saúde. A criação da tabela pode ser feita com o seguinte comando SQL:

```sql
CREATE TABLE OPERADORAS(
	REGISTRO_ANS INT PRIMARY KEY,
	CNPJ VARCHAR(14),
	RAZAO_SOCIAL VARCHAR(150),
	NOME_FANTASIA VARCHAR(150),
	MODALIDADE VARCHAR(150),
	LOGRADOURO VARCHAR(150),
	NUMERO VARCHAR(50),
	COMPLEMENTO VARCHAR(150),
	BAIRRO VARCHAR(150),
	CIDADE VARCHAR(150),
	UF VARCHAR(2),
	CEP VARCHAR(8),
	DDD VARCHAR(2),
	TELEFONE VARCHAR(40),
	FAX VARCHAR(40),
	ENDERECO_ELETRONICO VARCHAR(150),
	REPRESENTANTE VARCHAR(150),
	CARGO_REPRESENTANTE VARCHAR(150),
	REGIAO_DE_COMERCIALIZACAO VARCHAR(150),
	DATA_REGISTRO_ANS DATE
);
## Tabela Temporária DADOS
Esta tabela é usada para armazenar temporariamente os dados financeiros antes de serem transferidos para a tabela permanente DEMONSTRACOES.
```sql
CREATE TEMPORARY TABLE DADOS(
	DATA_REFERENCIA DATE,
	REG_ANS INT,
	CD_CONTA_CONTABIL VARCHAR(20),
	DESCRICAO VARCHAR(200),
	VALOR_SALDO_INICIAL VARCHAR(20),
	VALOR_SALDO_FINAL VARCHAR(20)
);
## Tabela Permanente DEMONSTRACOES
Esta tabela armazena os dados financeiros de forma permanente após a transformação dos dados da tabela temporária DADOS.
```sql
CREATE TABLE DEMONSTRACOES(
	ID SERIAL PRIMARY KEY,
	DATA_REFERENCIA DATE,
	REG_ANS INT NOT NULL,
	CD_CONTA_CONTABIL VARCHAR(20),
	DESCRICAO VARCHAR(200),
	VALOR_SALDO_INICIAL NUMERIC,
	VALOR_SALDO_FINAL NUMERIC
);



