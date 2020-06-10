---
output:
  html_document:
    theme: united
---

<head>
<font color="#800000" size="+3">OPERATING PROCEDURE R: SUNBURST PLOT</font>
</head>

<style>
body {
text-align: justify;
word-wrap: break-word}
</style>


#  {.tabset .tabset-fade}

## <font color="#cc0000">Introduction</font>


```{r include=FALSE}
library(ggplot2)
library(plotly)
library(htmltools)
```

```{r include = FALSE}

colorset <- c("NA" = '#660033',
                   "Blue" = 'blue',
                   "Pink" = 'pink',
                   "Yellow" = 'yellow',
                   "Green" = 'green',
                   "Red" = 'red',
                   "Grey" = 'grey',
                   "Black" = 'black',
                   "Orange" = 'orange',
                   "Purple" = 'purple',
                   "White" = 'white',
                   "Dark Brown" = '#331a00',
                   "Light Brown" = '#996633',
                   "Turquoise" = '#66ffcc',
                   "Lilac" = '#cc99ff',
                   "Light Pink" = '#ffccdd' )

Sunburst <- plot_ly(
  labels = c("NA", "Blue", "Pink", "Yellow", "Green", "Red", "Grey", "Black", "Orange", "Purple", "White", "Dark Brown", "Light Brown", "Turquoise", "Lilac", "Light Pink"),
  parents = c("", "", "Blue", "Blue", "Yellow", "Yellow", "Blue", "Blue", "Black", "Blue", "Grey", "Purple", "Purple", "Pink", "Pink", "Pink"),
  values = c(23 ,65, 37, 12, 10, 2, 6, 6, 6, 4, 6, 1,3, 23, 9, 5),
  type = 'sunburst',
  branchvalues = 'total',
  marker = list(colors = colorset)
)
```

```{r}
Sunburst #This is an exemple of a sunburst plot 
```
Parasite of ruminants cause diseases of major socio-economic importance worldwide.The Gastrointestinal Nematodes (GIN), are currently the most important disease problem of sheep worldwide causing reductionin meat, milk, and fibre production. GIN are rapidly becoming more and more resistant to all the different anthelmintic drug treatments, which is why analysing parasitic GIN community structure is essential for diagnostics, surveillance and research. 

This is why the Nemabiome deep amplicon sequencing targeting ITS-2 (Internal Transcribed Spacer 2) region of nematode nuclear ribosomal DNA has been developed. This next generation sequencing provides a picture of the species composition of the GIN parasite community structure in small and large sample sets. The Nemabiome sequencing pipeline gives us the results condensed into a twelves sheet excel file which indicates IdTaxa (60 and 30) results summed up by species or by ASV. 

IdTaxa is a taxonomic classification tool to classify rRNA or ITS sequences. ASV or Amplicon sequence variant refers to individual DNA sequences recovered using a high-throughput marker gene analysis when false sequences are removed after sequencing. The excel file also contains the results obtained from BLAST. BLAST or Basic Local Alignment Search Tool is a program which finds regions of local similarity between sequences by comparing either nucleotide or protein sequences to sequence databases in order to calculate statistical significance. Unfortunately, the results obtained in those different excel sheet are not simple to read and to analyse and the most compelling information’s might be lost without proper data visualization. Using the sequencing data output, it would be great to be able to understand the different classification algorithms used BLAST and IdTaxa and highlight various piece of information’s emerging from these classifications. This walkthrough will be using interactive plots as an additional way to highlight any classification discrepancies which are hard to visualize by only looking at the excel spreadsheets.

**Sunburst plot** (exemple above) is a tool to enable visualisation of hierarchal data spanning outward radially from root to leaves. This tutorial will use this tool to visualize the **Taxonomic classification** of the gastrointestinal nematodes found in our samples, using the Nemabiome sequencing data.

**The Walkthrough is using as an example a dataset of 10 deer samples**

## <font color="#cc0000">Walkthrough</font> {.tabset .tabset-fade}

### <font color="#ff6666">Files Needed</font>

Ensure you know where all the following files are located on your computer (file directories):

