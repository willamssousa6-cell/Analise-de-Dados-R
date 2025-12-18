Análise de Dados
================
Willams Sousa Brito
20/10/2025

## **Estudo do Preço do combustível automotivo no Brasil em 1º Sem/2025**

### Uso da Ferramenta de análise de dados - Software R

### **Objetivos da Análise:**

#### ➡️ **Demonstrar através da análise exploratoria de dados (EDA) o processo para contar uma história a partir fontes de dados brutos**

#### ➡️ **Explorar nos dados, descobertas, estruturação, limpeza, união, validação e apresentação**

#### ➡️ **Verificar qualidade, entender o conjunto de dados para extrair insights**

#### ➡️ **Utilizar visualizações de dados geográficos e métodos estatísticos para explorar os dados**

#### ➡️ **Utilizar ferramenta de BI (Tableau) para visualizações de dados**

### **Fonte de Dados:**

#### 1- ANP: Dados do Preço do Combustível Automotivo - 1º semestre do ano de 2025.

#### Acesso através do Portal de Dados Abertos, dados da Agência Nacional do Petróleo, Gás Natural e Biocombustíveis - ANP

#### Link da Planilha <https://dados.gov.br/dados/conjuntos-dados/serie-historica-de-precos-de-combustiveis-e-de-glp>

#### 2- IBGE: Dados de localização dos Municípios.

#### Acesso através da Pacote de Dados Geográficos Brasileiros (geobr), permite coletar dados dos shapefiles do Instituto Brasileiro de Geografia e Estatística (IBGE) e outros conjuntos oficiais de dados espaciais do Brasil

#### Link da BigQuery <https://basedosdados.org/dataset/49ace9c8-ae2d-454b-bed9-9b9492a3a642?table=b39609b4-ffb2-4b4f-a182-47b0d160037b>

### **Importações pacotes e dados para análise:**

### 🎯 Objetivos

#### - Instalar e importar pacotes relevantes para análise.

#### - Carregar os dados em um DataFrame, converte arquivos em estruturas manipulaveis.

#### - Realizar análise prévia dos dados carregados.

#### - Identificar problemas como codificação de texto, separadores, tipos de variáveis.

#### ✔️Instalar pacotes utilizando a função install.packages().

``` r
#Instalar pacotes utilizando 

#install.packages("readr")
#install.packages("dplyr")
#install.packages("tidyverse")
#install.packages("naniar")
#install.packages("readxl")
#install.packages("leaflet")
#install.packages("openxlsx")

#grafico de geolocalização

#install.packages("geobr")
#install.packages("sf")
```

#### ✔️Importar pacotes utilizando a função library().

``` r
#Importar pacotes

library(openxlsx)
```

    ## Warning: pacote 'openxlsx' foi compilado no R versão 4.5.2

``` r
library(naniar)
library(readr)
library(readxl)
library(dplyr)
```

    ## 
    ## Anexando pacote: 'dplyr'

    ## Os seguintes objetos são mascarados por 'package:stats':
    ## 
    ##     filter, lag

    ## Os seguintes objetos são mascarados por 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(ggplot2)
library(plotly)
```

    ## 
    ## Anexando pacote: 'plotly'

    ## O seguinte objeto é mascarado por 'package:ggplot2':
    ## 
    ##     last_plot

    ## O seguinte objeto é mascarado por 'package:stats':
    ## 
    ##     filter

    ## O seguinte objeto é mascarado por 'package:graphics':
    ## 
    ##     layout

``` r
library(httr)
```

    ## 
    ## Anexando pacote: 'httr'

    ## O seguinte objeto é mascarado por 'package:plotly':
    ## 
    ##     config

``` r
library(jsonlite)
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ forcats   1.0.1     ✔ stringr   1.5.2
    ## ✔ lubridate 1.9.4     ✔ tibble    3.3.0
    ## ✔ purrr     1.1.0     ✔ tidyr     1.3.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ httr::config()   masks plotly::config()
    ## ✖ plotly::filter() masks dplyr::filter(), stats::filter()
    ## ✖ purrr::flatten() masks jsonlite::flatten()
    ## ✖ dplyr::lag()     masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(leaflet)
library(geobr)
```

    ## Carregando namespace exigido: sf

``` r
library(sf)
```

    ## Linking to GEOS 3.13.1, GDAL 3.11.0, PROJ 9.6.0; sf_use_s2() is TRUE

``` r
library(stringi)
```

#### ✔️**Carregar dados em um DataFrame.**

#### O 1º conjunto de dados escolhido está no formato de um arquivo xls (Excel), disponivel para Download no Portal de Dados Abertos.

``` r
#Carregar os dados em um DataFrame e salvar em uma variável chamada (dados.anp) e realizar análise prévia dos dados.
Precos_semestrais_AUTOMOTIVOS_2025_01 <- read_excel("Precos semestrais - AUTOMOTIVOS_2025.01.xlsx")

#salvar em uma variável chamada (dados.anp)
dados.anp<-Precos_semestrais_AUTOMOTIVOS_2025_01

#A função str(), exibe informações básica dos dados importados,como o tipo de objeto (ex: data frame, vetor, lista), número de elementos ou dimensões, nomes das variáveis e seus tipos de dados e uma amostra dos dados.
#revisão básica dos dados importados
str(dados.anp) #nova variavel geografica incluida
```

    ## tibble [420,409 × 16] (S3: tbl_df/tbl/data.frame)
    ##  $ Regiao - Sigla   : chr [1:420409] "NE" "NE" "NE" "NE" ...
    ##  $ Estado - Sigla   : chr [1:420409] "CE" "CE" "CE" "CE" ...
    ##  $ Municipio        : chr [1:420409] "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" ...
    ##  $ Revenda          : chr [1:420409] "ECONOGÁS DO BRASIL DIST. DERIV. DE PET. BIOC. E GÁS NAT" "ECONOGÁS DO BRASIL DIST. DERIV. DE PET. BIOC. E GÁS NAT" "ECONOGÁS DO BRASIL DIST. DERIV. DE PET. BIOC. E GÁS NAT" "ECONOGÁS DO BRASIL DIST. DERIV. DE PET. BIOC. E GÁS NAT" ...
    ##  $ CNPJ da Revenda  : chr [1:420409] "08.775.979/0002-62" "08.775.979/0002-62" "08.775.979/0002-62" "08.775.979/0002-62" ...
    ##  $ Nome da Rua      : chr [1:420409] "RUA TABELIÃO IDELFONSO CAVALCANTI" "RUA TABELIÃO IDELFONSO CAVALCANTI" "RUA TABELIÃO IDELFONSO CAVALCANTI" "RUA TABELIÃO IDELFONSO CAVALCANTI" ...
    ##  $ Numero Rua       : chr [1:420409] "455" "455" "455" "455" ...
    ##  $ Complemento      : chr [1:420409] NA NA NA NA ...
    ##  $ Bairro           : chr [1:420409] "CENTRO" "CENTRO" "CENTRO" "CENTRO" ...
    ##  $ Cep              : chr [1:420409] "62010-000" "62010-000" "62010-000" "62010-000" ...
    ##  $ Produto          : chr [1:420409] "GASOLINA" "GASOLINA ADITIVADA" "DIESEL S10" "ETANOL" ...
    ##  $ Data da Coleta   : POSIXct[1:420409], format: "2025-01-01" "2025-01-01" ...
    ##  $ Valor de Venda   : num [1:420409] 6.29 6.49 6.19 5.19 6.53 6.83 6.38 5.29 6.69 6.79 ...
    ##  $ Valor de Compra  : logi [1:420409] NA NA NA NA NA NA ...
    ##  $ Unidade de Medida: chr [1:420409] "R$ / litro" "R$ / litro" "R$ / litro" "R$ / litro" ...
    ##  $ Bandeira         : chr [1:420409] "RAIZEN" "RAIZEN" "RAIZEN" "RAIZEN" ...

#### O banco de dados dos preços dos combustiveis é um data.frame, possui 420.409 linhas e 16 Colunas(Variaveis), a maioria das variaveis são qualitativas e a variável “Valor de Venda” a única numérica, do tipo contínua”.

#### O 2º conjunto de dados escolhido está disponivel na BigQuery plataforma de análise de dados do Google, os dados podem ser acessados pelo pacote(geobr).

``` r
# Carregar os dados em um DataFrame e salvar em uma variável chamada (municipio) e realizar análise prévia dos dados.

