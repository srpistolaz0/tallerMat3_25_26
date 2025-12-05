---
title: "ENUNCIADO taller en grupo Mat3 GIN 2025-2026"
author: "Taller"
lang: es
format:
  html:
    theme: superhero
    toc: true
    toc_depth: 4
    html-math-method: katex
    code-tools: true
    code-fold: true
    collapse: true
    keep-md: true
    code-overflow: wrap
---




# Instrucciones para el taller

Se entrega en grupos que deben de estar constituidos en la actividad de grupos. Los grupos son de 2 o 3 ESTUDIANTES, loa caso especiales consultadlos con el profesor para que los autorice.

**Enlaces y Bibliografía**

-   [R for data science, Hadley Wickham, Garret Grolemund.](https://r4ds.had.co.nz/)
-   [Fundamentos de ciencia de datos con R.](https://cdr-book.github.io/)
-   [Tablas avanzadas: kable, KableExtra.](https://haozhu233.github.io/kableExtra/awesome_table_in_html.html)
-   [Geocomputation with R, Robin Lovelace, Jakub Nowosad, Jannes Muenchow](https://r.geocompx.org/)
-   Apuntes de R-basico y tidyverse moodel MAT3.

## Objetivo MALLORCA

Leeremos los siguientes datos de la zona de etiqueta `mallorca` con el código siguiente:


::: {.cell}

```{.r .cell-code}
load("clean_data/mallorca/listing_common0.RData")
ls()
```

::: {.cell-output .cell-output-stdout}

```
[1] "listings_common0"
```


:::

```{.r .cell-code}
listings0 = listings_common0 %>%
  select(id, scrape_id, listing_url,
         neighbourhood_cleansed, price,
         number_of_reviews,
         review_scores_rating,
         review_scores_rating,
         review_scores_cleanliness,
         review_scores_location,
         review_scores_value,
         number_of_reviews,
         accommodates,
         bathrooms_text,
         bedrooms,
         beds,
         minimum_nights,
         description,
         latitude,
         longitude,
         property_type,
         room_type)
```
:::



**listings**

Generamos la  tibble `listings0` con datos DE  8 periodos  DE  apartamentos de inside Airbnb de Mallorca Y  seleccionando cuantas variables nos parecen más interesantes.

Separararemos la fecha del scrapping que es en la que se observaron  los datos de cada apartamento  nos quedaremos con los apartamentos que aparecen en las 8 periodos "scrapeados".


::: {.cell}

```{.r .cell-code}
listings0= listings0 %>% 
  mutate(date=as.Date(substr(
    as.character(scrape_id),1,8),
    format="%Y%m%d"),
    .after=id)
```
:::


Ahora  analizamos  las fechas de los scrapings y el número de veces que aparecen 
 cada  apartamentos.




::: {.cell}

```{.r .cell-code}
table(listings0$date)
```

::: {.cell-output .cell-output-stdout}

```

2023-12-17 2024-03-23 2024-06-19 2024-09-13 2024-12-14 2025-03-07 2025-06-15 
      9197       9197       9197       9197       9197       9197       9197 
2025-09-21 
      9197 
```


:::
:::

Hay 8 periodos de scrapping y vamos a quedarnos con los apartamentos que aparecen en todos los periodos


Vemos que cada apartamento aparece 8 veces una por periodo.



::: {.cell}

```{.r .cell-code}
table(table(listings0$id))
```

::: {.cell-output .cell-output-stdout}

```

   8 
9197 
```


:::
:::



Notemos que cada apartamento:

-   queda identificado por id y por date que nos da el periodo en la que apareció el dato.
-   así que cada apartamento aparece 8 veces ya que hemos elegido solo los apartamentos que aparecen en las 8 muestras.
-   Las muestras son 2023-12-17, 2024-03-23, 2024-06-19, 2024-09-13, 2024-12-14, 2025-03-07, 2025-06-15, 2025-09-21,


::: {.cell}

```{.r .cell-code}
unique(listings0$date)
```

::: {.cell-output .cell-output-stdout}

```
[1] "2023-12-17" "2024-03-23" "2024-06-19" "2024-09-13" "2024-12-14"
[6] "2025-03-07" "2025-06-15" "2025-09-21"
```


:::
:::


**reviews**

Estos datos necesitan leerse de forma adecuada, las columnas 1, 2 y 4 deben ser de tipo `character` las otras son correctas


::: {.cell}

```{.r .cell-code}
reviews=read_csv("data/mallorca/2025-09-21/reviews.csv.gz")
str(reviews)
```

::: {.cell-output .cell-output-stdout}

```
spc_tbl_ [398,782 × 6] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
 $ listing_id   : num [1:398782] 69998 69998 69998 69998 69998 ...
 $ id           : num [1:398782] 881474 4007103 4170371 4408459 4485779 ...
 $ date         : Date[1:398782], format: "2012-01-24" "2013-04-02" ...
 $ reviewer_id  : num [1:398782] 1595616 3868130 5730759 5921885 810469 ...
 $ reviewer_name: chr [1:398782] "Jean-Pierre" "Jo And Mike" "Elizabeth" "Jone" ...
 $ comments     : chr [1:398782] "This place was charming! Lorenzo himself is a very warm and engaging host and made us feel very welcome. \r<br/"| __truncated__ "We had a four night stay at this gorgeous apartment and it was absolutely perfect. It's really pretty, beautifu"| __truncated__ "Lor's apartment looks exactly like the pictures! It is perfectly located for historic Palma - close to the Cath"| __truncated__ "Wonderful place! 10/10. Charming, spacious and comfortable. Looks even more splendid than in the pictures. The "| __truncated__ ...
 - attr(*, "spec")=
  .. cols(
  ..   listing_id = col_double(),
  ..   id = col_double(),
  ..   date = col_date(format = ""),
  ..   reviewer_id = col_double(),
  ..   reviewer_name = col_character(),
  ..   comments = col_character()
  .. )
 - attr(*, "problems")=<externalptr> 
```


:::

```{.r .cell-code}
head(reviews)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 6
  listing_id      id date       reviewer_id reviewer_name comments              
       <dbl>   <dbl> <date>           <dbl> <chr>         <chr>                 
1      69998  881474 2012-01-24     1595616 Jean-Pierre   "This place was charm…
2      69998 4007103 2013-04-02     3868130 Jo And Mike   "We had a four night …
3      69998 4170371 2013-04-15     5730759 Elizabeth     "Lor's apartment look…
4      69998 4408459 2013-05-03     5921885 Jone          "Wonderful place! 10/…
5      69998 4485779 2013-05-07      810469 Andrea        "My boyfriend and I, …
6      69998 4619699 2013-05-15     3318059 Devii         "We had a very last m…
```


:::
:::


**neighbourhoods.csv**

Son dos columnas y la primera es una agrupación de municipios (están NA) y la segunda es el nombre del municipio


::: {.cell}

```{.r .cell-code}
municipios=read_csv("data/mallorca/2025-09-21/neighbourhoods.csv")
str(municipios)
```

::: {.cell-output .cell-output-stdout}

```
spc_tbl_ [53 × 2] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
 $ neighbourhood_group: logi [1:53] NA NA NA NA NA NA ...
 $ neighbourhood      : chr [1:53] "Alaró" "Alcúdia" "Algaida" "Andratx" ...
 - attr(*, "spec")=
  .. cols(
  ..   neighbourhood_group = col_logical(),
  ..   neighbourhood = col_character()
  .. )
 - attr(*, "problems")=<externalptr> 
```


:::

```{.r .cell-code}
head(municipios)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 2
  neighbourhood_group neighbourhood
  <lgl>               <chr>        
1 NA                  Alaró        
2 NA                  Alcúdia      
3 NA                  Algaida      
4 NA                  Andratx      
5 NA                  Ariany       
6 NA                  Artà         
```


:::
:::


**neighbourhoods.geojson**

Es el mapa de Mallorca, o podemos leer así:


::: {.cell}

```{.r .cell-code}
library(sf)
library(tmap)

# Leer el archivo GeoJSON
geojson_sf <- sf::st_read("data/mallorca/2025-09-21/neighbourhoods.geojson")
```

::: {.cell-output .cell-output-stdout}

```
Reading layer `neighbourhoods' from data source 
  `/home/Zelix47/Documentos/Estadistica/tallerMat3_25_26/data/mallorca/2025-09-21/neighbourhoods.geojson' 
  using driver `GeoJSON'
Simple feature collection with 53 features and 2 fields
Geometry type: MULTIPOLYGON
Dimension:     XY
Bounding box:  xmin: 2.303195 ymin: 39.26403 xmax: 3.479028 ymax: 39.96236
Geodetic CRS:  WGS 84
```


:::

```{.r .cell-code}
# Crear un mapa

# interactivo
tmap_mode("plot") # Cambiar a modo  view/plot   que es interactivo/estático
tm_shape(geojson_sf) +
  tm_polygons(col = "cyan", alpha = 0.6) +
  tm_layout(title = "Mapa - GeoJSON Mallorca con municipios")
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-8-1.png){width=672}
:::
:::


Tenéis que consultar en la documentación de inside Airbnb para saber que significa cada variable. Os puede ser útil leer los ficheros [DATA_ABB_modelo_de_datos.html](DATA_ABB_modelo_de_datos.html) y [DATA_ABB_modelo_de_datos.pdf](DATA_ABB_modelo_de_datos.html) en los que se explica el modelo de datos de inside Airbnb y como se cargan en el espacio de trabajo.

Responder las siguientes preguntas con formato Rmarkdown (.Rmd) o quarto (.qmd) y entregad la fuente un fichero en formato html como salida del informe. Se puntúa la claridad de la respuesta, la calidad de la redacción y la corrección de la respuesta.

## Pregunta 1 (**1punto**)

Del fichero con los datos de listings `listings0` calcula los estadísticos descriptivos de las variable `price` y de la variable `number_of_reviews` agrupados por municipio y por periodo.

Presenta los resultados con una tabla de kableExtra.


::: {.cell}

```{.r .cell-code}
# ---------------------------------------------------------------
# PREGUNTA 1
# Estadísticos descriptivos de:
#   - price
#   - number_of_reviews
# agrupados por:
#   - municipio (neighbourhood_cleansed)
#   - periodo (date)
# Tabla de salida con kableExtra
# ---------------------------------------------------------------

# IMPORTANTE:
# Aquí asumimos que:
#  - listings0 ya está creado según el enunciado
#  - listings0 tiene la columna 'date'
#  - 'price' es numérica (si no lo es, habría que convertirla en otro chunk)
library(dplyr)
library(kableExtra)

# 1. Calcular los estadísticos descriptivos por municipio y periodo.
#    group_by(): forma grupos por municipio y fecha de scraping
#    summarise(): calcula los resúmenes para cada grupo
resumen_p1 <- listings0 %>%
  group_by(neighbourhood_cleansed, date) %>%
  summarise(
    # Tamaño de muestra en cada grupo
    n = n(),
    
    # ---- Estadísticos para 'price' ----
    mean_price = mean(price, na.rm = TRUE),              # media
    sd_price   = sd(price, na.rm = TRUE),                # desviación estándar
    min_price  = min(price, na.rm = TRUE),               # mínimo
    q1_price   = quantile(price, 0.25, na.rm = TRUE),    # cuartil 1
    med_price  = median(price, na.rm = TRUE),            # mediana
    q3_price   = quantile(price, 0.75, na.rm = TRUE),    # cuartil 3
    max_price  = max(price, na.rm = TRUE),               # máximo
    
    # ---- Estadísticos para 'number_of_reviews' ----
    mean_reviews = mean(number_of_reviews, na.rm = TRUE),   # media
    sd_reviews   = sd(number_of_reviews, na.rm = TRUE),     # desviación estándar
    med_reviews  = median(number_of_reviews, na.rm = TRUE), # mediana
    
    # Indicar a dplyr que "desagrupe" al terminar
    .groups = "drop"
  )

# (Opcional) ver una muestra de la tabla en la consola
# head(resumen_p1)

# 2. Ordenar y mostrar la tabla con kableExtra


# Opcional: que los NA aparezcan como vacío en la tabla
options(knitr.kable.NA = "")

tabla_p1 <- resumen_p1 %>%
  arrange(neighbourhood_cleansed, date) %>%              # ordenar municipio/fecha
  mutate(across(where(is.numeric), ~ round(., 2)))       # redondear numéricos

# Necesitas saber el número de filas para colorear el cuerpo
n_filas <- nrow(tabla_p1) 

tabla_p1 %>%
  kbl(
    caption = "Estadísticos descriptivos de price y number_of_reviews por municipio y periodo",
    col.names = c(
      "Municipio", "Periodo", "n",
      "Media", "SD", "Mín.", "Q1", "Mediana", "Q3", "Máx.",
      "Media", "SD", "Mediana"
    ),
    align    = c("l", "c", "r", rep("r", 10)),
    booktabs = TRUE
  ) %>%
  add_header_above(c(" " = 3, "Price" = 7, "Number of reviews" = 3),background = "#2c3e50") %>%
  kable_styling(
    full_width        = FALSE,
    # Nota: Si pones un color de fondo manual, a veces es mejor quitar "striped"
    # para que no se mezcle, pero puedes dejarlo si quieres.
    bootstrap_options = c("striped", "hover", "condensed", "responsive")
  ) %>%
  column_spec(1, bold = TRUE) %>%
  
  # --- AQUÍ ESTÁ EL CAMBIO DE COLOR ---
  
  # 1. Cambiar color de fondo de la CABECERA (Fila 0)
  # 'color' es el texto, 'background' es el fondo
  row_spec(0, bold = TRUE, color = "white", background = "#2c3e50") %>% 
  
  # 2. Cambiar color de fondo de las FILAS DE DATOS (Filas 1 hasta n)
  # Esto quitará el blanco de fondo. Puedes usar nombres ("lightyellow") o Hex ("#fffff0")
  row_spec(1:n_filas, color = "white", background = "#2c3e50") %>% 
  
  # -------------------------------------
  
  collapse_rows(columns = 1, valign = "top") %>%
  scroll_box(width="100%", height="600px")
```

::: {.cell-output-display}
`````{=html}
<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:600px; overflow-x: scroll; width:100%; "><table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
<caption>Estadísticos descriptivos de price y number_of_reviews por municipio y periodo</caption>
 <thead>
<tr>
<th style="empty-cells: hide;border-bottom:hidden;position: sticky; top:0; background-color: #FFFFFF;" colspan="3"></th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; padding-right: 4px; padding-left: 4px; background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;" colspan="7"><div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">Price</div></th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; padding-right: 4px; padding-left: 4px; background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;" colspan="3"><div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">Number of reviews</div></th>
</tr>
  <tr>
   <th style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> Municipio </th>
   <th style="text-align:center;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> Periodo </th>
   <th style="text-align:right;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> n </th>
   <th style="text-align:right;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> Media </th>
   <th style="text-align:right;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> SD </th>
   <th style="text-align:right;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> Mín. </th>
   <th style="text-align:right;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> Q1 </th>
   <th style="text-align:right;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> Mediana </th>
   <th style="text-align:right;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> Q3 </th>
   <th style="text-align:right;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> Máx. </th>
   <th style="text-align:right;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> Media </th>
   <th style="text-align:right;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> SD </th>
   <th style="text-align:right;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> Mediana </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Alaró </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 425.23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 884.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 138.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 194.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 335.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 65.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 400.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 771.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 187.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 68.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 439.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 831.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 347.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 71.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 599.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1496.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 220.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 361.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75.31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 567.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1487.23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 206.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 347.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 48.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 537.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1321.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 160.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 220.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 366.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 724.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1795.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 231.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 390.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 83.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 851.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2020.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 253.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 406.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Alcúdia </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 956 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 164.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 114.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 251.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1758 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 955 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 209.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 170.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 119.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 166.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1758 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 955 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 272.23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 196.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 329.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1658 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 955 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 284.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 485.23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 156.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 322.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 953 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 259.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 583.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 178.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 955 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 753.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2151.10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 127.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 186.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.02 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 955 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 909.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2309.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 249.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 397.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 955 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 816.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2158.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 242.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 381.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10053 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Algaida </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 279.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 402.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 145.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 187.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 295.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3125 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 255.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 261.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 141.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 187.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 297.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1994 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 397.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 108 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 177.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 232.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 311.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3085 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 310.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 403.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 108 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 224.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3125 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 301.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 151.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 203.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 335.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2188 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1372.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3014.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 152.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 240.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 423.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1536.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3190.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 191.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 232.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 447.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1380.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2952.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 118 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 233.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 454.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Andratx </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 586.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1058.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 122.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 493.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6711 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 584.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 933.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 128.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 223.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 518.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4756 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 752.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1253.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 182.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 311.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 612.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8403 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.03 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 795.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1415.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 317.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 615.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 54.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 693.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1350.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 128.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 557.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 930.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1866.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 133.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 262.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 674.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1020.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1844.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 182.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 326.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 668.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1120.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2070.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 326.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 670.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 61.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Ariany </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 218.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 154.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 125.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 151.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 246.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 689 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 211.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 121.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 153.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 240.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 675 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 256.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 153.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 256.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 987 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 243.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 179.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 151.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 256.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 987 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 222.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 122.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 138.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 296.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 579 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2705.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4159.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 156.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 283.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3069.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4268.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 202.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 276.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8875.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2861.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4136.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 189.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 285.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8875.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.5 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Artà </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 244.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 226.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 141.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 266.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1590 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 248.78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 139.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 202.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 273.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1610 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 247.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 132.03 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 158.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 225.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1036 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 298.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 750.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 158.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 229.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 301.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 291.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 757.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 138.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 224.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 296.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1611.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3306.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 153.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 251.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 359.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2329.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3843.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 179.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 275.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 463.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10018 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1954.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3570.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 177.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 285.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 410.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10016 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Banyalbufar </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 213.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 161.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 119.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 167.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 231.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 871 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 64.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 84.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 168.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 232.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 66.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 86.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 258.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 233.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 149.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 188.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 292.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1500 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 90.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 257.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 158.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 202.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 320.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 875 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 93.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 244.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 206.85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 131.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 268.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1159 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 96.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 666.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2033.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 134.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 172.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 268.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 99.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 660.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1849.59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 156.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 202.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 350.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 102.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 647.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1849.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 162.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 208.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 353.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 91.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 107.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Binissalem </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 259.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 194.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 144.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 222.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 331.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 977 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 264.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 193.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 147.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 336.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 963 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 198.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 172.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 235.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 334.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 963 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 294.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 174.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 235.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 342.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1277 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 256.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 144.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 197.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 333.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1350 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 969.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2428.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 380.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1000.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2372.78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 261.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 370.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9143 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1124.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2600.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 192.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 266.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 393.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50.31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Bunyola </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 288.28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 151.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 235.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 334.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1257 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 48.23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 298.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 223.69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 174.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 250.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 350.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1300 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 48.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 354.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 269.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 289.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 485.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1400 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 350.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 269.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 202.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 403.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1400 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 316.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 271.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 142.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 236.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 371.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1400 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 54.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 752.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1832.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 182.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 297.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 425.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 786.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1845.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 227.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 323.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 479.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 769.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1823.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 319.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 484.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.5 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Búger </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 254.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 146.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 154.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 196.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 349.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 772 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 252.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 145.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 151.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 203.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 349.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 693 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 297.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 238.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 357.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 861 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 305.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 182.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 181.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 236.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 378.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 861 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 283.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 168.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 223.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 381.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1037 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 851.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2223.10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 172.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 227.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 409.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 718.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1838.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 206.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 265.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 413.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 784.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 201.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 277.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 405.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Calvià </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 462.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 600.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 129.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 245.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 500.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3700 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 91.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 448.31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 543.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 126.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 240.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 517.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3125 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 96.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 508.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 490.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 188.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 332.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 637.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3146 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 510.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 533.10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 189.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 294.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 685.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3305 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 51.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 122.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 451.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 510.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 250.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 551.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3125 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 54.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 129.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 740.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1731.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 125.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 594.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 131.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 791.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1538.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 196.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 356.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 800.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 139.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 833.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1760.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 184.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 315.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 696.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 64.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 146.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Campanet </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 123.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 128.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 160.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 212.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1032 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 154.02 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 131.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 164.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 212.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1483 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 244.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 207.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 216.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 277.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2088 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 240.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 204.85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 149.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 209.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 272.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1993 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 220.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 131.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 140.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 192.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 265.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1079 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1423.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3129.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 151.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 303.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1365.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2983.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 111 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 187.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 258.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 350.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1299.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2918.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 255.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 351.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Campos </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 311 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 263.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 245.69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 191.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 275.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2171 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 311 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 216.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 149.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 197.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 279.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1650 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 310 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 293.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 166.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 235.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 324.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2150 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 310 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 322.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 599.78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 164.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 309.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 310 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 323.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 609.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 220.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 347.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 311 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1171.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2777.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 237.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 409.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 311 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1455.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3029.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 265.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 498.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 312 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1346.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2927.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 257.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 457.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Capdepera </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 222.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 259.10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 95.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 257.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 224.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 257.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 99.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 151.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 254.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 259.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 282.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 262.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 287.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 116.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 178.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 241.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 289.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 99.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 158.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 275.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2503.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3937.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 270.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2749.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10013 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2879.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4080.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 343.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10013 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2850.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4079.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 177.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 316.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Consell </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 253.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 250.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 269.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 516 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 253.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 125.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 207.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 258.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 272.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 490 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 288.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 131.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 261.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 270.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 320.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 518 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 197.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 258.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 303.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 498 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 51.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 252.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 220.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 273.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 600 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1622.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3695.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 176.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 240.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 346.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3376.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4756.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 298.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 452.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6887.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 51.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2908.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4513.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 214.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 347.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4755.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 54.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Costitx </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 118.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 118.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 217.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 630 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 197.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 122.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 119.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 223.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 630 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 231.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 152.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 123.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 263.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 762 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 241.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 149.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 147.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 178.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 283.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 762 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 204.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 127.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 127.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 174.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 229.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 707 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2035.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3723.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 120.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 184.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 303.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2262.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3793.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 163.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 218.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 528.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9143 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2055.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3715.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 245.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 674.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Deyá </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 462.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 368.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 218.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 393.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 539.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1685 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 494.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 411.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 100 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 207.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 391.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 605.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1697 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 657.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 590.03 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 246.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 475.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 917.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2374 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 656.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 575.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 116 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 259.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 456.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 942.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2248 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 51.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 515.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 493.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 223.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 359.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 570.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2299 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 48.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 545.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 471.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 225.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 387.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 695.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2318 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 48.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 54.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 698.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 618.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 120 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 277.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 475.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 917.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2597 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 51.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 652.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 576.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 467.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 797.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2327 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Escorca </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 243.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 209.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 152.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 187.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 203.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 246.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 228.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 152.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 203.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1100 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 280.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 288.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 163.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 196.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 203.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1350 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 277.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 290.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 104 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 203.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1350 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 264.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 222.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 244.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1050 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 794.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2128.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 223.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 244.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 240.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 145.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 109 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 167.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 198.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 244.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 678 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50.85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 296.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 288.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 158.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 244.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1350 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52.78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Esporles </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 373.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 310.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 273.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 531.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1141 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 417.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 266.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 271.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 352.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 523.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1112 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 488.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 244.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 447.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 609.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1064 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 502.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 251.02 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 351.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 482.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 629.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1299 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 569.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 373.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 495.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 720.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1486 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 716.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1686.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 270.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 375.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 553.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1018.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2055.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 384.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 544.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 665.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9143 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 992.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2043.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 369.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 509.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 638.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 51.28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.5 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Estellencs </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 514.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 390.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 163.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 448.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 999.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 242.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 147.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 201.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 265.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 690 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 297.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 186.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 201.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 251.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 359.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 690 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1898.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3731.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 194.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 275.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 582.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2114.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3647.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 188.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 787.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 999.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 766.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2308.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 129.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 203.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 274.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1894.67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3742.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 167.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 244.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 472.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10022 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 51.28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1376.28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3142.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 204.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 290.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 465.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Felanitx </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 387 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 351.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 378.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 120.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 213.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 384.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1606 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 387 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 337.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 365.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 116.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 218.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 375.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1500 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 387 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 327.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 266.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 255.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 400.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2310 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 387 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 342.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 550.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 153.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 255.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 399.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 387 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 394.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 621.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 132.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 436.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 387 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1502.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2987.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 700.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 388 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1633.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3155.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 196.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 346.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 649.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 388 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1567.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3121.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 329.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 600.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Fornalutx </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 245.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 233.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 243.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1645 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 51.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 249.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 220.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 174.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 249.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1511 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 248.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 139.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 207.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 296.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 752 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 48.03 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 54.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 245.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 127.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 105 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 166.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 196.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 282.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 673 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 51.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 127.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 142.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 250.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 762 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 54.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1040.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2659.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 154.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 346.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59.02 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1125.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2714.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 168.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 229.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 398.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58.69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 61.59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1357.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2970.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 170.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 251.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 436.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Inca </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 241.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 138.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 199.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1154 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 246.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 176.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 199.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 283.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1387 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 278.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 243.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 178.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 233.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 324.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2680 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 272.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 243.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 231.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 307.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2680 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 249.85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 139.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 302.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1154 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 769.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2144.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 149.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 339.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 790.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2116.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 184.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 400.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 866.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2230.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 188.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 258.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 387.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10013 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Lloret de Vistalegre </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 191.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 145.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 201.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 474 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 187.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 65.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 100 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 186.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 204.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 368 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 223.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 86.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 162.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 248.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 474 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 54.78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 215.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 71.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 205.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 246.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 396 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 94.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 214.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 458 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 88.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 500 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 95.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 118 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 312.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 415 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 232.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 93.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 118 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 209.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 279.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 500 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Lloseta </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 246.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 141.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 223.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 251.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 990 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 241.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.03 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 156.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 821 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 282.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 144.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 114 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 186.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 323.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 806 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 284.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 108 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 262.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 323.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 715 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 293.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 198.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 148.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 236.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 419.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 913 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.02 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 824.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2246.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 152.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 222.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 340.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1132.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2641.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 216.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 319.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 395.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1089.85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2543.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 106 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 212.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 289.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 387.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Llubí </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 208.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 108.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 204.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1500 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 184.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 153.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 108.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 156.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 187.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1100 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 217.59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 131.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1100 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 226.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 132.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 179.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 234.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1400 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 233.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 264.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 144.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 222.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1500 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 552.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1720.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 225.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 598.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1693.28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 158.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 198.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 314.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 448.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1345.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 149.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 186.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 277.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Llucmajor </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 314 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 321.78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 487.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 129.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 317.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3125 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 314 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 258.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 242.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 129.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 314.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3050 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 315 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 164.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 246.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 387.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2320 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.02 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 315 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 367.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 808.69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 161.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 247.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 389.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 372.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 988.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 131.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 211.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 344.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1178.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2721.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 158.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 245.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 428.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1622.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3140.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 197.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 325.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 599.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10032 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1624.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3161.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 188.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 309.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 550.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Manacor </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 442 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 251.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 212.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 124.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2083 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 442 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 247.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 125.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 196.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2083 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 442 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 285.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 162.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 330.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2607 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 443 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 350.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 833.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 162.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 335.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 443 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 340.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 844.28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 140.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 329.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 443 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1101.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2665.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 140.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 231.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 383.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 443 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1170.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2704.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 274.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 424.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 443 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1178.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2725.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 176.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 268.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 421.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Mancor de la Vall </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 100.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 147.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 208.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 250.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 473 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 216.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 158.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 251.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 421 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 275.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 106.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 105 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 167.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 298.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 364.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 490 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 276.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 107.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 103 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 291.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 366.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 485 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 264.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 177.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 241.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 291.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 567 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 388.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 628.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 253.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 358.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3539 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 301.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 108.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 198.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 339.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 378.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 486 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 289.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 371.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 514 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Maria de la Salut </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 160.85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 73.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 103.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 341 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 177.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 116.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 161.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 211.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 382 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 212.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 94.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 208.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 273.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 444 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 224.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 116.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 146.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 271.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 553 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 209.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 109.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 194.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 271.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 560 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 903.59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2567.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 166.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 233.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 901.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2369.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 168.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 208.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 357.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 893.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2339.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 167.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 204.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 368.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 48.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Marratxí </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 351.10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 243.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 196.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 285.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 422.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1235 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 298.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 131.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 198.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 271.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 360.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 613 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 365.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 365.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 457.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 773 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 520.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1236.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 233.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 324.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 468.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 490.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1262.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 188.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 283.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 446.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024.03 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3569.59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 208.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 358.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 645.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1566.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3080.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 270.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 421.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 637.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1761.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3182.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 250.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 429.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 658.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Montuïri </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 99.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 90.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 184.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 541 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97.31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 113.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 145.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 191.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 541 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 224.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 115.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 148.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 184.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 257.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 614 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 235.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 120.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 176.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 201.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 252.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 635 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 182.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 90.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 290.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 747 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 209.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 96.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 252.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 747 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 561.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1787.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 191.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 265.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 232.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 209.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 252.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 615 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.5 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Muro </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 222.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 125.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 132.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 188.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 663 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 227.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 128.03 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 142.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 281.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 758 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 270.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 161.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 351.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 863 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 353.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 941.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 160.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 236.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 321.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 329.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 945.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 201.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.02 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 582.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1792.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 138.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 213.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 296.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 814.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2135.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 279.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 420.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 849.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2195.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 182.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 290.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 415.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Palma de Mallorca </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 503 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 268.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 382.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 95.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 276.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3125 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 81.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 503 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 226.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 199.31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 178.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 279.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1741 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 84.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 503 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 296.85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 249.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 124.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 235.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 379.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1821 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 64.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 88.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 503 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 312.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 529.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 118.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 355.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 91.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 505 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 274.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 550.10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 93.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 279.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 74.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 94.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 505 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 442.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1339.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 105.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 76.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 96.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 505 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 519.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1351.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 129.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 257.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 441.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 81.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 101.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 505 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 517.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1377.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 128.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 254.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 401.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 86.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 104.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Petra </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 275.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 133.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 364.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 269.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 341.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 960 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 293.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 161.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 241.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 399.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 758 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 312.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 204.31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 152.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 245.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 400.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 292.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 222.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 225.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 371.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1858.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3496.28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 163.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 280.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 536.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2053.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3629.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 285.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 541.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1584.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3182.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 187.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 284.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 509.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.5 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Pollença </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1593 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 270.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 487.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 182.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 283.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11600 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1593 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 249.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 252.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 134.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 183.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 278.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5500 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1592 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 319.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 353.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 161.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 234.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 358.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8797 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1593 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 303.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 363.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 156.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 224.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 339.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1595 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 288.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 394.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 140.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 188.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1593 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 965.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2455.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 144.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 203.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 370.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10011 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1593 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1262.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2777.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 189.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 285.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 499.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1593 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1242.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2741.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 275.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 488.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Porreres </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 298.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 481.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 197.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 245.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3200 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 240.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 125.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 189.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 240.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1500 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 254.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 168.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 211.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 272.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1188 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 432.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1335.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 100 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 170.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 218.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 295.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 486.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1397.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 132.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 338.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1212.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2934.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 152.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 350.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1006.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2601.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 106 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 172.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 357.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10011 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1154.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2822.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 218.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 370.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Puigpunyent </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 247.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 147.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 168.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 194.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 284.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 750 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 79.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 274.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 179.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 205.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 380.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 750 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 279.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 160.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 240.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 370.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 750 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 66.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 83.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 713.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2078.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 118 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 192.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 233.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 342.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 87.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 687.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2038.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 168.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 350.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 73.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 88.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1051.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2672.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 118 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 168.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 340.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 73.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 89.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1161.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2792.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 115 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 208.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 325.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 422.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 76.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 91.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1130.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2802.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 181.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 305.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 439.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 93.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Sa Pobla </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 296.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 765.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 149.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 203.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 291.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 220.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 120.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 147.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 268.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 934 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 274.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 160.03 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 177.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 250.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 324.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1120 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 380.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1063.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 248.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 333.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 376.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1075.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 161.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 222.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 324.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 852.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2295.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 306.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 922.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2318.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 204.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 264.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 417.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 170 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 796.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2088.67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 177.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 256.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 386.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Sant Joan </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 202.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 102.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 139.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 501 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 189.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 81.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 137.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 217.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 419 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 206.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 97.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 139.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 187.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 245.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 566 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 95.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 118 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 146.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 245.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 566 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 257.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 144.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 124 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 202.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 288.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 715 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 642.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2042.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 105 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 275.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 581.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1619.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 120 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 214.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 240.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 266.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 559.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1587.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 100 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 196.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 238.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 275.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.5 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Sant Llorenç des Cardassar </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 90.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 158.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 282.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1200 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 209.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 95.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 158.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1200 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 259.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 217.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 129.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 197.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1750 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 322.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 816.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 128.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 311.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.02 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 292.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 809.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 96.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 311.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 892.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2420.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 100.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 170.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 342.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1104.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2640.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 139.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 226.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 394.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 52.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 157 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1077.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2620.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 406.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Santa Eugènia </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 258.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 145.59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 337.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 489 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 266.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 147.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 203.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 358.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 489 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 282.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 129.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 152 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 243.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 378.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 464 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 282.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 132.78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 152 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 238.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 376.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 476 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 227.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 140.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 137 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 187.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 476 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1693.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3580.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 357.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2054.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3883.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 381.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 405.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1778.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3539.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 243 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 262.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 327.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 480.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 37.17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.5 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Santa Margalida </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 405 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 186.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 100.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 141.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 217.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1747 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 405 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 186.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 156.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 100.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 139.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 201.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1900 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 405 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 227.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 194.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 132.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 176.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 254.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2813 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 405 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 252.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 524.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 134.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 254.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.76 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 405 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 233.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 523.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 100.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 151.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 233.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 405 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 873.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2394.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 163.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 277.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10035 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 405 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1004.64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2522.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 145.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 209.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 347.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 405 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1015.28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2555.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 333.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10005 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Santa María del Camí </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 402.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 565.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 181.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 270.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 400.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2800 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 423.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 649.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 224.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 261.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 399.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3200 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 344.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 238.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 307.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 398.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 705 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 568.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1105.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 307.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 400.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 5329 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 467.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 569.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 218.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 439.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 441.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2800 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 790.45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1975.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 188.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 220.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 396.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 887.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2005.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 249.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 315.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 419.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 48.48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 872.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1956.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 277.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 319.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 450.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Santanyí </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 661 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 338.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 541.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 125.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 196.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 349.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10698 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 661 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 308.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 326.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 129.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 198.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 350.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1724 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 661 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 327.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 373.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 152.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 229.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 393.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6963 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 661 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 326.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 461.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 151.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 378.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 661 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 356.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 395.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 130.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 202.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 378.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2031 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 661 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1258.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2735.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 142.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 533.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 660 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1382.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2904.28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 288.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 560.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10028 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 660 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1322.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2840.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 176.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 285.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 514.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.81 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.5 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Selva </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 256.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 187.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 140.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 320.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1350 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 255.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 181.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 138.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 201.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 312.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1050 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 6.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 298.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.54 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 225.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 364.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1458 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 40.85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 461.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1304.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 374.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 447.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1315.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 147.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 216.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 379.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 839.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2239.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 147.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 219.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 388.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 168 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 929.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2353.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 48 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 178.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 264.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 397.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10018 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 46.03 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 973.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2427.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 261.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 404.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Sencelles </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 259.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 147.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 308.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 830 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 266.28 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 175.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 143.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 316.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 868 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.09 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 14.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 312.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 199.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 280.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 366.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1150 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 459.42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1210.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 192.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 262.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 396.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 433.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1241.78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 206.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 357.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1481.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3067.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 148.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 234.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 472.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.03 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1182.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2572.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 221.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 304.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 482.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 33.97 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 67 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1626.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3105.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 193.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 309.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 526.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Ses Salines </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 192.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 120.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 42 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 120.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 170.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 240.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 656 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 193.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 120.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 165.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 249.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 656 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 251.59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 173.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 198.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 304.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.04 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 251.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 169.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 148.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 308.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 18.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 216.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 162.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 171.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 250.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 980 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50.78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 747.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2138.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 119.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 167.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 296.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 35.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 51.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1136.37 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2725.01 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 158.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 225.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 353.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.47 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1129.72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2742.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 225.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 356.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.43 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Sineu </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 204.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 147.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 125.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 155.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 213.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 978 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.63 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 207.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136.53 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 129.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 172.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 218.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 899 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 234.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 145.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 140.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 294.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 782 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 245.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 185.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 78 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 144.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 282.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1216 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 231.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 152.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 132.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 180.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 300.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 960 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.02 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1066.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2706.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 142.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 192.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 285.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1119.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2727.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 101 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 178.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 375.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 23.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 934.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2451.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 105 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 176.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 220.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 315.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.51 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Son Servera </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 226.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 188.69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 115.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 252.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1224 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 225.99 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 182.56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 112.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 190.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 250.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1193 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.58 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.60 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 7.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 283.74 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 237.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 137.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 230.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 339.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2045 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.33 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 8.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 402.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1107.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 64 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 144.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 232.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 339.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 386.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1112.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 45 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 124.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 211.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 311.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9999 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.05 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1189.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2794.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 123.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 229.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 350.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1617.18 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3210.93 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 284.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 479.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10018 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.82 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1506.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3087.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 72 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 282.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 489.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.86 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.02 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Sóller </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 314 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 275.26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 324.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 184.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 292.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3742 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 41.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 283.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 319.95 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 149.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 309.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3742 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 43.11 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50.80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 312 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 336.66 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 407.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 170.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 235.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 390.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 4212 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 47.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 53.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 32.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 312 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 322.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 354.24 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 178.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 232.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 362.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 3360 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 51.91 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55.89 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 312 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 263.06 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 262.38 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 140.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 182.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 297.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2260 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 55.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 58.34 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 38.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 710.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1948.87 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 153.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 198.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 330.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10044 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56.22 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59.27 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 39.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 985.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2306.90 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 198.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 280.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 465.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 990.88 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2330.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 80 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 195.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 270.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 459.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 10000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 65.57 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 65.15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 49.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Valldemossa </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 265.07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 229.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 69 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 129.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 215.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 314.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1450 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 34.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 279.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 217.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 134.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 214.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 342.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1160 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 57.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 36.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 328.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 243.19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 100 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 151.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 250.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 385.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1477 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 63.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 59.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 44.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 340.02 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 251.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 100 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 153.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 269.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 413.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1485 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 68.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 62.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 50.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 290.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 252.83 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 135.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 247.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 364.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1620 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 72.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 64.29 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 288.32 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 205.49 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 138.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 264.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 362.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1260 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 73.39 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 65.70 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 338.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 263.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 154.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 284.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 412.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1620 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 77.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 68.41 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 60.5 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 56 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 346.77 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.55 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 85 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 174.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 271.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 435.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1620 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 81.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 70.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 64.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: rgba(44, 62, 80, 255) !important;vertical-align: top !important;" rowspan="8"> Vilafranc de Bonany </td>
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 220.65 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 81.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 105 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 159.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 214.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 260.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 458 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 20.96 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 11.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 227.62 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 117.94 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 110 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 150.25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 215.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 261.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 699 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 21.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.92 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 12.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 271.46 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 137.98 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 144 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 186.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 239.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 291.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 826 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 22.73 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 24.14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 283.79 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 137.40 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 154 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 210.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 254.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 290.75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 825 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 13.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 270.36 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 127.35 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 75 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 189.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 255.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 329.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 569 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.44 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 27.30 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 15.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 643.12 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1784.13 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 111 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 196.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 263.5 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 358.50 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 26.84 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.08 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 16.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 638.52 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 1746.61 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 212.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 273.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 313.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 9000 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 28.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 29.71 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 17.0 </td>
  </tr>
  <tr>
   
   <td style="text-align:center;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 25 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 304.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 164.20 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 136 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 200.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 264.0 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 312.00 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 831 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 30.16 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 31.68 </td>
   <td style="text-align:right;color: white !important;background-color: rgba(44, 62, 80, 255) !important;"> 19.0 </td>
  </tr>
</tbody>
</table></div>

`````
:::
:::



## Pregunta 2 (**1punto**)

Consideremos las variables `price` y `number_of_reviews` de Pollença y Palma del periodo "2024-09-13", del fichero `listing_common0_select.RData`. 
Estudiad si estos datos se aproximan a una distribución normal gráficamente. Para ello, dibujad el histograma, la función "kernel-density" que aproxima la densidad y la densidad de la normal de media y varianza las de las muestras de las variables `price` (para precios mayores de 50 y menores de 400) y `number_of_reviews` para Palma y Pollença


::: {.cell}

```{.r .cell-code}
library(dplyr)
library(ggplot2)

# -------------------------------------------------------------------
# Supongo que ya tienes 'listings0' creado con la columna 'date'.
# Si lo cargas de otro fichero (p.ej. listing_common0_select.RData),
# simplemente asegúrate de que se llama 'listings0' y tiene:
#   - date
#   - neighbourhood_cleansed
#   - price
#   - number_of_reviews
# -------------------------------------------------------------------

# 1) Filtrar solo la fecha y los municipios de interés
datos_2 <- listings0 %>%
  filter(
    date == as.Date("2024-09-13"),
    neighbourhood_cleansed %in% c("Pollença", "Palma de Mallorca")
  )

# 2) Para PRICE solo queremos precios entre 50 y 400 €
datos_price <- datos_2 %>%
  filter(price > 50, price < 400)

# ----------------- FUNCIONES AUXILIARES DE GRÁFICO -----------------

# Histograma + densidad kernel + curva normal (misma media y sd) para PRICE
grafico_price <- function(df, titulo) {
  m <- mean(df$price, na.rm = TRUE)
  s <- sd(df$price, na.rm = TRUE)
  
  # Secuencia de x y densidad normal teórica
  x_seq <- seq(
    from = min(df$price, na.rm = TRUE),
    to   = max(df$price, na.rm = TRUE),
    length.out = 400
  )
  normal_df <- data.frame(
    x    = x_seq,
    dens = dnorm(x_seq, mean = m, sd = s)
  )
  
  ggplot(df, aes(x = price)) +
    geom_histogram(aes(y = ..density..), bins = 30, alpha = 0.4) +
    geom_density(linewidth = 1) +
    geom_line(data = normal_df, aes(x = x, y = dens),
              linewidth = 1, linetype = "dashed") +
    labs(
      title = titulo,
      x = "Precio por noche (€)",
      y = "Densidad"
    ) +
    theme_minimal()
}

# Histograma + densidad kernel + curva normal para NUMBER_OF_REVIEWS
grafico_reviews <- function(df, titulo) {
  m <- mean(df$number_of_reviews, na.rm = TRUE)
  s <- sd(df$number_of_reviews,   na.rm = TRUE)
  
  x_seq <- seq(
    from = min(df$number_of_reviews, na.rm = TRUE),
    to   = max(df$number_of_reviews, na.rm = TRUE),
    length.out = 400
  )
  normal_df <- data.frame(
    x    = x_seq,
    dens = dnorm(x_seq, mean = m, sd = s)
  )
  
  ggplot(df, aes(x = number_of_reviews)) +
    geom_histogram(aes(y = ..density..), bins = 30, alpha = 0.4) +
    geom_density(linewidth = 1) +
    geom_line(data = normal_df, aes(x = x, y = dens),
              linewidth = 1, linetype = "dashed") +
    labs(
      title = titulo,
      x = "Número de reviews",
      y = "Densidad"
    ) +
    theme_minimal()
}

# ----------------- SEPARAR DATOS POR MUNICIPIO -----------------

# Palma de Mallorca
palma_price <- datos_price %>%
  filter(neighbourhood_cleansed == "Palma de Mallorca")
palma_rev <- datos_2 %>%
  filter(neighbourhood_cleansed == "Palma de Mallorca")

# Pollença
pollenca_price <- datos_price %>%
  filter(neighbourhood_cleansed == "Pollença")
pollenca_rev <- datos_2 %>%
  filter(neighbourhood_cleansed == "Pollença")

# ----------------- GRÁFICOS A MOSTRAR EN EL INFORME -----------------

# 1) PRICE (50–400 €) Palma
grafico_price(
  palma_price,
  "Palma de Mallorca – price (50–400 €), 2024-09-13"
)
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/pregunta2-1.png){width=672}
:::

```{.r .cell-code}
# 2) PRICE (50–400 €) Pollença
grafico_price(
  pollenca_price,
  "Pollença – price (50–400 €), 2024-09-13"
)
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/pregunta2-2.png){width=672}
:::

```{.r .cell-code}
# 3) NUMBER_OF_REVIEWS Palma
grafico_reviews(
  palma_rev,
  "Palma de Mallorca – number_of_reviews, 2024-09-13"
)
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/pregunta2-3.png){width=672}
:::

```{.r .cell-code}
# 4) NUMBER_OF_REVIEWS Pollença
grafico_reviews(
  pollenca_rev,
  "Pollença – number_of_reviews, 2024-09-13"
)
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/pregunta2-4.png){width=672}
:::
:::


## Pregunta 3 (**1punto**)

Con los datos de `listings0` de todos los periodos, contrastar si la media del precio en Alcudia es igual a la de Palma **contra** que es mayor que en Palma para los precios mayores que 50 euros y menores de 400. Construid la hipótesis nula y alternativa, calculad el $p$-valor y el intervalo de confianza asociado al contraste. Justifica técnicamente la conclusión del contraste.


::: {.cell}

```{.r .cell-code}
# 1. Filtrar los datos:
#    - Seleccionamos solo los registros con precio > 50 y < 400
#    - Seleccionamos los municipios "Alcúdia" y "Palma de Mallorca"
datos_p3 <- listings0 %>%
  filter(price > 50 & price < 400) %>%
  filter(neighbourhood_cleansed %in% c("Alcúdia", "Palma de Mallorca"))

# 2. Separar en dos vectores para facilitar el t.test
# Nota: Asegurarse de cómo está escrito el nombre en los datos (con o sin acento). 
# En los datos oficiales suele ser "Alcúdia".
precios_alcudia <- datos_p3 %>%
  filter(neighbourhood_cleansed == "Alcúdia") %>%
  pull(price)

precios_palma <- datos_p3 %>%
  filter(neighbourhood_cleansed == "Palma de Mallorca") %>%
  pull(price)

# 3. Realizar el t-test
#    alternative = "greater" porque queremos probar si Alcúdia > Palma
test_p3 <- t.test(x = precios_alcudia, 
                  y = precios_palma, 
                  alternative = "greater", 
                  var.equal = FALSE) # Asumimos varianzas desiguales (Welch)

# Mostramos el resultado
test_p3
```

::: {.cell-output .cell-output-stdout}

```

	Welch Two Sample t-test

data:  precios_alcudia and precios_palma
t = 5.1623, df = 5180.6, p-value = 1.265e-07
alternative hypothesis: true difference in means is greater than 0
95 percent confidence interval:
 7.02432     Inf
sample estimates:
mean of x mean of y 
 193.9472  183.6373 
```


:::
:::




## Pregunta 4 (**1punto**)

Con los  datos de `listings0`, contrastar si las medias de los precios en Alcudia entre los periodos  2025-06-15 y   2025-09-21 son iguales contra que son menores en 2023. Construid la hipótesis nula y alternativa, calculad el $p$-valor y el intervalo de confianza asociado al contraste.

Haced un diagrama de caja comparativo de los precios  en Alcudia  por periodo y coméntalo.


::: {.cell}

```{.r .cell-code}
# -------------------------------------------------------------------
# PREGUNTA 4
# Contraste de medias en Alcúdia: 2025-06-15 vs 2025-09-21
# Hipótesis: Media(Junio) < Media(Septiembre)
# -------------------------------------------------------------------

# 1. Filtrar los datos para Alcúdia y las dos fechas de interés
#    Aseguramos que la fecha sea de tipo Date para filtrar correctamente
fechas_interes <- as.Date(c("2025-06-15", "2025-09-21"))

datos_p4 <- listings0 %>%
  filter(neighbourhood_cleansed == "Alcúdia",
         date %in% fechas_interes) %>%
  # Seleccionamos variables útiles para limpiar un poco
  select(price, date, neighbourhood_cleansed)

# 2. Separar los precios en dos vectores para el t.test
precios_jun <- datos_p4 %>%
  filter(date == "2025-06-15") %>%
  pull(price)

precios_sep <- datos_p4 %>%
  filter(date == "2025-09-21") %>%
  pull(price)

# 3. Realizar el t-test (Welch Two Sample t-test)
#    alternative = "less" porque la hipótesis es que precios_jun < precios_sep
test_p4 <- t.test(x = precios_jun, 
                  y = precios_sep, 
                  alternative = "less", 
                  var.equal = FALSE)

# Mostrar resultados del test
test_p4
```

::: {.cell-output .cell-output-stdout}

```

	Welch Two Sample t-test

data:  precios_jun and precios_sep
t = 0.88858, df = 1822.3, p-value = 0.8128
alternative hypothesis: true difference in means is less than 0
95 percent confidence interval:
     -Inf 263.4871
sample estimates:
mean of x mean of y 
 909.0679  816.6827 
```


:::

```{.r .cell-code}
# -------------------------------------------------------------------
# DIAGRAMA DE CAJA (BOXPLOT) COMPARATIVO
# -------------------------------------------------------------------

ggplot(datos_p4, aes(x = factor(date), y = price, fill = factor(date))) +
  geom_boxplot(alpha = 0.7, outlier.colour = "red", outlier.size = 1) +
  scale_fill_manual(values = c("#3498db", "#e74c3c")) + # Colores personalizados
  labs(
    title = "Distribución de precios en Alcúdia por periodo",
    subtitle = "Comparativa: 15-Jun-2025 vs 21-Sep-2025",
    x = "Fecha de Scrapping",
    y = "Precio (€)",
    fill = "Fecha"
  ) +
  theme_minimal() +
  theme(legend.position = "none") # Ocultamos leyenda porque el eje X ya es claro
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-10-1.png){width=672}
:::
:::



## Pregunta 5 (**1 punto**)

Comparar con un bopxlot de las valoraciones medias `review_scores_rating` para Alcudia, Palma, Calvià y
Pollença. Hacer el gráfico con ggplot2 y todo lujo de destalles.


::: {.cell}

```{.r .cell-code}
# -------------------------------------------------------------------
# PREGUNTA 5
# Comparación de review_scores_rating mediante Boxplot
# Municipios: Alcúdia, Palma de Mallorca, Calvià, Pollença
# -------------------------------------------------------------------

library(dplyr)
library(ggplot2)

# 1. Definir los municipios de interés
# Nota: Ajusta los nombres exactos según aparezcan en tus datos (tildes, etc.)
municipios_p5 <- c("Alcúdia", "Palma de Mallorca", "Calvià", "Pollença")

# 2. Preparar los datos
datos_p5 <- listings0 %>%
  filter(neighbourhood_cleansed %in% municipios_p5) %>%
  # Eliminamos filas sin puntuación para limpiar el gráfico
  filter(!is.na(review_scores_rating))

# 3. Generar el gráfico con "todo lujo de detalles"
ggplot(datos_p5, aes(x = neighbourhood_cleansed, y = review_scores_rating, fill = neighbourhood_cleansed)) +
  
  # Capa de Boxplot
  # outlier.alpha = 0.3 hace que los puntos atípicos sean semitransparentes para no saturar
  geom_boxplot(alpha = 0.7, outlier.colour = "red", outlier.alpha = 0.5, outlier.size = 1.5) +
  
  # Añadir un punto blanco/negro para indicar la MEDIA (el boxplot solo muestra la mediana)
  stat_summary(fun = mean, geom = "point", shape = 23, size = 3, fill = "white", color = "black") +
  
  # Colores personalizados (Paleta Brewer o manual)
  scale_fill_brewer(palette = "Set2") +
  
  # Etiquetas y Títulos completos
  labs(
    title = "Distribución de las valoraciones de los usuarios",
    subtitle = "Comparativa: Alcúdia, Calvià, Palma y Pollença",
    x = "Municipio",
    y = "Puntuación Media (Rating 0-5)",
    fill = "Municipio",
    caption = "Fuente: Inside Airbnb (listings0) | El rombo blanco indica la media"
  ) +
  
  # Tema estético limpio
  theme_minimal() +
  theme(
    plot.title = element_text(face = "bold", size = 14, hjust = 0.5),
    plot.subtitle = element_text(size = 11, hjust = 0.5, color = "gray40"),
    axis.text.x = element_text(face = "bold", size = 10),
    legend.position = "none" # Ocultamos leyenda porque el eje X ya identifica los grupos
  ) +
  
  # Ajustar escala Y si es necesario (el rating suele ser de 0 a 5 o de 0 a 100 según la versión)
  # En datasets recientes es sobre 5. Si tus datos son sobre 100, ajusta los límites.
  coord_cartesian(ylim = c(3, 5)) # Hacemos zoom en la parte superior donde están casi todos los datos
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-11-1.png){width=672}
:::
:::



