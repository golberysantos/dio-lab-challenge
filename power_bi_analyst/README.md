# Desafio Power BI

## DESCRIÇÃO

Projeto desenvolvido durante o bootcamp de Power BI.

## DESAFIO I

O desafio consistiu em:

- Reproduzir duas páginas apresentadas durante as aulas
- Criar uma terceira página utilizando diferentes visualizações
- Trabalhar com mapas, indicadores de lucro e segmentação de dados

### Visuais Desenvolvidos

#### Página 1
Dashboard de vendas.

#### Página 2
Dashboard de desempenho e análises.

#### Página 3

- Mapa de Vendas e Unidades Vendidas por País
- Mapa de Lucro por País
- Gráfico de Pizza com Lucro por Segmento

## DESAFIO II
O desafio consistiu em:
- Botões de navegação que fornecem navegabilidade 
- Segmentadores utilizados e botões com imagem associado 
- Utilização do Painel de Indicadores e botões para selecionar diferentes visuais sobre um mesmo assunto 
#### Página 1
Dashboard de vendas.

#### Página 2
Dashboard de Vendas por País.


<br>

## DESAFIO III

O desafio consistiu em:

- Criar uma instância MySQL no Azure
- Configurar regras de firewall para acesso ao banco de dados
- Conectar ao MySQL utilizando MySQL Workbench
- Integrar o Power BI ao banco MySQL hospedado no Azure
- Realizar transformações e tratamento dos dados utilizando Power Query

### Transformações Realizadas

- Verificação dos cabeçalhos das tabelas
- Ajuste dos tipos de dados
- Conversão dos valores monetários para tipo decimal
- Análise e tratamento de valores nulos
- Verificação de colaboradores sem gerente associado
- Verificação de departamentos sem gerente
- Validação das horas registradas nos projetos
- Separação de colunas compostas
- Criação de coluna com nome completo dos colaboradores
- Mesclagem das tabelas Employee e Department
- Associação de colaboradores aos respectivos departamentos
- Associação de colaboradores aos respectivos gerentes
- Criação de identificador único utilizando Departamento + Localização
- Agrupamento de colaboradores por gerente
- Remoção de colunas desnecessárias para otimização do modelo

### Consulta SQL Utilizada

```sql
SELECT
    e.Ssn,
    CONCAT(e.Fname, ' ', e.Lname) AS Employee_Name,
    CONCAT(m.Fname, ' ', m.Lname) AS Manager_Name
FROM employee e
LEFT JOIN employee m
ON e.Super_ssn = m.Ssn;
```



## 📁 Estrutura do Projeto
<br>

```text

├── power_bi_analyst/
│   ├── Análise de Dados com SQL/
│   │   ├── sql_script_mysql.sql
│   │   └── sql_script_sqlite.sql
│   ├── dataset/
│   │   ├── Business Unit.csv
│   │   ├── Customer.csv
│   │   ├── Dates.csv
│   │   └── financial_sample.xlsx
│   ├── sample_financial_desafio_1.pbix
│   ├── relatorio_criativo_desafio_2.pbix
│   └── mysql_azure_desafio_3.pbix
├── .gitignore
└── README.md
```



### Ferramentas

<br>

- Power BI Desktop
- Microsoft Azure
- MySQL
- MySQL Workbench
- GitHub

### Autor

Golbery Oliveira