municipio <- read_municipality(year = 2024) #a função permite escolha do ano em que o dado foi coletado.
```

    ## Using year/date 2024

``` r
str(municipio)#informações gerais
```

    ## Classes 'sf' and 'data.frame':   5571 obs. of  8 variables:
    ##  $ code_muni   : num  1100015 1100023 1100031 1100049 1100056 ...
    ##  $ name_muni   : chr  "Alta Floresta D'oeste" "Ariquemes" "Cabixi" "Cacoal" ...
    ##  $ code_state  : num  11 11 11 11 11 11 11 11 11 11 ...
    ##  $ abbrev_state: chr  "RO" "RO" "RO" "RO" ...
    ##  $ name_state  : chr  "Rondônia" "Rondônia" "Rondônia" "Rondônia" ...
    ##  $ code_region : num  1 1 1 1 1 1 1 1 1 1 ...
    ##  $ name_region : chr  "Norte" "Norte" "Norte" "Norte" ...
    ##  $ geom        :sfc_MULTIPOLYGON of length 5571; first list element: List of 1
    ##   ..$ :List of 1
    ##   .. ..$ : num [1:789, 1:2] -61.9 -61.9 -61.9 -61.9 -61.9 ...
    ##   ..- attr(*, "class")= chr [1:3] "XY" "MULTIPOLYGON" "sfg"
    ##  - attr(*, "sf_column")= chr "geom"
    ##  - attr(*, "agr")= Factor w/ 3 levels "constant","aggregate",..: NA NA NA NA NA NA NA
    ##   ..- attr(*, "names")= chr [1:7] "code_muni" "name_muni" "code_state" "abbrev_state" ...

#### O banco de dados geografico é um data.frame com 5.571 linhas e 8 Colunas(Variaveis), são variaveis qualitativas e uma variável especial “geom”, composta por uma lista de geometrias espaciais (classe - sf).

#### A variavel “geom”, contem uma lista de dados, para a presente análise iremos utilizar a coordenadas de longitude e latitude dos municípios.

``` r
# Adicionar coordenadas de latitude e longitude no data.frame
# 1- Calcular os centroides (ponto central de cada município)
mun_centroids <- st_centroid(municipio)
```

    ## Warning: st_centroid assumes attributes are constant over geometries

``` r
# 2- Extrair coordenadas de latitude e longitude
coords <- st_coordinates(mun_centroids)

# Adicionar as coordenadas ao dataframe original
municipio <- municipio %>% mutate(longitude = coords[,1], latitude = coords[,2])

# Visualizar os dados
head(municipio,10)
```

    ## Simple feature collection with 10 features and 9 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: -65.42964 ymin: -13.6937 xmax: -60.33317 ymax: -9.66916
    ## Geodetic CRS:  SIRGAS 2000
    ##    code_muni             name_muni code_state abbrev_state name_state
    ## 1    1100015 Alta Floresta D'oeste         11           RO   Rondônia
    ## 2    1100023             Ariquemes         11           RO   Rondônia
    ## 3    1100031                Cabixi         11           RO   Rondônia
    ## 4    1100049                Cacoal         11           RO   Rondônia
    ## 5    1100056            Cerejeiras         11           RO   Rondônia
    ## 6    1100064     Colorado do Oeste         11           RO   Rondônia
    ## 7    1100072            Corumbiara         11           RO   Rondônia
    ## 8    1100080         Costa Marques         11           RO   Rondônia
    ## 9    1100098       Espigão D'oeste         11           RO   Rondônia
    ## 10   1100106         Guajará-Mirim         11           RO   Rondônia
    ##    code_region name_region                           geom longitude   latitude
    ## 1            1       Norte MULTIPOLYGON (((-61.93004 -... -62.27447 -12.469632
    ## 2            1       Norte MULTIPOLYGON (((-63.18118 -... -62.95727  -9.951972
    ## 3            1       Norte MULTIPOLYGON (((-60.56184 -... -60.63981 -13.474899
    ## 4            1       Norte MULTIPOLYGON (((-61.00075 -... -61.32462 -11.301218
    ## 5            1       Norte MULTIPOLYGON (((-60.82135 -... -61.26094 -13.203662
    ## 6            1       Norte MULTIPOLYGON (((-60.41521 -... -60.55024 -13.159084
    ## 7            1       Norte MULTIPOLYGON (((-60.99651 -... -61.09167 -12.926691
    ## 8            1       Norte MULTIPOLYGON (((-64.14167 -... -64.05860 -12.147081
    ## 9            1       Norte MULTIPOLYGON (((-60.92521 -... -60.78476 -11.351142
    ## 10           1       Norte MULTIPOLYGON (((-64.73786 -... -64.53775 -11.305491

### **Estruturação e Limpeza dos dados**

### 🎯 Objetivos

#### - Corrigir inconsistências, padronizar nomes de colunas e uniformizar valores

#### - Unir os dois data.frame criados (dados.anp e municipios), realizar análise prévia do novo data.frame

#### ✔️**União do 1º e 2º Conjunto de dados**

#### Análise inicial dos dados sugere que a varivel CHAVE principal é o “NOME DO MUNICIPIO”, entretanto a ocorrência de nomes de municÍpios iguais em diferentes regiões é comum no Brasil, dessa maneira ao utilizar a segunda chave “SIGLA DO ESTADO”, ira garantir a correta união dos dados.

``` r
#Padronizar dos nomes de Variaveis e uniformizar valores.
#Para realizar a união dos conjuntos de dados, inicialmente as variaveis "Nome do Municipio" e "Nome do Estado" deverão estar Padronizadas.

