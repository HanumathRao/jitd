

FILES = \
	src/PrettyFormat\
	src/Var\
	src/Cog\
	src/diesel/Diesel

LIBS = str

LEXERS = src/diesel/DieselLexer
PARSERS = src/diesel/DieselParser

AUTOGENFILES = \
	$(patsubst %.mly, %, $(PARSERS))\
	$(patsubst %.mll, %, $(LEXERS))

FILES += $(AUTOGENFILES)
DIRS = $(shell for i in $(FILES) ; do dirname $$i; done | sort | uniq)

OCAML_ARGS += $(patsubst %,%.cma,$(LIBS))

OCAMLC = ocamlc -g $(patsubst %, -I %, $(DIRS)) $(OCAML_ARGS)

C_FILES = $(patsubst %, %.cmo, $(FILES))

all: jitd

$(patsubst %, %.ml,$(LEXERS)) : %.ml : %.mll
	@echo Building Lexer $(*)
	@ocamllex $< 

$(patsubst %, %.ml,$(PARSERS)) : %.ml : %.mly
	@echo Building Parser $(*)
	@ocamlyacc $<
  
$(C_FILES) : %.cmo : %.ml
	@if [ -f $(*).mli ] ; then \
		echo Compiling Header $(*);\
		$(OCAMLC) -c $(*).mli;\
	fi
	@echo Compiling $(*)
	@$(OCAMLC) -c $<

jitd: $(patsubst %, %.cmo, $(FILES)) src/Driver.ml
	@echo Building $@
	@$(OCAMLC) -o $@ $^

clean: 
	rm -f $(patsubst %,%.ml,$(AUTOGENFILES)) $(patsubst %,%.mli,$(PARSERS))
	rm -f jitd 
	rm -f $(patsubst %,%/*.cmo,$(DIRS))
	rm -f $(patsubst %,%/*.cmx,$(DIRS))
	rm -f $(patsubst %,%/*.cmi,$(DIRS))
	rm -f $(patsubst %,%/*.cmxi,$(DIRS))
	make -C examples clean
	
test: jitd
	@make -C examples test

.PHONY: all clean test

