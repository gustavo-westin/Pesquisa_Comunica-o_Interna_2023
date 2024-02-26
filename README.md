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

Há algumas alterações relevantes nos dados para torná-los melhor manipuláveis ao longo do processo de análise, como: eliminar valores ausentes ou nulos, padronizar padrões de escrita, realizar slice para obter as siglas de diretorias e superintendências.

Os exemplos abaixo demonstram essas operações:


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
#include <iostream>

int main() {
    std::cout << "Hello, world!" << std::endl;
    return 0;
}
```




```c++
#include <iostream>

int main() {
    std::cout << "Hello, world!" << std::endl;
    return 0;
}
```