#Padronização dos nomes de Variaveis
#os nomes de variaveis estão com idiomas e padrao de escrita diferentes.
names(dados.anp)
```

    ##  [1] "Regiao - Sigla"    "Estado - Sigla"    "Municipio"        
    ##  [4] "Revenda"           "CNPJ da Revenda"   "Nome da Rua"      
    ##  [7] "Numero Rua"        "Complemento"       "Bairro"           
    ## [10] "Cep"               "Produto"           "Data da Coleta"   
    ## [13] "Valor de Venda"    "Valor de Compra"   "Unidade de Medida"
    ## [16] "Bandeira"

``` r
names(municipio)
```

    ##  [1] "code_muni"    "name_muni"    "code_state"   "abbrev_state" "name_state"  
    ##  [6] "code_region"  "name_region"  "geom"         "longitude"    "latitude"

``` r
#Renomeando os nomes de variaveis do 2º conjunto de daods de acordo com o 1º.
municipio <- setNames(municipio, c("codigo do Municipio", "Municipio", "Estado - Codigo", "Estado - Sigla","Nome do Estado","Regiao - Codigo","Nome da Regiao","geom","longitude","latitude" ))
names(municipio)
```

    ##  [1] "codigo do Municipio" "Municipio"           "Estado - Codigo"    
    ##  [4] "Estado - Sigla"      "Nome do Estado"      "Regiao - Codigo"    
    ##  [7] "Nome da Regiao"      "geom"                "longitude"          
    ## [10] "latitude"

``` r
# Análise previa das variaveis Chaves.

#Chave:Nomes dos Municipios
head(dados.anp$Municipio,15) #A variavel:Municipio, contem nomes em letras maiúsculas e sem acentos.
```

    ##  [1] "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL"
    ##  [9] "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL"

``` r
head(municipio$Municipio,15)  #A variavel:name_muni, contem nomes em letras minúsculas e com acentos.  
```

    ##  [1] "Alta Floresta D'oeste"    "Ariquemes"               
    ##  [3] "Cabixi"                   "Cacoal"                  
    ##  [5] "Cerejeiras"               "Colorado do Oeste"       
    ##  [7] "Corumbiara"               "Costa Marques"           
    ##  [9] "Espigão D'oeste"          "Guajará-Mirim"           
    ## [11] "Jaru"                     "Ji-Paraná"               
    ## [13] "Machadinho D'oeste"       "Nova Brasilândia D'oeste"
    ## [15] "Ouro Preto do Oeste"

``` r
# 1ª- Padronizar tudo em letras MAIUSCULAS.
dados.anp <- dados.anp %>% mutate(Municipio = toupper(Municipio))
municipio <- municipio %>% mutate(Municipio = toupper(Municipio))

# 2ª-padronizar tudo em palavras sem acentuação.
dados.anp <- dados.anp %>% mutate(Municipio = stri_trans_general(Municipio, "Latin-ASCII"))
municipio <- municipio %>% mutate(Municipio = stri_trans_general(Municipio, "Latin-ASCII"))

#verificando resultado
head(dados.anp$Municipio,15) #A variavel:Municipio, contem nomes em letras minúsculas e sem acentos.
```

    ##  [1] "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL"
    ##  [9] "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL"

``` r
head(municipio$Municipio,15)  #A variavel:name_muni, contem nomes em letras minúsculas e sem acentos.  
```

    ##  [1] "ALTA FLORESTA D'OESTE"    "ARIQUEMES"               
    ##  [3] "CABIXI"                   "CACOAL"                  
    ##  [5] "CEREJEIRAS"               "COLORADO DO OESTE"       
    ##  [7] "CORUMBIARA"               "COSTA MARQUES"           
    ##  [9] "ESPIGAO D'OESTE"          "GUAJARA-MIRIM"           
    ## [11] "JARU"                     "JI-PARANA"               
    ## [13] "MACHADINHO D'OESTE"       "NOVA BRASILANDIA D'OESTE"
    ## [15] "OURO PRETO DO OESTE"

``` r
#Chave:Nomes dos Estados já estão padronizados
head(dados.anp$`Estado - Sigla`,15)    
```

    ##  [1] "CE" "CE" "CE" "CE" "CE" "CE" "CE" "CE" "CE" "CE" "CE" "CE" "CE" "CE" "CE"

``` r
head(municipio$`Estado - Sigla`,15)    
```

    ##  [1] "RO" "RO" "RO" "RO" "RO" "RO" "RO" "RO" "RO" "RO" "RO" "RO" "RO" "RO" "RO"

#### 🎯 Unir os dados e avaliar o resultado

#### O novo conjunto de dados será salvo com o mesmo nome do 1º conjunto (dados.anp), o método utilizado (Left join) combina duas tabelas mantendo todas as linhas da tabela da esquerda e adicionando colunas da tabela da direita quando as chaves coincidem. Linhas da esquerda sem correspondência recebem valores ausentes nas colunas adicionadas

``` r
#verificar a dimensão dos conjuntos de dados antes da união
dim(dados.anp) #Linhas:420409 e Colunas:16
```

    ## [1] 420409     16

``` r
dim(municipio) #Linhas:5571 e  Coliunas:8
```

    ## [1] 5571   10

``` r
#verificar os nomes de veriaveis
names(dados.anp)
```

    ##  [1] "Regiao - Sigla"    "Estado - Sigla"    "Municipio"        
    ##  [4] "Revenda"           "CNPJ da Revenda"   "Nome da Rua"      
    ##  [7] "Numero Rua"        "Complemento"       "Bairro"           
    ## [10] "Cep"               "Produto"           "Data da Coleta"   
    ## [13] "Valor de Venda"    "Valor de Compra"   "Unidade de Medida"
    ## [16] "Bandeira"

``` r
names(municipio)
```

    ##  [1] "codigo do Municipio" "Municipio"           "Estado - Codigo"    
    ##  [4] "Estado - Sigla"      "Nome do Estado"      "Regiao - Codigo"    
    ##  [7] "Nome da Regiao"      "geom"                "longitude"          
    ## [10] "latitude"

``` r
#realizar a união dos conjuntos de dados, usando duas chaves
dados.anp <- left_join(dados.anp,municipio, by = c("Municipio","Estado - Sigla"))

#verificar anti_join, para listar chaves nao correspondentes e descobrir as possiveis causas
dados.anp1 <- anti_join(dados.anp,municipio, by = c("Municipio","Estado - Sigla"))
#View(dados.anp1) #existe um municipio (santana do livramento) do RS que não possui dados do IBGE registrados em 2024. 
head(dados.anp1) #817 linhas 
```

    ## # A tibble: 6 × 24
    ##   `Regiao - Sigla` `Estado - Sigla` Municipio          Revenda `CNPJ da Revenda`
    ##   <chr>            <chr>            <chr>              <chr>   <chr>            
    ## 1 S                RS               SANTANA DO LIVRAM… LARRAT… 09.303.361/0001-…
    ## 2 S                RS               SANTANA DO LIVRAM… LARRAT… 09.303.361/0001-…
    ## 3 S                RS               SANTANA DO LIVRAM… LARRAT… 09.303.361/0001-…
    ## 4 S                RS               SANTANA DO LIVRAM… LARRAT… 09.303.361/0001-…
    ## 5 S                RS               SANTANA DO LIVRAM… JOSÉ C… 07.179.657/0001-…
    ## 6 S                RS               SANTANA DO LIVRAM… JOSÉ C… 07.179.657/0001-…
    ## # ℹ 19 more variables: `Nome da Rua` <chr>, `Numero Rua` <chr>,
    ## #   Complemento <chr>, Bairro <chr>, Cep <chr>, Produto <chr>,
    ## #   `Data da Coleta` <dttm>, `Valor de Venda` <dbl>, `Valor de Compra` <lgl>,
    ## #   `Unidade de Medida` <chr>, Bandeira <chr>, `codigo do Municipio` <dbl>,
    ## #   `Estado - Codigo` <dbl>, `Nome do Estado` <chr>, `Regiao - Codigo` <dbl>,
    ## #   `Nome da Regiao` <chr>, geom <MULTIPOLYGON [°]>, longitude <dbl>,
    ## #   latitude <dbl>

``` r
#View(dados.anp1)

