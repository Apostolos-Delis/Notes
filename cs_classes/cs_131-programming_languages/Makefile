
MD_FILES=lecture_1.md lecture_2.md lecture_3.md lecture_4.md \
		lecture_5.md lecture_6.md lecture_7.md lecture_8.md \
		lecture_9.md lecture_10.md lecture_11.md midterm_review.md


HTML_FILES=$(MD_FILES:.md=.html)
BUILD_HTML_FILES=$(HTML_FILES:%=build/%)

PDF_FILES=$(MD_FILES:.md=.pdf)
BUILD_PDF_FILES=$(PDF_FILES:%=build/%)

EXTRA_PDFS=midterm_review.md

OUTPUT_PDF=programming_languages.pdf


.PHONY: all
all: $(BUILD_PDF_FILES)

.PHONY: build
build: $(OUTPUT_PDF)

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
