MD_FILES=chapter_2.md chapter_3.md

HTML_FILES=$(MD_FILES:.md=.html)
BUILD_HTML_FILES=$(HTML_FILES:%=build/%)

PDF_FILES=$(MD_FILES:.md=.pdf)
BUILD_PDF_FILES=$(PDF_FILES:%=build/%)

EXTRA_PDFS=

OUTPUT_PDF=compilers.pdf


.PHONY: all
all: $(BUILD_PDF_FILES)

.PHONY: build
build: $(OUTPUT_PDF)
	@cp $(OUTPUT_PDF) ..

$(OUTPUT_PDF): $(BUILD_PDF_FILES)
	@gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/default \
		-dNOPAUSE -dQUIET -dBATCH -dDetectDuplicateImages \
		-dCompressFonts=true -r150 -sOutputFile=$@ $^ $(EXTRA_PDFS)

build/%.pdf: %.md
	@mkdir -p $$(dirname $@)
	@pandoc -V geometry:margin=1in -o $@ $?

.PHONY: clean
clean:
	@rm -rf $(OUTPUT_PDF) build &>/dev/null