#verificando quantidades de registros 
table(dados.anp$`Nome do Estado`) #Rio Grande do SUl:31296  
```

    ## 
    ##                Acre             Alagoas               Amapá            Amazonas 
    ##                1520                4601                1136                5081 
    ##               Bahia               Ceará    Distrito Federal      Espírito Santo 
    ##               23121               10919                4091                8732 
    ##               Goiás            Maranhão         Mato Grosso  Mato Grosso do Sul 
    ##               16416                9109                7554                4815 
    ##        Minas Gerais                Pará             Paraíba              Paraná 
    ##               42479                9067                6389               27124 
    ##          Pernambuco               Piauí      Rio de Janeiro Rio Grande do Norte 
    ##               16382                5030               36254                4659 
    ##   Rio Grande do Sul            Rondônia             Roraima      Santa Catarina 
    ##               31296                4886                1617               14294 
    ##           São Paulo             Sergipe           Tocantins 
    ##              115427                2897                4696

``` r
table(dados.anp$`Estado - Sigla`) #Rio Grande do SUl:32113
```

    ## 
    ##     AC     AL     AM     AP     BA     CE     DF     ES     GO     MA     MG 
    ##   1520   4601   5081   1136  23121  10919   4091   8732  16416   9109  42479 
    ##     MS     MT     PA     PB     PE     PI     PR     RJ     RN     RO     RR 
    ##   4815   7554   9067   6389  16382   5030  27124  36254   4659   4886   1617 
    ##     RS     SC     SE     SP     TO 
    ##  32113  14294   2897 115427   4696

``` r
table(dados.anp$`Nome da Regiao`) #Sul:72714
```

    ## 
    ## Centro-Oeste     Nordeste        Norte      Sudeste          Sul 
    ##        32876        83107        28003       202892        72714

``` r
table(dados.anp$`Regiao - Sigla`)  #Sul: 73531
```

    ## 
    ##     CO      N     NE      S     SE 
    ##  32876  28003  83107  73531 202892

``` r
#Substituir (imputar) valores ausentes para as Variaveis Nome da Regiao e Nome do Estado, referente ao municipio Santana do Livramento
#Essa ação evita tabelas com NA, quando resumidas por Nome do Estado e Nome da Região.
dados.anp$`Nome do Estado` <- replace_na(dados.anp$`Nome do Estado`, "Rio Grande do Sul")
dados.anp$`Nome da Regiao` <- replace_na(dados.anp$`Nome da Regiao`, "Sul")