## Pregunta 6 (**1 punto**)

Calcular la proporción de apartamentos de la muestra "2025-09-21" con media de valoración `review_scores_rating` mayor que 4 en Alcudia y en Calvià son iguales contra que son distintas. Construid un intervalo de confianza para la diferencia de proporciones.


::: {.cell}

```{.r .cell-code}
# -------------------------------------------------------------------
# PREGUNTA 6
# Comparación de proporciones (Rating > 4): Alcúdia vs Calvià
# Fecha: 2025-09-21
# -------------------------------------------------------------------

# 1. Preparar los datos
datos_p6 <- listings0 %>%
  filter(date == as.Date("2025-09-21"),
         neighbourhood_cleansed %in% c("Alcúdia", "Calvià")) %>%
  filter(!is.na(review_scores_rating)) # Eliminamos los que no tienen puntuación

# 2. Calcular los conteos de "éxitos" y el total de observaciones por municipio
resumen_p6 <- datos_p6 %>%
  group_by(neighbourhood_cleansed) %>%
  summarise(
    exitos = sum(review_scores_rating > 4), # Conteo de ratings > 4
    total = n()                             # Total de apartamentos con rating
  )

# Mostramos la tabla resumen para verificar
resumen_p6
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 2 × 3
  neighbourhood_cleansed exitos total
  <chr>                   <int> <int>
1 Alcúdia                   855   904
2 Calvià                    159   170
```


