# Data manipulation in R

Examples of dplyr functions in R 

First download the data and make sure you have dplyr installed


```r
#install.packages("dplyr")
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(tidyverse)
```

```
## Loading tidyverse: ggplot2
## Loading tidyverse: tibble
## Loading tidyverse: tidyr
## Loading tidyverse: readr
## Loading tidyverse: purrr
```

```
## Conflicts with tidy packages ----------------------------------------------
```

```
## filter(): dplyr, stats
## lag():    dplyr, stats
```

```r
library(downloader)
url <- "https://raw.githubusercontent.com/genomicsclass/dagdata/master/inst/extdata/msleep_ggplot2.csv"
filename <- "msleep_ggplot2.csv"
if (!file.exists(filename)) download(url,filename)
msleep <- read.csv("msleep_ggplot2.csv")
head(msleep)
```

```
##                         name      genus  vore        order conservation
## 1                    Cheetah   Acinonyx carni    Carnivora           lc
## 2                 Owl monkey      Aotus  omni     Primates         <NA>
## 3            Mountain beaver Aplodontia herbi     Rodentia           nt
## 4 Greater short-tailed shrew    Blarina  omni Soricomorpha           lc
## 5                        Cow        Bos herbi Artiodactyla domesticated
## 6           Three-toed sloth   Bradypus herbi       Pilosa         <NA>
##   sleep_total sleep_rem sleep_cycle awake brainwt  bodywt
## 1        12.1        NA          NA  11.9      NA  50.000
## 2        17.0       1.8          NA   7.0 0.01550   0.480
## 3        14.4       2.4          NA   9.6      NA   1.350
## 4        14.9       2.3   0.1333333   9.1 0.00029   0.019
## 5         4.0       0.7   0.6666667  20.0 0.42300 600.000
## 6        14.4       2.2   0.7666667   9.6      NA   3.850
```

#Key dplyr verbs


`select()`	select columns   
`filter()`	filter rows   
`arrange()`	re-order or arrange rows   
`mutate()`	create new columns   
`summarise()` summarise values   
`group_by()`	allows for group operations in the “split-apply-combine” concept   

#Select columns


```r
#select columns
sleepData <- dplyr::select(msleep, name, sleep_total)
head(sleepData)
```

```
##                         name sleep_total
## 1                    Cheetah        12.1
## 2                 Owl monkey        17.0
## 3            Mountain beaver        14.4
## 4 Greater short-tailed shrew        14.9
## 5                        Cow         4.0
## 6           Three-toed sloth        14.4
```

```r
#remove columns
head(dplyr::select(msleep, -name))
```

```
##        genus  vore        order conservation sleep_total sleep_rem
## 1   Acinonyx carni    Carnivora           lc        12.1        NA
## 2      Aotus  omni     Primates         <NA>        17.0       1.8
## 3 Aplodontia herbi     Rodentia           nt        14.4       2.4
## 4    Blarina  omni Soricomorpha           lc        14.9       2.3
## 5        Bos herbi Artiodactyla domesticated         4.0       0.7
## 6   Bradypus herbi       Pilosa         <NA>        14.4       2.2
##   sleep_cycle awake brainwt  bodywt
## 1          NA  11.9      NA  50.000
## 2          NA   7.0 0.01550   0.480
## 3          NA   9.6      NA   1.350
## 4   0.1333333   9.1 0.00029   0.019
## 5   0.6666667  20.0 0.42300 600.000
## 6   0.7666667   9.6      NA   3.850
```

```r
#select by a range of columns :
head(dplyr::select(msleep, name:order))
```

```
##                         name      genus  vore        order
## 1                    Cheetah   Acinonyx carni    Carnivora
## 2                 Owl monkey      Aotus  omni     Primates
## 3            Mountain beaver Aplodontia herbi     Rodentia
## 4 Greater short-tailed shrew    Blarina  omni Soricomorpha
## 5                        Cow        Bos herbi Artiodactyla
## 6           Three-toed sloth   Bradypus herbi       Pilosa
```

try other commands like starts_with("InsertCharacter")

What about if the variable ends with a certain character?   
Contains a certain character? (`contains()`)   
Matches a regular expression? (`matches()`)   
Matches a numerical range? (`num_range()`)   
Is a variable in a character vector? (`one_of()`)   
All of them? (`everything()`) 

e.g. head(dplyr::select(msleep, starts_with("vo"))) will select columns starting with "vo"

#Reordering the selected columns

What if you want to easily reorder columns and you dont want to type all the column names?   

Or rename column names?

```r
dplyr::select(msleep, vore, everything())
```