You will need the **FinalSummary.xlsx** excel file obtained after running the Nemabiome pipeline. The **"IdTaxa60Classification"** tab will be specifically used. 

***

### <font color="#ff6666">Packages</font>


The first step is to install the following packages. These packages are available in the **Comprehensive R Archive Network (CRAN)** repository and can be installed via install.packages(). By default, the latest version available will be installed.

  a. dplyr package: Commonly use to modify data frame like objects, both in memory and out of memory by providing set of verbs such as select(), summarize() or group_by().

  b. ggplot2 package: Creates graphics using “The Grammar of Graphics”. This package takes into consideration the variables mapped to its aesthetics function as well as the graphical function chosen and creates a graph.

  c. openxlsx package: Provides an easier way to interact with Excel.xlsx files. Allows for the creation, styling and editing of excel spreadsheets

  d. plotly package: Turns the graphics designed using ggplot2 into interactive web graphics.

  e. reshape2 package: Restructure and aggregate data with function such as melt()

  f. tidyr package: Tidy data sets using functions such as gather()

  g. stringr package: Treats with common string operations. This package contains a cohesive set of functions designed to work with strings in a simple manner

```{r, attr.source='.numberLines'}
##remove the # to install, leave the # if already installed 

#install.packages("dplyr")
#install.packages("ggplot2")
#install.packages("openxlsx")
#install.packages("plotly")
#install.packages("reshape2")
#install.packages("tidyr")
#install.packages("stringr")
```

With the packages now installed, you can load them into R (or Rstudio) via the `library()` function. 

```{r include=TRUE, attr.source='.numberLines'}
## Load packages into R (Remove # to load)

#library(openxlsx)
#library(reshape2)
#library(ggplot2)
#library(plotly)
#library(dplyr)
#library(tidyr)
#library(stringr)
```

```{r include=FALSE}
## Load packages into R

library(openxlsx)
library(reshape2)
library(ggplot2)
library(plotly)
library(dplyr)
library(tidyr)
library(stringr)
```

***

### <font color="#ff6666">Setting Path to Samples</font>

Here, you are telling R where your samples are located. This **directory will need to be changed** depending on where your samples are on the server/locally. The **Variables to be Changed tab** contains all directory changes in a more concise manner.


**Setting Path to Samples**:  R needs to know where your samples are

```{r}
My_Data <- read.xlsx("/Users/eleonorecharrier/Dropbox (Gilleard Lab)/Eleonores work/Coding_projects/Eleonore/Summary.xlsx", sheet = "IdTaxa60Classification")  
``` 
***

### <font color="#ff6666">Colors</font>

This step allows to format species color into an object which can be reuse later on for graph aesthetic purposes. 

https://www.rapidtables.com/web/color/RGB_Color.html 

This link will provide easy hexadecimal colors code. You can pick your color and format species color. 
If you need to add new species, you can add another line as such: "genus_species" = "#hexadecimalcode"

```{r}
## This first colour object will be later on used in the aesthetic for the stackbar plot

manualcolorSb <- c("NA" = 'black',
                    "Strongylida"= '#800000',
                    "Haemonchidae" = '#b30000',
                    "Chabertiidae" = '#ff6600',
                    "Molineidae" = '#ffcc00',
                    "Spiculopteragia" = '#ff1a1a',
                    "Ostertagia" = '#ff4d4d',
                    "Haemonchus" = '#ff8080',
                    "Oesophagostomum" = '#ff944d',
                    "Nematodirus" = '#ffe680',
                    "Spiculopteragia_boehmi" = '#ff8566', 
                    "unclassified_Spiculopteragia" = '#ff3333',
                    "unclassified_Ostertagia" = '#ffcccc',
                    "Haemonchus_placei" = '#ffe6e6',
                    "unclassified_Haemonchus" = '#ffccff',
                    "Oesophagostomum_venulosum" = '#ffc299',
                    "Nematodirus_belvetianus" = '#fff5cc') 
```
***

### <font color="#ff6666">Functions</font>

This step will allow the creation of custom functions which will be later of use for the construction of sunburst plot. These custom functions are created as a way to simplify the code as well as a way to make it more understandable. Those custom functions will also allow the user to have a code frame where only their data input will be changed. This will make the code more stable and reliable. 