#quantidades apos alteracao
length(dados.anp$`Nome do Estado`[dados.anp$`Nome do Estado`=="Rio Grande do Sul"])
```

    ## [1] 32113

``` r
length(dados.anp$`Nome da Regiao`[dados.anp$`Nome da Regiao`=="Sul"])
```

    ## [1] 73531

#### Visualizar informações de variaveis(quantidade,nomes,tipo de dados,primeiros valores) e verificar a dimensão do novo conjunto.

``` r
dim(dados.anp)  #o novo conjunto de dados contem a mesma quantidae de linhas do 1º conjunto (420.409) e 22 variaveis em estudo, 16 (1º Conjunto) considerando as duas  chaves + 6 (2º Conjunto)
```

    ## [1] 420409     24

``` r
head(dados.anp, 10) #avaliando as 10 primeiras linhas
```

    ## # A tibble: 10 × 24
    ##    `Regiao - Sigla` `Estado - Sigla` Municipio Revenda         `CNPJ da Revenda`
    ##    <chr>            <chr>            <chr>     <chr>           <chr>            
    ##  1 NE               CE               SOBRAL    ECONOGÁS DO BR… 08.775.979/0002-…
    ##  2 NE               CE               SOBRAL    ECONOGÁS DO BR… 08.775.979/0002-…
    ##  3 NE               CE               SOBRAL    ECONOGÁS DO BR… 08.775.979/0002-…
    ##  4 NE               CE               SOBRAL    ECONOGÁS DO BR… 08.775.979/0002-…
    ##  5 NE               CE               SOBRAL    V.C.EMPREENDIM… 03.551.935/0002-…
    ##  6 NE               CE               SOBRAL    V.C.EMPREENDIM… 03.551.935/0002-…
    ##  7 NE               CE               SOBRAL    V.C.EMPREENDIM… 03.551.935/0002-…
    ##  8 NE               CE               SOBRAL    V.C.EMPREENDIM… 03.551.935/0002-…
    ##  9 NE               CE               SOBRAL    CENTER POSTO C… 02.905.892/0001-…
    ## 10 NE               CE               SOBRAL    CENTER POSTO C… 02.905.892/0001-…
    ## # ℹ 19 more variables: `Nome da Rua` <chr>, `Numero Rua` <chr>,
    ## #   Complemento <chr>, Bairro <chr>, Cep <chr>, Produto <chr>,
    ## #   `Data da Coleta` <dttm>, `Valor de Venda` <dbl>, `Valor de Compra` <lgl>,
    ## #   `Unidade de Medida` <chr>, Bandeira <chr>, `codigo do Municipio` <dbl>,
    ## #   `Estado - Codigo` <dbl>, `Nome do Estado` <chr>, `Regiao - Codigo` <dbl>,
    ## #   `Nome da Regiao` <chr>, geom <MULTIPOLYGON [°]>, longitude <dbl>,
    ## #   latitude <dbl>

``` r
names(dados.anp)
```

    ##  [1] "Regiao - Sigla"      "Estado - Sigla"      "Municipio"          
    ##  [4] "Revenda"             "CNPJ da Revenda"     "Nome da Rua"        
    ##  [7] "Numero Rua"          "Complemento"         "Bairro"             
    ## [10] "Cep"                 "Produto"             "Data da Coleta"     
    ## [13] "Valor de Venda"      "Valor de Compra"     "Unidade de Medida"  
    ## [16] "Bandeira"            "codigo do Municipio" "Estado - Codigo"    
    ## [19] "Nome do Estado"      "Regiao - Codigo"     "Nome da Regiao"     
    ## [22] "geom"                "longitude"           "latitude"

``` r
#View(dados.anp)
str(dados.anp)
```

    ## tibble [420,409 × 24] (S3: tbl_df/tbl/data.frame)
    ##  $ Regiao - Sigla     : chr [1:420409] "NE" "NE" "NE" "NE" ...
    ##  $ Estado - Sigla     : chr [1:420409] "CE" "CE" "CE" "CE" ...
    ##  $ Municipio          : chr [1:420409] "SOBRAL" "SOBRAL" "SOBRAL" "SOBRAL" ...
    ##  $ Revenda            : chr [1:420409] "ECONOGÁS DO BRASIL DIST. DERIV. DE PET. BIOC. E GÁS NAT" "ECONOGÁS DO BRASIL DIST. DERIV. DE PET. BIOC. E GÁS NAT" "ECONOGÁS DO BRASIL DIST. DERIV. DE PET. BIOC. E GÁS NAT" "ECONOGÁS DO BRASIL DIST. DERIV. DE PET. BIOC. E GÁS NAT" ...
    ##  $ CNPJ da Revenda    : chr [1:420409] "08.775.979/0002-62" "08.775.979/0002-62" "08.775.979/0002-62" "08.775.979/0002-62" ...
    ##  $ Nome da Rua        : chr [1:420409] "RUA TABELIÃO IDELFONSO CAVALCANTI" "RUA TABELIÃO IDELFONSO CAVALCANTI" "RUA TABELIÃO IDELFONSO CAVALCANTI" "RUA TABELIÃO IDELFONSO CAVALCANTI" ...
    ##  $ Numero Rua         : chr [1:420409] "455" "455" "455" "455" ...
    ##  $ Complemento        : chr [1:420409] NA NA NA NA ...
    ##  $ Bairro             : chr [1:420409] "CENTRO" "CENTRO" "CENTRO" "CENTRO" ...
    ##  $ Cep                : chr [1:420409] "62010-000" "62010-000" "62010-000" "62010-000" ...
    ##  $ Produto            : chr [1:420409] "GASOLINA" "GASOLINA ADITIVADA" "DIESEL S10" "ETANOL" ...
    ##  $ Data da Coleta     : POSIXct[1:420409], format: "2025-01-01" "2025-01-01" ...
    ##  $ Valor de Venda     : num [1:420409] 6.29 6.49 6.19 5.19 6.53 6.83 6.38 5.29 6.69 6.79 ...
    ##  $ Valor de Compra    : logi [1:420409] NA NA NA NA NA NA ...
    ##  $ Unidade de Medida  : chr [1:420409] "R$ / litro" "R$ / litro" "R$ / litro" "R$ / litro" ...
    ##  $ Bandeira           : chr [1:420409] "RAIZEN" "RAIZEN" "RAIZEN" "RAIZEN" ...
    ##  $ codigo do Municipio: num [1:420409] 2312908 2312908 2312908 2312908 2312908 ...
    ##  $ Estado - Codigo    : num [1:420409] 23 23 23 23 23 23 23 23 23 23 ...
    ##  $ Nome do Estado     : chr [1:420409] "Ceará" "Ceará" "Ceará" "Ceará" ...
    ##  $ Regiao - Codigo    : num [1:420409] 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ Nome da Regiao     : chr [1:420409] "Nordeste" "Nordeste" "Nordeste" "Nordeste" ...
    ##  $ geom               :sfc_MULTIPOLYGON of length 420409; first list element: List of 1
    ##   ..$ :List of 1
    ##   .. ..$ : num [1:409, 1:2] -40.4 -40.3 -40.3 -40.3 -40.3 ...
    ##   ..- attr(*, "class")= chr [1:3] "XY" "MULTIPOLYGON" "sfg"
    ##  $ longitude          : num [1:420409] -40.2 -40.2 -40.2 -40.2 -40.2 ...
    ##  $ latitude           : num [1:420409] -3.81 -3.81 -3.81 -3.81 -3.81 ...

#### Resumo descritico para verificar a estrutura dos dados

``` r
summary(dados.anp) #Resumo descritivo apontou NA's valores nao observados que devem ser verificado o tratamento adequado para essas variaveis. 
```

    ##  Regiao - Sigla     Estado - Sigla      Municipio           Revenda         
    ##  Length:420409      Length:420409      Length:420409      Length:420409     
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##  CNPJ da Revenda    Nome da Rua         Numero Rua        Complemento       
    ##  Length:420409      Length:420409      Length:420409      Length:420409     
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##     Bairro              Cep              Produto         
    ##  Length:420409      Length:420409      Length:420409     
    ##  Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character  
    ##                                                          
    ##                                                          
    ##                                                          
    ##                                                          
    ##  Data da Coleta                Valor de Venda  Valor de Compra
    ##  Min.   :2025-01-01 00:00:00   Min.   :3.190   Mode:logical   
    ##  1st Qu.:2025-02-17 00:00:00   1st Qu.:5.590   NA's:420409    
    ##  Median :2025-04-01 00:00:00   Median :6.170                  
    ##  Mean   :2025-04-02 06:53:17   Mean   :5.891                  
    ##  3rd Qu.:2025-05-19 00:00:00   3rd Qu.:6.490                  
    ##  Max.   :2025-06-30 00:00:00   Max.   :9.490                  
    ##                                                               
    ##  Unidade de Medida    Bandeira         codigo do Municipio Estado - Codigo
    ##  Length:420409      Length:420409      Min.   :1100023     Min.   :11.00  
    ##  Class :character   Class :character   1st Qu.:2927408     1st Qu.:29.00  
    ##  Mode  :character   Mode  :character   Median :3506102     Median :35.00  
    ##                                        Mean   :3380232     Mean   :33.62  
    ##                                        3rd Qu.:4101408     3rd Qu.:41.00  
    ##                                        Max.   :5300108     Max.   :53.00  
    ##                                        NA's   :817         NA's   :817    
    ##  Nome do Estado     Regiao - Codigo Nome da Regiao                geom       
    ##  Length:420409      Min.   :1.000   Length:420409      MULTIPOLYGON :420409  
    ##  Class :character   1st Qu.:2.000   Class :character   epsg:4674    :     0  
    ##  Mode  :character   Median :3.000   Mode  :character   +proj=long...:     0  
    ##                     Mean   :2.998                                            
    ##                     3rd Qu.:4.000                                            
    ##                     Max.   :5.000                                            
    ##                     NA's   :817                                              
    ##    longitude         latitude      
    ##  Min.   :-72.76   Min.   :-31.581  
    ##  1st Qu.:-49.38   1st Qu.:-23.621  
    ##  Median :-46.65   Median :-21.650  
    ##  Mean   :-46.51   Mean   :-18.761  
    ##  3rd Qu.:-43.30   3rd Qu.:-13.948  
    ##  Max.   :-34.85   Max.   :  3.118  
    ##  NA's   :817      NA's   :817

``` r
#contagem de dados ausentes(NA)  E NULOS(NULL) por coluna

