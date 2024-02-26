# Pesquisa Comunicao Interna 2023

:droplet:@Sabesp:droplet:, 

esse é o processo de **ETL** (Extract, Transform and Load) e **análise de dados** da Pesquisa de Comunicação Interna de 2023.

:file_folder: Files: o arquivo principal é o Pesq_Interna_2023.ipynb, com os códigos e visualização. O arquivo PPT é a parte visual que demonstra a narrativa.

:computer: Processo de ETL:
*  extração automática em nuvem (azure)
* carga em um csv
*  transformação dos dados via código (python) no próprio Jupyter Notebook
*  Carga dos dados em planilhas na nuvem


:chart_with_upwards_trend: Processo de análise de dados:
*  limpeza dos dados
*  anáise estatística descritiva
*  análise qualitativa
*  análise de recorrência

:books: Bibliotecas utilizadas: numpy, pandas, matplot, spacy e wordcloud.

# RESUMO


## PESQUISA E A AMOSTRA
A pesquisa de comunicação de 2023 teve 1615 respondentes (15% da população), com nível de confiança de 95% e erro estimado de 2,27%. A pesquisa buscou obter informações sobre a percepção do público interno sobre os canais de comunicação, seus atributos, o papel da liderança e a satisfação do cliente, entre outros temas. 


Distribuição entre o público interno:<br>


| Por diretoria | Por cargo |
|----------|----------|
| ![Alt text 1](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/ca21d208-d112-4e86-b083-7c7cd7e78653) | ![Alt text 2](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/334600f6-105d-4fab-a839-0d8bef3ff308) |


Obs: os respondentes tem variação distribuída quanto ao tempo de casa e a idade <br>
Também não há correlação relevante entre as colunas numéricas

![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/4f80616e-7297-4486-8e4e-cd7457bec386)



## LIMPEZA DOS DADOS

Dados vindos de pesquisa precisam passar por um rigoroso processo de uniformização, que envolve diferentes processos de tratamento para facilitar a manipulação e as operações com variáveis. Eliminar valores ausentes ou nulos, padronizar padrões de escrita, realizar slice para obter as siglas de diretorias e superintendências são alguns exemplos.

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
Após o tratamento dos dados é possível obter informações sobre o padrão de distribuição e dispersão dos dados. A primeira análise é de prioridade dos veículso de comunicação interna:
<p align="center">
  <img src="[(caminho/para/sua/imagem.png](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/b1026aea-59a9-4c82-900c-73999d2d701f)]" alt="Descrição da Imagem">
</p>


<p align="center">
  <img src="[(https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/2b02cfc1-7a75-4b9f-a0fa-0f0e7c1cf977]" alt="Descrição da Imagem)">
</p>








