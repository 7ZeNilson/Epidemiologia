# Epidemiologia
Análise de Dados: Epidemiologia

# **Projeto de Análise de Dados: Epidemiologia e Doenças de Impacto em Saúde Coletiva**

---

## **1. Introdução: Contexto e Objetivo Analítico**

- **Contexto:** A epidemiologia é fundamental para compreender a distribuição, frequência e determinantes das doenças em populações. Em saúde coletiva, a análise de dados permite identificar padrões, monitorar surtos e subsidiar políticas públicas baseadas em evidências.
- **Problema:** Grandes volumes de dados epidemiológicos (casos, óbitos, vacinação, internações) são frequentemente subutilizados ou analisados de forma superficial, limitando a tomada de decisão estratégica.
- **Objetivo:** Desenvolver um pipeline de **Análise de Dados** capaz de explorar, transformar e gerar insights a partir de dados epidemiológicos, com foco em doenças de alto impacto (ex: dengue, COVID-19, influenza), apoiando decisões em saúde pública.

---

## **2. Coleta e Importação dos Dados**

- **Fontes de Dados:**
    - Sistemas públicos (DATASUS, OpenDataSUS)
    - Bases epidemiológicas (casos confirmados, óbitos, vacinação)
    - Dados demográficos (IBGE)

```
importpandasaspd

# Importação das bases
casos=pd.read_csv("casos_epidemiologicos.csv")
obitos=pd.read_csv("obitos.csv")
vacinacao=pd.read_csv("vacinacao.csv")

# Visualização inicial
display(casos.head())
display(obitos.head())
display(vacinacao.head())
```

---

## **3. Entendimento e Estruturação dos Dados**

- **Análise estrutural**

```
casos.info()
obitos.info()
vacinacao.info()
```

- **Padronização de colunas**

```
casos.columns=casos.columns.str.lower().str.strip()
obitos.columns=obitos.columns.str.lower().str.strip()
vacinacao.columns=vacinacao.columns.str.lower().str.strip()
```

- **Conversão de datas**

```
casos["data_notificacao"]=pd.to_datetime(casos["data_notificacao"])
obitos["data_obito"]=pd.to_datetime(obitos["data_obito"])
vacinacao["data_aplicacao"]=pd.to_datetime(vacinacao["data_aplicacao"])
```

---

## **4. Limpeza e Tratamento de Dados**

- **Tratamento de valores nulos**

```
casos=casos.dropna(subset=["municipio","data_notificacao"])
obitos=obitos.dropna(subset=["data_obito"])
vacinacao=vacinacao.fillna(0)
```

- **Remoção de duplicidades**

```
casos=casos.drop_duplicates()
obitos=obitos.drop_duplicates()
vacinacao=vacinacao.drop_duplicates()
```

- **Padronização de categorias**

```
casos["sexo"]=casos["sexo"].replace({"M":"Masculino","F":"Feminino"})
```

---

## **5. Integração das Bases de Dados**

- **Merge entre datasets**

```
base=casos.merge(obitos,on=["id_paciente"],how="left")
base=base.merge(vacinacao,on=["id_paciente"],how="left")

display(base.head())
```

---

## **6. Criação de Indicadores Epidemiológicos**

- **Taxa de incidência**

```
populacao=pd.read_csv("populacao.csv")

base_inc=base.groupby("municipio").size().reset_index(name="casos")

base_inc=base_inc.merge(populacao,on="municipio")

base_inc["taxa_incidencia"]= (base_inc["casos"]/base_inc["populacao"])*100000
```

- **Taxa de letalidade**

```
letalidade=base.groupby("municipio").agg(
casos=("id_paciente","count"),
obitos=("data_obito",lambdax:x.notnull().sum())
).reset_index()

letalidade["taxa_letalidade"]= (letalidade["obitos"]/letalidade["casos"])*100
```

- **Cobertura vacinal**

```
vacinados=vacinacao.groupby("municipio").size().reset_index(name="vacinados")

cobertura=vacinados.merge(populacao,on="municipio")
cobertura["cobertura_vacinal"]= (cobertura["vacinados"]/cobertura["populacao"])*100
```

---

## **7. Análise Exploratória de Dados (EDA)**

- **Casos ao longo do tempo**

```
casos_tempo=casos.groupby("data_notificacao").size()

casos_tempo.plot(figsize=(12,6),title="Evolução de Casos ao Longo do Tempo")
```

- **Distribuição por faixa etária**

```
importmatplotlib.pyplotasplt

casos["idade"].hist(bins=20)
plt.title("Distribuição de Idade")
plt.show()
```

- **Top municípios com maior incidência**

```
top_municipios=base_inc.sort_values("taxa_incidencia",ascending=False).head(10)
print(top_municipios)
```

---

## **8. Análise Temporal e Sazonalidade**

```
casos["mes"]=casos["data_notificacao"].dt.month

sazonalidade=casos.groupby("mes").size()

sazonalidade.plot(kind="bar",title="Sazonalidade dos Casos")
```

---

## **9. Geração de Insights Estratégicos**

- Identificação de regiões críticas com alta incidência
- Correlação entre baixa vacinação e aumento de casos
- Padrões sazonais de doenças (ex: aumento de dengue em períodos chuvosos)
- Perfil demográfico mais afetado

---

## **10. Criação de Dashboard (Power BI / Python)**

```
importseabornassns

sns.barplot(data=top_municipios,x="taxa_incidencia",y="municipio")
plt.title("Top Municípios por Incidência")
plt.show()
```

---

## **11. Automação do Pipeline de Análise**

```
defpipeline_analise():
# Importação
casos=pd.read_csv("casos_epidemiologicos.csv")

# Limpeza
casos=casos.dropna()

# Agregação
resumo=casos.groupby("municipio").size()

returnresumo

resultado=pipeline_analise()
print(resultado)
```

---

## **12. Conclusão**

- A análise permitiu identificar padrões epidemiológicos relevantes
- Dados tratados possibilitam decisões mais assertivas
- Indicadores como incidência e letalidade são essenciais para gestão pública

---

## **13. Próximos Passos**

- Integração com dados em tempo real (APIs públicas)
- Geolocalização e mapas (GeoPandas / Folium)
- Modelos preditivos (evolução de surtos)
- Deploy de dashboards interativos

---

## **14. Impacto Esperado**

- Melhor alocação de recursos em saúde
- Resposta mais rápida a surtos epidemiológicos
- Suporte à formulação de políticas públicas
- Redução de morbidade e mortalidade

---

## **15. Considerações Finais**

Este projeto estabelece um pipeline robusto de **Análise de Dados Epidemiológicos**, cobrindo desde a ingestão até a geração de insights estratégicos. A abordagem orientada a dados fortalece a atuação da saúde coletiva, permitindo decisões baseadas em evidências e maior eficiência na gestão pública.