# Identificar quais variaveis apresentam dados NULL
sapply(dados.anp, is.null) #sem dados Null
```

    ##      Regiao - Sigla      Estado - Sigla           Municipio             Revenda 
    ##               FALSE               FALSE               FALSE               FALSE 
    ##     CNPJ da Revenda         Nome da Rua          Numero Rua         Complemento 
    ##               FALSE               FALSE               FALSE               FALSE 
    ##              Bairro                 Cep             Produto      Data da Coleta 
    ##               FALSE               FALSE               FALSE               FALSE 
    ##      Valor de Venda     Valor de Compra   Unidade de Medida            Bandeira 
    ##               FALSE               FALSE               FALSE               FALSE 
    ## codigo do Municipio     Estado - Codigo      Nome do Estado     Regiao - Codigo 
    ##               FALSE               FALSE               FALSE               FALSE 
    ##      Nome da Regiao                geom           longitude            latitude 
    ##               FALSE               FALSE               FALSE               FALSE

``` r
#verificação dados ausentes
colSums(is.na(dados.anp))
```

    ##      Regiao - Sigla      Estado - Sigla           Municipio             Revenda 
    ##                   0                   0                   0                   0 
    ##     CNPJ da Revenda         Nome da Rua          Numero Rua         Complemento 
    ##                   0                   0                  58              323946 
    ##              Bairro                 Cep             Produto      Data da Coleta 
    ##                 862                   0                   0                   0 
    ##      Valor de Venda     Valor de Compra   Unidade de Medida            Bandeira 
    ##                   0              420409                   0                   0 
    ## codigo do Municipio     Estado - Codigo      Nome do Estado     Regiao - Codigo 
    ##                 817                 817                   0                 817 
    ##      Nome da Regiao                geom           longitude            latitude 
    ##                   0                   0                 817                 817

``` r
#A contagem de NA's, evidencia o resultado da junção de dados, 817 linhas do caso especifico do municipio (santana do livramento) do RS que nao possui dados do IBGE registrados em 2024. 
gg_miss_var(dados.anp)
```

![](README_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
# Gráfico de barras por variável, identificamos que a variavel "valor de compras" e "Complemento" apresentam um número elevado de dados ausentes, portanto essas variaveis não são uteis para análise.
```

#### Eliminar da análise algumas variaveis que não são interesantes para o estudo do preco do combustivel, remover colunas pelo nome.

``` r
names(dados.anp)
```

    ##  [1] "Regiao - Sigla"      "Estado - Sigla"      "Municipio"          
    ##  [4] "Revenda"             "CNPJ da Revenda"     "Nome da Rua"        
    ##  [7] "Numero Rua"          "Complemento"         "Bairro"             
    ## [10] "Cep"                 "Produto"             "Data da Coleta"     
    ## [13] "Valor de Venda"      "Valor de Compra"     "Unidade de Medida"  
    ## [16] "Bandeira"            "codigo do Municipio" "Estado - Codigo"    
    ## [19] "Nome do Estado"      "Regiao - Codigo"     "Nome da Regiao"     
    ## [22] "geom"                "longitude"           "latitude"

``` r
dados.anp <- dados.anp %>% select(-`Nome da Rua`,-geom,-Complemento,-`Valor de Compra`);dados.anp
```

    ## # A tibble: 420,409 × 20
    ##    `Regiao - Sigla` `Estado - Sigla` Municipio Revenda         `CNPJ da Revenda`
    ##    <chr>            <chr>            <chr>     <chr>           <chr>            
    ##  1 NE               CE               SOBRAL    ECONOGÁS DO BR… 08.775.979/0002-…
    ##  2 NE               CE               SOBRAL    ECONOGÁS DO BR… 08.775.979/0002-…
    ##  3 NE               CE               SOBRAL    ECONOGÁS DO BR… 08.775.979/0002-…
    ##  4 NE               CE               SOBRAL    ECONOGÁS DO BR… 08.775.979/0002-…
    ##  5 NE               CE               SOBRAL    V.C.EMPREENDIM… 03.551.935/0002-…
    ##  6 NE               CE               SOBRAL    V.C.EMPREENDIM… 03.551.935/0002-…
    ##  7 NE               CE               SOBRAL    V.C.EMPREENDIM… 03.551.935/0002-…
    ##  8 NE               CE               SOBRAL    V.C.EMPREENDIM… 03.551.935/0002-…
    ##  9 NE               CE               SOBRAL    CENTER POSTO C… 02.905.892/0001-…
    ## 10 NE               CE               SOBRAL    CENTER POSTO C… 02.905.892/0001-…
    ## # ℹ 420,399 more rows
    ## # ℹ 15 more variables: `Numero Rua` <chr>, Bairro <chr>, Cep <chr>,
    ## #   Produto <chr>, `Data da Coleta` <dttm>, `Valor de Venda` <dbl>,
    ## #   `Unidade de Medida` <chr>, Bandeira <chr>, `codigo do Municipio` <dbl>,
    ## #   `Estado - Codigo` <dbl>, `Nome do Estado` <chr>, `Regiao - Codigo` <dbl>,
    ## #   `Nome da Regiao` <chr>, longitude <dbl>, latitude <dbl>

``` r
names(dados.anp)
```

    ##  [1] "Regiao - Sigla"      "Estado - Sigla"      "Municipio"          
    ##  [4] "Revenda"             "CNPJ da Revenda"     "Numero Rua"         
    ##  [7] "Bairro"              "Cep"                 "Produto"            
    ## [10] "Data da Coleta"      "Valor de Venda"      "Unidade de Medida"  
    ## [13] "Bandeira"            "codigo do Municipio" "Estado - Codigo"    
    ## [16] "Nome do Estado"      "Regiao - Codigo"     "Nome da Regiao"     
    ## [19] "longitude"           "latitude"

#### Para visualização de dados utilizaremos o Tableau (Ferramenta de BI),para isso exportaremos os dados para uma planilha (dados.anp_para_tableau)

``` r
# Adicionando uma coluna de ID para cada linha unidade amostral de locais de revenda
dados.anp <- dados.anp %>%
  mutate(id_local_revenda = row_number())

write.xlsx(dados.anp, "dados.anp_para_tableau.xlsx")
```

### **Criando uma história com a estatística, transformando números em conhecimento, padrões em descobertas.**

#### Visualização Interativa

#### Histograma para visualiziar a distribuição do Preço do Combustível

##### 👉 Clique no link abaixo para acessar o dashboard completo no Tableau:

##### [Visualizar no Tableau](https://public.tableau.com/views/AnlisePreoCombustiveis/Histograma_Preco?:language=pt-BR&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

------------------------------------------------------------------------

<img src="Histograma_Preco.png" width="705" />

##### A distribuicao dos das dados sugere dois conjuntos distintos, apresentando dois picos.

##### Um histograma com dois picos indica uma distribuição bimodal — ou seja, os dados têm dois agrupamentos principais de frequência. Isso pode sugerir a presença de dois grupos distintos dentro do conjunto de dados.

##### Separando os dados: analisar os dois picos separadamente pode revelar padrões distintos.

##### Visulizar o valor de venda e o tipo de produto.

#### Visualização Interativa

#### Boxplot para visualizar a distribuição do Preço por Tipo de Combustível

##### 👉 Clique no link abaixo para acessar o dashboard completo no Tableau:

##### [Visualizar no Tableau](https://public.tableau.com/views/AnlisePreoCombustiveis/BoxplotVVMunicpios3?:language=pt-BR&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

------------------------------------------------------------------------

![](Boxplot_tipode%20produto.png)