```
##       vore                           name         genus           order
## 1    carni                        Cheetah      Acinonyx       Carnivora
## 2     omni                     Owl monkey         Aotus        Primates
## 3    herbi                Mountain beaver    Aplodontia        Rodentia
## 4     omni     Greater short-tailed shrew       Blarina    Soricomorpha
## 5    herbi                            Cow           Bos    Artiodactyla
## 6    herbi               Three-toed sloth      Bradypus          Pilosa
## 7    carni              Northern fur seal   Callorhinus       Carnivora
## 8     <NA>                   Vesper mouse       Calomys        Rodentia
## 9    carni                            Dog         Canis       Carnivora
## 10   herbi                       Roe deer     Capreolus    Artiodactyla
## 11   herbi                           Goat         Capri    Artiodactyla
## 12   herbi                     Guinea pig         Cavis        Rodentia
## 13    omni                         Grivet Cercopithecus        Primates
## 14   herbi                     Chinchilla    Chinchilla        Rodentia
## 15    omni                Star-nosed mole     Condylura    Soricomorpha
## 16    omni      African giant pouched rat    Cricetomys        Rodentia
## 17    omni      Lesser short-tailed shrew     Cryptotis    Soricomorpha
## 18   carni           Long-nosed armadillo       Dasypus       Cingulata
## 19   herbi                     Tree hyrax   Dendrohyrax      Hyracoidea
## 20    omni         North American Opossum     Didelphis Didelphimorphia
## 21   herbi                 Asian elephant       Elephas     Proboscidea
## 22 insecti                  Big brown bat     Eptesicus      Chiroptera
## 23   herbi                          Horse         Equus  Perissodactyla
## 24   herbi                         Donkey         Equus  Perissodactyla
## 25    omni              European hedgehog     Erinaceus  Erinaceomorpha
## 26    omni                   Patas monkey  Erythrocebus        Primates
## 27   herbi      Western american chipmunk      Eutamias        Rodentia
## 28   carni                   Domestic cat         Felis       Carnivora
## 29    omni                         Galago        Galago        Primates
## 30   herbi                        Giraffe       Giraffa    Artiodactyla
## 31   carni                    Pilot whale Globicephalus         Cetacea
## 32   carni                      Gray seal  Haliochoerus       Carnivora
## 33   herbi                     Gray hyrax   Heterohyrax      Hyracoidea
## 34    omni                          Human          Homo        Primates
## 35   herbi                 Mongoose lemur         Lemur        Primates
## 36   herbi               African elephant     Loxodonta     Proboscidea
## 37   carni           Thick-tailed opposum    Lutreolina Didelphimorphia
## 38    omni                        Macaque        Macaca        Primates
## 39   herbi               Mongolian gerbil      Meriones        Rodentia
## 40   herbi                 Golden hamster  Mesocricetus        Rodentia
## 41   herbi                          Vole       Microtus        Rodentia
## 42   herbi                    House mouse           Mus        Rodentia
## 43 insecti               Little brown bat        Myotis      Chiroptera
## 44   herbi           Round-tailed muskrat      Neofiber        Rodentia
## 45   carni                     Slow loris     Nyctibeus        Primates
## 46   herbi                           Degu       Octodon        Rodentia
## 47   carni     Northern grasshopper mouse     Onychomys        Rodentia
## 48   herbi                         Rabbit   Oryctolagus      Lagomorpha
## 49   herbi                          Sheep          Ovis    Artiodactyla
## 50    omni                     Chimpanzee           Pan        Primates
## 51   carni                          Tiger      Panthera       Carnivora
## 52   carni                         Jaguar      Panthera       Carnivora
## 53   carni                           Lion      Panthera       Carnivora
## 54    omni                         Baboon         Papio        Primates
## 55    <NA>                Desert hedgehog   Paraechinus  Erinaceomorpha
## 56    omni                          Potto  Perodicticus        Primates
## 57    <NA>                     Deer mouse    Peromyscus        Rodentia
## 58    <NA>                      Phalanger     Phalanger   Diprotodontia
## 59   carni                   Caspian seal         Phoca       Carnivora
## 60   carni                Common porpoise      Phocoena         Cetacea
## 61   herbi                        Potoroo      Potorous   Diprotodontia
## 62 insecti                Giant armadillo    Priodontes       Cingulata
## 63    <NA>                     Rock hyrax      Procavia      Hyracoidea
## 64   herbi                 Laboratory rat        Rattus        Rodentia
## 65    omni          African striped mouse     Rhabdomys        Rodentia
## 66    omni                Squirrel monkey       Saimiri        Primates
## 67 insecti          Eastern american mole      Scalopus    Soricomorpha
## 68   herbi                     Cotton rat      Sigmodon        Rodentia
## 69    <NA>                       Mole rat        Spalax        Rodentia
## 70   herbi         Arctic ground squirrel  Spermophilus        Rodentia
## 71   herbi Thirteen-lined ground squirrel  Spermophilus        Rodentia
## 72   herbi Golden-mantled ground squirrel  Spermophilus        Rodentia
## 73    <NA>                     Musk shrew        Suncus    Soricomorpha
## 74    omni                            Pig           Sus    Artiodactyla
## 75 insecti            Short-nosed echidna  Tachyglossus     Monotremata
## 76   herbi      Eastern american chipmunk        Tamias        Rodentia
## 77   herbi                Brazilian tapir       Tapirus  Perissodactyla
## 78    omni                         Tenrec        Tenrec    Afrosoricida
## 79    omni                     Tree shrew        Tupaia      Scandentia
## 80   carni           Bottle-nosed dolphin      Tursiops         Cetacea
## 81   carni                          Genet       Genetta       Carnivora
## 82   carni                     Arctic fox        Vulpes       Carnivora
## 83   carni                        Red fox        Vulpes       Carnivora
##    conservation sleep_total sleep_rem sleep_cycle awake brainwt   bodywt
## 1            lc        12.1        NA          NA 11.90      NA   50.000
## 2          <NA>        17.0       1.8          NA  7.00 0.01550    0.480
## 3            nt        14.4       2.4          NA  9.60      NA    1.350
## 4            lc        14.9       2.3   0.1333333  9.10 0.00029    0.019
## 5  domesticated         4.0       0.7   0.6666667 20.00 0.42300  600.000
## 6          <NA>        14.4       2.2   0.7666667  9.60      NA    3.850
## 7            vu         8.7       1.4   0.3833333 15.30      NA   20.490
## 8          <NA>         7.0        NA          NA 17.00      NA    0.045
## 9  domesticated        10.1       2.9   0.3333333 13.90 0.07000   14.000
## 10           lc         3.0        NA          NA 21.00 0.09820   14.800
## 11           lc         5.3       0.6          NA 18.70 0.11500   33.500
## 12 domesticated         9.4       0.8   0.2166667 14.60 0.00550    0.728
## 13           lc        10.0       0.7          NA 14.00      NA    4.750
## 14 domesticated        12.5       1.5   0.1166667 11.50 0.00640    0.420
## 15           lc        10.3       2.2          NA 13.70 0.00100    0.060
## 16         <NA>         8.3       2.0          NA 15.70 0.00660    1.000
## 17           lc         9.1       1.4   0.1500000 14.90 0.00014    0.005
## 18           lc        17.4       3.1   0.3833333  6.60 0.01080    3.500
## 19           lc         5.3       0.5          NA 18.70 0.01230    2.950
## 20           lc        18.0       4.9   0.3333333  6.00 0.00630    1.700
## 21           en         3.9        NA          NA 20.10 4.60300 2547.000
## 22           lc        19.7       3.9   0.1166667  4.30 0.00030    0.023
## 23 domesticated         2.9       0.6   1.0000000 21.10 0.65500  521.000
## 24 domesticated         3.1       0.4          NA 20.90 0.41900  187.000
## 25           lc        10.1       3.5   0.2833333 13.90 0.00350    0.770
## 26           lc        10.9       1.1          NA 13.10 0.11500   10.000
## 27         <NA>        14.9        NA          NA  9.10      NA    0.071
## 28 domesticated        12.5       3.2   0.4166667 11.50 0.02560    3.300
## 29         <NA>         9.8       1.1   0.5500000 14.20 0.00500    0.200
## 30           cd         1.9       0.4          NA 22.10      NA  899.995
## 31           cd         2.7       0.1          NA 21.35      NA  800.000
## 32           lc         6.2       1.5          NA 17.80 0.32500   85.000
## 33           lc         6.3       0.6          NA 17.70 0.01227    2.625
## 34         <NA>         8.0       1.9   1.5000000 16.00 1.32000   62.000
## 35           vu         9.5       0.9          NA 14.50      NA    1.670
## 36           vu         3.3        NA          NA 20.70 5.71200 6654.000
## 37           lc        19.4       6.6          NA  4.60      NA    0.370
## 38         <NA>        10.1       1.2   0.7500000 13.90 0.17900    6.800
## 39           lc        14.2       1.9          NA  9.80      NA    0.053
## 40           en        14.3       3.1   0.2000000  9.70 0.00100    0.120
## 41         <NA>        12.8        NA          NA 11.20      NA    0.035
## 42           nt        12.5       1.4   0.1833333 11.50 0.00040    0.022
## 43         <NA>        19.9       2.0   0.2000000  4.10 0.00025    0.010
## 44           nt        14.6        NA          NA  9.40      NA    0.266
## 45         <NA>        11.0        NA          NA 13.00 0.01250    1.400
## 46           lc         7.7       0.9          NA 16.30      NA    0.210
## 47           lc        14.5        NA          NA  9.50      NA    0.028
## 48 domesticated         8.4       0.9   0.4166667 15.60 0.01210    2.500
## 49 domesticated         3.8       0.6          NA 20.20 0.17500   55.500
## 50         <NA>         9.7       1.4   1.4166667 14.30 0.44000   52.200
## 51           en        15.8        NA          NA  8.20      NA  162.564
## 52           nt        10.4        NA          NA 13.60 0.15700  100.000
## 53           vu        13.5        NA          NA 10.50      NA  161.499
## 54         <NA>         9.4       1.0   0.6666667 14.60 0.18000   25.235
## 55           lc        10.3       2.7          NA 13.70 0.00240    0.550
## 56           lc        11.0        NA          NA 13.00      NA    1.100
## 57         <NA>        11.5        NA          NA 12.50      NA    0.021
## 58         <NA>        13.7       1.8          NA 10.30 0.01140    1.620
## 59           vu         3.5       0.4          NA 20.50      NA   86.000
## 60           vu         5.6        NA          NA 18.45      NA   53.180
## 61         <NA>        11.1       1.5          NA 12.90      NA    1.100
## 62           en        18.1       6.1          NA  5.90 0.08100   60.000
## 63           lc         5.4       0.5          NA 18.60 0.02100    3.600
## 64           lc        13.0       2.4   0.1833333 11.00 0.00190    0.320
## 65         <NA>         8.7        NA          NA 15.30      NA    0.044
## 66         <NA>         9.6       1.4          NA 14.40 0.02000    0.743
## 67           lc         8.4       2.1   0.1666667 15.60 0.00120    0.075
## 68         <NA>        11.3       1.1   0.1500000 12.70 0.00118    0.148
## 69         <NA>        10.6       2.4          NA 13.40 0.00300    0.122
## 70           lc        16.6        NA          NA  7.40 0.00570    0.920
## 71           lc        13.8       3.4   0.2166667 10.20 0.00400    0.101
## 72           lc        15.9       3.0          NA  8.10      NA    0.205
## 73         <NA>        12.8       2.0   0.1833333 11.20 0.00033    0.048
## 74 domesticated         9.1       2.4   0.5000000 14.90 0.18000   86.250
## 75         <NA>         8.6        NA          NA 15.40 0.02500    4.500
## 76         <NA>        15.8        NA          NA  8.20      NA    0.112
## 77           vu         4.4       1.0   0.9000000 19.60 0.16900  207.501
## 78         <NA>        15.6       2.3          NA  8.40 0.00260    0.900
## 79         <NA>         8.9       2.6   0.2333333 15.10 0.00250    0.104
## 80         <NA>         5.2        NA          NA 18.80      NA  173.330
## 81         <NA>         6.3       1.3          NA 17.70 0.01750    2.000
## 82         <NA>        12.5        NA          NA 11.50 0.04450    3.380
## 83         <NA>         9.8       2.4   0.3500000 14.20 0.05040    4.230
```

```r
rename(msleep, cvore = vore)
```

