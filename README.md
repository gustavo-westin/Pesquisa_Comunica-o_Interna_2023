# Pesquisa Comunicão Interna 2023

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


## PESQUISA E A AMOSTRA
A pesquisa de comunicação de 2023 teve 1615 respondentes (15% da população), com nível de confiança de 95% e erro estimado de 2,27%. A pesquisa buscou obter informações sobre a percepção do público interno sobre os canais de comunicação, seus atributos, o papel da liderança, a satisfação do cliente, entre outros temas de interesse. 


Distribuição entre o público interno:<br>


| Por diretoria | Por cargo |
|----------|----------|
| ![Alt text 1](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/ca21d208-d112-4e86-b083-7c7cd7e78653) | ![Alt text 2](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/334600f6-105d-4fab-a839-0d8bef3ff308) |


Obs: os respondentes tem variação distribuída quanto ao tempo de casa e a idade <br>
Também não há correlação relevante entre as colunas numéricas

![image](https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/4f80616e-7297-4486-8e4e-cd7457bec386)



## LIMPEZA DOS DADOS

Dados vindos de pesquisa precisam passar por um rigoroso processo de uniformização, que envolve diferentes processos de tratamento para facilitar a manipulação e as operações com as variáveis. Eliminar valores ausentes ou nulos, padronizar padrões de escrita, realizar slice para obter as siglas de diretorias e superintendências são alguns exemplos desse processo.

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
  <img src="https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/b1026aea-59a9-4c82-900c-73999d2d701f" alt="Descrição da Imagem">
</p>


<p align="center">
  <img src="https://github.com/gustavo-westin/Pesquisa_Comunicao_Interna_2023/assets/113940727/2b02cfc1-7a75-4b9f-a0fa-0f0e7c1cf977" alt="Descrição da Imagem)">
</p>

Os dados foram então avaliados, quanto a preferência, por gênero e idade para cada canal.

Exemplo:

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


Depois pelo perfil profissional.
Exemplo:
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




E por diretoria.

Exemplo: 

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






