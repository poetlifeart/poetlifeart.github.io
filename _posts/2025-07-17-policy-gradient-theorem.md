---
title: "Discrete Flow Matching"
date: 2025-07-17
tags:
- flow matching 
- mathematics
layout: post
---



<!-- Load MathJax so LaTeX renders in GitHub Pages without touching layouts -->
<script>
  window.MathJax = {
    tex: {
      inlineMath: [['\\(','\\)'], ['\\[','\\]']]
    }
  };
</script>
<script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>



This is pdfTeX, Version 3.14159265-2.6-1.40.18 (TeX Live 2017/Debian) (preloaded format=pdflatex 2025.1.7)  28 MAY 2025 04:58
entering extended mode
 restricted \write18 enabled.
 %&-line parsing enabled.
**discreteflow1.tex
(./discreteflow1.tex
LaTeX2e <2017-04-15>
Babel <3.18> and hyphenation patterns for 3 language(s) loaded.
(/usr/share/texlive/texmf-dist/tex/latex/base/article.cls
Document Class: article 2014/09/29 v1.4h Standard LaTeX document class
(/usr/share/texlive/texmf-dist/tex/latex/base/size10.clo
File: size10.clo 2014/09/29 v1.4h Standard LaTeX file (size option)
)
\c@part=\count79
\c@section=\count80
\c@subsection=\count81
\c@subsubsection=\count82
\c@paragraph=\count83
\c@subparagraph=\count84
\c@figure=\count85
\c@table=\count86
\abovecaptionskip=\skip41
\belowcaptionskip=\skip42
\bibindent=\dimen102
)
(/usr/share/texlive/texmf-dist/tex/latex/amsmath/amsmath.sty
Package: amsmath 2017/09/02 v2.17a AMS math features
\@mathmargin=\skip43

For additional information on amsmath, use the `?' option.
(/usr/share/texlive/texmf-dist/tex/latex/amsmath/amstext.sty
Package: amstext 2000/06/29 v2.01 AMS text

(/usr/share/texlive/texmf-dist/tex/latex/amsmath/amsgen.sty
File: amsgen.sty 1999/11/30 v2.0 generic functions
\@emptytoks=\toks14
\ex@=\dimen103
))
(/usr/share/texlive/texmf-dist/tex/latex/amsmath/amsbsy.sty
Package: amsbsy 1999/11/29 v1.2d Bold Symbols
\pmbraise@=\dimen104
)
(/usr/share/texlive/texmf-dist/tex/latex/amsmath/amsopn.sty
Package: amsopn 2016/03/08 v2.02 operator names
)
\inf@bad=\count87
LaTeX Info: Redefining \frac on input line 213.
\uproot@=\count88
\leftroot@=\count89
LaTeX Info: Redefining \overline on input line 375.
\classnum@=\count90
\DOTSCASE@=\count91
LaTeX Info: Redefining \ldots on input line 472.
LaTeX Info: Redefining \dots on input line 475.
LaTeX Info: Redefining \cdots on input line 596.
\Mathstrutbox@=\box26
\strutbox@=\box27
\big@size=\dimen105
LaTeX Font Info:    Redeclaring font encoding OML on input line 712.
LaTeX Font Info:    Redeclaring font encoding OMS on input line 713.
\macc@depth=\count92
\c@MaxMatrixCols=\count93
\dotsspace@=\muskip10
\c@parentequation=\count94
\dspbrk@lvl=\count95
\tag@help=\toks15
\row@=\count96
\column@=\count97
\maxfields@=\count98
\andhelp@=\toks16
\eqnshift@=\dimen106
\alignsep@=\dimen107
\tagshift@=\dimen108
\tagwidth@=\dimen109
\totwidth@=\dimen110
\lineht@=\dimen111
\@envbody=\toks17
\multlinegap=\skip44
\multlinetaggap=\skip45
\mathdisplay@stack=\toks18
LaTeX Info: Redefining \[ on input line 2817.
LaTeX Info: Redefining \] on input line 2818.
)
(/usr/share/texlive/texmf-dist/tex/latex/amsfonts/amssymb.sty
Package: amssymb 2013/01/14 v3.01 AMS font symbols

(/usr/share/texlive/texmf-dist/tex/latex/amsfonts/amsfonts.sty
Package: amsfonts 2013/01/14 v3.01 Basic AMSFonts support
\symAMSa=\mathgroup4
\symAMSb=\mathgroup5
LaTeX Font Info:    Overwriting math alphabet `\mathfrak' in version `bold'
(Font)                  U/euf/m/n --> U/euf/b/n on input line 106.
))
(/usr/share/texlive/texmf-dist/tex/latex/xcolor/xcolor.sty
Package: xcolor 2016/05/11 v2.12 LaTeX color extensions (UK)

(/usr/share/texlive/texmf-dist/tex/latex/graphics-cfg/color.cfg
File: color.cfg 2016/01/02 v1.6 sample color configuration
)
Package xcolor Info: Driver file: pdftex.def on input line 225.

(/usr/share/texlive/texmf-dist/tex/latex/graphics-def/pdftex.def
File: pdftex.def 2018/01/08 v1.0l Graphics/color driver for pdftex
)
Package xcolor Info: Model `cmy' substituted by `cmy0' on input line 1348.
Package xcolor Info: Model `hsb' substituted by `rgb' on input line 1352.
Package xcolor Info: Model `RGB' extended on input line 1364.
Package xcolor Info: Model `HTML' substituted by `rgb' on input line 1366.
Package xcolor Info: Model `Hsb' substituted by `hsb' on input line 1367.
Package xcolor Info: Model `tHsb' substituted by `hsb' on input line 1368.
Package xcolor Info: Model `HSB' substituted by `hsb' on input line 1369.
Package xcolor Info: Model `Gray' substituted by `gray' on input line 1370.
Package xcolor Info: Model `wave' substituted by `hsb' on input line 1371.
)
(./discreteflow1.aux)
\openout1 = `discreteflow1.aux'.

LaTeX Font Info:    Checking defaults for OML/cmm/m/it on input line 5.
LaTeX Font Info:    ... okay on input line 5.
LaTeX Font Info:    Checking defaults for T1/cmr/m/n on input line 5.
LaTeX Font Info:    ... okay on input line 5.
LaTeX Font Info:    Checking defaults for OT1/cmr/m/n on input line 5.
LaTeX Font Info:    ... okay on input line 5.
LaTeX Font Info:    Checking defaults for OMS/cmsy/m/n on input line 5.
LaTeX Font Info:    ... okay on input line 5.
LaTeX Font Info:    Checking defaults for OMX/cmex/m/n on input line 5.
LaTeX Font Info:    ... okay on input line 5.
LaTeX Font Info:    Checking defaults for U/cmr/m/n on input line 5.
LaTeX Font Info:    ... okay on input line 5.

(/usr/share/texlive/texmf-dist/tex/context/base/mkii/supp-pdf.mkii
[Loading MPS to PDF converter (version 2006.09.02).]
\scratchcounter=\count99
\scratchdimen=\dimen112
\scratchbox=\box28
\nofMPsegments=\count100
\nofMParguments=\count101
\everyMPshowfont=\toks19
\MPscratchCnt=\count102
\MPscratchDim=\dimen113
\MPnumerator=\count103
\makeMPintoPDFobject=\count104
\everyMPtoPDFconversion=\toks20
)
LaTeX Font Info:    Try loading font information for U+msa on input line 8.
 (/usr/share/texlive/texmf-dist/tex/latex/amsfonts/umsa.fd
File: umsa.fd 2013/01/14 v3.01 AMS symbols A
)
LaTeX Font Info:    Try loading font information for U+msb on input line 8.

(/usr/share/texlive/texmf-dist/tex/latex/amsfonts/umsb.fd
File: umsb.fd 2013/01/14 v3.01 AMS symbols B
)

LaTeX Warning: No \author given.

Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
LaTeX Font Info:    Try loading font information for OMS+cmr on input line 14.
(/usr/share/texlive/texmf-dist/tex/latex/base/omscmr.fd
File: omscmr.fd 2014/09/29 v2.5h Standard LaTeX font definitions
)
LaTeX Font Info:    Font shape `OMS/cmr/m/n' in size <10> not available
(Font)              Font shape `OMS/cmsy/m/n' tried instead on input line 14.
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
 [1

{/var/lib/texmf/fonts/map/pdftex/updmap/pdftex.map}]
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmbx12!
 [2]
Missing character: There is no � in font cmti10!
Missing character: There is no � in font cmti10!
Missing character: There is no � in font cmti10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!

Overfull \hbox (4.11313pt too wide) in paragraph at lines 108--109
\OT1/cmr/m/n/10 Introduce an aux-il-iary cou-pling $\OML/cmm/m/it/10 Z \OT1/cmr
/m/n/10 = (\OML/cmm/m/it/10 X[]; X[]\OT1/cmr/m/n/10 )$. For each co-or-di-nate 
$\OML/cmm/m/it/10 i \OMS/cmsy/m/n/10 2 f\OT1/cmr/m/n/10 1\OML/cmm/m/it/10 ; [] 
; d\OMS/cmsy/m/n/10 g$\OT1/cmr/m/n/10 ,
 []

Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!

Overfull \hbox (18.11595pt too wide) detected at line 127
[][]\OML/cmm/m/it/10 X[] \OT1/cmr/m/n/10 = \OML/cmm/m/it/10 X[] \OMS/cmsy/m/n/1
0 j \OML/cmm/m/it/10 X[] \OT1/cmr/m/n/10 = \OML/cmm/m/it/10 x[];  X[] \OT1/cmr/
m/n/10 = \OML/cmm/m/it/10 x[][] \OT1/cmr/m/n/10 = \OML/cmm/m/it/10 t;  [][]X[] 
\OT1/cmr/m/n/10 = \OML/cmm/m/it/10 X[] \OMS/cmsy/m/n/10 j \OML/cmm/m/it/10 X[] 
\OT1/cmr/m/n/10 = \OML/cmm/m/it/10 x[];  X[] \OT1/cmr/m/n/10 = \OML/cmm/m/it/10
 x[][] \OT1/cmr/m/n/10 = 1 \OMS/cmsy/m/n/10 ^^@ \OML/cmm/m/it/10 t:
 []

Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!

Overfull \hbox (68.0789pt too wide) detected at line 155
\OML/cmm/m/it/10 u[]\OT1/cmr/m/n/10 (\OML/cmm/m/it/10 y[]; x\OT1/cmr/m/n/10 ) =
 [] \OML/cmm/m/it/10 u[]\OT1/cmr/m/n/10 (\OML/cmm/m/it/10 y[]; x \OMS/cmsy/m/n/
10 j \OML/cmm/m/it/10 x[]; x[]\OT1/cmr/m/n/10 ) \OML/cmm/m/it/10 p[]\OT1/cmr/m/
n/10 (\OML/cmm/m/it/10 x[] \OMS/cmsy/m/n/10 j \OML/cmm/m/it/10 x\OT1/cmr/m/n/10
 ) = [] [][]\OML/cmm/m/it/10 ^^N\OT1/cmr/m/n/10 (\OML/cmm/m/it/10 y[]; x[]\OT1/
cmr/m/n/10 ) \OMS/cmsy/m/n/10 ^^@ \OML/cmm/m/it/10 ^^N\OT1/cmr/m/n/10 (\OML/cmm
/m/it/10 y[]; x[]\OT1/cmr/m/n/10 )[] \OML/cmm/m/it/10 p[]\OT1/cmr/m/n/10 (\OML/
cmm/m/it/10 x[] \OMS/cmsy/m/n/10 j \OML/cmm/m/it/10 x\OT1/cmr/m/n/10 )\OML/cmm/
m/it/10 :
 []

Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!

Overfull \hbox (41.84436pt too wide) detected at line 171
\OML/cmm/m/it/10 P[]X[] \OT1/cmr/m/n/10 = \OML/cmm/m/it/10 y[] \OMS/cmsy/m/n/10
 j \OML/cmm/m/it/10 X[] \OT1/cmr/m/n/10 = \OML/cmm/m/it/10 x[] \OT1/cmr/m/n/10 
= [][]\OML/cmm/m/it/10 ^^N\OT1/cmr/m/n/10 (\OML/cmm/m/it/10 y[]; x[]\OT1/cmr/m/
n/10 ) + [][]\OML/cmm/m/it/10 ^^N\OT1/cmr/m/n/10 (\OML/cmm/m/it/10 y[]; x[]\OT1
/cmr/m/n/10 ) \OMS/cmsy/m/n/10 ^^@ \OML/cmm/m/it/10 ^^N\OT1/cmr/m/n/10 (\OML/cm
m/m/it/10 y[]; x[]\OT1/cmr/m/n/10 )[][] \OML/cmm/m/it/10 p[]\OT1/cmr/m/n/10 (\O
ML/cmm/m/it/10 x[] \OMS/cmsy/m/n/10 j \OML/cmm/m/it/10 x\OT1/cmr/m/n/10 ) + \OM
L/cmm/m/it/10 o\OT1/cmr/m/n/10 (\OML/cmm/m/it/10 h\OT1/cmr/m/n/10 )\OML/cmm/m/i
t/10 :
 []

[3]
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!

Overfull \hbox (3.86166pt too wide) in paragraph at lines 177--178
\OT1/cmr/m/n/10 This makes ex-plicit how the learned pos-te-rior per co-or-di-n
ate drives the sourcesink
 []

Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!

Overfull \hbox (49.42064pt too wide) detected at line 205
\OML/cmm/m/it/10 P[]X[] \OT1/cmr/m/n/10 = \OML/cmm/m/it/10 y[] \OMS/cmsy/m/n/10
 j \OML/cmm/m/it/10 X[] \OT1/cmr/m/n/10 = \OML/cmm/m/it/10 x[] \OT1/cmr/m/n/10 
= [][]\OML/cmm/m/it/10 ^^N\OT1/cmr/m/n/10 (\OML/cmm/m/it/10 y[]; x[]\OT1/cmr/m/
n/10 ) + [][]\OML/cmm/m/it/10 ^^N\OT1/cmr/m/n/10 (\OML/cmm/m/it/10 y[]; x[]\OT1
/cmr/m/n/10 ) \OMS/cmsy/m/n/10 ^^@ \OML/cmm/m/it/10 ^^N\OT1/cmr/m/n/10 (\OML/cm
m/m/it/10 y[]; x[]\OT1/cmr/m/n/10 )[][] \OML/cmm/m/it/10 p[]\OT1/cmr/m/n/10 (\O
ML/cmm/m/it/10 x[] \OMS/cmsy/m/n/10 j \OML/cmm/m/it/10 x\OT1/cmr/m/n/10 ) + \OM
L/cmm/m/it/10 o\OT1/cmr/m/n/10 (\OML/cmm/m/it/10 h\OT1/cmr/m/n/10 )\OML/cmm/m/i
t/10 :
 []

Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmbx12!
Missing character: There is no � in font cmbx12!

Overfull \hbox (5.92294pt too wide) in paragraph at lines 211--211
[]\OT1/cmr/bx/n/14.4 PerCoordinate Pos-te-rior Pa-ram-e-ter-i-za-tion and
 []

Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!

LaTeX Warning: Reference `sec:implementation' on page 4 undefined on input line
 217.

Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
[4]
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
Missing character: There is no � in font cmr10!
 [5] (./discreteflow1.aux)

