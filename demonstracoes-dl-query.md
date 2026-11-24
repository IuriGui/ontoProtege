# Demonstrações em DL Query

Consultas sobre a ontologia do Sistema ERP (`sistema-erp-completo.ttl`), para executar no Protégé.

**Preparação:** abrir a ontologia, executar **Reasoner → HermiT → Start reasoner** e abrir a aba **DL Query**. Antes de cada consulta, marcar a opção indicada em "Marcar".

## 1 Classificação automática

Nenhuma destas classes foi atribuída diretamente às instâncias do resultado.

### 1.1 Quem são os clientes?

```
Cliente
```

- **Marcar:** Instances
- **Resultado esperado:** joao, mercadoLtda
- **Por quê:** ambos foram declarados apenas como Pessoa, mas são compradores em uma venda, e todo comprador é Cliente.

### 1.2 Quem são os fornecedores?

```
Fornecedor
```

- **Marcar:** Instances
- **Resultado esperado:** carlos, techLtda
- **Por quê:** ambos foram declarados apenas como Pessoa, mas fornecem produtos, e todo aquele que fornece é Fornecedor.

### 1.3 Quem é cliente recorrente?

```
ClienteRecorrente
```

- **Marcar:** Instances
- **Resultado esperado:** joao
- **Por quê:** joao é comprador em duas vendas, venda1 e venda2, declaradas distintas.

### 1.4 Quem tem cobrança pendente?

```
ClienteComPendencia
```

- **Marcar:** Instances
- **Resultado esperado:** joao
- **Por quê:** joao tem a cobranca1 pendente.

### 1.5 Quais são os produtos?

```
Produto
```

- **Marcar:** Instances
- **Resultado esperado:** notebook, mouse, cadeira
- **Por quê:** nenhum dos três tem classe declarada. Eles foram vendidos em uma venda, e só produtos são vendidos.

## 2 Relações inferidas

### 2.1 Quem comprou o notebook?

```
Comprou value notebook
```

- **Marcar:** Instances
- **Resultado esperado:** joao
- **Por quê:** a compra não foi declarada. Ela decorre de joao ser o comprador na venda1, em que o notebook foi vendido.

### 2.2 O que joao comprou?

```
foiCompradoPor value joao
```

- **Marcar:** Instances
- **Resultado esperado:** notebook, mouse
- **Por quê:** se joao comprou um produto, o produto foi comprado por joao.

### 2.3 Quais pessoas participam de uma venda?

```
Pessoa and (participatedIn some RegistroDeVenda)
```

- **Marcar:** Instances
- **Resultado esperado:** joao, mercadoLtda
- **Por quê:** ser comprador em uma venda é uma forma de participar dela.

### 2.4 Quais operações financeiras fazem parte de uma cobrança?

```
OperacaoFinanceira and (isComponentOf some Cobranca)
```

- **Marcar:** Instances
- **Resultado esperado:** operacao1
- **Por quê:** operacao1 é uma operação parcelada, logo OperacaoFinanceira, e faz parte de cobranca1. A operacao2 faz parte de uma conta.

## 3 Consultas combinadas

### 3.1 Quem fornece produtos comprados por mercadoLtda?

```
Fornecedor and (Fornece some (foiCompradoPor value mercadoLtda))
```

- **Marcar:** Instances
- **Resultado esperado:** carlos
- **Por quê:** carlos fornece a cadeira, comprada por mercadoLtda. Nem Fornecedor nem a compra foram declarados.

### 3.2 Quem fornece produtos comprados por clientes recorrentes?

```
Fornecedor and (Fornece some (foiCompradoPor some ClienteRecorrente))
```

- **Marcar:** Instances
- **Resultado esperado:** techLtda
- **Por quê:** techLtda fornece o notebook e o mouse, comprados por joao, que é classificado como cliente recorrente.

### 3.3 Quais produtos foram comprados por clientes com pendência?

```
Produto and (foiCompradoPor some ClienteComPendencia)
```

- **Marcar:** Instances
- **Resultado esperado:** notebook, mouse

