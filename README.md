# 📈 Análise de Dados e Consultas SQL
![Python Versions](https://img.shields.io/pypi/pyversions/st_pages.svg)
![License](https://img.shields.io/github/license/blackary/st_pages)

*Autor:* [Thiago Klebis](https://www.linkedin.com/in/thiagoklebis/)

Este projeto consiste em uma análise de dados de diferentes tabelas de banco de dados e a execução de consultas SQL para obter informações específicas. Abaixo estão detalhados os passos realizados no projeto.

## 📝 Objetivo

O objetivo deste projeto é realizar uma análise de dados a partir de vários arquivos CSV e usar consultas SQL para responder a perguntas específicas sobre esses dados. As tabelas usadas nos arquivos CSV incluem:

- Sales.SalesOrderDetail
- Sales.SpecialOfferProduct
- Production.Product
- Sales.SalesOrderHeader
- Sales.Customer
- Person.Person

## 🔬 Preparação de Dados

- Foram carregados os arquivos CSV no Python usando a biblioteca pandas.
```python
import pandas as pd

df = pd.read_csv("/content/Sales.SpecialOfferProduct.csv", sep=';')
df2 = pd.read_csv("/content/Sales.SalesOrderHeader.csv", sep=';')
df3= pd.read_csv('/content/Sales.SalesOrderDetail.csv', sep=';')
df4= pd.read_csv('/content/Person.Person.csv', sep=';')
df5= pd.read_csv('/content/Sales.Customer.csv', sep=';')
df6= pd.read_csv('/content/Production.Product.csv', sep=';')
```
- Os dados foram modelados e transformados conforme necessário para garantir que estivessem prontos para análise.
```python
# Esta linha converte a coluna 'ModifiedDate' para datetime
df['ModifiedDate'] = pd.to_datetime(df['ModifiedDate'])

# Calcula a contagem de dados faltantes em cada coluna
dados_faltantes = df.isna().sum()

# Lista de colunas que precisam ter vírgulas substituídas por pontos e serem convertidas para float
colunas_para_substituir = ['SubTotal', 'TaxAmt', 'Freight', 'TotalDue']

# Substitui vírgulas por pontos nas colunas especificadas
df2[colunas_para_substituir] = df2[colunas_para_substituir].str.replace(',', '.', regex=True)

# Converte as colunas para o tipo float
df2[colunas_para_substituir] = df2[colunas_para_substituir].astype(float)

# Converter a coluna ModifiedDate em tipo datetime
df2['ModifiedDate'] = pd.to_datetime(df2['ModifiedDate'])

# Transforme as colunas em formato de data
df2['OrderDate'] = pd.to_datetime(df2['OrderDate'])
df2['DueDate'] = pd.to_datetime(df2['DueDate'])
df2['ShipDate'] = pd.to_datetime(df2['ShipDate'])

# Substitua todos os valores nulos por 0
df2.fillna(0, inplace=True)

# Substituir vírgulas por pontos na coluna 'UnitPrice'
df3['UnitPriceDiscount'] = df3['UnitPriceDiscount'].str.replace(',', '.')

# Converter a coluna 'UnitPrice' para float
df3['UnitPriceDiscount'] = df3['UnitPriceDiscount'].astype(float)

# Converter a coluna ModifiedDate em tipo datetime
df3['ModifiedDate'] = pd.to_datetime(df3['ModifiedDate'])

# Converter a coluna ModifiedDate em tipo datetime
df4['ModifiedDate'] = pd.to_datetime(df4['ModifiedDate'])

# Converter a coluna ModifiedDate em tipo datetime
df5['ModifiedDate'] = pd.to_datetime(df5['ModifiedDate'])

df5['StoreID'].fillna(0, inplace=True)
df5['StoreID'] = df5['StoreID'].astype(float)
df5['PersonID'].fillna(0, inplace=True)
df5['PersonID'] = df5['PersonID'].astype(float)

# Converter a coluna ModifiedDate em tipo datetime
df6['ModifiedDate'] = pd.to_datetime(df6['ModifiedDate'])

# Converter a coluna ModifiedDate em tipo datetime
df6['SellStartDate'] = pd.to_datetime(df6['SellStartDate'])

# Converte os valores para o formato de data
df6['SellEndDate'] = pd.to_datetime(df6['SellEndDate'], errors='coerce', format='%Y-%m-%d')

# Substitui "não informado" por 0 nas colunas ProductSubcategoryID e ProductModelID
df6['ProductSubcategoryID'].replace('não informado', 0, inplace=True)
df6['ProductModelID'].replace('não informado', 0, inplace=True)

# Converte as colunas para o tipo inteiro (int)
df6['ProductSubcategoryID'] = df6['ProductSubcategoryID'].astype(int)
df6['ProductModelID'] = df6['ProductModelID'].astype(int)

# Substitui "," por "." nas colunas "ListPrice" e "StandardCost"
df6['ListPrice'] = df6['ListPrice'].str.replace(',', '.').astype(float)
df6['StandardCost'] = df6['StandardCost'].str.replace(',', '.').astype(float)
```

- Os arquivos csv, após serem modelados e analisados foram salvos.
```python
# Salvar o DataFrame modificado em um arquivo CSV
df.to_csv("Sales.SpecialOfferProduct_modified.csv", index=False)

# Salvar o DataFrame modificado em um arquivo CSV
df2.to_csv("/content/Sales.SalesOrderHeader_modified.csv", sep=';', index=False)

# Salvar o DataFrame modificado em um novo arquivo CSV
df3.to_csv('/content/Sales.SalesOrderDetail_modified.csv', sep=';', index=False)

# Salva as modificações de volta em um arquivo CSV
df4.to_csv('/content/Person.Person_modified.csv', sep=';', index=False)

# Especificar o caminho para o arquivo de destino
caminho_destino = '/content/Sales.Customer_modified.csv'

# Salvar o DataFrame modificado em um arquivo CSV
df5.to_csv(caminho_destino, index=False, sep=';')

# Salva as modificações de volta em um arquivo CSV
df6.to_csv('/content/Production.Product_modified.csv', sep=';', index=False)
```

## 🗄️ As tabelas correspondentes foram criadas em um banco de dados SQL

```sql
CREATE TABLE [dbo].[salesorderheader] (
    SalesOrderID serial PRIMARY KEY,
    RevisionNumber integer,
    OrderDate date,
    DueDate date,
    ShipDate date,
    Status integer,
    OnlineOrderFlag integer,
    SalesOrderNumber varchar(255),
    PurchaseOrderNumber varchar(255),
    AccountNumber varchar(255),
    CustomerID integer,
    SalesPersonID double precision,
    TerritoryID integer,
    BillToAddressID integer,
    ShipToAddressID integer,
    ShipMethodID integer,
    CreditCardID double precision,
    CreditCardApprovalCode varchar(255),
    CurrencyRateID double precision,
    SubTotal double precision,
    TaxAmt double precision,
    Freight double precision,
    TotalDue double precision,
    Comment text,
    rowguid uuid,
    ModifiedDate date
);

CREATE TABLE [dbo].[Production.Product_modified]  (
    product_id serial PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    product_number VARCHAR(50) NOT NULL,
    make_flag INTEGER NOT NULL,
    finished_goods_flag INTEGER NOT NULL,
    color VARCHAR(50) NOT NULL,
    safety_stock_level INTEGER NOT NULL,
    reorder_point INTEGER NOT NULL,
    standard_cost NUMERIC(10, 2) NOT NULL,
    list_price NUMERIC(10, 2) NOT NULL,
    size VARCHAR(50) NOT NULL,
    size_unit_measure_code VARCHAR(10) NOT NULL,
    weight_unit_measure_code VARCHAR(10) NOT NULL,
    weight NUMERIC(10, 2) NOT NULL,
    days_to_manufacture INTEGER NOT NULL,
    product_line VARCHAR(50) NOT NULL,
    class VARCHAR(50) NOT NULL,
    style VARCHAR(50) NOT NULL,
    product_subcategory_id INTEGER NOT NULL,
    product_model_id INTEGER NOT NULL,
    sell_start_date DATE NOT NULL,
    sell_end_date DATE,
    discontinued_date DATE NOT NULL,
    rowguid UUID NOT NULL,
    modified_date TIMESTAMP NOT NULL
);

CREATE TABLE [dbo].[Person.Person_modified] (
    BusinessEntityID INT,
    PersonType VARCHAR(255),
    NameStyle INT,
    Title VARCHAR(255),
    FirstName VARCHAR(255),
    MiddleName VARCHAR(255),
    LastName VARCHAR(255),
    Suffix VARCHAR(255),
    EmailPromotion INT,
    AdditionalContactInfo TEXT,
    Demographics TEXT,
    rowguid UUID,
    ModifiedDate TIMESTAMP
);

CREATE TABLE [dbo].[Sales.Customer_modified]r (
    CustomerID INT PRIMARY KEY,
    PersonID INT,
    StoreID INT,
    TerritoryID INT,
    AccountNumber VARCHAR(255),
    rowguid UUID,
    ModifiedDate TIMESTAMP
);

CREATE TABLE [dbo].[Sales.SalesOrderDetail_modified] (
    SalesOrderID INT,
    SalesOrderDetailID INT,
    CarrierTrackingNumber VARCHAR(255),
    OrderQty INT,
    ProductID INT,
    SpecialOfferID INT,
    UnitPrice NUMERIC(10, 2), -- Ajuste a precisão conforme necessário
    UnitPriceDiscount NUMERIC(10, 2), -- Ajuste a precisão conforme necessário
    LineTotal NUMERIC(10, 2), -- Ajuste a precisão conforme necessário
    rowguid UUID, -- Se for uma coluna UUID, caso contrário, ajuste o tipo de dados
    ModifiedDate TIMESTAMP
);

CREATE TABLE [dbo].[Sales.SpecialOfferProduct_modified] (
    SpecialOfferID integer,
    ProductID integer,
    rowguid uuid,
    ModifiedDate timestamp
);

```

## 📊 Consultas SQL

Aqui estão as consultas SQL executadas para responder às perguntas específicas:

### 1. Contagem de Linhas na Tabela Sales.SalesOrderDetail

```sql
SELECT SalesOrderID, COUNT(*) as QuantidadeDeLinhas
FROM [dbo].[Sales.SalesOrderDetail_modified]
GROUP BY SalesOrderID
HAVING COUNT(*) >= 3;
```
### 2. Os 3 Produtos Mais Vendidos por Dias para Manufatura
```sql
SELECT TOP 3 pp.Name AS ProductName, pp.DaysToManufacture, SUM(sod.OrderQty) AS TotalQuantitySold
FROM [dbo].[Sales.SalesOrderDetail_modified] sod
INNER JOIN [dbo].[Production.Product_modified] pp ON sod.ProductID = pp.productid
GROUP BY pp.Name, pp.DaysToManufacture
ORDER BY SUM(sod.OrderQty) DESC;
```
### 3. Lista de Clientes e Contagem de Pedidos
```sql
Select p.FirstName, p.LastName, COUNT(soh.SalesOrderID) as ContagemPedidos
FROM [dbo].[Person.Person_modified] p
Join [dbo].[Sales.Customer_modified] c ON p.BusinessEntityID = c.PersonID
JOIN [dbo].[salesorderheader] soh ON c.CustomerID = soh.CustomerID
GROUP BY p.FirstName, p.LastName;
```
### 4. Soma Total de Produtos por ProductID e OrderDate
```sql
SELECT 
    pp.productid AS ProductID,
    soh.orderdate AS OrderDate,
    SUM(sod.orderqty) AS TotalOrderQty
FROM 
    [dbo].[Sales.SalesOrderDetail_modified] sod
INNER JOIN 
    [dbo].[salesorderheader] soh ON sod.salesorderid = soh.salesorderid
INNER JOIN 
    [dbo].[Production.Product_modified] pp ON sod.productid = pp.productid
GROUP BY 
    pp.productid, soh.orderdate
ORDER BY 
    soh.orderdate, pp.productid;
```

### 5. Lista de Ordens em Setembro de 2011 com Total Devido Acima de 1.000

```sql
SELECT salesOrderID, orderdate, totaldue
FROM [dbo].[salesorderheader]
WHERE YEAR(orderdate) = 2011 AND MONTH(orderdate) = 9 AND totaldue > 1000
ORDER BY totaldue DESC;
```

## 🚀 Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para criar problemas, enviar solicitações de pull ou relatar quaisquer problemas que você encontrar no projeto.

*Autor:* [Thiago Klebis](https://www.linkedin.com/in/thiagoklebis/)
