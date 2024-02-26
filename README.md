![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/ef6cde11-5877-4126-ba33-e76009cac4bb)# Pesquisa Comunição Interna 2023

:droplet:@Sabesp:droplet:, 

esse é o processo de **ETL** (Extract, Transform and Load) e **Análise de Dados** da Pesquisa de Comunicação Interna de 2023.

:file_folder: Files: o arquivo principal é o Pesq_Interna_2023.ipynb, com os códigos e visualização; o arquivo PPT contém a apresentação detalhada da pesquisa e os resultados.

:computer: Processo de ETL:
*  extração dos formulários em nuvem (azure)
*  transformação em csv
*  carga em ambiente Python para manipulação (Jupyter Notebook)
*  nova transformação para disponibilização local em xlsx com dados tratados


:chart_with_upwards_trend: Processo de análise de dados:
*  limpeza e manipulação dos dados 
*  anáise estatística descritiva
*  análise qualitativa
*  análise de recorrência

:books: Bibliotecas utilizadas: numpy, pandas, matplot, spacy e wordcloud.

# RESUMO
Breve resumo da metodologia, aplicação e resultados. Dividido nas seguintes seções:

*  Pesquisa e amostra: informações sobre a amostra e suas variáveis
*  Limpeza e tratamento dos dados: breve exemplificação das técnicas aplicadas
*  Análise descritiva: demonstração simplificada do emprego de técnicas estatísticas utilizados e cruzamento de variáveis
*  análise qualitativa: emprega-se técnicas de recorrência de palavras, agrupamento (clusterização) e comparações não numéricas para avaliar o comportamento e identificar cenários

Obs: o arquivo em PPT contém a apresentação completa e comentada


## PESQUISA E A AMOSTRA
A pesquisa de comunicação de 2023 teve 1615 respondentes (15% da população), com nível de confiança de 95% e erro estimado de 2,27%. A pesquisa buscou obter informações sobre a percepção do público interno sobre os canais de comunicação, seus atributos, o papel da liderança, a satisfação do cliente, entre outros temas de interesse. O arquivo final após tratamento possui **1615 linhas (respondentes) e 54 colunas (variáveis)**.

O perfil dos respondentes é bem distribuído e representativo da população nos quesitos alocação funcional, cargos, gênero, idade e tempo de casa. Há predominância natural de respondentes das unidades M e R (O), mas todas as diretorias, à exceção da K, tem significância estatística.


| Por diretoria | Por cargo |
|----------|----------|
| ![Alt text 1](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/ca21d208-d112-4e86-b083-7c7cd7e78653) | ![Alt text 2](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/334600f6-105d-4fab-a839-0d8bef3ff308) |


Obs: não há correlação relevante entre as colunas numéricas de modo a comprometer análises cruzadas

![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/4f80616e-7297-4486-8e4e-cd7457bec386)



## LIMPEZA E TRATAMENTO DOS DADOS

Dados vindos de pesquisa precisam passar por um rigoroso processo de uniformização, que envolve diferentes processos de tratamento para facilitar a manipulação e as operações com as variáveis. Eliminar valores ausentes ou nulos, padronizar strings e data types, realizar slices para obter as siglas de diretorias e superintendências desejáveis em comparações, aplicar transformação de valores agrupados em variáveis dummies, entre outras atividades de pré-análise. 

Os trechos abaixo demonstram parte dessas operações:


```c++
# split df em duas séries para dropar NaN values
dfnum_serie1 = df_num[['Comunicado Sabesp (serie 0 a 7)', 'E-mail matinal Workplace', 'Jornal Mural', 'Página da Unidade no Portal Sabesp', 'Portal Corporativo Sabesp', 'TV Corporativa', 'Workplace']]
dfnum_serie2 = df_num[['É ágil (serie 0 a 5)', 'Conteúdo divulgado é suficiente e claro', 'Dá visibilidade para as atividades e projetos relacionados ao meu trabalho.', 'Qual o seu nível de satisfação com a Comunicação Interna Sabesp?']]
# dropar linhas NaN para dfnum_serie2
dfnum_serie2 = dfnum_serie2.dropna()
dfnum_serie2.isna().sum()
# serie descritiva do tipo de público respondente
df_profile = df[['Idade', 'Gênero', 'Categoria do cargo', 'Sigla da UN', 'Tempo de atuação na Sabesp']]
df_profile.head()
```