##### A análise do Boxplot sugere que existe dois distintos de valores de combustiveis, o primeiro grupo com valores mais elevados (DIESEL, DIESEL S10, GASOLINA, GASOLINA ADITIVADA) e o segundo grupo com valores mais baixos (ETANAL e GNV).

##### O gráfico mostra presença de dados discrepantes (outliers), são valores que se desviam significativamente do padrão da maioria dos dados.

#### Estatísticas do valor de venda dos combustiveis

``` r
#ESTATÍSTICAS - CÁLCULO DE MÉTRICAS 

#seleção de variaveis numéricas para exploração inicial 

sapply(dados.anp %>% select(where(is.numeric)), function(x) c(
  n = sum(!is.na(x)),          
  mean = mean(x, na.rm = TRUE),
  sd = sd(x, na.rm = TRUE),
  median = median(x, na.rm = TRUE),
  p25 = quantile(x, 0.25, na.rm = TRUE),
  p75 = quantile(x, 0.75, na.rm = TRUE),
  min = min(x, na.rm = TRUE),
  max = max(x, na.rm = TRUE)
))
```

    ##         Valor de Venda codigo do Municipio Estado - Codigo Regiao - Codigo
    ## n         4.204090e+05            419592.0    4.195920e+05    4.195920e+05
    ## mean      5.890884e+00           3380232.0    3.362390e+01    2.998458e+00
    ## sd        8.932598e-01            891213.5    8.899313e+00    9.755649e-01
    ## median    6.170000e+00           3506102.0    3.500000e+01    3.000000e+00
    ## p25.25%   5.590000e+00           2927408.0    2.900000e+01    2.000000e+00
    ## p75.75%   6.490000e+00           4101408.0    4.100000e+01    4.000000e+00
    ## min       3.190000e+00           1100023.0    1.100000e+01    1.000000e+00
    ## max       9.490000e+00           5300108.0    5.300000e+01    5.000000e+00
    ##             longitude      latitude id_local_revenda
    ## n       419592.000000 419592.000000         420409.0
    ## mean       -46.505940    -18.761389         210205.0
    ## sd           5.659431      7.602637         121361.8
    ## median     -46.648048    -21.649745         210205.0
    ## p25.25%    -49.379757    -23.620591         105103.0
    ## p75.75%    -43.299567    -13.947770         315307.0
    ## min        -72.755325    -31.581087              1.0
    ## max        -34.847531      3.117840         420409.0

``` r
#Do conjunto de  dados numéricos, somente a variaviavel valor de venda é interessante ser analisada.  
summary(dados.anp$`Valor de Venda`) #a função mostra estatísticas básicas como mínimo, 1º quartil, mediana, média, 3º quartil e máximo.
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   3.190   5.590   6.170   5.891   6.490   9.490

##### O resumo indica que no geral existe uma variação considerável no preço dos combustiveis, mínimo R\$ 3.1 a R\$ 9.4, vejamos as estatísticas por tipo de produto.

``` r
#Resumo de estatísticas descritivas do valor de venda dos combustiveis por tipo de Produto

dados.anp%>%  group_by(Produto) %>%
  summarise(
    n = n(),
    n_na = sum(!is.na(`Valor de Venda`)),
    Min = min(`Valor de Venda`, na.rm = TRUE),
    Q1 = quantile(`Valor de Venda`, 0.25, na.rm = TRUE),
    Mediana = median(`Valor de Venda`, na.rm = TRUE),
    Media = mean(`Valor de Venda`, na.rm = TRUE),
    Q3 = quantile(`Valor de Venda`, 0.75, na.rm = TRUE),
    Max = max(`Valor de Venda`, na.rm = TRUE),
    sd_preco = sd(`Valor de Venda`, na.rm = TRUE))
```

    ## # A tibble: 6 × 10
    ##   Produto                n   n_na   Min    Q1 Mediana Media    Q3   Max sd_preco
    ##   <chr>              <int>  <int> <dbl> <dbl>   <dbl> <dbl> <dbl> <dbl>    <dbl>
    ## 1 DIESEL             45771  45771  5.29  5.97    6.19  6.24  6.45  8.33    0.400
    ## 2 DIESEL S10         76134  76134  5.19  5.99    6.29  6.29  6.49  8.99    0.413
    ## 3 ETANOL             92598  92598  3.19  4.14    4.47  4.48  4.79  6.89    0.459
    ## 4 GASOLINA          110818 110818  4.99  5.99    6.28  6.29  6.49  8.99    0.405
    ## 5 GASOLINA ADITIVA…  85580  85580  5.19  6.21    6.49  6.48  6.69  9.49    0.410
    ## 6 GNV                 9508   9508  3.79  4.49    4.74  4.81  4.99  6.49    0.435

##### Descobertas do resumo estatístico:

##### - A quantidade de valores coletados na amostra, indica que a Gasolina tem a maior quantidade, enquanto o GNV tem a menor;

##### - O menor valor de combustivel é do Etanol (R\$3.19) e o maior valor coletado é da Gasolina Aditivida (R\$9.49);

##### - As médias e medianas menores (R\$4.48 e R\$4.47) são do Etanol e as maiores (R\$6.48 e R\$6.49) são da Gasolina Aditivida.

``` r
#Resumo de estatísticas descritivas do valor de venda dos combustiveis por Região

dados.anp%>%  group_by(`Regiao - Sigla`) %>%
  summarise(
    n = n(),
    n_na = sum(!is.na(`Valor de Venda`)),
    Min = min(`Valor de Venda`, na.rm = TRUE),
    Q1 = quantile(`Valor de Venda`, 0.25, na.rm = TRUE),
    Mediana = median(`Valor de Venda`, na.rm = TRUE),
    Média = mean(`Valor de Venda`, na.rm = TRUE),
    Q3 = quantile(`Valor de Venda`, 0.75, na.rm = TRUE),
    Max = max(`Valor de Venda`, na.rm = TRUE),
    sd_preco = sd(`Valor de Venda`, na.rm = TRUE))
```

    ## # A tibble: 5 × 10
    ##   `Regiao - Sigla`      n   n_na   Min    Q1 Mediana Média    Q3   Max sd_preco
    ##   <chr>             <int>  <int> <dbl> <dbl>   <dbl> <dbl> <dbl> <dbl>    <dbl>
    ## 1 CO                32876  32876  3.49  5.65    6.19  5.85  6.48  8.39    0.910
    ## 2 N                 28003  28003  3.99  6.14    6.69  6.54  7.05  8.72    0.799
    ## 3 NE                83107  83107  3.39  5.58    6.19  5.98  6.53  7.99    0.724
    ## 4 S                 73531  73531  3.45  5.89    6.29  6.05  6.49  8.59    0.746
    ## 5 SE               202892 202892  3.19  4.89    5.99  5.72  6.37  9.49    0.956

#### Descobertas do resumo estatístico:

##### - A amostra indica que a Região Sudeste (SE) concentra a maior quantidade de observações, essa Região é a mais populosa do Brasil;

##### - As médias e medianas menores (R\$5.72 e R\$5.99) são do Sudeste e as maiores (R\$6.54 e R\$6.69) são do Norte (N)

#### Cálculo de médias agrupadas por categorias para incluir no data.set

``` r
dados.anp <- dados.anp %>%
  group_by(`Regiao - Sigla`) %>%
  mutate(media_regiao = mean(`Valor de Venda`, na.rm = TRUE)) %>%
  group_by(`Estado - Sigla`, .add = TRUE) %>%
  mutate(media_estado = mean(`Valor de Venda`, na.rm = TRUE)) %>%
  group_by(Municipio, `Estado - Sigla`, `Regiao - Sigla`, .add = TRUE) %>%
  mutate(media_municipio = mean(`Valor de Venda`, na.rm = TRUE)) %>%
  ungroup()