LaTeX Warning: There were undefined references.

 ) 
Here is how much of TeX's memory you used:
 2089 strings out of 494923
 24389 string characters out of 6180743
 82869 words of memory out of 5000000
 5420 multiletter control sequences out of 15000+600000
 11710 words of font info for 46 fonts, out of 8000000 for 9000
 14 hyphenation exceptions out of 8191
 27i,12n,35p,572b,200s stack positions out of 5000i,500n,10000p,200000b,80000s
</usr/share/texlive/texmf-dist/fonts/type1/public/amsfonts/cm/cmbx10.pfb></us
r/share/texlive/texmf-dist/fonts/type1/public/amsfonts/cm/cmbx12.pfb></usr/shar
e/texlive/texmf-dist/fonts/type1/public/amsfonts/cm/cmex10.pfb></usr/share/texl
ive/texmf-dist/fonts/type1/public/amsfonts/cm/cmmi10.pfb></usr/share/texlive/te
xmf-dist/fonts/type1/public/amsfonts/cm/cmmi5.pfb></usr/share/texlive/texmf-dis
t/fonts/type1/public/amsfonts/cm/cmmi7.pfb></usr/share/texlive/texmf-dist/fonts
/type1/public/amsfonts/cm/cmr10.pfb></usr/share/texlive/texmf-dist/fonts/type1/
public/amsfonts/cm/cmr12.pfb></usr/share/texlive/texmf-dist/fonts/type1/public/
amsfonts/cm/cmr17.pfb></usr/share/texlive/texmf-dist/fonts/type1/public/amsfont
s/cm/cmr5.pfb></usr/share/texlive/texmf-dist/fonts/type1/public/amsfonts/cm/cmr
7.pfb></usr/share/texlive/texmf-dist/fonts/type1/public/amsfonts/cm/cmsy10.pfb>
</usr/share/texlive/texmf-dist/fonts/type1/public/amsfonts/cm/cmsy7.pfb></usr/s
hare/texlive/texmf-dist/fonts/type1/public/amsfonts/cm/cmti10.pfb></usr/share/t
exlive/texmf-dist/fonts/type1/public/amsfonts/cm/cmtt10.pfb></usr/share/texlive
/texmf-dist/fonts/type1/public/amsfonts/symbols/msbm10.pfb>
Output written on discreteflow1.pdf (5 pages, 178710 bytes).
PDF statistics:
 84 PDF objects out of 1000 (max. 8388607)
 60 compressed objects within 1 object stream
 0 named destinations out of 1000 (max. 500000)
 1 words of extra memory for PDF output out of 10000 (max. 10000000)