```c++
# encontrar as diretorias e superintendências por slice das entras
# as entradas precisam ser do tipo string e maiusculas (ou minusculas)
 
# Novo df diretoria
df_dir = df_profile['Sigla da UN'].str[0]
df_dir = df_dir.rename('Diretoria')

# Criar um novo DataFrame com as duas primeiras letras
df_sup = df_profile['Sigla da UN'].str[:2]
df_sup = df_sup.rename('Superintendencia')

# Filtrar linhas onde a coluna 'Sigla da UN' começa com 'N', 'O', 'S', 'E', 'U', 'D'
filtros = [
    df_profile['Sigla da UN'].str.startswith('N'),
    df_profile['Sigla da UN'].str.startswith('O'),
    df_profile['Sigla da UN'].str.startswith('S'),
    df_profile['Sigla da UN'].str.startswith('E'),
    df_profile['Sigla da UN'].str.startswith('U'),
    df_profile['Sigla da UN'].str.startswith('D')
]

linhas = pd.concat([df_profile[filtro] for filtro in filtros])

linhas

# adicionar diretoria ao df_profile

df_profile['diretoria'] = df_profile['Sigla da UN'].str[0]
df_profile['sup'] = df_profile['Sigla da UN'].str[0:2]

mapeamento = {
    'SA': 'M',
    'OM': 'R',
    'OG': 'M',
    'OP': 'R',
    'ON': 'M',
    'OD': 'M',
    'EN': 'T',
    'UG': 'M',
    'UB': 'R',
    'NC': 'X',
    'DR': 'X',
    'NO': 'X',
    'NÃ': 'X',
    'E': 'X'
}

# Substituir os valores com base no mapeamento
df_profile['diretoria'] = df_profile['sup'].replace(mapeamento)
# retornar a diretoria com apenas uma letra 
df_profile['diretoria'] = df_profile['diretoria'].str[0]

# Exemplo de exibição do DataFrame resultante
linhas = pd.concat([df_profile[filtro] for filtro in filtros])
linhas
```

## ANÁLISE DESCRITIVA
Após o tratamento dos dados é possível obter informações sobre o padrão de distribuição e dispersão dos dados. São dois eixos principais de análise: priorização dos canais de comunicação e avaliação dos atributos de comunicação e satisfação.


### Priorização dos canais
Nesta seção são avaliados elementos como a percepção do público sobre a priorização dos canais de comunicação (sete) de forma comparada com alguns elementos da amostra: gênero, idade, diretoria, atuação da liderança. Cada análise é realizada de forma separada para cada um dos sete canais corporativos. 

A análise base é prioridade geral dos veículso de comunicação interna:
<p align="center">
  <img src="https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/b1026aea-59a9-4c82-900c-73999d2d701f" alt="Descrição da Imagem">
</p>


<p align="center">
  <img src="https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/2b02cfc1-7a75-4b9f-a0fa-0f0e7c1cf977" alt="Descrição da Imagem)">
</p>

A partir desse primeiro parâmetro realiza-se cruzamentos para comparação. Para fins de vizualização, respeitando os critérios e a demonstração de dispersão dos dados, optou-se por modelos comparados de boxplot.

No exemplo abaixo avaliamos a preferência por gênero e idade para o canal **Workplace**

```
# ordem desejada do eixo x para todas as séries abaixo
ordem_desejada = ['Menor de 18 anos', 'Entre 18 e 30 anos', 'Entre 31 e 40 anos', 'Entre 41 e 50 anos', 'Entre 51 e 60 anos', 'Entre 61 e 70 anos']

# gráfico boxplot para e-mail por idade e gênero
email = sns.catplot(x="Idade", y="E-mail matinal Workplace",
                col="Gênero",
                data=df_idade, kind="box",              
                height=4.8, aspect=0.9)

# Ajuste a posição inicial dos rótulos no eixo x
email.set_xticklabels(rotation=45, ha='right')


# Rotacionar os rótulos do eixo x em 45 graus
for ax in email.axes.flat:
    ax.set_xticks(range(len(ordem_desejada)))
    ax.set_xticklabels(ordem_desejada)
    
    # Adicionar linha pontilhada vermelha para a média
    mean_value = df_idade['E-mail matinal Workplace'].mean()
    ax.axhline(mean_value, color='red', linestyle='dashed', linewidth=1.5, label='Média')
    
    
# Adicionar título geral e subtítulo
plt.figtext(0.05, 1.15, 'Canal Interno por Faixa Etária e Gênero', fontsize=14, fontweight='bold', ha='left')
plt.figtext(0.05, 1.10, 'E-mail matinal Workplace', fontsize=12, fontweight='bold', color='gray', ha='left')

plt.show()
```
![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/f9b6e5a5-c20b-4db4-89ea-b4f48f1632b6)


