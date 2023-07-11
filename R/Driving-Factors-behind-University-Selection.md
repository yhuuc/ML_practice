## Driving Factors behind University Selection

Dataset source:
<https://www.studyinaustralia.gov.au/English/Australian-Education/Universities-Higher-Education/University-rankings>

    library(readxl)
    HE <- read.csv("HE Rankings updated.csv")

### Introduction

Tertiary international students are one of the major contributors to
Australia and New Zealand national economy. Especially that Australia is
a country which celebrates cultural diversity with 30% immigration
population (Australian Bureau of Statistics 2022). It is reported that
the source of more than half of the revenue of the University and Sydney
and the University of NSW is gained from international students (McGowan
2018). Aside from financial gain, further benefits for Australia and New
Zealand include accelerated regional development, created job
opportunities and skilled immigrants with tertiary education.
Unfortunately, due to Covid-19 pandemic and its related restrictions,
this financial source has decreased substantially in the past three
years (The University of Sydney 2020). Presence is a time for national
post-pandemic rehabilitation. There is an urgent need to attract back
international students. Systematic research has been conducted on the
reasons behind university choice of international students (Mazzarol &
Soutar 2002). However, studies focusing on pacific countries are
lacking. This report collects and analyzes data regarding international
students enrollment into Australia and New Zealand universities. The
first part overlooks the situation of international student enrollment
in general using Five Number Summary on university student enrollment
rates in both countries as a whole. The second part proceeds to
investigate the correlation and possible causation between enrollment
rate and several potential driving factors, such as university ranking.

### International Student University Enrollment Overview

The total international student enrollment number in Australia and New
Zealand is described using five-number summary and visualized with
boxplot. The information of universities with lowest and highest
enrollments are separately displayed.

    enrollment=HE$Uni.enrolment
    summary(enrollment)

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##    2695   22807   32948   33938   45580   64479

    HE[HE$Uni.enrolment==min(enrollment),]

    ##   World.Ranking    University.Name     Country International.Student.Ranking
    ## 9           356 Lincoln University New Zealand                            43
    ##   Population.of.host.city Uni.enrolment
    ## 9                  624200          2695

    HE[HE$Uni.enrolment==max(enrollment),]

    ##   World.Ranking   University.Name   Country International.Student.Ranking
    ## 4            58 Monash University Australia                            27
    ##   Population.of.host.city Uni.enrolment
    ## 4           5.078 million         64479

    boxplot(enrollment,
            main="Total International Students' Enrollment into 
            Australia and New Zealand Universities",
            at = c(1),
            names = c("Enrollment"),
            las = 1,
            border = "brown",
            horizontal = FALSE,
            notch = FALSE
    )

