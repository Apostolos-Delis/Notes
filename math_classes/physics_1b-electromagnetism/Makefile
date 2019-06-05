
TEX_FILES=$(shell find . -name *.tex)

.PHONY: all
all:
	@latexmk -pdf $(TEX_FILES)

.PHONY: clean
clean:
	@rm -rf *.aux *.fls *.pdf *.gz *.toc *.log *.fdb_latexmk