:::

```{.r .cell-code}
# 3. Extraer vectores para el prop.test
# Aseguramos el orden: Alcúdia primero, luego Calvià (orden alfabético por defecto)
# Ojo: prop.test pide vectores: x (éxitos) y n (totales)
x_exitos <- resumen_p6$exitos
n_totales <- resumen_p6$total

# 4. Realizar el test de proporciones (Two-sample test for equality of proportions)
test_p6 <- prop.test(x = x_exitos, n = n_totales, alternative = "two.sided")

# Mostrar resultados
test_p6
```

::: {.cell-output .cell-output-stdout}

```

	2-sample test for equality of proportions with continuity correction

data:  x_exitos out of n_totales
X-squared = 0.13324, df = 1, p-value = 0.7151
alternative hypothesis: two.sided
95 percent confidence interval:
 -0.03280883  0.05381351
sample estimates:
   prop 1    prop 2 
0.9457965 0.9352941 
```


:::
:::



## Pregunta 7 (**1punto**)

Calcular la proporción de apartamentos de los periodos 2025-06-15 y  2025-09-21  con media de valoración `review_scores_rating` mayor que 4 en Palma  y en Pollença son iguales contra que son distintas.


::: {.cell}

```{.r .cell-code}
# -------------------------------------------------------------------
# PREGUNTA 7
# Comparación de proporciones (Rating > 4): Palma vs Pollença
# Periodos combinados: 2025-06-15 y 2025-09-21
# -------------------------------------------------------------------

# 1. Filtrar datos: Fechas y Municipios específicos
fechas_p7 <- as.Date(c("2025-06-15", "2025-09-21"))
municipios_p7 <- c("Palma de Mallorca", "Pollença")

datos_p7 <- listings0 %>%
  filter(date %in% fechas_p7,
         neighbourhood_cleansed %in% municipios_p7) %>%
  filter(!is.na(review_scores_rating)) # Eliminar NAs en la valoración

# 2. Calcular éxitos y totales por municipio
# Al agrupar por municipio, R sumará los casos de ambas fechas automáticamente
resumen_p7 <- datos_p7 %>%
  group_by(neighbourhood_cleansed) %>%
  summarise(
    exitos = sum(review_scores_rating > 4),
    total = n()
  )

# Ver tabla resumen
resumen_p7
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 2 × 3
  neighbourhood_cleansed exitos total
  <chr>                   <int> <int>
1 Palma de Mallorca         972   992
2 Pollença                 2478  2700
```


