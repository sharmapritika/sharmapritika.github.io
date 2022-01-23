# How to use latexdiff on MacOS
by Bharat Sharma, bharat.sharma.neu@gmail.comThis Tutorial is designed to help users that want to use latexdiff when they have one or multiple .tex files for one document class
## If you have any difficulties regarding installation please use this trouble shooting method:

#### http://www.deanbodenham.com/learn/troubleshooting-latexdiff.html

####   

## If you have multiple .tex files in your document and you use latexdiff on only one file, and you are not able to see the corrections, following steps will help fix the issue:

### If you original '.tex' file is "Original.tex"
This is the Original File.
### If you revised '.tex' file is "Revisions.tex"
This is the revision file.
### Create a .tex file, "head.tex"
\documentclass[12pt]{article} 
\usepackage{amsmath} 
\usepackage{amssymb} 
\usepackage{graphicx} 
\usepackage{natbib}
\begin{document}

### Now, on your terminal type following commands for Original.tex:

$ echo "\end{document}" >> Original.tex

$ cat head.tex Original.tex >> Original_full.tex 

### And, for Revisions.tex:
$ echo "\end{document}" >> Revisions.tex

$ cat head.tex Revisions.tex >> Revisions_full.tex 
#    

# Use latexdiff
$ latexdiff Original_full.tex Revisions_full.tex >> Difference.tex
####    
$ pdflatex Difference.tex
#### This will create a Difference.pdf, which will have corrections

![Screen%20Shot%202020-06-11%20at%2012.00.13%20AM.png](attachment:Screen%20Shot%202020-06-11%20at%2012.00.13%20AM.png)


```python

```
