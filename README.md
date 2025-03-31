
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
```

### Tabela Temporária `DADOS`
Esta tabela é usada para armazenar temporariamente os dados financeiros antes de serem transferidos para a tabela permanente `DEMONSTRACOES`.

```sql
CREATE TEMPORARY TABLE DADOS(
	DATA_REFERENCIA DATE,
	REG_ANS INT,
	CD_CONTA_CONTABIL VARCHAR(20),
	DESCRICAO VARCHAR(200),
	VALOR_SALDO_INICIAL VARCHAR(20),
	VALOR_SALDO_FINAL VARCHAR(20)
);
```

### Tabela Permanente `DEMONSTRACOES`
Esta tabela armazena os dados financeiros de forma permanente após a transformação dos dados da tabela temporária `DADOS`.

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
```

## Carregamento de Dados

### Importação dos Dados CSV para a Tabela `DADOS`

Para carregar os dados financeiros de 2023 e 2024, você pode usar os seguintes comandos `COPY` para cada trimestre:

```sql
COPY DADOS 
FROM 'D:\Intuitive\1T2023.csv'  
DELIMITER ';' 
CSV HEADER;
```

Alterando o nome do arquivo conforme o trimestre:

- `1T2023.csv`, `2T2023.csv`, `3T2023.csv`, `4T2023.csv`
- `1T2024.csv`, `2T2024.csv`, `3T2024.csv`, `4T2024.csv`

### Importação dos Dados da Tabela `OPERADORAS`

Carregar os dados das operadoras a partir de um arquivo CSV:

```sql
COPY OPERADORAS
FROM 'D:\Relatorio.csv'  
DELIMITER ';' 
CSV HEADER;
```

### Transformação de Dados

Antes de transferir os dados da tabela temporária `DADOS` para a tabela permanente `DEMONSTRACOES`, realizamos uma transformação para garantir que os valores numéricos usem o ponto (.) em vez da vírgula (,):

```sql
UPDATE DADOS
SET VALOR_SALDO_INICIAL = REPLACE(VALOR_SALDO_INICIAL, ',', '.'),
	VALOR_SALDO_FINAL = REPLACE(VALOR_SALDO_FINAL, ',', '.');
```

### Inserção de Dados na Tabela Permanente

Após a transformação, os dados são inseridos na tabela `DEMONSTRACOES`:

```sql
INSERT INTO DEMONSTRACOES (DATA_REFERENCIA, REG_ANS, CD_CONTA_CONTABIL, DESCRICAO, VALOR_SALDO_INICIAL, VALOR_SALDO_FINAL)
SELECT
	DATA_REFERENCIA,
	REG_ANS,
	CD_CONTA_CONTABIL,
	DESCRICAO,
	CAST(VALOR_SALDO_INICIAL AS NUMERIC),
	CAST(VALOR_SALDO_FINAL AS NUMERIC)
FROM TEMP_DADOS;
```

### Remoção da Tabela Temporária

Após a inserção dos dados na tabela permanente, a tabela temporária `DADOS` é removida:

```sql
DROP TABLE DADOS;
```

## Consultas SQL

### Consultar as 10 Operadoras com Maiores Despesas no Último Trimestre

```sql
SELECT O.RAZAO_SOCIAL, SUM(D.VALOR_SALDO_FINAL)
FROM DEMONSTRACOES D
INNER JOIN OPERADORAS O ON O.REGISTRO_ANS = D.REG_ANS
WHERE D.DESCRICAO = 'EVENTOS/ SINISTROS CONHECIDOS OU AVISADOS DE ASSISTÊNCIA A SAÚDE MEDICO HOSPITALAR'
	AND D.DATA_REFERENCIA BETWEEN '2024-10-01' AND '2024-12-31'
GROUP BY O.RAZAO_SOCIAL
ORDER BY SUM(D.VALOR_SALDO_FINAL) DESC
LIMIT 10;
```

### Consultar as 10 Operadoras com Maiores Despesas no Último Ano

```sql
SELECT O.RAZAO_SOCIAL, SUM(D.VALOR_SALDO_FINAL)
FROM DEMONSTRACOES D
INNER JOIN OPERADORAS O ON O.REGISTRO_ANS = D.REG_ANS
WHERE D.DESCRICAO = 'EVENTOS/ SINISTROS CONHECIDOS OU AVISADOS DE ASSISTÊNCIA A SAÚDE MEDICO HOSPITALAR'
	AND D.DATA_REFERENCIA BETWEEN '2023-01-01' AND '2023-12-31'
GROUP BY O.RAZAO_SOCIAL
ORDER BY SUM(D.VALOR_SALDO_FINAL) DESC
LIMIT 10;
```

## Conclusão

Este projeto foi desenvolvido para facilitar o gerenciamento e a consulta de dados financeiros de operadoras de saúde. Utilizando o banco de dados relacional, é possível realizar consultas detalhadas sobre as despesas de operadoras em categorias específicas, permitindo análises eficientes.
```