:::

```{.r .cell-code}
# 3. Preparar vectores para el test
# Nota: Verifica el orden alfabético en 'resumen_p7' para saber cuál es prop 1 y prop 2.
# Generalmente: 1 = Palma de Mallorca, 2 = Pollença
x_exitos_p7 <- resumen_p7$exitos
n_totales_p7 <- resumen_p7$total

# 4. Realizar el test de proporciones (Two-sample test for equality of proportions)
test_p7 <- prop.test(x = x_exitos_p7, n = n_totales_p7, alternative = "two.sided")

# Mostrar resultados
test_p7
```

::: {.cell-output .cell-output-stdout}

```

	2-sample test for equality of proportions with continuity correction

data:  x_exitos_p7 out of n_totales_p7
X-squared = 44.611, df = 1, p-value = 2.404e-11
alternative hypothesis: two.sided
95 percent confidence interval:
 0.04781209 0.07630977
sample estimates:
   prop 1    prop 2 
0.9798387 0.9177778 
```


:::
:::



## Pregunta 8 (**1punto**)

Agrupa las variables `review_scores_rating` y `review_scores_location` de `listings0` en 5 categorías cada una y construid una tabla de contingencia con las dos variables agrupadas. Agrupar de forma que no cruces de categorías vacías. Contratar si esta varibles son independientes con  un test $\chi^2$. 