```
##                              name         genus   cvore           order
## 1                         Cheetah      Acinonyx   carni       Carnivora
## 2                      Owl monkey         Aotus    omni        Primates
## 3                 Mountain beaver    Aplodontia   herbi        Rodentia
## 4      Greater short-tailed shrew       Blarina    omni    Soricomorpha
## 5                             Cow           Bos   herbi    Artiodactyla
## 6                Three-toed sloth      Bradypus   herbi          Pilosa
## 7               Northern fur seal   Callorhinus   carni       Carnivora
## 8                    Vesper mouse       Calomys    <NA>        Rodentia
## 9                             Dog         Canis   carni       Carnivora
## 10                       Roe deer     Capreolus   herbi    Artiodactyla
## 11                           Goat         Capri   herbi    Artiodactyla
## 12                     Guinea pig         Cavis   herbi        Rodentia
## 13                         Grivet Cercopithecus    omni        Primates
## 14                     Chinchilla    Chinchilla   herbi        Rodentia
## 15                Star-nosed mole     Condylura    omni    Soricomorpha
## 16      African giant pouched rat    Cricetomys    omni        Rodentia
## 17      Lesser short-tailed shrew     Cryptotis    omni    Soricomorpha
## 18           Long-nosed armadillo       Dasypus   carni       Cingulata
## 19                     Tree hyrax   Dendrohyrax   herbi      Hyracoidea
## 20         North American Opossum     Didelphis    omni Didelphimorphia
## 21                 Asian elephant       Elephas   herbi     Proboscidea
## 22                  Big brown bat     Eptesicus insecti      Chiroptera
## 23                          Horse         Equus   herbi  Perissodactyla
## 24                         Donkey         Equus   herbi  Perissodactyla
## 25              European hedgehog     Erinaceus    omni  Erinaceomorpha
## 26                   Patas monkey  Erythrocebus    omni        Primates
## 27      Western american chipmunk      Eutamias   herbi        Rodentia
## 28                   Domestic cat         Felis   carni       Carnivora
## 29                         Galago        Galago    omni        Primates
## 30                        Giraffe       Giraffa   herbi    Artiodactyla
## 31                    Pilot whale Globicephalus   carni         Cetacea
## 32                      Gray seal  Haliochoerus   carni       Carnivora
## 33                     Gray hyrax   Heterohyrax   herbi      Hyracoidea
## 34                          Human          Homo    omni        Primates
## 35                 Mongoose lemur         Lemur   herbi        Primates
## 36               African elephant     Loxodonta   herbi     Proboscidea
## 37           Thick-tailed opposum    Lutreolina   carni Didelphimorphia
## 38                        Macaque        Macaca    omni        Primates
## 39               Mongolian gerbil      Meriones   herbi        Rodentia
## 40                 Golden hamster  Mesocricetus   herbi        Rodentia
## 41                          Vole       Microtus   herbi        Rodentia
## 42                    House mouse           Mus   herbi        Rodentia
## 43               Little brown bat        Myotis insecti      Chiroptera
## 44           Round-tailed muskrat      Neofiber   herbi        Rodentia
## 45                     Slow loris     Nyctibeus   carni        Primates
## 46                           Degu       Octodon   herbi        Rodentia
## 47     Northern grasshopper mouse     Onychomys   carni        Rodentia
## 48                         Rabbit   Oryctolagus   herbi      Lagomorpha
## 49                          Sheep          Ovis   herbi    Artiodactyla
## 50                     Chimpanzee           Pan    omni        Primates
## 51                          Tiger      Panthera   carni       Carnivora
## 52                         Jaguar      Panthera   carni       Carnivora
## 53                           Lion      Panthera   carni       Carnivora
## 54                         Baboon         Papio    omni        Primates
## 55                Desert hedgehog   Paraechinus    <NA>  Erinaceomorpha
## 56                          Potto  Perodicticus    omni        Primates
## 57                     Deer mouse    Peromyscus    <NA>        Rodentia
## 58                      Phalanger     Phalanger    <NA>   Diprotodontia
## 59                   Caspian seal         Phoca   carni       Carnivora
## 60                Common porpoise      Phocoena   carni         Cetacea
## 61                        Potoroo      Potorous   herbi   Diprotodontia
## 62                Giant armadillo    Priodontes insecti       Cingulata
## 63                     Rock hyrax      Procavia    <NA>      Hyracoidea
## 64                 Laboratory rat        Rattus   herbi        Rodentia
## 65          African striped mouse     Rhabdomys    omni        Rodentia
## 66                Squirrel monkey       Saimiri    omni        Primates
## 67          Eastern american mole      Scalopus insecti    Soricomorpha
## 68                     Cotton rat      Sigmodon   herbi        Rodentia
## 69                       Mole rat        Spalax    <NA>        Rodentia
## 70         Arctic ground squirrel  Spermophilus   herbi        Rodentia
## 71 Thirteen-lined ground squirrel  Spermophilus   herbi        Rodentia
## 72 Golden-mantled ground squirrel  Spermophilus   herbi        Rodentia
## 73                     Musk shrew        Suncus    <NA>    Soricomorpha
## 74                            Pig           Sus    omni    Artiodactyla
## 75            Short-nosed echidna  Tachyglossus insecti     Monotremata
## 76      Eastern american chipmunk        Tamias   herbi        Rodentia
## 77                Brazilian tapir       Tapirus   herbi  Perissodactyla
## 78                         Tenrec        Tenrec    omni    Afrosoricida
## 79                     Tree shrew        Tupaia    omni      Scandentia
## 80           Bottle-nosed dolphin      Tursiops   carni         Cetacea
## 81                          Genet       Genetta   carni       Carnivora
## 82                     Arctic fox        Vulpes   carni       Carnivora
## 83                        Red fox        Vulpes   carni       Carnivora
##    conservation sleep_total sleep_rem sleep_cycle awake brainwt   bodywt
## 1            lc        12.1        NA          NA 11.90      NA   50.000
## 2          <NA>        17.0       1.8          NA  7.00 0.01550    0.480
## 3            nt        14.4       2.4          NA  9.60      NA    1.350
## 4            lc        14.9       2.3   0.1333333  9.10 0.00029    0.019
## 5  domesticated         4.0       0.7   0.6666667 20.00 0.42300  600.000
## 6          <NA>        14.4       2.2   0.7666667  9.60      NA    3.850
## 7            vu         8.7       1.4   0.3833333 15.30      NA   20.490
## 8          <NA>         7.0        NA          NA 17.00      NA    0.045
## 9  domesticated        10.1       2.9   0.3333333 13.90 0.07000   14.000
## 10           lc         3.0        NA          NA 21.00 0.09820   14.800
## 11           lc         5.3       0.6          NA 18.70 0.11500   33.500
## 12 domesticated         9.4       0.8   0.2166667 14.60 0.00550    0.728
## 13           lc        10.0       0.7          NA 14.00      NA    4.750
## 14 domesticated        12.5       1.5   0.1166667 11.50 0.00640    0.420
## 15           lc        10.3       2.2          NA 13.70 0.00100    0.060
## 16         <NA>         8.3       2.0          NA 15.70 0.00660    1.000
## 17           lc         9.1       1.4   0.1500000 14.90 0.00014    0.005
## 18           lc        17.4       3.1   0.3833333  6.60 0.01080    3.500
## 19           lc         5.3       0.5          NA 18.70 0.01230    2.950
## 20           lc        18.0       4.9   0.3333333  6.00 0.00630    1.700
## 21           en         3.9        NA          NA 20.10 4.60300 2547.000
## 22           lc        19.7       3.9   0.1166667  4.30 0.00030    0.023
## 23 domesticated         2.9       0.6   1.0000000 21.10 0.65500  521.000
## 24 domesticated         3.1       0.4          NA 20.90 0.41900  187.000
## 25           lc        10.1       3.5   0.2833333 13.90 0.00350    0.770
## 26           lc        10.9       1.1          NA 13.10 0.11500   10.000
## 27         <NA>        14.9        NA          NA  9.10      NA    0.071
## 28 domesticated        12.5       3.2   0.4166667 11.50 0.02560    3.300
## 29         <NA>         9.8       1.1   0.5500000 14.20 0.00500    0.200
## 30           cd         1.9       0.4          NA 22.10      NA  899.995
## 31           cd         2.7       0.1          NA 21.35      NA  800.000
## 32           lc         6.2       1.5          NA 17.80 0.32500   85.000
## 33           lc         6.3       0.6          NA 17.70 0.01227    2.625
## 34         <NA>         8.0       1.9   1.5000000 16.00 1.32000   62.000
## 35           vu         9.5       0.9          NA 14.50      NA    1.670
## 36           vu         3.3        NA          NA 20.70 5.71200 6654.000
## 37           lc        19.4       6.6          NA  4.60      NA    0.370
## 38         <NA>        10.1       1.2   0.7500000 13.90 0.17900    6.800
## 39           lc        14.2       1.9          NA  9.80      NA    0.053
## 40           en        14.3       3.1   0.2000000  9.70 0.00100    0.120
## 41         <NA>        12.8        NA          NA 11.20      NA    0.035
## 42           nt        12.5       1.4   0.1833333 11.50 0.00040    0.022
## 43         <NA>        19.9       2.0   0.2000000  4.10 0.00025    0.010
## 44           nt        14.6        NA          NA  9.40      NA    0.266
## 45         <NA>        11.0        NA          NA 13.00 0.01250    1.400
## 46           lc         7.7       0.9          NA 16.30      NA    0.210
## 47           lc        14.5        NA          NA  9.50      NA    0.028
## 48 domesticated         8.4       0.9   0.4166667 15.60 0.01210    2.500
## 49 domesticated         3.8       0.6          NA 20.20 0.17500   55.500
## 50         <NA>         9.7       1.4   1.4166667 14.30 0.44000   52.200
## 51           en        15.8        NA          NA  8.20      NA  162.564
## 52           nt        10.4        NA          NA 13.60 0.15700  100.000
## 53           vu        13.5        NA          NA 10.50      NA  161.499
## 54         <NA>         9.4       1.0   0.6666667 14.60 0.18000   25.235
## 55           lc        10.3       2.7          NA 13.70 0.00240    0.550
## 56           lc        11.0        NA          NA 13.00      NA    1.100
## 57         <NA>        11.5        NA          NA 12.50      NA    0.021
## 58         <NA>        13.7       1.8          NA 10.30 0.01140    1.620
## 59           vu         3.5       0.4          NA 20.50      NA   86.000
## 60           vu         5.6        NA          NA 18.45      NA   53.180
## 61         <NA>        11.1       1.5          NA 12.90      NA    1.100
## 62           en        18.1       6.1          NA  5.90 0.08100   60.000
## 63           lc         5.4       0.5          NA 18.60 0.02100    3.600
## 64           lc        13.0       2.4   0.1833333 11.00 0.00190    0.320
## 65         <NA>         8.7        NA          NA 15.30      NA    0.044
## 66         <NA>         9.6       1.4          NA 14.40 0.02000    0.743
## 67           lc         8.4       2.1   0.1666667 15.60 0.00120    0.075
## 68         <NA>        11.3       1.1   0.1500000 12.70 0.00118    0.148
## 69         <NA>        10.6       2.4          NA 13.40 0.00300    0.122
## 70           lc        16.6        NA          NA  7.40 0.00570    0.920
## 71           lc        13.8       3.4   0.2166667 10.20 0.00400    0.101
## 72           lc        15.9       3.0          NA  8.10      NA    0.205
## 73         <NA>        12.8       2.0   0.1833333 11.20 0.00033    0.048
## 74 domesticated         9.1       2.4   0.5000000 14.90 0.18000   86.250
## 75         <NA>         8.6        NA          NA 15.40 0.02500    4.500
## 76         <NA>        15.8        NA          NA  8.20      NA    0.112
## 77           vu         4.4       1.0   0.9000000 19.60 0.16900  207.501
## 78         <NA>        15.6       2.3          NA  8.40 0.00260    0.900
## 79         <NA>         8.9       2.6   0.2333333 15.10 0.00250    0.104
## 80         <NA>         5.2        NA          NA 18.80      NA  173.330
## 81         <NA>         6.3       1.3          NA 17.70 0.01750    2.000
## 82         <NA>        12.5        NA          NA 11.50 0.04450    3.380
## 83         <NA>         9.8       2.4   0.3500000 14.20 0.05040    4.230
```


