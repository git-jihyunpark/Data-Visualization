# [R] Data Visualization

🗓️ **Date**: 2024.09.05 ~ 2024.12.18

<br/>

📊 **Objective**
 1. (Communication) Utilize data visualization as an effective tool for communication and enhance written communication skills.
 2. (How to render) Learn the skills necessary to implement desired visualization objects (visualization products).
 3. (What to render) Acquire the ability to define and design visualization products that align with communication objectives, based on an understanding of data structures and characteristics.
<br/>

📒 **Materials**
  - 바바라 민토 논리의 기술 (2019), 바바라 민토 
  - R 을 활용한 데이터 과학 (2019), 해들리 위컴, 개럿 그롤문드 https://r4ds.had.co.nz/
  - Visualization Analysis and Design (2014), Tamara Munzner, CRC
Press. https://www.cs.ubc.ca/~tmm/vadbook/
  - ggplot2 - R 로 분석한 데이터를 멋진 그래픽으로 (2018), 해들리
위컴 저/박진수 역 https://ggplot2-book.org/
  - R Markdown: The Definitive Guide (2018), Yihui Xie, J. J. Allaire,
Garrett Grolemund, Chapman & Hall/CRC. https://bookdown.org/yihui/rmarkdown/
  - The Visual Display of Quantitative Information (2001), Edward
R. Tufte, Graphics Press.
<br/>