Buscan información sobre el coeficiente de contingencia de Carl Pearson, cacularlo desde  la salida de chisq.test interpretarlo  en esta caso



::: {.cell}

```{.r .cell-code}
table(cut(listings0$review_scores_rating,5),
      cut(listings0$review_scores_location,5))
```

::: {.cell-output .cell-output-stdout}

```
             
              (0.996,1.8] (1.8,2.6] (2.6,3.4] (3.4,4.2] (4.2,5]
  (0.996,1.8]          17         8         6        10       2
  (1.8,2.6]             8        11        12        30      34
  (2.6,3.4]             8        20        59       125     168
  (3.4,4.2]             0         0       105      1217    2905
  (4.2,5]               0         9        93      2313   57567
```


:::
:::



::: {.cell}

```{.r .cell-code}
# -------------------------------------------------------------------
# PREGUNTA 8
# Test de Independencia Chi-cuadrado y Coeficiente de Contingencia
# Variables: review_scores_rating y review_scores_location
# -------------------------------------------------------------------

# 1. Preparar los datos (eliminar NAs)
datos_p8 <- listings0 %>%
  select(review_scores_rating, review_scores_location) %>%
  na.omit()

# 2. Agrupar en 5 categorías usando QUANTILES para evitar celdas vacías
#    La función quantile calcula los puntos de corte para tener grupos equilibrados (0%, 20%, 40%, 60%, 80%, 100%)
#    Nota: Si hay muchos valores repetidos (ej. muchos 5.0), 'cut' puede dar error por "breaks not unique".
#    En ese caso, ajustamos ligeramente o reducimos grupos, pero intentamos primero con quintiles.

# Calculamos los cortes
breaks_rating <- unique(quantile(datos_p8$review_scores_rating, probs = seq(0, 1, 0.2)))
breaks_location <- unique(quantile(datos_p8$review_scores_location, probs = seq(0, 1, 0.2)))

# Creamos los factores
# include.lowest = TRUE asegura que el valor mínimo entre en el primer intervalo
datos_p8$cat_rating <- cut(datos_p8$review_scores_rating, breaks = breaks_rating, include.lowest = TRUE)
datos_p8$cat_location <- cut(datos_p8$review_scores_location, breaks = breaks_location, include.lowest = TRUE)

# 3. Construir la Tabla de Contingencia
tabla_contingencia <- table(datos_p8$cat_rating, datos_p8$cat_location)

print("Tabla de Contingencia:")
```