![](https://github.com/yhuuc/data_analysis/blob/main/R/7.png)
As the five-number summary shows, the minimum and maximum enrollments
are 2695 for Lincoln University in New Zealand and 64479 for Monash
University in Australia. On average, there are 33938 international
students enrolled in listed universities. Half of the universities have
enrollment counts between 22807 and 45580. The most common number that
appeared is 32948. The university with the least enrollment is based in
New Zealand while the one with highest enrollment is in Australia,
suggesting that tertiary education quality discrepancy between the two
countries as perceived by international students exists. This is also
supported by the difference in the two nations’ income from
international students.In Australia, the export income produced by
international students increased every year and rose up to 37.6 billion
AUD in 2019 (Statista 2022). In New Zealand in 2018, the annual profit
from international education is 5.1 billion NZD (Hipkins 2018).
Additionally, according to the boxplot, there is no outliers, which
means the admission number stays fairly stable among different
universities in Australia and New Zealand. However, this could also
indicate a selection bias such that only tertiary education institutions
up to a certain standard are included in the original dataset.

### Evaluating Driving Factors

The primary purpose of this section is to find connections between
admission numbers and likely influential factors, including nation,
university QR ranking and International Student Ranking. Possible
correlation amongst these factors themselves are also explored.

#### Nation

    country=HE$Country

    c_enroll <- c(sum(HE[which(HE$Country=="Australia"),"Uni.enrolment"]),sum(HE[which(HE$Country=="New Zealand"),"Uni.enrolment"]))

    options(scipen=999)
    barplot(c_enroll,
            names.arg = c("Australia","New Zealand"),
            main="Enrollment vs. Country",
            ylab="Enrollment",)

![](https://github.com/yhuuc/data_analysis/blob/main/R/8.png)
The tertiary student enrollments in Australia and New Zealand are
calculated and compared using a bar graph. Noticeably, the enrollment
number in Australia is several times higher than that in New Zealand.
Country can be therefore considered as a strong driving factor behind
international students’ choice on university.

#### Ranking: QR World Ranking vs. International Student Ranking

    library(ggpubr)

    ## Loading required package: ggplot2

    ggscatter(HE,x="World.Ranking",y="Uni.enrolment",add = "reg.line",cor.coef = TRUE)

    ## `geom_smooth()` using formula 'y ~ x'

![](https://github.com/yhuuc/data_analysis/blob/main/R/9.png)

    ggscatter(HE,x="International.Student.Ranking",y="Uni.enrolment",add = "reg.line",cor.coef = TRUE)

    ## `geom_smooth()` using formula 'y ~ x'

![](https://github.com/yhuuc/data_analysis/blob/main/R/10.png)
The QR World Ranking and International Student Ranking are tested in
their correlation with enrollment rate. As demonstrated in the graphs,
the QR ranking as a negative 0.43 correlation, which means the lower the
ranking (larger the number), the fewer the enrollments. However, the
International Student Ranking and enrollment do not show any
correlation, indicated by a coefficient of -0.00024. In one sentence,
the QR Ranking is successful in predicting international student
university choice while the International Student Ranking is not. This
difference could be contributed to low popularity and awareness of the
International Student Ranking.

#### country vs. ranking

It is suspected that there is a causal relationship between education
quality (university ranking) and country selection. Higher ranking could
drive more students to choose this country. Since in last section it was
shown that only QR ranking has prediction power, it is used to test
correlation between ranking and country. Because it was calculated that
Australia attracts more international student, it is predicted to reside
more universities with higher QR World ranking.

university number in each country 33 uni, 8 new, 25 aus

    c1=which(country=='Australia')
    c2=which(country=='New Zealand')
    aus=HE[c1,]
    nz=HE[c2,]
    aus_rank=aus$World.Ranking
    nz_rank=nz$World.Ranking

    boxplot(aus_rank,nz_rank,
            main="QR World Ranking of Australia vs. New Zealand Universities",
            at = c(1,2),
            names = c("Australia", "New 
                      Zealand"),
            las = 2,
            col = c("orange","red"),
            border = "brown",
            horizontal = TRUE,
            notch = FALSE
    )

![](https://github.com/yhuuc/data_analysis/blob/main/R/11.png)
A side by side boxplot is chosen to present the difference in university
rankings between two countries. The result matches previous prediction
that Australia universities have higher QR ranking, as the minimum and
first quartile of Australia universities are much higher than those of
New Zealand universities. This could be one of the explanations as to
why Australia is favored by more international university students.

### Conclusion

The analysis on data supports that both destination country and QR World
ranking are strong driving factors behind internaitonal tertiary
students’ choice on university, while the International Student ranking
is not as relevant, possibly due to low awareness and reputation.
Therefore, countries that attracted large amount of international
students previous to the Covid-19 pandemic are likely to remain their
advantage and gain back the lost students. At the mean time,
universities that achieve higher education quality and are able to
obtain higher ranking on world-renowned university ranking systems can
also gain more favoring among international students.

### References

Australian Bureau of Statistics 2022, Australia’s overseas-born
population drops during pandemic, ABS, viewed 20 September 2022,
<https://www.abs.gov.au/media-centre/media-releases/australias-overseas-born-population-drops-during-pandemic>.

Hipkins, HC 2018, International education contributes $5.1 billion to
New Zealand economy,Beehive.govt.nz,viewed 20 September 2022, <a
href="https://www.beehive.govt.nz/release/international-education-contributes-51-billion-new-zealand-economy#:\~:text=%E2%80%9C%244.8%20billion%20is%20attributed%20to,only%20nationally%20but%20also%20regionally."
class="uri">https://www.beehive.govt.nz/release/international-education-contributes-51-billion-new-zealand-economy#:\~:text=%E2%80%9C%244.8%20billion%20is%20attributed%20to,only%20nationally%20but%20also%20regionally.</a>.

Mazzarol, T & Soutar, GN 2002, “Push‐pull” factors influencing
international student destination choice, International Journal of
Educational Management, vol. 16, no. 2, pp. 82-90,
<https://doi.org/10.1108/09513540210418403>.

McGowan, M 2018, Australian universities slip in world rankings amid
funding cuts, The Guardian, viewed 20 September 2022,
<https://www.theguardian.com/australia-news/2018/jun/09/universities-rely-too-much-on-foreign-student-fees-auditor-says>.

Statista 2022, Export income from international education activity in
Australia from financial years 2013 to 2019, viewed 20 September 2022,
<a
href="https://www.statista.com/statistics/977688/australia-export-income-from-international-education-services/#:\~:text=From%202013%20to%202019%20the,billion%20Australian%20dollars%20in%202019"
class="uri">https://www.statista.com/statistics/977688/australia-export-income-from-international-education-services/#:\~:text=From%202013%20to%202019%20the,billion%20Australian%20dollars%20in%202019</a>.

The University of Sydney 2020, How universities came to rely on
international students, viewed 20 September 2022,
<https://www.sydney.edu.au/news-opinion/news/2020/05/25/how-universities-came-to-rely-on-international-students.html>.