#Selecting rows using filter

`filter` allows you extract rows


```r
filter(msleep, sleep_total >= 16)
```

```
##                     name        genus    vore           order conservation
## 1             Owl monkey        Aotus    omni        Primates         <NA>
## 2   Long-nosed armadillo      Dasypus   carni       Cingulata           lc
## 3 North American Opossum    Didelphis    omni Didelphimorphia           lc
## 4          Big brown bat    Eptesicus insecti      Chiroptera           lc
## 5   Thick-tailed opposum   Lutreolina   carni Didelphimorphia           lc
## 6       Little brown bat       Myotis insecti      Chiroptera         <NA>
## 7        Giant armadillo   Priodontes insecti       Cingulata           en
## 8 Arctic ground squirrel Spermophilus   herbi        Rodentia           lc
##   sleep_total sleep_rem sleep_cycle awake brainwt bodywt
## 1        17.0       1.8          NA   7.0 0.01550  0.480
## 2        17.4       3.1   0.3833333   6.6 0.01080  3.500
## 3        18.0       4.9   0.3333333   6.0 0.00630  1.700
## 4        19.7       3.9   0.1166667   4.3 0.00030  0.023
## 5        19.4       6.6          NA   4.6      NA  0.370
## 6        19.9       2.0   0.2000000   4.1 0.00025  0.010
## 7        18.1       6.1          NA   5.9 0.08100 60.000
## 8        16.6        NA          NA   7.4 0.00570  0.920
```

```r
filter(msleep, sleep_total >= 16, bodywt >=1)
```

```
##                     name      genus    vore           order conservation
## 1   Long-nosed armadillo    Dasypus   carni       Cingulata           lc
## 2 North American Opossum  Didelphis    omni Didelphimorphia           lc
## 3        Giant armadillo Priodontes insecti       Cingulata           en
##   sleep_total sleep_rem sleep_cycle awake brainwt bodywt
## 1        17.4       3.1   0.3833333   6.6  0.0108    3.5
## 2        18.0       4.9   0.3333333   6.0  0.0063    1.7
## 3        18.1       6.1          NA   5.9  0.0810   60.0
```

```r
filter(msleep, vore %in% c("omni", "carni"))
```

```
##                          name         genus  vore           order
## 1                     Cheetah      Acinonyx carni       Carnivora
## 2                  Owl monkey         Aotus  omni        Primates
## 3  Greater short-tailed shrew       Blarina  omni    Soricomorpha
## 4           Northern fur seal   Callorhinus carni       Carnivora
## 5                         Dog         Canis carni       Carnivora
## 6                      Grivet Cercopithecus  omni        Primates
## 7             Star-nosed mole     Condylura  omni    Soricomorpha
## 8   African giant pouched rat    Cricetomys  omni        Rodentia
## 9   Lesser short-tailed shrew     Cryptotis  omni    Soricomorpha
## 10       Long-nosed armadillo       Dasypus carni       Cingulata
## 11     North American Opossum     Didelphis  omni Didelphimorphia
## 12          European hedgehog     Erinaceus  omni  Erinaceomorpha
## 13               Patas monkey  Erythrocebus  omni        Primates
## 14               Domestic cat         Felis carni       Carnivora
## 15                     Galago        Galago  omni        Primates
## 16                Pilot whale Globicephalus carni         Cetacea
## 17                  Gray seal  Haliochoerus carni       Carnivora
## 18                      Human          Homo  omni        Primates
## 19       Thick-tailed opposum    Lutreolina carni Didelphimorphia
## 20                    Macaque        Macaca  omni        Primates
## 21                 Slow loris     Nyctibeus carni        Primates
## 22 Northern grasshopper mouse     Onychomys carni        Rodentia
## 23                 Chimpanzee           Pan  omni        Primates
## 24                      Tiger      Panthera carni       Carnivora
## 25                     Jaguar      Panthera carni       Carnivora
## 26                       Lion      Panthera carni       Carnivora
## 27                     Baboon         Papio  omni        Primates
## 28                      Potto  Perodicticus  omni        Primates
## 29               Caspian seal         Phoca carni       Carnivora
## 30            Common porpoise      Phocoena carni         Cetacea
## 31      African striped mouse     Rhabdomys  omni        Rodentia
## 32            Squirrel monkey       Saimiri  omni        Primates
## 33                        Pig           Sus  omni    Artiodactyla
## 34                     Tenrec        Tenrec  omni    Afrosoricida
## 35                 Tree shrew        Tupaia  omni      Scandentia
## 36       Bottle-nosed dolphin      Tursiops carni         Cetacea
## 37                      Genet       Genetta carni       Carnivora
## 38                 Arctic fox        Vulpes carni       Carnivora
## 39                    Red fox        Vulpes carni       Carnivora
##    conservation sleep_total sleep_rem sleep_cycle awake brainwt  bodywt
## 1            lc        12.1        NA          NA 11.90      NA  50.000
## 2          <NA>        17.0       1.8          NA  7.00 0.01550   0.480
## 3            lc        14.9       2.3   0.1333333  9.10 0.00029   0.019
## 4            vu         8.7       1.4   0.3833333 15.30      NA  20.490
## 5  domesticated        10.1       2.9   0.3333333 13.90 0.07000  14.000
## 6            lc        10.0       0.7          NA 14.00      NA   4.750
## 7            lc        10.3       2.2          NA 13.70 0.00100   0.060
## 8          <NA>         8.3       2.0          NA 15.70 0.00660   1.000
## 9            lc         9.1       1.4   0.1500000 14.90 0.00014   0.005
## 10           lc        17.4       3.1   0.3833333  6.60 0.01080   3.500
## 11           lc        18.0       4.9   0.3333333  6.00 0.00630   1.700
## 12           lc        10.1       3.5   0.2833333 13.90 0.00350   0.770
## 13           lc        10.9       1.1          NA 13.10 0.11500  10.000
## 14 domesticated        12.5       3.2   0.4166667 11.50 0.02560   3.300
## 15         <NA>         9.8       1.1   0.5500000 14.20 0.00500   0.200
## 16           cd         2.7       0.1          NA 21.35      NA 800.000
## 17           lc         6.2       1.5          NA 17.80 0.32500  85.000
## 18         <NA>         8.0       1.9   1.5000000 16.00 1.32000  62.000
## 19           lc        19.4       6.6          NA  4.60      NA   0.370
## 20         <NA>        10.1       1.2   0.7500000 13.90 0.17900   6.800
## 21         <NA>        11.0        NA          NA 13.00 0.01250   1.400
## 22           lc        14.5        NA          NA  9.50      NA   0.028
## 23         <NA>         9.7       1.4   1.4166667 14.30 0.44000  52.200
## 24           en        15.8        NA          NA  8.20      NA 162.564
## 25           nt        10.4        NA          NA 13.60 0.15700 100.000
## 26           vu        13.5        NA          NA 10.50      NA 161.499
## 27         <NA>         9.4       1.0   0.6666667 14.60 0.18000  25.235
## 28           lc        11.0        NA          NA 13.00      NA   1.100
## 29           vu         3.5       0.4          NA 20.50      NA  86.000
## 30           vu         5.6        NA          NA 18.45      NA  53.180
## 31         <NA>         8.7        NA          NA 15.30      NA   0.044
## 32         <NA>         9.6       1.4          NA 14.40 0.02000   0.743
## 33 domesticated         9.1       2.4   0.5000000 14.90 0.18000  86.250
## 34         <NA>        15.6       2.3          NA  8.40 0.00260   0.900
## 35         <NA>         8.9       2.6   0.2333333 15.10 0.00250   0.104
## 36         <NA>         5.2        NA          NA 18.80      NA 173.330
## 37         <NA>         6.3       1.3          NA 17.70 0.01750   2.000
## 38         <NA>        12.5        NA          NA 11.50 0.04450   3.380
## 39         <NA>         9.8       2.4   0.3500000 14.20 0.05040   4.230
```

```r
filter(msleep, !conservation%in% c("lc", "domesticated"))
```