🧩 **Table of Contents**
|Num|Content|
|---|-------|
|01|[Markdown](https://github.com/git-jihyunpark/Data-Visualization/blob/main/VS_HW01.Rmd)|
|02|[Basic Static Analysis](https://github.com/git-jihyunpark/Data-Visualization/blob/main/VS_HW02.Rmd)|
|03|[Data Transformation](https://github.com/git-jihyunpark/Data-Visualization/blob/main/VS_HW03.Rmd)|
|04|[ggplot() (1)](https://github.com/git-jihyunpark/Data-Visualization/blob/main/VS_HW04.Rmd)|
|05|[3 Frameworks](https://github.com/git-jihyunpark/Data-Visualization/blob/main/VS_HW05.Rmd)|
|06|[ggplot() (2)](https://github.com/git-jihyunpark/Data-Visualization/blob/main/VS_HW06.Rmd)|
|07|[Improving Bad Visualization](https://github.com/git-jihyunpark/Data-Visualization/blob/main/VS_Final.Rmd)|
<br/>
<br/>



## 🔷 Final Project: Improving Bad Visualization

📌 **Introduction**
- Identify a bad visualization and discuss why it represents a poor visualization design.
- Propose an alternative good visualization and explain why it is a better design.
<br/>
<br/>

👎 **Bad Visualization**
- Title: 2023 OTT Market – Number of Users and Usage Time
- Reason for Selection:
  - The number of users and usage time are presented in separate graphs, making it difficult to compare them comprehensively.
  - While rankings can be identified, the use of area-based visualization makes it hard to compare user numbers or usage time across OTT apps.
  - The basis for classifying the OTT market structure into a “1-strong, 2-mid, 2-weak” system is unclear.
- Source: [Mad Times (2023), 신성수 기자, "우리나라 OTT 시장은 1강 - 2중 - 2약 체제", 2023.09.13](https://www.madtimes.org/news/articleView.html?idxno=18724)

<br/>

<img width="600" height="337" alt="image" src="https://github.com/user-attachments/assets/ab1db439-bd3e-4559-a3b0-7c7291408f38" /> <br/>

<br/>
<br/> 

  
👍 **Good Visualization**

**1) Data Define**
- X-axis: Usage time, Y-axis: Number of users
- Based on zero, the horizontal axis represents usage time and the vertical axis represents the number of users.
- The area (usage time × number of users) provides additional information about the total usage volume.

```r
# Load Library 
library(ggplot2)
library(dplyr)
library(scales)


# Data Define
data_combined <- data.frame(
  OTT = c("Disney+", "Wave", "TVING", "Coupang Play", "Netflix"),
  Users = c(270, 439, 540, 563, 1223),        # 단위: 만 명
  UsageTime = c(915, 1827, 4536, 4492, 10000) # 단위: 만 시간
)

colors <- c("Netflix" = "#FF5733",   
            "TVING" = "#A020F0",    
            "Coupang Play" = "#3357FF",  
            "Wave" = "#33FF57",      
            "Disney+" = "#FFC300")

legend_order <- c("Netflix", "TVING", "Coupang Play", "Wave", "Disney+")

data_combined <- data_combined %>%
  mutate(
    label_x = c(915 * 1.2, 1827 * 1.0, 4536 * 1.2, 4492 * 0.7, 10000 * 1.1), # 각 UsageTime에 따른 x 좌표
    label_y = c(270 * 0.6, 439 * 0.9, 540 * 0.8, 563 * 1.2, 1223 * 1.1)      # 각 Users에 따른 y 좌표
  )

mean_users <- mean(data_combined$Users)
mean_usage <- mean(data_combined$UsageTime)
```
<br/>
<br/>

**2) Visualization**
- Added labels
- Added reference lines for average number of users and average usage time

```r
# 3. Visualization
ggplot(data_combined) +
  geom_rect(aes(
    xmin = 0,                      # x축 시작
    xmax = UsageTime,              # x축 끝
    ymin = 0,                      # y축 시작
    ymax = Users,                  # y축 끝
    fill = OTT,                    
    color = OTT                    
  ), alpha = 0.05, size = 0.5) +
  # x축 평균값
  geom_vline(xintercept = mean_usage, linetype = "dotted", color = "gray", size = 1) + 
  # y축 평균값 
  geom_hline(yintercept = mean_users, linetype = "dotted", color = "gray", size = 1) +   
  geom_text(aes(
    x = mean_usage - 550, 
    y = max(data_combined$Users) + 50, 
    label = paste0("Avg.사용 시간 ", comma(round(mean_usage, 1)))
  ), color = "gray60", size = 3, hjust = 0.5, vjust = 0) +
  geom_text(aes(
    x = max(data_combined$UsageTime) - 200, 
    y = mean_users + 50, 
    label = paste0("Avg.사용자 수 ", comma(round(mean_users, 1)))
  ), color = "gray60", size = 3, hjust = 0, vjust = -0.5) +
  # 레이블 추가
  geom_text(aes(
    x = label_x,  # 지정된 x 좌표
    y = label_y,  # 지정된 y 좌표
    label = paste0("[", OTT, "]\n 사용시간", comma(UsageTime), "\n 사용자 수", comma(Users))
  ), color = "black", size = 3, lineheight = 0.9) +
  scale_fill_manual(
    values = colors,
    breaks = legend_order
  ) +
  scale_fill_manual(
    values = colors,           
    breaks = legend_order      
  ) +
  scale_color_manual(
    values = colors,           
    breaks = legend_order     
  ) +
  # x축 범위
  scale_x_continuous(labels = comma, limits = c(0, 12000)) +  
  # y축 범위
  scale_y_continuous(
    labels = comma,
    limits = c(0, 1500),                                     
    breaks = seq(0, 1500, 500)   
  ) +
  # 레이블
  labs(
    title = "2023년 8월 주요 OTT앱 시장 분석", 
    subtitle = "사용 시간 & 사용자 수 모두 1강-2중-2약 체제",  
    x = "사용 시간 (단위: 만 시간)",                
    y = "사용자 수 (단위: 만 명)"                   
  ) +
  theme_minimal() +
  theme(
    legend.title = element_blank(),
    legend.position = "right",
    plot.title = element_text(hjust = 0.5, face = "bold"), 
    plot.subtitle = element_text(hjust = 0.5), 
    axis.title.x = element_text(face = "plain"),           
    axis.title.y = element_text(face = "plain")           
  )

ggsave("final_ggplot.jpg",dpi = 300)
```
<br/>
<br/>

- Reason for Improvement:
  - Set the x-axis as usage time and the y-axis as number of users, allowing both variables to be examined within a single graph.
  - With zero as the baseline, the horizontal axis represents usage time and the vertical axis represents number of users, while the area indicates the total usage volume for each OTT service.
  - By including reference lines for the average number of users and average usage time, the visualization enables clear distinction among the “1-strong, 2-mid, 2-weak” groups.
![final_fig2](https://github.com/user-attachments/assets/0f194a6d-37ec-4109-ace1-636f0ecc6b8d) <br/>
<br/>
<br/> 


---


💖 **Lesson & Learn**
1. Importance of Data Visualization
   > Visualization Analysis Framework <br/>
   > (What-Why-How) ↔ (Data-Task-Vis Idiom) 
2. Proficiency in using the `R` tool
   > markdown, ggplot, ioslides, flexdashboard
3. Effective Communication Skill
   > Minto’s Pyramid Principle, The Magic Number Seven, S-C-Q-A