# Conferir resultado
head(dados.anp[,19:24],10)
```

    ## # A tibble: 10 × 6
    ##    longitude latitude id_local_revenda media_regiao media_estado media_municipio
    ##        <dbl>    <dbl>            <int>        <dbl>        <dbl>           <dbl>
    ##  1     -40.2    -3.81                1         5.98         6.12            6.28
    ##  2     -40.2    -3.81                2         5.98         6.12            6.28
    ##  3     -40.2    -3.81                3         5.98         6.12            6.28
    ##  4     -40.2    -3.81                4         5.98         6.12            6.28
    ##  5     -40.2    -3.81                5         5.98         6.12            6.28
    ##  6     -40.2    -3.81                6         5.98         6.12            6.28
    ##  7     -40.2    -3.81                7         5.98         6.12            6.28
    ##  8     -40.2    -3.81                8         5.98         6.12            6.28
    ##  9     -40.2    -3.81                9         5.98         6.12            6.28
    ## 10     -40.2    -3.81               10         5.98         6.12            6.28

``` r
#EXPORTAR PARA TABLEAU
write.xlsx(dados.anp, "dados.anp_para_tableau.xlsx")
```

#### Ranking do valor médio dos combustíveis por Municipio

``` r
## 1. Calcular média por Municipio
media_municipio <- dados.anp %>%
 group_by(`codigo do Municipio`,Municipio) %>%
 summarise(valor_medio = mean(`Valor de Venda`), .groups = "drop")%>%
  arrange(desc(valor_medio))
media_municipio
```

    ## # A tibble: 409 × 3
    ##    `codigo do Municipio` Municipio       valor_medio
    ##                    <dbl> <chr>                 <dbl>
    ##  1               1303403 PARINTINS              8.29
    ##  2               1200203 CRUZEIRO DO SUL        7.78
    ##  3               1200401 RIO BRANCO             7.18
    ##  4               1500404 ALENQUER               7.16
    ##  5               4301602 BAGE                   7.00
    ##  6               5003207 CORUMBA                6.99
    ##  7               1100122 JI-PARANA              6.94
    ##  8               1506807 SANTAREM               6.88
    ##  9               1500602 ALTAMIRA               6.85
    ## 10               1400100 BOA VISTA              6.84
    ## # ℹ 399 more rows

``` r
# 5 primeiros
head(media_municipio, 5)
```

    ## # A tibble: 5 × 3
    ##   `codigo do Municipio` Municipio       valor_medio
    ##                   <dbl> <chr>                 <dbl>
    ## 1               1303403 PARINTINS              8.29
    ## 2               1200203 CRUZEIRO DO SUL        7.78
    ## 3               1200401 RIO BRANCO             7.18
    ## 4               1500404 ALENQUER               7.16
    ## 5               4301602 BAGE                   7.00

``` r
# 5 últimos
tail(media_municipio, 5)
```

    ## # A tibble: 5 × 3
    ##   `codigo do Municipio` Municipio  valor_medio
    ##                   <dbl> <chr>            <dbl>
    ## 1               3524402 JACAREI           5.37
    ## 2               3527108 LINS              5.35
    ## 3               3503208 ARARAQUARA        5.32
    ## 4               5209101 GOIATUBA          5.31
    ## 5               3506003 BAURU             5.23

#### Ranking do valor médio dos combustíveis por Estado

``` r
## 1. Calcular média por Estado
media_estado <- dados.anp %>%
 group_by(`Nome do Estado`) %>%
 summarise(valor_medio = mean(`Valor de Venda`), .groups = "drop")%>%
  arrange(desc(valor_medio))
media_estado
```

    ## # A tibble: 27 × 2
    ##    `Nome do Estado`  valor_medio
    ##    <chr>                   <dbl>
    ##  1 Acre                     7.42
    ##  2 Roraima                  6.84
    ##  3 Amazonas                 6.80
    ##  4 Rondônia                 6.73
    ##  5 Pará                     6.37
    ##  6 Amapá                    6.24
    ##  7 Distrito Federal         6.15
    ##  8 Rio Grande do Sul        6.13
    ##  9 Ceará                    6.12
    ## 10 Santa Catarina           6.11
    ## # ℹ 17 more rows

``` r
# 5 primeiros
head(media_estado, 5)
```

    ## # A tibble: 5 × 2
    ##   `Nome do Estado` valor_medio
    ##   <chr>                  <dbl>
    ## 1 Acre                    7.42
    ## 2 Roraima                 6.84
    ## 3 Amazonas                6.80
    ## 4 Rondônia                6.73
    ## 5 Pará                    6.37

``` r
# 5 últimos
tail(media_estado, 5)
```

    ## # A tibble: 5 × 2
    ##   `Nome do Estado` valor_medio
    ##   <chr>                  <dbl>
    ## 1 Paraíba                 5.79
    ## 2 Rio de Janeiro          5.78
    ## 3 Mato Grosso             5.75
    ## 4 Minas Gerais            5.73
    ## 5 São Paulo               5.67

#### Ranking do valor médio dos combustíveis por Região

``` r
## 1. Calcular média por Região
media_regiao <- dados.anp %>%
 group_by(`Nome da Regiao`) %>%
 summarise(valor_medio = mean(`Valor de Venda`), .groups = "drop")%>%
  arrange(desc(valor_medio))
media_regiao
```

    ## # A tibble: 5 × 2
    ##   `Nome da Regiao` valor_medio
    ##   <chr>                  <dbl>
    ## 1 Norte                   6.54
    ## 2 Sul                     6.05
    ## 3 Nordeste                5.98
    ## 4 Centro-Oeste            5.85
    ## 5 Sudeste                 5.72

### **Compreender o preço do combustível a nível nacional com visualizações geográficas no Tableau.**

#### Veja de forma interativa o cenário de Preços dos Combustíveis.

##### 👉 Clique no link abaixo para acessar o dashboard completo no Tableau:

##### [Visualizar no Tableau](https://public.tableau.com/views/AnlisePreoCombustiveis/Histria1?:language=pt-BR&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

<img src="História_Painel2" width="796" />

------------------------------------------------------------------------

![](História%20_Painel.png)

### **Descorbertas da análise**

#### - As Regiões do Brasil que possuem os maiores e menores preços de combustíveis;

#### - Regiões no país com maior consumo de combustíveis;

#### - Regiões com maiores variações de preço;

#### - Tipo de combustíveis mais consumidos no país;

#### - Municípios com alto preços em relação ao Estado;

#### - Revendedoras com médias de valor de venda discrepantes.
