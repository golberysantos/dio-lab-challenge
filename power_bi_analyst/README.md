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

## DESAFIO IV — Modelagem Dimensional

O desafio consiste em transformar o modelo relacional da Universidade em um
modelo dimensional no formato **star schema**, tendo o **professor** como foco
da análise. O modelo de origem contempla professores, departamentos, cursos e
disciplinas; os relacionamentos N:N entre disciplinas e cursos são resolvidos
pela tabela associativa `Disciplina_Curso`.

### Granularidade e Tabela Fato

A granularidade proposta para `FatoOfertaProfessor` é **uma linha por oferta de
uma disciplina, ministrada por um professor para um curso, em uma data/período
de oferta**. Essa granularidade permite analisar a atuação docente por curso,
disciplina, departamento e tempo.

Como o modelo relacional não contém registros de oferta nem medidas numéricas,
a fato pode ser implementada como uma **tabela fato sem medidas aditivas**,
usando `QuantidadeOfertas = 1` para contar ofertas. As chaves para professor,
departamento, curso, disciplina e data identificam o contexto de cada linha.

### Dimensões

- **DimProfessor** — identificação e atributos descritivos do professor.
- **DimDepartamento** — nome e campus do departamento ao qual o professor
  pertence; o departamento do professor é o contexto usado para essa dimensão.
- **DimCurso** — identificação e atributos descritivos do curso.
- **DimDisciplina** — identificação e atributos descritivos da disciplina.
- **DimData** — data completa, dia, mês, nome do mês, trimestre, semestre e ano,
  permitindo análises em diferentes níveis de tempo.

```mermaid
erDiagram
    DimProfessor ||--o{ FatoOfertaProfessor : ministra
    DimDepartamento ||--o{ FatoOfertaProfessor : pertence
    DimCurso ||--o{ FatoOfertaProfessor : oferta
    DimDisciplina ||--o{ FatoOfertaProfessor : contempla
    DimData ||--o{ FatoOfertaProfessor : ocorre

    FatoOfertaProfessor {
        int ProfessorKey FK
        int DepartamentoKey FK
        int CursoKey FK
        int DisciplinaKey FK
        int DataKey FK
        int QuantidadeOfertas
    }
```

### Datas e Premissas

Como não há datas no diagrama relacional, as datas de oferta devem ser
complementadas com dados disponíveis ou simulados para o exercício. Pode-se
considerar, por exemplo, a data de oferta da disciplina ou do curso. A
`DimData` deve cobrir o intervalo de datas utilizado e ter uma linha por dia;
se houver mais de um tipo de data, a mesma dimensão pode ser reutilizada em
papéis diferentes, como data da oferta do curso e data da oferta da disciplina.

O relacionamento entre professores e departamentos e a associação
`Disciplina_Curso` servem de base para compor o contexto da fato. As datas de
oferta e a medida `QuantidadeOfertas` são premissas da modelagem, pois não
existem no diagrama de origem. **Dados de alunos e a tabela `Matriculado` não
fazem parte deste desafio.**

## DESAFIO V — Dashboard de E-commerce com DAX

Neste desafio, a tabela **Financial Sample** foi usada como origem para construir
um modelo dimensional em estrela no Power BI. A tabela original foi mantida como
`financials_origem`, para servir de backup, e as tabelas analíticas foram
montadas selecionando e agrupando os campos necessários para cada contexto.

### Tabelas do Modelo

- **`financials_origem`** — cópia de segurança da fonte original, mantida oculta
  para não aparecer como tabela de análise no relatório.
- **`D_Produtos`** — dimensão de produtos com identificador e nome do produto,
  além de estatísticas por produto: média de unidades vendidas, média e mediana
  de vendas, maior venda e menor venda.
- **`D_Produtos_Detalhes`** — informações adicionais do produto: identificador,
  faixa de desconto (`Discount Band`), preço de venda (`Sale Price`), unidades
  vendidas (`Units Sold`) e custo de fabricação (`Manufacturing Price`).
- **`D_Descontos`** — identificador do produto, valor do desconto e faixa de
  desconto.
- **`D_Detalhes`** — atributos de venda que complementam as demais dimensões,
  como segmento, país e vendedor. Essa dimensão reúne os detalhes que não foram
  alocados às dimensões específicas de produto e desconto.
- **`D_Calendário`** — dimensão de datas criada em DAX, com uma linha para cada
  data do intervalo analisado.
- **`F_Vendas`** — tabela fato no nível dos registros de venda, incluindo a
  chave substituta `SK_ID` e campos como produto, unidades, preço, faixa e valor
  do desconto, segmento, país, vendedor, lucro e data.

```mermaid
erDiagram
    D_Produtos ||--o{ F_Vendas : produto
    D_Produtos_Detalhes ||--o{ F_Vendas : detalhes_do_produto
    D_Descontos ||--o{ F_Vendas : desconto
    D_Detalhes ||--o{ F_Vendas : atributos_da_venda
    D_Calendario ||--o{ F_Vendas : data
```

As dimensões permitem filtrar e agrupar os registros de `F_Vendas`, enquanto a
tabela fato concentra os dados usados nas análises. A tabela de origem é
preservada como backup, mas não participa da visualização do esquema em estrela.

### Processo e DAX

1. Manter a tabela Financial Sample original como backup e ocultá-la da
   experiência do relatório.
2. Criar as tabelas do modelo a partir da origem, selecionando os campos
   relevantes para produto, detalhes do produto, descontos e atributos de venda.
3. Agrupar os dados por produto para obter as estatísticas apresentadas em
   `D_Produtos`.
4. Criar `D_Calendário` com a função DAX `CALENDAR()`, cobrindo o intervalo das
   datas de venda.
5. Criar a coluna de índice de produtos usando uma expressão condicional em DAX.
6. Organizar as colunas e configurar os relacionamentos entre as dimensões e
   `F_Vendas`, usando as chaves correspondentes ao modelo.
7. Salvar o relatório Power BI no arquivo do desafio.

As estatísticas de produto correspondem a agregações de média, mediana, máximo
e mínimo — funções DAX `AVERAGE()`, `MEDIAN()`, `MAX()` e `MIN()` quando
implementadas como cálculos DAX. A expressão específica da coluna condicional
depende da regra usada para definir o índice. A granularidade da fato e os
relacionamentos devem acompanhar as chaves disponíveis nos dados de origem.



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
│   │   ├── financial_sample.xlsx
│   │   └── financial_sample_desafio_2.xlsx
│   ├── img/
│   │   ├── clean blue.png
│   │   ├── clean white.png
│   │   └── logo_dio.png
│   ├── sample_financial_desafio_1.pbix
│   ├── sample_financial_desafio_1.pdf
│   ├── relatorio_criativo_desafio_2.pbix
│   ├── mysql_azure_desafio_3.pbix
│   ├── modelo_dimensional_professores_desafio_4.mwb
│   ├── formulas_dax_desafio_5.pbix
│   └── star_schema_formulas_dax_desafio_5.pbix
├── .gitignore
└── README.md
```



### Ferramentas

<br>

- Power BI Desktop
- Microsoft Azure
- MySQL
- MySQL Workbench
- PostgreSQL
- DBeaver
- GitHub

### Autor

Golbery Oliveira