## 4 Consultas sobre propriedades

### 4.1 Quais produtos custam mais de 500?

```
Produto and (preco some xsd:decimal[> 500])
```

- **Marcar:** Instances
- **Resultado esperado:** notebook, cadeira
- **Por quê:** os preços são 3500,00 e 600,00. O mouse custa 80,00.

### 4.2 Quais estoques têm menos de 50 unidades?

```
Estoque and (quantidade some xsd:integer[< 50])
```

- **Marcar:** Instances
- **Resultado esperado:** estoqueCentral

### 4.3 Quais clientes têm cobrança pendente acima de 1000?

```
Cliente and (temCobrancaPendente some (valor some xsd:decimal[> 1000]))
```

- **Marcar:** Instances
- **Resultado esperado:** joao
- **Por quê:** a cobranca1 tem valor de 3500,00.

## 5 Encadeamento dos processos

Estas consultas usam as regras das classes.

### 5.1 O que leva à geração de uma cobrança?

```
broughtAbout some (contributedToTrigger some GerarCobranca)
```

- **Marcar:** Subclasses e Instances
- **Resultado esperado (Subclasses):** RegistroDeVenda
- **Resultado esperado (Instances):** venda1, venda2, venda3
- **Por quê:** toda venda produz a situação ProdutoVendido, que contribui para disparar a geração de uma cobrança. Só a venda1 tem a situação declarada. Para as outras duas, o reasoner sabe que ela existe.

### 5.2 O que leva à geração de contas?

```
broughtAbout some (contributedToTrigger some GerarContas)
```

- **Marcar:** Subclasses e Instances
- **Resultado esperado (Subclasses):** CompraDeProdutos
- **Resultado esperado (Instances):** compra1

### 5.3 Quais situações alteram o estoque?

```
contributedToTrigger some AlteracaoEstoque
```

- **Marcar:** Subclasses
- **Resultado esperado:** ProdutoVendido, ProdutoComprado

Em Subclasses, o Protégé também pode listar `owl:Nothing`, que é subclasse de todas as classes.

## 6 Disjunção e mundo aberto

### 6.1 Algo pode ser Pessoa e Empresa?

```
Pessoa and Empresa
```

- **Marcar:** Equivalent classes
- **Resultado esperado:** owl:Nothing
- **Por quê:** as duas classes são disjuntas, então nenhuma instância pode pertencer a ambas.

### 6.2 Quais clientes certamente não são a empresa que utiliza o ERP?

```
Cliente and not Empresa
```

- **Marcar:** Instances
- **Resultado esperado:** joao, mercadoLtda
- **Por quê:** todo Cliente é Pessoa, e Pessoa e Empresa são disjuntas, o que permite **provar** que nenhum dos dois é Empresa. Na ontologia, Empresa é a que utiliza o ERP, e Pessoa pode ser física ou jurídica.

### 6.3 Quais clientes não têm pendência?

```
Cliente and not ClienteComPendencia
```

- **Marcar:** Instances
- **Resultado esperado:** nenhum
- **Por quê:** mercadoLtda não tem pendência declarada, mas a ausência de informação não prova que ela não exista. Esse é o pressuposto do mundo aberto da OWL. Em 6.2, a negação pôde ser provada; aqui, não.

## 7 Demonstração de inconsistência

1. Na aba **Individuals**, selecionar mercadoLtda e acrescentar a relação `compradorEm venda1`.
2. Sincronizar o reasoner.

- **Resultado esperado:** ontologia inconsistente
- **Por quê:** a venda1 passaria a ter dois clientes, joao e mercadoLtda, declarados distintos, mas cada venda tem exatamente um cliente.

Depois, remover a relação e sincronizar de novo. Enquanto a ontologia estiver inconsistente, as consultas não funcionam.

## 8 Perguntas de competência

Todas as perguntas do artigo, com o comando, a opção a marcar e o resultado esperado. Algumas repetem consultas das seções anteriores.

### QC1 Quem são os clientes?

```
Cliente
```