Neste outro exemplo avaliamos a preferência por cargo, em análise comparada do E-mail matinal, Comunicado Sabesp e Workplace:
```
# gráfico boxplot para Comunicado Sabesp por idade e gênero
email = sns.catplot(x="Categoria do cargo", y="Workplace",
                col="Sua liderança estimula o uso da plataforma?",
                data=df_plot, kind="box",               
                height=4.8, aspect=0.9)

# Ajuste a posição inicial dos rótulos no eixo x
email.set_xticklabels(rotation=45, ha='right')

# Rotacionar os rótulos do eixo x em 45 graus
for ax in email.axes.flat:
    ax.set_xticks(range(len(ordem_cargo)))
    ax.set_xticklabels(ordem_cargo)
    
    # Adicionar linha pontilhada vermelha para a média
    mean_value = df_idade['Workplace'].mean()
    ax.axhline(mean_value, color='red', linestyle='dashed', linewidth=1.5, label='Média')


# Adicionar título geral e subtítulo
plt.figtext(0.05, 1.15, 'Canal Interno por Cargo e Gênero', fontsize=14, fontweight='bold', ha='left')
plt.figtext(0.05, 1.10, 'Workplace, considerando o estímulo da liderança', fontsize=12, fontweight='bold', color='gray', ha='left')
 
    
plt.show()
```

![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/f6b3692c-8d9c-4996-9cd4-fa1f2fbe98ba)




Neste exemplo, avaliamos de acordo com a diretoria de forma comparada em relação ao portal, página da UN, jornal mural e TV corporativa:

```
# gráfico boxplot para Página da Unidade no Portal Sabesp por diretoria
email = sns.catplot(x="diretoria", y="Página da Unidade no Portal Sabesp",
                data=df_dir, kind="box",               
                height=4.8, aspect=0.9)

# Definição de ordem para o eixo x
for ax in email.axes.flat:
    ax.set_xticks(range(len(ordem_diretoria)))
    ax.set_xticklabels(ordem_diretoria)


# Adicionar título geral e subtítulo
plt.figtext(0.05, 1.15, 'Canal Interno por Diretoria', fontsize=14, fontweight='bold', ha='left')
plt.figtext(0.05, 1.10, 'Página da UN', fontsize=12, fontweight='bold', color='gray', ha='left')
 
    
plt.show()
```

![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/81d90779-67bb-4e78-97b9-9a17e70c4eaf)


Outra análise interessante é a variação da percepção de prioridade conforme a presença ou incentivo da liderança ao Workplace.
![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/bb372f1d-b5f8-4b63-ac75-1084a9bf2052)

O que, no entanto, contrasta com a pouca adesão da liderança. 
![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/6dd7f980-1366-4101-ac6a-37d138692101)


### Avaliação dos atributos e satisfação geral
Nesta seção, de caráter mais objetivo e simplificado, observa-se como o público percebe atributos essenciais da comunicação, como agilidade, relevância e visibilidade. Além de avaliar a satisfação da comunicação para diferentes grupos. 

**Avaliação por atributos**<br>


```
#Gráfico conjunto dos atributos

# Criar figura e eixos
fig, axes = plt.subplots(nrows=1, ncols=3, figsize=(15, 5))

# Gráfico de barras para 'Conteúdo divulgado é suficiente e claro'
sns.countplot(x='Conteúdo divulgado é suficiente e claro', data=df_plot, ax=axes[0], color='skyblue', edgecolor='black')
axes[0].set_title('Conteúdo Suficiente')

# Gráfico de barras para 'É ágil (serie 0 a 5)'
sns.countplot(x='É ágil (serie 0 a 5)', data=df_plot, ax=axes[1], color='salmon', edgecolor='black')
axes[1].set_title('Agilidade')

# Gráfico de barras para 'Dá visibilidade para as atividades e projetos relacionados ao meu trabalho.'
sns.countplot(x='Dá visibilidade para as atividades e projetos relacionados ao meu trabalho.', data=df_plot, ax=axes[2], color='lightgreen', edgecolor='black')
axes[2].set_title('Visibilidade')

# Ajustar layout manualmente
plt.subplots_adjust(wspace=0.4)

# Mostrar o gráfico
plt.show()
```