The first custom function will render possible the gathering of column of interest from the mother file of choice. This function will allow the user to replace both of those by: attributing the column of interest to "To_Replace" and the mother dataframe of choice to "Data_Of_Choice". This function use the `gather()` function to collect a set of wanted column names and places them into a single “key” column. This function is used twice. A first time, to gather the column samples of choice into a single column called "Taxonomy_Value", and a second time to gather the number of reads per "Taxonomy_Value". The `group_by()` function is then used to group our two columns by the "Taxonomy_Value" column. Finally, the `summarise()` on the grouped data previously created by the `group_by()` function. This function applied to the number of reads per species will create one row for each "Taxonomy_Value". If ordered into a new object, this function will create a new data frame containing only the number of reads per taxonomy_value (species). na.rm=TRUE removes the NA values in the dataset.

```{r, attr.source='.numberLines'}
Function_Gather <-function(To_Replace, Data_Of_Choice) {
 To_Replace <- enquo(To_Replace)
   Data_Of_Choice %>% 
  gather(key = "Sample", value = "Read", !!To_Replace) %>%
    gather(key = "taxonomy", value = "Taxonomy_Value", order:species, na.rm=TRUE) %>%
      group_by(Taxonomy_Value) %>%
        summarise(Total = sum(Read))
}
```

The second custom function will arrange a data frame to be assigned to "Name_Arrange" in a custom order. The `arrange()` and `match()` functions are used in this custom function to reorder the rows by one or more variable from the wanted column "Column_Of_Interest" which will be attributed to the object "Name_Taxonomy_Replace". The custom order of the rows is the order in which your classification is in the "Labels" column of the "Sunburst Table" tab of the walkthrough. 

```{r, attr.source='.numberLines'}
Function_Arrange <- function(Name_Arrange, Column_Of_Interest ,Name_Taxonomy_Replace){
Name_Arrange %>% 
  arrange(match(Column_Of_Interest, c(Name_Taxonomy_Replace)))
} 
```

The third custom function has for aim to create a data frame which can be use to create a Sunburt plot. In order to do so, the `data.frame()` function is used to create a dataframe with the different parameters needed for the construction of Sunburst plot. 

The sunburst sectors are determined by the entries in "ids", "labels" and "parents".
  - ids: This will assign id labels to each datum. These ids will be used as an object constancy of the different data points during the sunburst animation. They should only be an array of strings. 
  - labels: This will enable us to set the labels for each of the different sectors. See "Labels" column of the "Sunburst Table" tab of the walkthrough. 
  - parents: This will set the parent sectors for each of the different created sectors. The use of empty string items '' will be understood as a way to reference the root node in the hierarchy. In this case, as the "ids" are filled, the "parents" items are understood to also be "ids" themselves. See "Parents" column of the "Sunburst Table" tab of the walkthrough. 
  - size: This will attribute the size of each of the different sectors. 

```{r, attr.source='.numberLines'}
Function_DataFrame_Sb <- function(Data_Arrange_Taxonomy, Label_Order, Parent_Order, Data_Arrange_Total){
data.frame(
    ids = c(Data_Arrange_Taxonomy) ,
  labels = c(Label_Order),
  parents = c(Parent_Order),
  size = Data_Arrange_Total,
   stringsAsFactors = FALSE) 
}
```

The fourth and final custom function will be used to create and allow the Sunburst plot to be interactive. 
In order to do so, the `plot_ly()` function is used. The hovertext is attributed to the selected data to appear when hovering the different sectors of the sunburst plot. 

```{r, attr.source='.numberLines'}
#This function will allow for the creation of the Sunburst Plot
Function_Sb <- function(Sunburst_Dataset, Dataset_Arrange_Total){
plot_ly(Sunburst_Dataset, 
        ids = ~ids, 
        labels = ~labels, 
        parents = ~parents, 
        type = 'sunburst',
        hovertext = ~Dataset_Arrange_Total,
        marker = list(colors = manualcolorSb))
}
```
***

