include ../../../Makefile.inc

# SAMPLES will be re-evaluated when the `paths` target is built, which depends
# on all the SAMPLE_DIRS being created and filled
TISSUES = $(shell ../../../config/get.sh tcga/tissues)
SAMPLE_DIRS = $(TISSUES:%=expr/%/.dirstamp)
SAMPLES = $(shell find expr -name "*.txt")
PATHS = $(SAMPLES:expr/%=path/%)

all: paths

clean:
	rm -rf expr path proteins.txt

expr: $(SAMPLE_DIRS)

# see: http://stackoverflow.com/questions/554742/reevaluate-makefile-variables
paths: expr
	$(MAKE) $(PATHS)

proteins.txt:
	grep ^protein < SuperPathway.txt | grep -v abstract | cut -f2 > $@

# this could probably be improved by:
# http://www.gnu.org/savannah-checkouts/gnu/make/manual/html_node/Prerequisite-Types.html
$(SAMPLE_DIRS): %/.dirstamp: preprocess_expr.r proteins.txt
	mkdir -p $*
	$(call bsub_log,20480) Rscript preprocess_expr.r $*
	touch $*/.dirstamp

$(PATHS): path/%.txt: expr/%.txt config.txt SuperPathway.txt
	mkdir -p $(dir $@)
	$(call bsub,10240) -oo $@ paradigm -m 10 -c config.txt -p SuperPathway.txt -b expr/$*