![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/881ecd48-a88e-4b6f-8b84-d42f0356e7f6)

**Avaliação da Satisfação**

| Por diretoria | Por cargo |
|----------|----------|
| ![Alt text 1](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/482d2636-0413-46c5-80c4-b7fb4f9084cf) | ![Alt text 2](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/9fafa7ae-db45-4be3-9b07-3319ba190612) |


### Conclusões
Após análise descritiva é possível observar:
*  De forma geral, não há alterações significativas na percepção dos veículos quando analisamos gêneros.
*  Quanto a idade, há maior adesão ao workplace em uma faixa intermediária, não necessariamente jovens, com uma nítida queda após os 50 anos. TV corporativa e Jornal Mural tem maior adesão em idades maiores.
*  **Não há diferenças significativas entre o nível operacional, técnico e universitário em relação ao Workplace**
*  Independente do cargo, tanto o incentivo como a mera adesão da liderança a plataforma geram efeitos positivos. 
*  Há pouca adesão ao workplace pela liderança (27%)
*  Há uma diferença muito desproporcional de prioridade da liderança em relação ao portal
*  M e R tem comportamentos marcadamente diferentes na priorização, o que não é comum nas pesquisas anteriores
*  Necessário investigar a comunicação em relação a diretoria T no caso do Workplace
*  Não há diferenças significativas na satisfação geral entre diretorias ou cargos, salvo aprendiz


## ANÁLISE QUALITATIVA
Os dados desta análise não são númericos ou categóricos, de modo que é necessário realizar tratamentos específicos para avaliar o conteúdo de forma computacional ou fazer avaliações comparativas com base em uma análise humana. De forma geral, utilizou-se três métodos de análise: 
*  Análise de recorrência, após tokenzinação por NLP (Natural Language Processment)
*  Agrupamento por clusterização (k-means)
*  Análise de texto (humano)

A análise de recorrência pode ser avaliada pela criação de wordclouds. No exemplo abaixo, buscamos a maior recorrência para a pergunta “Existem outros meios de comunicação que você utiliza para se manter informado sobre a Sabesp ou suas atividades?”
```
# Supondo que 'df_veiculos' seja o seu dataframe
# deixei tudo como string e tudo como lower para evitar erros ou repetições não desejadas
text = ' '.join(df_veiculos.astype(str).str.lower())

# Processamento de texto com Spacy
nlp = spacy.load('pt_core_news_sm')
# Transformação do texto em documento
doc = nlp(text)

# Filtrando palavras relevantes (substantivos, adjetivos, etc.) e removendo stopwords
relevant_words = [token.text for token in doc if token.is_alpha and not token.is_stop]

# Criando a nuvem de palavras com WordCloud
wordcloud = WordCloud(width=800, height=400, max_words=200, background_color='white').generate(' '.join(relevant_words))

# Exibindo a nuvem de palavras
plt.figure(figsize=(10, 5))
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis('off')
plt.show()
```
![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/aebe84de-d695-4325-8248-e36b84afa52a)

Já a análise de clusters consegue compor uma avaliação das principais evidências da avaliação positiva ou negativa em relação a TV Corporativa.

![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/e9cb4cc9-4827-45d8-bf83-ae80fb108756)

Já a análise de texto propriamente dita, permite elaborar sentimentos e percepções mais específicas do público, identificado padrões similares entre os pontos de vista e resumindo-os em frases explicativas. O exemplo abaixo se refere ao Workplace.
* “Não tenho tempo para acompanhar tudo, entro somente quando é extremamente necessário.“
* “Gostaria de acompanhar mais, mas a rotina do dia a dia não permite.”
* "O Workplace está mais para uma plataforma de propaganda de eventos"
* "A grande vantagem dessa ferramenta é que as informações não são mais filtradas ou censuradas.”
* “Workplace já faz parte diária das Informações inclusive complementares do portal sabesp”
* “Ajuda na interação com outros colaboradores e outras areas da sabesp”

### Recomendações :dart:
A análise conjunta entre as duas abordagens, estatística descritiva e análise qualitativa, permite ao analista fazer recomendações ao grupo responsável pela comunicação interna. Para não alongar muito a análise, nos ateremos apenas as recomendações do Workplace. O restante do material pode ser acessado nos arquivos anexos. 

![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/fd9c8af9-8446-473c-b796-62bb14a60807)




