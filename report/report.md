---
# pandoc report.md -o pdf/report.pdf --from markdown --template eisvogel.tex --listings --pdf-engine=xelatex --toc --number-sections -H disable_float.tex

papersize: a4
lang: en-US
documentclass: article
title: UE10 

author: \textbf{LAI Khang Duy} \newline
        \textbf{Mazen TEBIB} \newline
        \newline
        \newline
        \textit{Université Paris-Saclay} \newline 
        \textit{Faculté des sciences d'Orsay}
footer-left: Université Paris-Saclay
date: 23-03-2023
titlepage: true
toc-own-page: true
# lof: true
# lof-own-page: true
titlepage-logo: report/images/ups.png
header-includes: 
      - |
        ``` {=latex}
        \let\originAlParaGraph\paragraph
        \renewcommand{\paragraph}[1]{\originAlParaGraph{#1} \hfill}
        ```
# output:
#   rmarkdown::pdf_document:
#     fig_caption: yes        
#     includes:  
#       in_header: figure_placement.tex
...

# Test 1 

# Test 2