::: {.cell-output .cell-output-stdout}

```
[1] "Tabla de Contingencia:"
```


:::

```{.r .cell-code}
print(tabla_contingencia)
```

::: {.cell-output .cell-output-stdout}

```
             
              [1,4.54] (4.54,4.73] (4.73,4.85] (4.85,4.98] (4.98,5]
  [1,4.54]        6088        2761        1595         771     1958
  (4.54,4.75]     3182        3931        3073        2312     1244
  (4.75,4.88]     1509        3293        3592        3652     1024
  (4.88,5]        2381        3267        4639        5905     8550
```


:::

```{.r .cell-code}
# 4. Realizar el Test Chi-cuadrado
test_chi <- chisq.test(tabla_contingencia)

print(test_chi)
```

::: {.cell-output .cell-output-stdout}

```

	Pearson's Chi-squared test

data:  tabla_contingencia
X-squared = 15019, df = 12, p-value < 2.2e-16
```


:::

```{.r .cell-code}
# 5. Calcular el Coeficiente de Contingencia de Pearson
#    Fórmula: C = raiz( Chi^2 / (N + Chi^2) )
chi_squared_stat <- test_chi$statistic
N <- sum(tabla_contingencia) # Tamaño total de la muestra

coef_contingencia <- sqrt(chi_squared_stat / (N + chi_squared_stat))

# Mostramos el resultado
cat("\nCoeficiente de Contingencia de Pearson (C):", round(coef_contingencia, 4), "\n")
```

