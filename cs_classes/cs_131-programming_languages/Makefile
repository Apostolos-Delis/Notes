MD_FILES=$(shell find . -name \*.md)

HTML_FILES=$(MD_FILES:.md=.html)
BUILD_HTML_FILES=$(HTML_FILES:%=build/%)

PDF_FILES=$(MD_FILES:.md=.pdf)
BUILD_PDF_FILES=$(PDF_FILES:%=build/%)

EXTRA_PDFS=

OUTPUT_PDF=programming_languages.pdf

all: $(OUTPUT_PDF)
.PHONY: pdf

$(OUTPUT_PDF): $(BUILD_PDF_FILES)
	@gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/default \
		-dNOPAUSE -dQUIET -dBATCH -dDetectDuplicateImages \
		-dCompressFonts=true -r150 -sOutputFile=$@ $^ $(EXTRA_PDFS)

build/%.pdf: %.md
	@mkdir -p $$(dirname $@)
	@pandoc -V geometry:margin=1in -o $@ $?

clean: 
	@rm -rf $(OUTPUT_PDF) build &>/dev/null 
.PHONY: clean