- **Marcar:** Instances
- **Resultado esperado:** joao, mercadoLtda

### QC2 Quem são os fornecedores?

```
Fornecedor
```

- **Marcar:** Instances
- **Resultado esperado:** carlos, techLtda

### QC3 Quais clientes são recorrentes?

```
ClienteRecorrente
```

- **Marcar:** Instances
- **Resultado esperado:** joao

### QC4 Quais clientes possuem cobrança pendente?

```
ClienteComPendencia
```

- **Marcar:** Instances
- **Resultado esperado:** joao

### QC5 Quais instâncias são registros de venda?

```
RegistroDeVenda
```

- **Marcar:** Instances
- **Resultado esperado:** venda1, venda2, venda3

### QC6 Quem comprou um determinado produto?

```
Comprou value notebook
```

- **Marcar:** Instances
- **Resultado esperado:** joao

### QC7 Quais produtos um cliente comprou?

```
foiCompradoPor value joao
```

- **Marcar:** Instances
- **Resultado esperado:** notebook, mouse

### QC8 Quem fornece produtos comprados por clientes recorrentes?

```
Fornecedor and (Fornece some (foiCompradoPor some ClienteRecorrente))
```

- **Marcar:** Instances
- **Resultado esperado:** techLtda

### QC9 De qual fornecedor foi realizada uma compra?

```
Fornecedor and (inverse mediates some (wasCreatedIn value compra1))
```

- **Marcar:** Instances
- **Resultado esperado:** techLtda

### QC10 Que evento leva à geração de uma cobrança?

```
broughtAbout some (contributedToTrigger some GerarCobranca)
```

- **Marcar:** Subclasses
- **Resultado esperado:** RegistroDeVenda

### QC11 Que evento leva à geração de contas?

```
broughtAbout some (contributedToTrigger some GerarContas)
```

- **Marcar:** Subclasses
- **Resultado esperado:** CompraDeProdutos

### QC12 Quais alterações do estoque foram causadas por uma venda?

```
AlteracaoEstoque and (inverse contributedToTrigger some ProdutoVendido)
```

- **Marcar:** Instances
- **Resultado esperado:** alteracaoEstoque1

### QC13 Algo pode ser Pessoa e Empresa ao mesmo tempo?

```
Pessoa and Empresa
```

- **Marcar:** Equivalent classes
- **Resultado esperado:** owl:Nothing

### QC14 Uma venda pode ter dois clientes?

```
RegistroDeVenda and (inverse participatedIn min 2 Cliente)
```

- **Marcar:** Equivalent classes
- **Resultado esperado:** owl:Nothing

### QC15 Quais clientes certamente não são a empresa que utiliza o ERP?

```
Cliente and not Empresa
```

- **Marcar:** Instances
- **Resultado esperado:** joao, mercadoLtda

### QC16 Quais clientes certamente não possuem pendência?

```
Cliente and not ClienteComPendencia
```

- **Marcar:** Instances
- **Resultado esperado:** nenhum

### QC17 Quais produtos foram vendidos em uma venda?

```
vendidoEm value venda1
```

- **Marcar:** Instances
- **Resultado esperado:** notebook

### QC18 Quais cobranças são parceladas?

```
Cobranca and (inverse isComponentOf some OperacaoParcelada)
```

- **Marcar:** Instances
- **Resultado esperado:** cobranca1

### QC19 Quais vendas tiveram valor acima de 1000?

```
RegistroDeVenda and (valor some xsd:decimal[> 1000])
```

- **Marcar:** Instances
- **Resultado esperado:** venda1

### QC20 Quais produtos têm menos de 50 unidades em estoque?

```
Produto and (produtoHasEstoque some (quantidade some xsd:integer[< 50]))
```

- **Marcar:** Instances
- **Resultado esperado:** notebook

### QC21 Quais contas vencem antes de dezembro de 2026?

```
Conta and (vencimento some xsd:dateTime[< "2026-12-01T00:00:00"^^xsd:dateTime])
```

- **Marcar:** Instances
- **Resultado esperado:** conta1