::: {.cell-output .cell-output-stdout}

```

Coeficiente de Contingencia de Pearson (C): 0.434 
```


:::

```{.r .cell-code}
# (Opcional) Calcular el máximo posible para C para normalizarlo (C_max)
# C nunca llega a 1. Su máximo depende del número de filas/columnas (k). C_max = raiz((k-1)/k)
k <- min(dim(tabla_contingencia))
c_max <- sqrt((k - 1) / k)
cat("Coeficiente normalizado (C / C_max):", round(coef_contingencia / c_max, 4), "\n")
```

::: {.cell-output .cell-output-stdout}

```
Coeficiente normalizado (C / C_max): 0.5011 
```


:::
:::



## Pregunta 9 (**3 puntos**)

Construye un data set con las variables 
review_scores_rating, review_scores_cleanliness, review_scores_location, review_scores_value de listings0 y  el municipio/zona `neighbourhood_cleansed`

Calcula la matriz de correlaciones entre estas variables y haz un gráfico de pares  de variables que muestre las correlaciones ([ggpairs](https://r-charts.com/correlation/ggpairs/)) con la librería GGally. Comenta los resultados.

Haz un `matrixplot` de las correlaciones con la librería `corrplot`. Comenta los resultados.


::: {.cell}

```{.r .cell-code}
# -------------------------------------------------------------------
# PREGUNTA 9
# Matriz de Correlaciones, ggpairs y corrplot
# Variables: review_scores_rating, cleanliness, location, value
# Agrupación: neighbourhood_cleansed
# -------------------------------------------------------------------

library(dplyr)
library(ggplot2)
library(GGally)   # Para ggpairs
library(corrplot) # Para corrplot

# 1. Construir el dataset y limpiar NAs
# Seleccionamos las variables pedidas
variables_interes <- c("review_scores_rating", 
                       "review_scores_cleanliness", 
                       "review_scores_location", 
                       "review_scores_value",
                       "neighbourhood_cleansed")

datos_p9 <- listings0 %>%
  select(all_of(variables_interes)) %>%
  na.omit() # Eliminamos filas con NAs para poder calcular correlaciones

# 2. Matriz de correlaciones (Solo variables numéricas)
# Excluimos 'neighbourhood_cleansed' porque es categórica
datos_numericos <- datos_p9 %>%
  select(-neighbourhood_cleansed)

matriz_cor <- cor(datos_numericos, method = "pearson")

print("Matriz de Correlaciones:")
```

::: {.cell-output .cell-output-stdout}

```
[1] "Matriz de Correlaciones:"
```


:::

```{.r .cell-code}
print(round(matriz_cor, 3))
```

::: {.cell-output .cell-output-stdout}

```
                          review_scores_rating review_scores_cleanliness
review_scores_rating                     1.000                     0.711
review_scores_cleanliness                0.711                     1.000
review_scores_location                   0.464                     0.340
review_scores_value                      0.784                     0.647
                          review_scores_location review_scores_value
review_scores_rating                       0.464               0.784
review_scores_cleanliness                  0.340               0.647
review_scores_location                     1.000               0.480
review_scores_value                        0.480               1.000
```


:::

```{.r .cell-code}
# 3. Gráfico ggpairs
# Para que el gráfico sea legible, filtramos solo los municipios principales.
# Si intentamos graficar los 50 municipios, será ininteligible.
municipios_top <- c("Palma de Mallorca", "Alcúdia", "Calvià", "Pollença")

datos_p9_plot <- datos_p9 %>%
  filter(neighbourhood_cleansed %in% municipios_top)

# Generamos el gráfico
# - upper: muestra las correlaciones
# - lower: muestra nubes de puntos (points)
# - diag: muestra densidades
# - aes(color = ...): separa los colores por municipio
ggpairs(
  datos_p9_plot,
  columns = 1:4, # Las columnas numéricas
  aes(color = neighbourhood_cleansed, alpha = 0.5),
  title = "Correlograma: Relación entre puntuaciones por Municipio",
  upper = list(continuous = wrap("cor", size = 3)),
  lower = list(continuous = wrap("points", alpha = 0.3, size=0.5))
) + 
  theme_minimal()
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-16-1.png){width=672}
:::

```{.r .cell-code}
# 4. Matrixplot de correlaciones (corrplot)
# Este gráfico visualiza la matriz numérica calculada en el paso 2
corrplot(matriz_cor, 
         method = "ellipse", # Forma de las celdas (elipse, círculo, cuadrado...)
         type = "upper",     # Mostrar solo la triangular superior
         order = "hclust",   # Ordenar por similitud (clustering jerárquico)
         tl.col = "black",   # Color del texto de las etiquetas
         tl.srt = 45,        # Rotación de las etiquetas
         addCoef.col = "black", # Añadir coeficientes numéricos en negro
         diag = FALSE,       # No mostrar la diagonal principal (que es siempre 1)
         title = "Matriz de Correlación de Puntuaciones",
         mar = c(0,0,2,0)    # Ajustar márgenes para el título
)
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-16-2.png){width=672}
:::
:::



## Pregunta 10 (**2 puntos**)

La [Zipf's law es una ley empírica](https://en.wikipedia.org/wiki/Zipf%27s_law#Word_frequencies_in_natural_languages) que dice que la frecuencia de las palabras en un texto es inversamente proporcional a su rango. Decidid si la ley se ajusta a los datos de la longitud de los comentarios de los apartamentos de la muestra "2025-09-21" Mallorca, haced lo mismo para description de `listings0`. Para ello, haced un análisis de regresión lineal de la frecuencia de las longitudes de los comentarios/descripciones de los apartamentos de Mallorca y el rango de las longitudes de los comentarios. Justificad la respuesta, estadísticamente y gráficamente.

Como ayuda estudiar el siguiente código, utilizadlo y comentadlo.


::: {.cell}

```{.r .cell-code}
library(stringr)
# para las reseñas
head(reviews)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 6
  listing_id      id date       reviewer_id reviewer_name comments              
       <dbl>   <dbl> <date>           <dbl> <chr>         <chr>                 
1      69998  881474 2012-01-24     1595616 Jean-Pierre   "This place was charm…
2      69998 4007103 2013-04-02     3868130 Jo And Mike   "We had a four night …
3      69998 4170371 2013-04-15     5730759 Elizabeth     "Lor's apartment look…
4      69998 4408459 2013-05-03     5921885 Jone          "Wonderful place! 10/…
5      69998 4485779 2013-05-07      810469 Andrea        "My boyfriend and I, …
6      69998 4619699 2013-05-15     3318059 Devii         "We had a very last m…
```


:::

```{.r .cell-code}
length_rewiews=stringr::str_count(reviews$comments,"\\w+")
barplot(table(length_rewiews))
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-17-1.png){width=672}
:::

```{.r .cell-code}
#para las descripciones
length_description=stringr::str_count(listings0$description,"\\w+")
barplot(table(length_description))
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-17-2.png){width=672}
:::
:::


Y ahora se calculan los rango os lo dejo para reviews par desctption lo haceís vosotros


::: {.cell}

```{.r .cell-code}
aux=table(length_rewiews)
head(aux)
```

::: {.cell-output .cell-output-stdout}

```
length_rewiews
   0    1    2    3    4    5 
1418 3444 7045 4221 5165 5776 
```


:::

```{.r .cell-code}
head(names(aux))
```

::: {.cell-output .cell-output-stdout}

```
[1] "0" "1" "2" "3" "4" "5"
```


:::

```{.r .cell-code}
tbl=tibble( L=as.numeric(names(aux)),Freq=as.numeric(aux),
            Rank=rank(L),Log_Freq=log(Freq),Log_Rank=log(Rank))
str(tbl)
```

::: {.cell-output .cell-output-stdout}

```
tibble [592 × 5] (S3: tbl_df/tbl/data.frame)
 $ L       : num [1:592] 0 1 2 3 4 5 6 7 8 9 ...
 $ Freq    : num [1:592] 1418 3444 7045 4221 5165 ...
 $ Rank    : num [1:592] 1 2 3 4 5 6 7 8 9 10 ...
 $ Log_Freq: num [1:592] 7.26 8.14 8.86 8.35 8.55 ...
 $ Log_Rank: num [1:592] 0 0.693 1.099 1.386 1.609 ...
```


:::
:::



::: {.cell}

```{.r .cell-code}
tbl2=tbl %>% filter(Rank>10) %>% filter(Rank<1000)
sol1=lm(tbl2$Freq~tbl2$Rank)
summary(sol1)
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = tbl2$Freq ~ tbl2$Rank)

Residuals:
    Min      1Q  Median      3Q     Max 
-1031.8  -786.9  -201.4   452.6  4046.6 

Coefficients:
             Estimate Std. Error t value Pr(>|t|)    
(Intercept) 2141.9530    85.2279   25.13   <2e-16 ***
tbl2$Rank     -5.1260     0.2469  -20.76   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 1001 on 580 degrees of freedom
Multiple R-squared:  0.4263,	Adjusted R-squared:  0.4253 
F-statistic: 430.9 on 1 and 580 DF,  p-value: < 2.2e-16
```


:::

```{.r .cell-code}
sol2=lm(tbl2$Freq~tbl2$Log_Rank)
summary(sol2)
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = tbl2$Freq ~ tbl2$Log_Rank)

Residuals:
    Min      1Q  Median      3Q     Max 
-896.17 -547.03    7.73  443.85 1629.79 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept)    8267.68     162.94   50.74   <2e-16 ***
tbl2$Log_Rank -1405.73      29.51  -47.63   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 596.2 on 580 degrees of freedom
Multiple R-squared:  0.7964,	Adjusted R-squared:  0.796 
F-statistic:  2269 on 1 and 580 DF,  p-value: < 2.2e-16
```


:::

```{.r .cell-code}
sol3=lm(tbl2$Log_Freq~tbl2$Log_Rank)
summary(sol3)
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = tbl2$Log_Freq ~ tbl2$Log_Rank)

Residuals:
    Min      1Q  Median      3Q     Max 
-4.3152 -0.5008  0.0081  0.5191  1.6510 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept)   20.58666    0.24105   85.40   <2e-16 ***
tbl2$Log_Rank -3.15950    0.04366  -72.36   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.882 on 580 degrees of freedom
Multiple R-squared:  0.9003,	Adjusted R-squared:  0.9001 
F-statistic:  5237 on 1 and 580 DF,  p-value: < 2.2e-16
```


:::
:::



::: {.cell}

```{.r .cell-code}
# -------------------------------------------------------------------
# PREGUNTA 10
# Ley de Zipf: Análisis de frecuencias de longitudes de texto
# Variables: reviews$comments y listings0$description
# -------------------------------------------------------------------

library(dplyr)
library(stringr)
library(ggplot2)

# Función auxiliar para realizar el análisis de Zipf completo
analisis_zipf <- function(texto_columna, titulo_grafico) {
  
  # 1. Calcular longitud (número de palabras) de cada texto
  #    Usamos na.omit para evitar errores con textos vacíos
  longitudes <- str_count(na.omit(texto_columna), "\\w+")
  
  # 2. Crear tabla de frecuencias de esas longitudes
  tabla_freq <- table(longitudes)
  
  # 3. Construir el Data Frame (Tibble) con los Rangos
  #    Convertimos la tabla en un dataframe numérico
  df_zipf <- tibble(
    Longitud = as.numeric(names(tabla_freq)), # La longitud (eje X original)
    Freq     = as.numeric(tabla_freq)         # Cuántas veces aparece esa longitud
  ) %>%
    arrange(desc(Freq)) %>%             # Ordenamos de mayor a menor frecuencia
    mutate(
      Rank = row_number(),              # Asignamos el Rango (1 = el más frecuente)
      Log_Freq = log(Freq),             # Logaritmo de la frecuencia
      Log_Rank = log(Rank)              # Logaritmo del rango
    )
  
  # 4. Filtrar datos (Heurística sugerida en el enunciado)
  #    Se eliminan los rangos muy bajos (top 10) y muy altos (>1000) 
  #    para evitar el ruido en las colas de la distribución.
  df_filtrado <- df_zipf %>%
    filter(Rank > 10 & Rank < 1000)
  
  # 5. Ajuste del Modelo Lineal (Regresión Log-Log)
  modelo <- lm(Log_Freq ~ Log_Rank, data = df_filtrado)
  
  # 6. Gráfico Log-Log con la recta de regresión
  plot <- ggplot(df_filtrado, aes(x = Log_Rank, y = Log_Freq)) +
    geom_point(alpha = 0.4, color = "darkblue") +
    geom_smooth(method = "lm", color = "red", se = FALSE, linewidth = 1) +
    labs(
      title = paste("Ley de Zipf:", titulo_grafico),
      subtitle = paste("Pendiente (alpha):", round(coef(modelo)[2], 4), 
                       "| R-squared:", round(summary(modelo)$r.squared, 4)),
      x = "Log(Rango)",
      y = "Log(Frecuencia)"
    ) +
    theme_minimal()
  
  return(list(modelo = modelo, grafico = plot))
}

# --- EJECUCIÓN DEL ANÁLISIS ---

# 1. Análisis para COMENTARIOS (Reviews)
# Asumimos que el objeto 'reviews' está cargado como se indica en el enunciado
resultados_reviews <- analisis_zipf(reviews$comments, "Longitud de Reviews")

# 2. Análisis para DESCRIPCIONES (Listings)
resultados_descriptions <- analisis_zipf(listings0$description, "Longitud de Descripciones")

# --- MOSTRAR RESULTADOS ---

# Gráficos
print(resultados_reviews$grafico)
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-20-1.png){width=672}
:::

```{.r .cell-code}
print(resultados_descriptions$grafico)
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-20-2.png){width=672}
:::