```
##                         name         genus    vore          order
## 1                 Owl monkey         Aotus    omni       Primates
## 2            Mountain beaver    Aplodontia   herbi       Rodentia
## 3           Three-toed sloth      Bradypus   herbi         Pilosa
## 4          Northern fur seal   Callorhinus   carni      Carnivora
## 5               Vesper mouse       Calomys    <NA>       Rodentia
## 6  African giant pouched rat    Cricetomys    omni       Rodentia
## 7             Asian elephant       Elephas   herbi    Proboscidea
## 8  Western american chipmunk      Eutamias   herbi       Rodentia
## 9                     Galago        Galago    omni       Primates
## 10                   Giraffe       Giraffa   herbi   Artiodactyla
## 11               Pilot whale Globicephalus   carni        Cetacea
## 12                     Human          Homo    omni       Primates
## 13            Mongoose lemur         Lemur   herbi       Primates
## 14          African elephant     Loxodonta   herbi    Proboscidea
## 15                   Macaque        Macaca    omni       Primates
## 16            Golden hamster  Mesocricetus   herbi       Rodentia
## 17                     Vole       Microtus   herbi       Rodentia
## 18               House mouse           Mus   herbi       Rodentia
## 19          Little brown bat        Myotis insecti     Chiroptera
## 20      Round-tailed muskrat      Neofiber   herbi       Rodentia
## 21                Slow loris     Nyctibeus   carni       Primates
## 22                Chimpanzee           Pan    omni       Primates
## 23                     Tiger      Panthera   carni      Carnivora
## 24                    Jaguar      Panthera   carni      Carnivora
## 25                      Lion      Panthera   carni      Carnivora
## 26                    Baboon         Papio    omni       Primates
## 27                Deer mouse    Peromyscus    <NA>       Rodentia
## 28                 Phalanger     Phalanger    <NA>  Diprotodontia
## 29              Caspian seal         Phoca   carni      Carnivora
## 30           Common porpoise      Phocoena   carni        Cetacea
## 31                   Potoroo      Potorous   herbi  Diprotodontia
## 32           Giant armadillo    Priodontes insecti      Cingulata
## 33     African striped mouse     Rhabdomys    omni       Rodentia
## 34           Squirrel monkey       Saimiri    omni       Primates
## 35                Cotton rat      Sigmodon   herbi       Rodentia
## 36                  Mole rat        Spalax    <NA>       Rodentia
## 37                Musk shrew        Suncus    <NA>   Soricomorpha
## 38       Short-nosed echidna  Tachyglossus insecti    Monotremata
## 39 Eastern american chipmunk        Tamias   herbi       Rodentia
## 40           Brazilian tapir       Tapirus   herbi Perissodactyla
## 41                    Tenrec        Tenrec    omni   Afrosoricida
## 42                Tree shrew        Tupaia    omni     Scandentia
## 43      Bottle-nosed dolphin      Tursiops   carni        Cetacea
## 44                     Genet       Genetta   carni      Carnivora
## 45                Arctic fox        Vulpes   carni      Carnivora
## 46                   Red fox        Vulpes   carni      Carnivora
##    conservation sleep_total sleep_rem sleep_cycle awake brainwt   bodywt
## 1          <NA>        17.0       1.8          NA  7.00 0.01550    0.480
## 2            nt        14.4       2.4          NA  9.60      NA    1.350
## 3          <NA>        14.4       2.2   0.7666667  9.60      NA    3.850
## 4            vu         8.7       1.4   0.3833333 15.30      NA   20.490
## 5          <NA>         7.0        NA          NA 17.00      NA    0.045
## 6          <NA>         8.3       2.0          NA 15.70 0.00660    1.000
## 7            en         3.9        NA          NA 20.10 4.60300 2547.000
## 8          <NA>        14.9        NA          NA  9.10      NA    0.071
## 9          <NA>         9.8       1.1   0.5500000 14.20 0.00500    0.200
## 10           cd         1.9       0.4          NA 22.10      NA  899.995
## 11           cd         2.7       0.1          NA 21.35      NA  800.000
## 12         <NA>         8.0       1.9   1.5000000 16.00 1.32000   62.000
## 13           vu         9.5       0.9          NA 14.50      NA    1.670
## 14           vu         3.3        NA          NA 20.70 5.71200 6654.000
## 15         <NA>        10.1       1.2   0.7500000 13.90 0.17900    6.800
## 16           en        14.3       3.1   0.2000000  9.70 0.00100    0.120
## 17         <NA>        12.8        NA          NA 11.20      NA    0.035
## 18           nt        12.5       1.4   0.1833333 11.50 0.00040    0.022
## 19         <NA>        19.9       2.0   0.2000000  4.10 0.00025    0.010
## 20           nt        14.6        NA          NA  9.40      NA    0.266
## 21         <NA>        11.0        NA          NA 13.00 0.01250    1.400
## 22         <NA>         9.7       1.4   1.4166667 14.30 0.44000   52.200
## 23           en        15.8        NA          NA  8.20      NA  162.564
## 24           nt        10.4        NA          NA 13.60 0.15700  100.000
## 25           vu        13.5        NA          NA 10.50      NA  161.499
## 26         <NA>         9.4       1.0   0.6666667 14.60 0.18000   25.235
## 27         <NA>        11.5        NA          NA 12.50      NA    0.021
## 28         <NA>        13.7       1.8          NA 10.30 0.01140    1.620
## 29           vu         3.5       0.4          NA 20.50      NA   86.000
## 30           vu         5.6        NA          NA 18.45      NA   53.180
## 31         <NA>        11.1       1.5          NA 12.90      NA    1.100
## 32           en        18.1       6.1          NA  5.90 0.08100   60.000
## 33         <NA>         8.7        NA          NA 15.30      NA    0.044
## 34         <NA>         9.6       1.4          NA 14.40 0.02000    0.743
## 35         <NA>        11.3       1.1   0.1500000 12.70 0.00118    0.148
## 36         <NA>        10.6       2.4          NA 13.40 0.00300    0.122
## 37         <NA>        12.8       2.0   0.1833333 11.20 0.00033    0.048
## 38         <NA>         8.6        NA          NA 15.40 0.02500    4.500
## 39         <NA>        15.8        NA          NA  8.20      NA    0.112
## 40           vu         4.4       1.0   0.9000000 19.60 0.16900  207.501
## 41         <NA>        15.6       2.3          NA  8.40 0.00260    0.900
## 42         <NA>         8.9       2.6   0.2333333 15.10 0.00250    0.104
## 43         <NA>         5.2        NA          NA 18.80      NA  173.330
## 44         <NA>         6.3       1.3          NA 17.70 0.01750    2.000
## 45         <NA>        12.5        NA          NA 11.50 0.04450    3.380
## 46         <NA>         9.8       2.4   0.3500000 14.20 0.05040    4.230
```

```r
#removing duplicates

distinct(msleep)
```

