
TEX_FILES=$(shell find . -name \*.tex)

all:
	@latexmk -pdf $(TEX_FILES)

clean:
	@rm -rf *.aux *.fls *.pdf *.gz *.toc *.log *.fdb_latexmk 

.PHONY: clean