```{.r .cell-code}
# Resúmenes estadísticos de los modelos
cat("\n--- Resumen Modelo REVIEWS ---\n")
```

::: {.cell-output .cell-output-stdout}

```

--- Resumen Modelo REVIEWS ---
```


:::

```{.r .cell-code}
print(summary(resultados_reviews$modelo))
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = Log_Freq ~ Log_Rank, data = df_filtrado)

Residuals:
    Min      1Q  Median      3Q     Max 
-4.3768 -0.4073 -0.0811  0.3706  1.5582 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) 20.63954    0.22680   91.00   <2e-16 ***
Log_Rank    -3.16999    0.04108  -77.17   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.8299 on 580 degrees of freedom
Multiple R-squared:  0.9112,	Adjusted R-squared:  0.9111 
F-statistic:  5955 on 1 and 580 DF,  p-value: < 2.2e-16
```


:::

```{.r .cell-code}
cat("\n--- Resumen Modelo DESCRIPTIONS ---\n")
```

::: {.cell-output .cell-output-stdout}

```

--- Resumen Modelo DESCRIPTIONS ---
```


:::

```{.r .cell-code}
print(summary(resultados_descriptions$modelo))
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = Log_Freq ~ Log_Rank, data = df_filtrado)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.6301 -0.9624 -0.1638  1.0706  1.7943 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)  15.6074     0.6660   23.43   <2e-16 ***
Log_Rank     -2.6186     0.1577  -16.60   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 1.175 on 131 degrees of freedom
Multiple R-squared:  0.6778,	Adjusted R-squared:  0.6754 
F-statistic: 275.6 on 1 and 131 DF,  p-value: < 2.2e-16
```


:::
:::




