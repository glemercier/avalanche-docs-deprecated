
LATEX=pdflatex

BIBTEX=bibtex
BIBFILE=ava-platform
PAPER=ava-platform
TEXFILES = $(wildcard *.tex) $(wildcard sections/*.tex) $(wildcard figures/*.tex) $(wildcard figures/*.pdf)

SUBDIRS =
#figs

all: subdirs $(PAPER).pdf

subdirs:
	@for dir in $(SUBDIRS); do $(MAKE) -C $$dir; done;

$(PAPER).pdf: $(TEXFILES) $(BIBFILE).bib
	$(LATEX) --enable-write18 $(PAPER)
	$(BIBTEX) $(BIBFILE)
	$(LATEX) $(PAPER)
	$(LATEX) $(PAPER)

cleantex:
	rm -f *.aux *.log *~ *.out .DS_Store *.dvi $(PAPER).ps $(PAPER).pdf \
		*.lot *.lof *.toc *.blg *.bbl *.ent *.bak *.glo *.gls *.ist \
		*.fdb_latexmk *.fls

clean: cleantex
	for dir in $(SUBDIRS); do $(MAKE) -C $$dir clean; done;\