### <font color="#ff6666">Sunburst Table</font>

This table is an example of how to create the table to understand the order of the sunburst more easily. This table is based on the 10 deer samples example. This table will help when allocating the parents and labels into the sunburst plot. **This table will vary with each set of data**. 

<p>
<center>
![Step 1](/Users/eleonorecharrier/Dropbox (Gilleard Lab)/Eleonores work/Coding_projects/Eleonore/S1.png)
</center>
</p>


<p>
<center>
![Step 2](/Users/eleonorecharrier/Dropbox (Gilleard Lab)/Eleonores work/Coding_projects/Eleonore/S2.png)
</center>
</p>


<p>
<center>
**Final Table:**
</center>
</p>

<p>
<center>
![Sunburst Classification Table.](/Users/eleonorecharrier/Dropbox (Gilleard Lab)/Eleonores work/Coding_projects/Eleonore/Sunburst_Table.png)
</center>
</p>

***
### <font color="#ff6666">Sunburst Plot</font>
Creating this sunburst will allow for a better understanding of the taxonomic relationship between the different gastrointestinal species population found within the  samples.

First, the `read.xlsx()` function is used to read specifically the IdTaxa60Classification sheet in the sequencing excel spreadsheet results. The IdTaxa 60 classification results is used as it represents a confidence threshold of 60% which is higher than the IdTaxa 30 classification results, thus more robust.Its structure is looked at to understand and look if changes need to be made. 

```{r, attr.source='.numberLines'}
My_Data <- read.xlsx("/Users/eleonorecharrier/Dropbox (Gilleard Lab)/Eleonores work/Coding_projects/Eleonore/Summary.xlsx", sheet = "IdTaxa60Classification")  #!!The first object of this function needs to be changed to wherever the `FinalSummary.xlxs` file is in your working directory
```

It is then needed to define the NA in the species column as a string character "Unknown" in the Taxonomy_Value column. This will be needed for the next step of rearranging the data is a custom order.

```{r, attr.source='.numberLines'}
My_Data[7,9]="Unknown"
My_Data[21,9]="Unknown"
```

After looking at the structure of our data frame "My_Data", a new data frame, containing only the column which will be of our interest in the creation of the Sunburst plot, is created.

The custom function "Function_Gather" create above is used. This allows to only select the column `193_S193_L001_R1_001.fastq.gz`to the column`273_S273_L001_R1_001.fastq.gz` (includes all the fasta.gz column of the FinalSummary.xlxs file) and consider them as 'Read' values. 
This function gives a new data frame "New_Data" grouped by taxonomy_value with the number of reads for each of these values.

```{r, attr.source='.numberLines'}
New_Data <- Function_Gather(`193_S193_L001_R1_001.fastq.gz`:`273_S273_L001_R1_001.fastq.gz`, My_Data)
str(New_Data) #Check if the the colunm of interest were corectly selected 
```

This is the last step in rearranging our new data frame. Our data is now organized in two column, one for the taxonomy classification and one for the number of reads allocated to each level of the taxonomy classification. The last step is to arrange the data in the custom order needed for the organisation of the sunburst plot. To do so, the custom function "Function_Arrange" create above is used. 

```{r, attr.source='.numberLines'}
New_Data_Arrange <- Function_Arrange(New_Data, New_Data$Taxonomy_Value ,c("Unknown", "Strongylida", "Haemonchidae", "Chabertiidae", "Molineidae", "Spiculopteragia", "Ostertagia", "Haemonchus", "Oesophagostomum", "Nematodirus", "Spiculopteragia_boehmi", "unclassified_Spiculopteragia", "unclassified_Ostertagia", "Haemonchus_placei", "unclassified_Haemonchus", "Oesophagostomum_venulosum", "Nematodirus_belvetianus"))
```

Using the custom function "Function_DataFrame_Sb" created above, the ids, labels, parents and size are assigned to the sunburst plot. 
 
