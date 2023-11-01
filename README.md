Description:
functions have been written entirely in base R; the only exception being the getTDS() function, which uses the median() function from the stats package (although we may re-write this at some point). All other packages listed under “Suggests” in the DESCRIPTION file only serve to illustrate the use of QDiabetes in examples or vignettes, or in testing the package. The primary factor limiting the package’s compatibility with older versions of R is the data storage method CRAN require us to use for the data frame backend of the getTDS() function. Owing to the memory footprint of this object (≈200MB), we need to make use of XZ compression to reduce the overall size of the package as much as possible. XZ compression was first implemented in R version 2.10.

Note
Many of the default values used in the risk prediction functions of this package were selected to be representative of a UK population. These values are only intended to minimise the amount of typing required when using the risk prediction functions in an exploratory manner. They are unlikely to be useful in a research setting, and you would need to know the exact values to assign to all function parameters in order to make an accurate risk prediction. Hence, while you can get risk predictions from the QDR2013() and QDR2018A() functions through the specification of only sex, age, and bmi, you would be assuming White or missing ethnicity, non-smoking status, a Townsend deprivation score of 0, and the complete absence of any relevant medical history/conditions and concomitant drug therapies. In the case of QDR2013(), you would also be assuming that a 10-year risk window is desired.

Examples
Below are some very simple examples using the QDiabetes package. Note that for convenience, either BMI [bmi] or height [ht] and weight [wt] may be specified in any of the risk prediction functions in this package.

In the interest of making life a little easier, a getTDS() helper function has been added to the package, which uses a lookup table to obtain Townsend deprivation scores from full or partial UK postcodes.

### Load Package Namespace ###
library(QDiabetes)

### Simple Usage ###
QDR2013(sex = "Female", age = 35, bmi = 25)
# [1] 0.6324508
QDR2018A(sex = "Male", age = 45, bmi = 35)
# [1] 9.88593
QDR2018B(sex = "Female", age = 65, bmi = 30, fpg = 6)
# [1] 18.43691
QDR2018C(sex = "Male", age = 25, bmi = 40, hba1c = 42)
# [1] 8.226301

### Making Use of the getTDS() Helper Function ###
getTDS("OX2 6GG")
# [1] 2.022583
QDR2013(sex = "Female", age = 41, ht = 1.65, wt = 60, tds = getTDS("OX3 9DU"))
# [1] 0.5004499
QDR2018A(sex = "Male", age = 33, bmi = 26, tds = getTDS("OX3 7LF"))
# [1] 0.6472644

### Making Use of Vectorisation ###
getTDS(c("OX3 7LF", "OX2 6NW", "OX2 6GG", "OX1 4AR"))
#    OX37LF    OX26NW    OX26GG    OX14AR
# -1.032394  1.640422  2.022583  2.309777
QDR2013(sex = "Female", age = 35, bmi = seq(20, 40, 5))
#        20        25        30        35        40
# 0.1801226 0.6324508 1.7885233 3.8983187 6.2964702
QDR2018A(sex = "Female", age = seq(25, 75, 10), bmi = 35)
#       25        35        45        55        65        75
# 1.085179  2.921454  5.893499  9.082108 10.713717  9.567516
QDR2018B(sex = "Male", age = 65, bmi = 35, fpg = 2:6)
#         2          3          4          5          6
# 0.9123063  0.5911511  1.8416081  7.8554831 30.8096968
QDR2018C(sex = "Female", age = 80, bmi = 28, hba1c = seq(15, 45, 5))
#          15           20           25           30           35           40           45
# 0.008084487  0.033019655  0.121238952  0.412396004  1.320727239  4.005759509 11.409509026

### Data Frame Usage ###
data(dat_qdr) # Synthetic sample data

## Using base R ##
dat_qdr[["risk"]] <- with(dat_qdr, QDR2013(sex = sex,
                                           age = age,
                                           ht = ht,
                                           wt = wt,
                                           ethn = ethn,
                                           smoke = smoke,
                                           tds = tds,
                                           htn = htn,
                                           cvd = cvd,
                                           ster = ster))

## Using dplyr ##
library(dplyr)
df_qdr <- as_tibble(dat_qdr)
df_qdr <- df_qdr %>%
  mutate(risk = QDR2013(sex = sex,
                        age = age,
                        ht = ht,
                        wt = wt,
                        ethn = ethn,
                        smoke = smoke,
                        tds = tds,
                        htn = htn,
                        cvd = cvd,
                        ster = ster))

## Using data.table ##
library(data.table)
dt_qdr <- as.data.table(dat_qdr)
dt_qdr[, risk := QDR2013(sex = sex,
                         age = age,
                         ht = ht,
                         wt = wt,
                         ethn = ethn,
                         smoke = smoke,
                         tds = tds,
                         htn = htn,
                         cvd = cvd,
                         ster = ster)]