```
##                              name         genus    vore           order
## 1                         Cheetah      Acinonyx   carni       Carnivora
## 2                      Owl monkey         Aotus    omni        Primates
## 3                 Mountain beaver    Aplodontia   herbi        Rodentia
## 4      Greater short-tailed shrew       Blarina    omni    Soricomorpha
## 5                             Cow           Bos   herbi    Artiodactyla
## 6                Three-toed sloth      Bradypus   herbi          Pilosa
## 7               Northern fur seal   Callorhinus   carni       Carnivora
## 8                    Vesper mouse       Calomys    <NA>        Rodentia
## 9                             Dog         Canis   carni       Carnivora
## 10                       Roe deer     Capreolus   herbi    Artiodactyla
## 11                           Goat         Capri   herbi    Artiodactyla
## 12                     Guinea pig         Cavis   herbi        Rodentia
## 13                         Grivet Cercopithecus    omni        Primates
## 14                     Chinchilla    Chinchilla   herbi        Rodentia
## 15                Star-nosed mole     Condylura    omni    Soricomorpha
## 16      African giant pouched rat    Cricetomys    omni        Rodentia
## 17      Lesser short-tailed shrew     Cryptotis    omni    Soricomorpha
## 18           Long-nosed armadillo       Dasypus   carni       Cingulata
## 19                     Tree hyrax   Dendrohyrax   herbi      Hyracoidea
## 20         North American Opossum     Didelphis    omni Didelphimorphia
## 21                 Asian elephant       Elephas   herbi     Proboscidea
## 22                  Big brown bat     Eptesicus insecti      Chiroptera
## 23                          Horse         Equus   herbi  Perissodactyla
## 24                         Donkey         Equus   herbi  Perissodactyla
## 25              European hedgehog     Erinaceus    omni  Erinaceomorpha
## 26                   Patas monkey  Erythrocebus    omni        Primates
## 27      Western american chipmunk      Eutamias   herbi        Rodentia
## 28                   Domestic cat         Felis   carni       Carnivora
## 29                         Galago        Galago    omni        Primates
## 30                        Giraffe       Giraffa   herbi    Artiodactyla
## 31                    Pilot whale Globicephalus   carni         Cetacea
## 32                      Gray seal  Haliochoerus   carni       Carnivora
## 33                     Gray hyrax   Heterohyrax   herbi      Hyracoidea
## 34                          Human          Homo    omni        Primates
## 35                 Mongoose lemur         Lemur   herbi        Primates
## 36               African elephant     Loxodonta   herbi     Proboscidea
## 37           Thick-tailed opposum    Lutreolina   carni Didelphimorphia
## 38                        Macaque        Macaca    omni        Primates
## 39               Mongolian gerbil      Meriones   herbi        Rodentia
## 40                 Golden hamster  Mesocricetus   herbi        Rodentia
## 41                          Vole       Microtus   herbi        Rodentia
## 42                    House mouse           Mus   herbi        Rodentia
## 43               Little brown bat        Myotis insecti      Chiroptera
## 44           Round-tailed muskrat      Neofiber   herbi        Rodentia
## 45                     Slow loris     Nyctibeus   carni        Primates
## 46                           Degu       Octodon   herbi        Rodentia
## 47     Northern grasshopper mouse     Onychomys   carni        Rodentia
## 48                         Rabbit   Oryctolagus   herbi      Lagomorpha
## 49                          Sheep          Ovis   herbi    Artiodactyla
## 50                     Chimpanzee           Pan    omni        Primates
## 51                          Tiger      Panthera   carni       Carnivora
## 52                         Jaguar      Panthera   carni       Carnivora
## 53                           Lion      Panthera   carni       Carnivora
## 54                         Baboon         Papio    omni        Primates
## 55                Desert hedgehog   Paraechinus    <NA>  Erinaceomorpha
## 56                          Potto  Perodicticus    omni        Primates
## 57                     Deer mouse    Peromyscus    <NA>        Rodentia
## 58                      Phalanger     Phalanger    <NA>   Diprotodontia
## 59                   Caspian seal         Phoca   carni       Carnivora
## 60                Common porpoise      Phocoena   carni         Cetacea
## 61                        Potoroo      Potorous   herbi   Diprotodontia
## 62                Giant armadillo    Priodontes insecti       Cingulata
## 63                     Rock hyrax      Procavia    <NA>      Hyracoidea
## 64                 Laboratory rat        Rattus   herbi        Rodentia
## 65          African striped mouse     Rhabdomys    omni        Rodentia
## 66                Squirrel monkey       Saimiri    omni        Primates
## 67          Eastern american mole      Scalopus insecti    Soricomorpha
## 68                     Cotton rat      Sigmodon   herbi        Rodentia
## 69                       Mole rat        Spalax    <NA>        Rodentia
## 70         Arctic ground squirrel  Spermophilus   herbi        Rodentia
## 71 Thirteen-lined ground squirrel  Spermophilus   herbi        Rodentia
## 72 Golden-mantled ground squirrel  Spermophilus   herbi        Rodentia
## 73                     Musk shrew        Suncus    <NA>    Soricomorpha
## 74                            Pig           Sus    omni    Artiodactyla
## 75            Short-nosed echidna  Tachyglossus insecti     Monotremata
## 76      Eastern american chipmunk        Tamias   herbi        Rodentia
## 77                Brazilian tapir       Tapirus   herbi  Perissodactyla
## 78                         Tenrec        Tenrec    omni    Afrosoricida
## 79                     Tree shrew        Tupaia    omni      Scandentia
## 80           Bottle-nosed dolphin      Tursiops   carni         Cetacea
## 81                          Genet       Genetta   carni       Carnivora
## 82                     Arctic fox        Vulpes   carni       Carnivora
## 83                        Red fox        Vulpes   carni       Carnivora
##    conservation sleep_total sleep_rem sleep_cycle awake brainwt   bodywt
## 1            lc        12.1        NA          NA 11.90      NA   50.000
## 2          <NA>        17.0       1.8          NA  7.00 0.01550    0.480
## 3            nt        14.4       2.4          NA  9.60      NA    1.350
## 4            lc        14.9       2.3   0.1333333  9.10 0.00029    0.019
## 5  domesticated         4.0       0.7   0.6666667 20.00 0.42300  600.000
## 6          <NA>        14.4       2.2   0.7666667  9.60      NA    3.850
## 7            vu         8.7       1.4   0.3833333 15.30      NA   20.490
## 8          <NA>         7.0        NA          NA 17.00      NA    0.045
## 9  domesticated        10.1       2.9   0.3333333 13.90 0.07000   14.000
## 10           lc         3.0        NA          NA 21.00 0.09820   14.800
## 11           lc         5.3       0.6          NA 18.70 0.11500   33.500
## 12 domesticated         9.4       0.8   0.2166667 14.60 0.00550    0.728
## 13           lc        10.0       0.7          NA 14.00      NA    4.750
## 14 domesticated        12.5       1.5   0.1166667 11.50 0.00640    0.420
## 15           lc        10.3       2.2          NA 13.70 0.00100    0.060
## 16         <NA>         8.3       2.0          NA 15.70 0.00660    1.000
## 17           lc         9.1       1.4   0.1500000 14.90 0.00014    0.005
## 18           lc        17.4       3.1   0.3833333  6.60 0.01080    3.500
## 19           lc         5.3       0.5          NA 18.70 0.01230    2.950
## 20           lc        18.0       4.9   0.3333333  6.00 0.00630    1.700
## 21           en         3.9        NA          NA 20.10 4.60300 2547.000
## 22           lc        19.7       3.9   0.1166667  4.30 0.00030    0.023
## 23 domesticated         2.9       0.6   1.0000000 21.10 0.65500  521.000
## 24 domesticated         3.1       0.4          NA 20.90 0.41900  187.000
## 25           lc        10.1       3.5   0.2833333 13.90 0.00350    0.770
## 26           lc        10.9       1.1          NA 13.10 0.11500   10.000
## 27         <NA>        14.9        NA          NA  9.10      NA    0.071
## 28 domesticated        12.5       3.2   0.4166667 11.50 0.02560    3.300
## 29         <NA>         9.8       1.1   0.5500000 14.20 0.00500    0.200
## 30           cd         1.9       0.4          NA 22.10      NA  899.995
## 31           cd         2.7       0.1          NA 21.35      NA  800.000
## 32           lc         6.2       1.5          NA 17.80 0.32500   85.000
## 33           lc         6.3       0.6          NA 17.70 0.01227    2.625
## 34         <NA>         8.0       1.9   1.5000000 16.00 1.32000   62.000
## 35           vu         9.5       0.9          NA 14.50      NA    1.670
## 36           vu         3.3        NA          NA 20.70 5.71200 6654.000
## 37           lc        19.4       6.6          NA  4.60      NA    0.370
## 38         <NA>        10.1       1.2   0.7500000 13.90 0.17900    6.800
## 39           lc        14.2       1.9          NA  9.80      NA    0.053
## 40           en        14.3       3.1   0.2000000  9.70 0.00100    0.120
## 41         <NA>        12.8        NA          NA 11.20      NA    0.035
## 42           nt        12.5       1.4   0.1833333 11.50 0.00040    0.022
## 43         <NA>        19.9       2.0   0.2000000  4.10 0.00025    0.010
## 44           nt        14.6        NA          NA  9.40      NA    0.266
## 45         <NA>        11.0        NA          NA 13.00 0.01250    1.400
## 46           lc         7.7       0.9          NA 16.30      NA    0.210
## 47           lc        14.5        NA          NA  9.50      NA    0.028
## 48 domesticated         8.4       0.9   0.4166667 15.60 0.01210    2.500
## 49 domesticated         3.8       0.6          NA 20.20 0.17500   55.500
## 50         <NA>         9.7       1.4   1.4166667 14.30 0.44000   52.200
## 51           en        15.8        NA          NA  8.20      NA  162.564
## 52           nt        10.4        NA          NA 13.60 0.15700  100.000
## 53           vu        13.5        NA          NA 10.50      NA  161.499
## 54         <NA>         9.4       1.0   0.6666667 14.60 0.18000   25.235
## 55           lc        10.3       2.7          NA 13.70 0.00240    0.550
## 56           lc        11.0        NA          NA 13.00      NA    1.100
## 57         <NA>        11.5        NA          NA 12.50      NA    0.021
## 58         <NA>        13.7       1.8          NA 10.30 0.01140    1.620
## 59           vu         3.5       0.4          NA 20.50      NA   86.000
## 60           vu         5.6        NA          NA 18.45      NA   53.180
## 61         <NA>        11.1       1.5          NA 12.90      NA    1.100
## 62           en        18.1       6.1          NA  5.90 0.08100   60.000
## 63           lc         5.4       0.5          NA 18.60 0.02100    3.600
## 64           lc        13.0       2.4   0.1833333 11.00 0.00190    0.320
## 65         <NA>         8.7        NA          NA 15.30      NA    0.044
## 66         <NA>         9.6       1.4          NA 14.40 0.02000    0.743
## 67           lc         8.4       2.1   0.1666667 15.60 0.00120    0.075
## 68         <NA>        11.3       1.1   0.1500000 12.70 0.00118    0.148
## 69         <NA>        10.6       2.4          NA 13.40 0.00300    0.122
## 70           lc        16.6        NA          NA  7.40 0.00570    0.920
## 71           lc        13.8       3.4   0.2166667 10.20 0.00400    0.101
## 72           lc        15.9       3.0          NA  8.10      NA    0.205
## 73         <NA>        12.8       2.0   0.1833333 11.20 0.00033    0.048
## 74 domesticated         9.1       2.4   0.5000000 14.90 0.18000   86.250
## 75         <NA>         8.6        NA          NA 15.40 0.02500    4.500
## 76         <NA>        15.8        NA          NA  8.20      NA    0.112
## 77           vu         4.4       1.0   0.9000000 19.60 0.16900  207.501
## 78         <NA>        15.6       2.3          NA  8.40 0.00260    0.900
## 79         <NA>         8.9       2.6   0.2333333 15.10 0.00250    0.104
## 80         <NA>         5.2        NA          NA 18.80      NA  173.330
## 81         <NA>         6.3       1.3          NA 17.70 0.01750    2.000
## 82         <NA>        12.5        NA          NA 11.50 0.04450    3.380
## 83         <NA>         9.8       2.4   0.3500000 14.20 0.05040    4.230
```

```r
distinct(msleep, order, .keep_all= TRUE)
```

```
##                          name         genus    vore           order
## 1                     Cheetah      Acinonyx   carni       Carnivora
## 2                  Owl monkey         Aotus    omni        Primates
## 3             Mountain beaver    Aplodontia   herbi        Rodentia
## 4  Greater short-tailed shrew       Blarina    omni    Soricomorpha
## 5                         Cow           Bos   herbi    Artiodactyla
## 6            Three-toed sloth      Bradypus   herbi          Pilosa
## 7        Long-nosed armadillo       Dasypus   carni       Cingulata
## 8                  Tree hyrax   Dendrohyrax   herbi      Hyracoidea
## 9      North American Opossum     Didelphis    omni Didelphimorphia
## 10             Asian elephant       Elephas   herbi     Proboscidea
## 11              Big brown bat     Eptesicus insecti      Chiroptera
## 12                      Horse         Equus   herbi  Perissodactyla
## 13          European hedgehog     Erinaceus    omni  Erinaceomorpha
## 14                Pilot whale Globicephalus   carni         Cetacea
## 15                     Rabbit   Oryctolagus   herbi      Lagomorpha
## 16                  Phalanger     Phalanger    <NA>   Diprotodontia
## 17        Short-nosed echidna  Tachyglossus insecti     Monotremata
## 18                     Tenrec        Tenrec    omni    Afrosoricida
## 19                 Tree shrew        Tupaia    omni      Scandentia
##    conservation sleep_total sleep_rem sleep_cycle awake brainwt   bodywt
## 1            lc        12.1        NA          NA 11.90      NA   50.000
## 2          <NA>        17.0       1.8          NA  7.00 0.01550    0.480
## 3            nt        14.4       2.4          NA  9.60      NA    1.350
## 4            lc        14.9       2.3   0.1333333  9.10 0.00029    0.019
## 5  domesticated         4.0       0.7   0.6666667 20.00 0.42300  600.000
## 6          <NA>        14.4       2.2   0.7666667  9.60      NA    3.850
## 7            lc        17.4       3.1   0.3833333  6.60 0.01080    3.500
## 8            lc         5.3       0.5          NA 18.70 0.01230    2.950
## 9            lc        18.0       4.9   0.3333333  6.00 0.00630    1.700
## 10           en         3.9        NA          NA 20.10 4.60300 2547.000
## 11           lc        19.7       3.9   0.1166667  4.30 0.00030    0.023
## 12 domesticated         2.9       0.6   1.0000000 21.10 0.65500  521.000
## 13           lc        10.1       3.5   0.2833333 13.90 0.00350    0.770
## 14           cd         2.7       0.1          NA 21.35      NA  800.000
## 15 domesticated         8.4       0.9   0.4166667 15.60 0.01210    2.500
## 16         <NA>        13.7       1.8          NA 10.30 0.01140    1.620
## 17         <NA>         8.6        NA          NA 15.40 0.02500    4.500
## 18         <NA>        15.6       2.3          NA  8.40 0.00260    0.900
## 19         <NA>         8.9       2.6   0.2333333 15.10 0.00250    0.104
```

