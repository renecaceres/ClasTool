.DELETE_ON_ERROR:

SHELL=/bin/bash
LATEXFILES := $(shell grep -l -e '^[^%]*\\begin{document}' *.tex)
PDFFILES = $(LATEXFILES:%.tex=%.pdf)

TEX = pdflatex

BAKTOC = if test -r $*.toc ; then cp $*.toc $*.toc.bak ; fi 
BAKLOF = if test -r $*.lof ; then cp $*.lof $*.lof.bak ; fi 
BAKLOT = if test -r $*.lot ; then cp $*.lot $*.lot.bak ; fi 

TOC = grep -q -e '^[^%]*\\tableofcontents'
LOF = grep -q -e '^[^%]*\\listoffigures'
LOT = grep -q -e '^[^%]*\\listoftables'

CMPTOC = ! cmp -s $*.toc $*.toc.bak
CMPLOF = ! cmp -s $*.lof $*.lof.bak
CMPLOT = ! cmp -s $*.lot $*.lot.bak

RERUN = grep -q -e '\(There were undefined references\|Rerun to get \(cross-references\|the bars\) right\)' $*.log

RMBAK = rm -rf $*.{toc,lof,toc}.bak

define RUN-TEX
	$(BAKTOC) ; $(BAKLOF) ; $(BAKLOT) ;\
	$(TEX) $< ;\
	if ( $(TOC) $< && $(CMPTOC) ) then $(TEX) $< ; fi ;\
	if ( $(LOF) $< && $(CMPLOF) ) then $(TEX) $< ; fi ;\
	if ( $(LOT) $< && $(CMPLOT) ) then $(TEX) $< ; fi ;\
	while ( $(RERUN) ) ; do  $(TEX) $< ; done ;\
	$(RMBAK)
endef

define DEPENDENCIES
	DEPFILES=`perl -ne '($$_)=/^[^%]*\\\(?:include|input)\{(.*?)\}/;@_=split /,/;foreach $$t (@_) {print "$$t.tex "}' $<`
endef

.PHONY:	all pdf clean aux 	

all: 	pdf

%.dep	: %.tex
	@$(DEPENDENCIES) ; \
	echo "$*.pdf : $< $$DEPFILES" > $@ 
	
-include $(LATEXFILES:.tex=.dep)

pdf: 	$(PDFFILES)

%.pdf: 	%.tex
	$(RUN-TEX)

clean: aux
	rm -f $(PDFFILES)

aux:
	rm -f *.{aux,log,dvi,nav,out,snm,vrb,toc,lof,toc,dep}