```{r, attr.source='.numberLines'}
DF_Sb1 <- Function_DataFrame_Sb(c(New_Data_Arrange$Taxonomy_Value),
          c("Unknown", "Strongylida", "Haemonchidae",  "Chabertiidae", "Molineidae", "Spiculopteragia", "Ostertagia", "Haemonchus", "Oesophagostomum", "Nematodirus", "Spiculopteragia<br>boehmi", "unclassified<br>Spiculopteragia", "unclassified<br>Ostertagia", "Haemonchus<br>placei", "unclassified<br>Haemonchus", "Oesophagostomum<br>venulosum", "Nematodirus<br>belvetianus"),
          c("","", "Strongylida","Strongylida","Strongylida", "Haemonchidae", "Haemonchidae", "Haemonchidae","Chabertiidae", "Molineidae", "Spiculopteragia", "Spiculopteragia", "Ostertagia", "Haemonchus", "Haemonchus","Oesophagostomum", "Nematodirus"),
          New_Data_Arrange$Total)
```

Finally, using the custom function "Function_Sb" created above, the sunburst plot of the **"NUMBERS OF READS PER LEVEL OF TAXONOMY WITHIN YOUR SAMPLE"** will be created. It is an interactive plot, thus if you over each section, it will inform you on the number of reads for each taxa. In addition, by clicking on a specific taxa you can zoom the sunburst on that specific taxa.

```{r, attr.source='.numberLines'}
Sb1 <- Function_Sb(DF_Sb1,New_Data_Arrange$Total)
Sb1 ##To visualize our sunburst plot 
```

***

## <font color="#cc0000">Variables To Be Changed</font>

!The **bold** part are to be changed in your code (TO CHANGE)!

***
***

* My_Data: You will need to change the directory to where your **FinalSummary.xlsx** excel file obtained after running the Nemabiome pipeline

```  {}
  My_Data <- read.xlsx(TO CHANGE, sheet = "IdTaxa60Classification") 
```

***
***

* You will need to change the **row number** to the number where the **NA row** is in the species column of your data. You can copy paste the copy to add more if you have more than 2 rows of unknown data. 

```{}
  My_Data[TO CHANGE,9]="Unknown"
  My_Data[TO CHANGE,9]="Unknown"
```

***
***

* New_Data: You will need to change the first function variable with your **fasta.gz column of interest**. 

```{}
  New_Data <- Function_Gather(TO CHANGE, My_Data)
```

***
***

* New_Data_Arrange: You will need to change the **last function variable** with the Classification name in the taxomic order of choice as seen in the **Labels "Sunburst table" tab** of the walkthrough. 

```{}
  New_Data_Arrange <- Function_Arrange(New_Data, New_Data$Taxonomy_Value ,c(TO CHANGE LABEL)
```

***
***

* DF_Sb1: You will have to change the **second and third function variable** with the correct 1. label and 2. parent order according to the classifiction chosen (should match the label and parent order from your **"Sunburst table"**)

```{}
DF_Sb1 <- Function_DataFrame_Sb(c(New_Data_Arrange&Taxonomy_Value),
          c(TO CHANGE LABEL),
          c(TO CHANGE PARENT),
          New_Data_Arrange$Total)
```

***
***

## <font color="#cc0000">Example</font>







***

## <font color="#cc0000">Links</font>

**LINK TO THE DROPBOX FOLDER CONTAINING ALL RELEVANT FILES** 

This link will bring you to the dropbox folder containing all files mentioned within the walkthrough. This includes R files, relevant excel files, etc:

https://www.dropbox.com/sh/d19n2j1h27xvlqn/AACl14bR-wm5MPtU5XQ54luta?dl=0




**NEMABIOME PIPELINE**

. Nemabiome Pipeline Walkthrough: https://jilldr.github.io/Dada2PipelineOperatingProcedure/#walkthrough

. Nemabiome Files: https://www.dropbox.com/sh/gks063m19qud9lx/AAC8w5bWWro22C8GAZ9MNZfTa?dl=0 




**GENERAL DOCUMENTATION** 

. https://cran.r-project.org 

. https://www.rdocumentation.org

. Plotly: https://www.rdocumentation.org/packages/plotly/versions/4.9.2/topics/plot_ly 

. Sunburst: https://plotly.com/r/sunburst-charts/