Try with using `&` or `|` to select using multiple criteria   

What about if want to select things not in that criteria?   

What about selecting rows based on containing some value? hint: grepl   
filter(msleep, grepl("Pr",order))   

What about selecting a random number of rows? (sample_n(msleep, 3))   

Or a random percentage of rows? (sample_frac(msleep, 0.1))   

#Piping

dplyr uses the pipe operator  `%>%`  which allows you to pipe output from one function to the input to another function. This means you can read functions from left to right rather than nesting functions, which can get very confusing!

Using the `.` means that you are referencing back to the data piped into the function


```r
#No piping
head(dplyr::select(msleep, name, sleep_total))
```

```
##                         name sleep_total
## 1                    Cheetah        12.1
## 2                 Owl monkey        17.0
## 3            Mountain beaver        14.4
## 4 Greater short-tailed shrew        14.9
## 5                        Cow         4.0
## 6           Three-toed sloth        14.4
```

```r
#Piping
msleep %>% 
    dplyr::select(name, sleep_total) %>% 
    head
```

```
##                         name sleep_total
## 1                    Cheetah        12.1
## 2                 Owl monkey        17.0
## 3            Mountain beaver        14.4
## 4 Greater short-tailed shrew        14.9
## 5                        Cow         4.0
## 6           Three-toed sloth        14.4
```

#More data handling options

What if you want to arrange by e.g. by date or by name?


```r
msleep %>% arrange(order) %>% head
```

```
##       name     genus  vore        order conservation sleep_total sleep_rem
## 1   Tenrec    Tenrec  omni Afrosoricida         <NA>        15.6       2.3
## 2      Cow       Bos herbi Artiodactyla domesticated         4.0       0.7
## 3 Roe deer Capreolus herbi Artiodactyla           lc         3.0        NA
## 4     Goat     Capri herbi Artiodactyla           lc         5.3       0.6
## 5  Giraffe   Giraffa herbi Artiodactyla           cd         1.9       0.4
## 6    Sheep      Ovis herbi Artiodactyla domesticated         3.8       0.6
##   sleep_cycle awake brainwt  bodywt
## 1          NA   8.4  0.0026   0.900
## 2   0.6666667  20.0  0.4230 600.000
## 3          NA  21.0  0.0982  14.800
## 4          NA  18.7  0.1150  33.500
## 5          NA  22.1      NA 899.995
## 6          NA  20.2  0.1750  55.500
```

```r
#more complex

msleep %>% 
    dplyr::select(name, order, sleep_total) %>%
    arrange(order, sleep_total) %>% 
    head
```

```
##       name        order sleep_total
## 1   Tenrec Afrosoricida        15.6
## 2  Giraffe Artiodactyla         1.9
## 3 Roe deer Artiodactyla         3.0
## 4    Sheep Artiodactyla         3.8
## 5      Cow Artiodactyla         4.0
## 6     Goat Artiodactyla         5.3
```

```r
msleep %>% 
    dplyr::select(name, order, sleep_total) %>%
    arrange(order, sleep_total) %>% 
    filter(sleep_total >= 16)
```

```
##                     name           order sleep_total
## 1          Big brown bat      Chiroptera        19.7
## 2       Little brown bat      Chiroptera        19.9
## 3   Long-nosed armadillo       Cingulata        17.4
## 4        Giant armadillo       Cingulata        18.1
## 5 North American Opossum Didelphimorphia        18.0
## 6   Thick-tailed opposum Didelphimorphia        19.4
## 7             Owl monkey        Primates        17.0
## 8 Arctic ground squirrel        Rodentia        16.6
```

What if you want to arrange by multiple variables? arrange(msleep, vore, conservation)   
Or one ascending / one descending? arrange(msleep, desc(vore), conservation)   

#Summarising

used to summarise data. Can involve base functions, own functions defined within the pipe / function or own functions defined outside the operation


```r
msleep%>%summarise(Mean_ST = mean(sleep_total), Med_ST = median(sleep_total))
```

```
##    Mean_ST Med_ST
## 1 10.43373   10.1
```

```r
#summarise at - allows summary by partiuclar variables

msleep%>%summarise_at(vars(sleep_cycle, sleep_total), funs(n(), mean, median))
```

```
##   sleep_cycle_n sleep_total_n sleep_cycle_mean sleep_total_mean
## 1            83            83               NA         10.43373
##   sleep_cycle_median sleep_total_median
## 1                 NA               10.1
```

```r
#play with other summarise variables

#e.g. including custom functions
summarise_at(msleep,vars(sleep_cycle,sleep_total), function(x) var(x - mean(x)))
```

```
##   sleep_cycle sleep_total
## 1          NA    19.80568
```

What if you want to summarise all of the numeric variables? summarise_if(msleep, is.numeric, funs(n(),mean,median))   

WHat if you want to summarise by factors? E.g check the number of levels / categories and count the missing observations: summarise_all(msleep["vore"], funs(nlevels(.), nmiss=sum(is.na(.))))   

#Add new columns - Mutate

The `mutate` function adds new columns to the dataframe   


```r
msleep %>% 
    mutate(rem_proportion = sleep_rem / sleep_total) %>%
    head
```

```
##                         name      genus  vore        order conservation
## 1                    Cheetah   Acinonyx carni    Carnivora           lc
## 2                 Owl monkey      Aotus  omni     Primates         <NA>
## 3            Mountain beaver Aplodontia herbi     Rodentia           nt
## 4 Greater short-tailed shrew    Blarina  omni Soricomorpha           lc
## 5                        Cow        Bos herbi Artiodactyla domesticated
## 6           Three-toed sloth   Bradypus herbi       Pilosa         <NA>
##   sleep_total sleep_rem sleep_cycle awake brainwt  bodywt rem_proportion
## 1        12.1        NA          NA  11.9      NA  50.000             NA
## 2        17.0       1.8          NA   7.0 0.01550   0.480      0.1058824
## 3        14.4       2.4          NA   9.6      NA   1.350      0.1666667
## 4        14.9       2.3   0.1333333   9.1 0.00029   0.019      0.1543624
## 5         4.0       0.7   0.6666667  20.0 0.42300 600.000      0.1750000
## 6        14.4       2.2   0.7666667   9.6      NA   3.850      0.1527778
```

```r
#add more than one
msleep %>% 
    mutate(rem_proportion = sleep_rem / sleep_total, 
           bodywt_grams = bodywt * 1000) %>%
    head
```

```
##                         name      genus  vore        order conservation
## 1                    Cheetah   Acinonyx carni    Carnivora           lc
## 2                 Owl monkey      Aotus  omni     Primates         <NA>
## 3            Mountain beaver Aplodontia herbi     Rodentia           nt
## 4 Greater short-tailed shrew    Blarina  omni Soricomorpha           lc
## 5                        Cow        Bos herbi Artiodactyla domesticated
## 6           Three-toed sloth   Bradypus herbi       Pilosa         <NA>
##   sleep_total sleep_rem sleep_cycle awake brainwt  bodywt rem_proportion
## 1        12.1        NA          NA  11.9      NA  50.000             NA
## 2        17.0       1.8          NA   7.0 0.01550   0.480      0.1058824
## 3        14.4       2.4          NA   9.6      NA   1.350      0.1666667
## 4        14.9       2.3   0.1333333   9.1 0.00029   0.019      0.1543624
## 5         4.0       0.7   0.6666667  20.0 0.42300 600.000      0.1750000
## 6        14.4       2.2   0.7666667   9.6      NA   3.850      0.1527778
##   bodywt_grams
## 1        50000
## 2          480
## 3         1350
## 4           19
## 5       600000
## 6         3850
```

What if you want to apply a function to all the columns? (mutate_all(msleep, funs("new" = .* 1000)))   

Investigate the other functions of mutate using the tab    

#Grouping and summarising

Piping comes into its when you want to group by a particular variable then summarise   


```r
msleep %>% 
    group_by(order) %>%
    summarise(avg_sleep = mean(sleep_total), 
              min_sleep = min(sleep_total), 
              max_sleep = max(sleep_total),
              total = n())
```

