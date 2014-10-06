
SRC= \
	dds.xml \
	introduction.xml \
	requirements.xml \
	download.xml \
	installation.xml \
	configuration.xml \
	how-to-run.xml \
	how-to-test.xml \
	command-line-interface.xml \
	dds-commander.xml \
	dds-getlog.xml \
	dds-info.xml \
	dds-server.xml \
	dds-submit.xml \
	dds-test.xml \
	dds-user-defaults.xml
 
DOCBOOK = dds.xml

OUT_DIR=../output

IMGS= \
	important.png\
	tip.png\
	warning.png\
	note.png

IMG_DIR = img

HTML_OUT= \
        dds.html\
	how-to-install.html

CSS_FILES=docbook.css

# Sun MSV (Multi-Schema Validator)
RELAMES=../lib/relames/relames.jar

DOCBOOK5_SCHEMA_RNG=../lib/schema/rng/docbook.rng

# stylesheet processor
XSLTPROC       = xsltproc
XSLTPROC_FLAGS = --nonet --xinclude

# FOP (for pdf)
FOP=fop
POD_PDF=dds.pdf


JAVA := $(shell which java)

ifeq ($(JAVA),)
$(warning No java found!)
endif


# file to log print output to
LOGFILE = print.log


all: gen_html
# gen_pdf

sync:
	rsync -avz --whole-file --progress -e ssh  $(OUT_DIR)/* ddswww@lxi001.gsi.de:~/web-docs/doc/nightly

$(OUT_DIR):
	@echo "Creating output directory for html doc [$(OUT_DIR)]..."
	@mkdir $(OUT_DIR)

$(OUT_DIR)/%.css:
	@echo "Using custom CSS..."
	@cp $(CSS_FILES) $(OUT_DIR)/

# chunked html, on a chapter-by-chapter basis
html-docs: $(SRC) 
	@echo "Generating html doc..."
	$(XSLTPROC) $(XSLTPROC_FLAGS) -o $(OUT_DIR)/ xslt/html.xsl $(DOCBOOK)

#	$(JAVA) -jar ../lib/saxon/saxon9he.jar \
#                 -ext:on -xi:on -expand:on -warnings:fatal -versionmsg:on -strip:ignorable \
#                '!indent=yes' \
#                -s:$(SRC) \
#                -xsl:xslt/html.xsl \
#                -o:$(OUT_DIR)/index.html
	
#$(XSLTPROC) $(XSLTPROC_FLAGS) -o $(HTML_OUT_DIR)/ config.xsl $(XSL_HTML_CHUNK_STYLE) $(DOCBOOK)

# Print formats
# pdf and postscript
#FOP
#http://www.sagehill.net/docbookxsl/InstallingAnFO.html
#needed a hyphenation pattern http://offo.sourceforge.net/
print-docs:
	@echo "Generating PDF file: $(OUT_DIR)/$(POD_PDF) (please be patient)...";
	$(XSLTPROC) $(XSLTPROC_FLAGS) -o $(OUT_DIR)/index.fo xslt/config_print.xsl $(SRC) && \
        (cd $(OUT_DIR) && \
         $(FOP) index.fo $(POD_PDF) &> $(LOGFILE) < /dev/null && \
         rm -f *.log *.aux *.fo *.out)


$(OUT_DIR)/%.png:
	@echo "Copying images..."
	@cp ${addprefix $(IMG_DIR)/, $(IMGS)} $(OUT_DIR)/

css_files: ${addprefix $(OUT_DIR)/, $(CSS_FILES)}
img_files: ${addprefix $(OUT_DIR)/, $(IMGS)}

validate_doc:
	@echo "Valiadating source files...";
	@echo "Perform basic validation and process XInclude tags..."
	xmllint -xinclude $(DOCBOOK) > $(OUT_DIR)/$(DOCBOOK)
#	xmllint --noout --postvalid --loaddtd $(OUT_DIR)/$(SRC)
	$(JAVA) -jar $(RELAMES) $(DOCBOOK5_SCHEMA_RNG) $(OUT_DIR)/$(DOCBOOK)


gen_html: $(OUT_DIR) validate_doc css_files img_files html-docs

gen_pdf:  $(OUT_DIR) validate_doc img_files print-docs

clean:
	@-rm -rf $(OUT_DIR)
