# Actividad Colaborativa Módulo 3

A continuación se describen las medidas aplicadas para la limpieza de un data set compuesto por 5000 películas procedente de la web http://www.imdb.com/

dataset source: https://raw.githubusercontent.com/sundeepblue/movie_rating_prediction/master/movie_metadata.csv

## Obtenemos directorio de trabajo
getwd()

## Creamos nuevo diretorio de trabajo
if(!file.exists("C:/Users/Antonio/Documents/Master-BigData/ActCol3-scripts//"))
{dir.create("C:/Users/Antonio/Documents/Master-BigData/ActCol3-scripts")}

## Establecemos nuevo directorio de trabajo
setwd("C:/Users/Antonio/Documents/Master-BigData/ActCol3-scripts")

## Limpieza directorio de trabajo
rm(list=ls())

## Creación de carpetas para los datos
if(!file.exists("..//ActCol3-messydataset")){dir.create("..//ActCol3-messydataset")}
if(!file.exists("..//ActCol3-tidydataset")){dir.create("..//ActCol3-tidydataset")}

## Descarga dataset con 5,000 películas (movies) de IMDB en messydataset y almacenado de la fecha de descarga
fileurl <-"https://raw.githubusercontent.com/sundeepblue/movie_rating_prediction/master/movie_metadata.csv"
download.file(fileurl,destfile="..//ActCol3-messydataset//movies.csv")
fechaDescarga <- date()

## Carga de messydataset movies y primera visualización de los datos
messymovies <-read.csv("..//ActCol3-messydataset/movies.csv", header=TRUE, stringsAsFactors = FALSE)
library(knitr)
head(messymovies)

### De la primera visuación se observa que tenemos multitud de caracteres especiales, un número elevado de columnas y numerosas observaiones con el valor ¨NA¨. Consideramos eliminar todas las filas que contengan algun valor ¨NA¨ y al ser un data set bastante pequeño se convertiran los caracteres especiales y eliminaran espacios en la nueva lectura del fichero correspondiente en este caso a tidydataset. Además se eliminaran columnas consideradas no relevantes.

## Carga de tidydataset movies eliminando caracteres especiales y espacios a ambos lados
tidymovies <- read.csv("..//ActCol3-messydataset/movies.csv", header=TRUE, 
                       stringsAsFactors = FALSE, encoding = "UTF-8", strip.white=TRUE)

## Convertimos valores de columnas gross y budget a enteros
tidymovies$budget <- as.integer(tidymovies$budget )
tidymovies$gross <- as.integer(tidymovies$gross )

## Omisión de todas las filas que contengan algún valor NA
tidymovies <- na.omit(tidymovies)

## Eliminamos columnas no relevantes 
tidymovies <- tidymovies[,!grepl("face", colnames(tidymovies))]
tidymovies <- tidymovies[,!grepl("num", colnames(tidymovies))]
tidymovies <- tidymovies[,!grepl("plot", colnames(tidymovies))]
tidymovies <- tidymovies[,!grepl("link", colnames(tidymovies))]
tidymovies <- tidymovies[,!grepl("actor_2", colnames(tidymovies))]
tidymovies <- tidymovies[,!grepl("actor_3", colnames(tidymovies))]

## Renombramos columnas
new_names <- c("color","director", "duration", "gross", "genres", "protagonist",
               "title", "language", "country", "content","budget", "release", "score", "ratio")
names(tidymovies) <- new_names

## Ordernamos columnas
tidymovies<- tidymovies[,c("color","duration","release","title","director",
                           "protagonist","country","language","genres","content","budget","gross",
                           "score","ratio")]

## Ordenamos valores por score and rario descendente
tidymovies <- tidymovies[order(tidymovies$score,tidymovies$ratio, decreasing = TRUE),]

## Ecritura del fichero limpiado en la carpeta de datos limpios
write.csv2(tidymovies,file = "../ActCol3-tidydataset/tidymovies.csv", row.names=FALSE)
