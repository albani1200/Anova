![image](https://github.com/user-attachments/assets/90f634f5-04e6-49c5-87b3-23bd756c7f58)


# Anova
## Gráfica anova en r desde, tukey hasta combinadas.

# Prueba de post-hoc de tukey 

*Primero se deben instalar los paquetes en r* 


**install.packages("tidyverse")

install.packages("ggplot2")**

Después cargar los paquetes **library(tidyverse)**    y   *library(ggplot2)*  

## Importar y transformar los datos 

**Cargar el archivo cvs (importandolo)

datos <- read.csv("ruta/al/archivo/data.csv") reemplaza la ruta del archivo con la tuya, donde contenga tu archivo. Clic derecho y propiedades y copias tu ruta y la reemplazas.

### Convertir los datos a un formato largo con este código 

datos_largos <- datos %>%
  pivot_longer(cols = starts_with("rep"), names_to = "replicado", values_to = "valor")

  **Se usa %>% pipe**  hay páginas que explican que es y para que funciona.

## *Paso 3, realizar ANOVA (análisis de varianza)**

Se convierte *Bacteria* en un factor 

datos_largos$Bacteria <- as.factor(datos_largos$Bacteria)

Después se realiza la Anova 

anova_result <- aov(valor ~ Bacteria, data = datos_largos)
summary(anova_result)

**aov** es el comando para anova, y summary es para mostrar el resultado.

## Paso 4 Prueba de Tukey (si el Anova muestra diferencias muy espaciadas o significativas, se puede hacer la prueba de Tukey para comparar los grupos)

tukey_result <- TukeyHSD(anova_result)
print(tukey_result)


## *Paso 5, realizar las gráficas del ANOVA Y TUKEY*

Gráfico del anova usando Boxplot 

ggplot(datos_largos, aes(x = Bacteria, y = valor)) +
  geom_boxplot() +
  labs(title = "Análisis de Varianza (ANOVA) por Grupo de Bacterias", 
       x = "Grupo de Bacterias", y = "Valores de Replicados") +
  theme_minimal()

  **Realizando el gráfico de Tukey para mostrar solamente los intervalos de confianza de Tukey**

  plot(tukey_result, las = 1)

  ![diferentes niveles de bacterias](https://github.com/user-attachments/assets/25bc35b6-5e5c-450c-b24c-9aa8631bc64f)



# Para crear la gráfica de ANOVA con ggplot2 y ggpubr

### Primero instalamos los paquetes y después cargamos las librerias

install.packages("ggplot2")

install.packages("ggpubr")

library(ggplot2)

library(ggpubr)

### Hacemos lo mismo que anteriormente, cargamos o importamos nuestro archivo y copiamos la ruta donde se encuentra y la reemplazamos. 

datos <- read.csv("ruta/a/tu/archivo/datacsv.csv")



**Yo le asigne datos, pero puede ser cualquier nombre y después leer cvs ya que es un archivo excel (aunque se podría instalar otro paquete xls para excel, pero es decisión de simplicidad)**

### Volvemos a transformar los datos a tamaño largo (no es la única manera) para que las columnas rep1, rep2 y rep3 se conviertan solamente a una columna de valores
y le asginé (Valor a valores y le coloque el nombre de **replicado** a la otra creada)

library(tidyr)
datos_largos <- datos %>%
  pivot_longer(cols = starts_with("rep"), names_to = "replicado", values_to = "valor")

**Cabe mencionar que se debe cargar la libreria tidyr para que funcione pipe o %>%** 

### Para esta segunda gráfica se crea una ANOVA usando boxplot donde las muestras aparacen en el eje *x* mientras que las distribuciones de los valores que rep1, rep2 y rep3 aparecen en el eje *y* con el nombre de valor de replicados, el cual se cambio en un paso anterior cuando se transformo a una sola columna. Para evitar confusión y saber de dónde y porque esa asignación.


ggboxplot(datos_largos, x = "muestras", y = "valor", color = "muestras", add = "jitter") +
  stat_compare_means(aes(label = paste0("p = ", ..p.format..)), 
                     method = "anova", label.y = max(datos_largos$valor)) +
  labs(title = "Prueba ANOVA para diferentes muestras",
       x = "Muestras",
       y = "Valor de replicados") +
  theme_minimal()


![anova diferentes muestras](https://github.com/user-attachments/assets/0be31eac-8c46-432a-9b2e-070ec277a004)




  # Crear una visualización de pruebas ANOVAS junto con una prueba POST-HOC en la misma gráfica. Usando el paquete ggstatsplot

### Lo principal siempre es instalar o cragar los paquetes necesarios

install.packages("ggstatsplot")

library(ggstatsplot)


### Volvemos a importar o cargar los datos y transformarlos.

datos <- read.csv("ruta/a/tu/archivo.csv")  

**Nuevamente debes de cambiar la ruta por la ruta donde se encuentra tu archivo**


  **Aquí volví a reutilizar para fines más sencillos la transfoemación y asignación de la gráfica pasada**

  library(tidyr)
datos_largos <- datos %>%
  pivot_longer(cols = starts_with("rep"), names_to = "replicado", values_to = "valor")

  ### Crear la visualización ANOVA con prueba POST-HOC usando el paquete de ggstatsplot 

  **Con ggstatsplot::ggbetweenstats** se puede realizar el ANOVA además que es muy eficaz ya que se agregan automáticamente comparaciones POST-HOC y se ahorran mucho código**


ggstatsplot::ggbetweenstats(
  data = datos_largos,
  x = muestras,
  y = valor,
  type = "parametric",        
  pairwise.comparisons = TRUE, 
  pairwise.display = "significant", 
  var.equal = TRUE,            
  title = "Prueba ANOVA y Comparaciones Post-hoc entre Muestras",
  xlab = "Muestras",
  ylab = "Valores de Replicados",
  messages = FALSE             
)

**type= ''parametric'' realiza una ANOVA, es algo así como un testeo Xd.**

**pairwise.comparisons = TRUE funciona como un activador de las pruebas post-hoc o más bien las activa, que al final solo son una comparación entre pares**

**pairwise.display = 'significant' es una asignación y variable que solo va a indicar que se muestren las comparaciones post-hoc que son significativas en la grafica. Se puede interpretar viendo la gráfica**

**var.equal =TRUE pues este comando es sencillo, varianza igual es verdad o cierta, pero asume una igualdad de varianzas entre grupos ANOVA**

**title, xlab, ylab es básico, se usan para asignar títulos y etiquetas a la gráfica, lo que se puede intuir al leer el último código xlab= "muestras", ahí se uso como una etiqueta y como titulo para muestras (que podría ser cualquier nombre, dependiendo de loq que se este analizando**

![prueba anova y comparaciones post-hoc entre muestras](https://github.com/user-attachments/assets/cc2d52ef-3cca-4fa6-9845-1c001748ce3b)


#Referencia
https://statsandr.com/blog/anova-in-r/


![image](https://github.com/user-attachments/assets/8ffe104c-b0b5-4b1c-9045-0612265e4eb5)