```
## # A tibble: 19 x 5
##    order           avg_sleep min_sleep max_sleep total
##    <fct>               <dbl>     <dbl>     <dbl> <int>
##  1 Afrosoricida        15.6      15.6      15.6      1
##  2 Artiodactyla         4.52      1.90      9.10     6
##  3 Carnivora           10.1       3.50     15.8     12
##  4 Cetacea              4.50      2.70      5.60     3
##  5 Chiroptera          19.8      19.7      19.9      2
##  6 Cingulata           17.8      17.4      18.1      2
##  7 Didelphimorphia     18.7      18.0      19.4      2
##  8 Diprotodontia       12.4      11.1      13.7      2
##  9 Erinaceomorpha      10.2      10.1      10.3      2
## 10 Hyracoidea           5.67      5.30      6.30     3
## 11 Lagomorpha           8.40      8.40      8.40     1
## 12 Monotremata          8.60      8.60      8.60     1
## 13 Perissodactyla       3.47      2.90      4.40     3
## 14 Pilosa              14.4      14.4      14.4      1
## 15 Primates            10.5       8.00     17.0     12
## 16 Proboscidea          3.60      3.30      3.90     2
## 17 Rodentia            12.5       7.00     16.6     22
## 18 Scandentia           8.90      8.90      8.90     1
## 19 Soricomorpha        11.1       8.40     14.9      5
```

```r
#how would you sort this by max sleep?
#What about if there are nas? 
#OR
msleep %>% group_by(vore) %>%
  summarise_at(vars(sleep_cycle:sleep_total), funs(n(), mean(., na.rm = TRUE)))
```

```
## # A tibble: 5 x 7
##   vore    sleep_cycle_n sleep_rem_n sleep_total_n sleep_cycle_mean
##   <fct>           <int>       <int>         <int>            <dbl>
## 1 carni              19          19            19            0.373
## 2 herbi              32          32            32            0.418
## 3 insecti             5           5             5            0.161
## 4 omni               20          20            20            0.592
## 5 <NA>                7           7             7            0.183
## # ... with 2 more variables: sleep_rem_mean <dbl>, sleep_total_mean <dbl>
```

#Do function

Function allows you to compute within groups   


```r
#eg get the top two rows from "cd", "nt" and "en" categories of conservation
msleep %>% filter(conservation%in% c("cd", "nt","en")) %>% group_by(conservation) %>%
  do(head( . , 2))
```

```
## # A tibble: 6 x 11
## # Groups:   conservation [3]
##   name   genus  vore  order conservation sleep_total sleep_rem sleep_cycle
##   <fct>  <fct>  <fct> <fct> <fct>              <dbl>     <dbl>       <dbl>
## 1 Giraf~ Giraf~ herbi Arti~ cd                  1.90     0.400      NA    
## 2 Pilot~ Globi~ carni Ceta~ cd                  2.70     0.100      NA    
## 3 Asian~ Eleph~ herbi Prob~ en                  3.90    NA          NA    
## 4 Golde~ Mesoc~ herbi Rode~ en                 14.3      3.10        0.200
## 5 Mount~ Aplod~ herbi Rode~ nt                 14.4      2.40       NA    
## 6 House~ Mus    herbi Rode~ nt                 12.5      1.40        0.183
## # ... with 3 more variables: awake <dbl>, brainwt <dbl>, bodywt <dbl>
```

```r
#Slice is used to select 
msleep %>% dplyr::select(conservation,sleep_total) %>%
  filter(conservation %in% c("cd", "nt","en")) %>%
  group_by(conservation) %>%
  do(arrange(.,desc(sleep_total))) %>%  slice(3)
```

```
## # A tibble: 2 x 2
## # Groups:   conservation [2]
##   conservation sleep_total
##   <fct>              <dbl>
## 1 en                  14.3
## 2 nt                  12.5
```

```r
#slice means select the third row
```


#Join functions

This allows you to merge datasets together (like merge function)

1. Make a new dataframe by selecting the name columns of msleep, and creating a new dataframe.    

2. Create new columns either from calculating new variables for sleep or sample from a distribution   

3. Try out some of the join functions   

4. Try removing some of the rows from one or both datasets (msleep and the new one) and playing with `left_join` and `right_join`   

Check the help to see what other join functions there are   

#Vertical combinations

Using `intersect` (rows that appear in both x and y)   
`union` (rows that appear in either or both x and y)  
`setdiff` (rows that appear in x but not y)   


```r
#set up data for example
mtcars$model <- rownames(mtcars)
first <- mtcars[1:20, ]
second <- mtcars[10:32, ]

#intersect selects the unique rows common to both datasets
intersect(first, second)
```

```
##     mpg cyl  disp  hp drat    wt  qsec vs am gear carb               model
## 1  19.2   6 167.6 123 3.92 3.440 18.30  1  0    4    4            Merc 280
## 2  17.8   6 167.6 123 3.92 3.440 18.90  1  0    4    4           Merc 280C
## 3  16.4   8 275.8 180 3.07 4.070 17.40  0  0    3    3          Merc 450SE
## 4  17.3   8 275.8 180 3.07 3.730 17.60  0  0    3    3          Merc 450SL
## 5  15.2   8 275.8 180 3.07 3.780 18.00  0  0    3    3         Merc 450SLC
## 6  10.4   8 472.0 205 2.93 5.250 17.98  0  0    3    4  Cadillac Fleetwood
## 7  10.4   8 460.0 215 3.00 5.424 17.82  0  0    3    4 Lincoln Continental
## 8  14.7   8 440.0 230 3.23 5.345 17.42  0  0    3    4   Chrysler Imperial
## 9  32.4   4  78.7  66 4.08 2.200 19.47  1  1    4    1            Fiat 128
## 10 30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2         Honda Civic
## 11 33.9   4  71.1  65 4.22 1.835 19.90  1  1    4    1      Toyota Corolla
```


#Other useful functions

1. `bind_rows` and `bind_cols` work in the same way as `rbind` and `cbind`, and can be used within piping (nb so can `rbind` and `cbind`)   

2. rowwise() allows you to apply a function across rows (useful for supporting complex operations to be applied to each row)   

3. Percentiles eg quantile()   



```r
msleep %>% group_by(order) %>%
  summarise(Pecentile_25=quantile(sleep_total, probs=0.25),
            Pecentile_50=quantile(sleep_total, probs=0.5),
            Pecentile_75=quantile(sleep_total, probs=0.75),
            Pecentile_99=quantile(sleep_total, probs=0.99))
```

```
## # A tibble: 19 x 5
##    order           Pecentile_25 Pecentile_50 Pecentile_75 Pecentile_99
##    <fct>                  <dbl>        <dbl>        <dbl>        <dbl>
##  1 Afrosoricida           15.6         15.6         15.6         15.6 
##  2 Artiodactyla            3.20         3.90         4.97         8.91
##  3 Carnivora               8.10        10.2         12.5         15.5 
##  4 Cetacea                 3.95         5.20         5.40         5.59
##  5 Chiroptera             19.8         19.8         19.8         19.9 
##  6 Cingulata              17.6         17.8         17.9         18.1 
##  7 Didelphimorphia        18.4         18.7         19.0         19.4 
##  8 Diprotodontia          11.8         12.4         13.0         13.7 
##  9 Erinaceomorpha         10.1         10.2         10.2         10.3 
## 10 Hyracoidea              5.35         5.40         5.85         6.28
## 11 Lagomorpha              8.40         8.40         8.40         8.40
## 12 Monotremata             8.60         8.60         8.60         8.60
## 13 Perissodactyla          3.00         3.10         3.75         4.37
## 14 Pilosa                 14.4         14.4         14.4         14.4 
## 15 Primates                9.57         9.90        10.9         16.3 
## 16 Proboscidea             3.45         3.60         3.75         3.89
## 17 Rodentia               10.8         12.9         14.5         16.5 
## 18 Scandentia              8.90         8.90         8.90         8.90
## 19 Soricomorpha            9.10        10.3         12.8         14.8
```

```r
#throw in a plot

msleep %>% group_by(order) %>%
  summarise(Percentile_25=quantile(sleep_total, probs=0.25),
            Percentile_50=quantile(sleep_total, probs=0.5),
            Percentile_75=quantile(sleep_total, probs=0.75),
            Percentile_99=quantile(sleep_total, probs=0.99)) %>%
  ggplot(.)+geom_bar(aes(order, Percentile_25), stat = "identity")+theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

![](dplyr_tutorial_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

#Automate model building

Using `do` function you can automate model building


```r
by_cyl <- group_by(mtcars, cyl)
models <- by_cyl %>% do(mod = lm(mpg ~ disp, data = .))
summarise(models, rsq = summary(mod)$r.squared)
```

```
## # A tibble: 3 x 1
##      rsq
##    <dbl>
## 1 0.648 
## 2 0.0106
## 3 0.270
```

```r
models %>% do(data.frame(
  var = names(coef(.$mod)),
  coef(summary(.$mod)))
)                                         
```

```
## Source: local data frame [6 x 5]
## Groups: <by row>
## 
## # A tibble: 6 x 5
##   var          Estimate Std..Error t.value   Pr...t..
## * <fct>           <dbl>      <dbl>   <dbl>      <dbl>
## 1 (Intercept)  40.9        3.59     11.4   0.00000120
## 2 disp        - 0.135      0.0332  - 4.07  0.00278   
## 3 (Intercept)  19.1        2.91      6.55  0.00124   
## 4 disp          0.00361    0.0156    0.232 0.826     
## 5 (Intercept)  22.0        3.35      6.59  0.0000259 
## 6 disp        - 0.0196     0.00932 - 2.11  0.0568
```



#If family of functions

These come into play with `mutate`, `summarise` etc when a condition is met   

e.g. `select_if`, `summarise_if`, `mutate_if`, `na_if` (this converts all given values to na, eg if you want to find "" or have an indicator value as a null)   

```r
msleep1 = mutate_if(msleep, is.numeric, funs("new" = .* 1000))
```

