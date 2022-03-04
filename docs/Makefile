BIB = ml.bib
PANDOC = pandoc --bibliography $(BIB) -f markdown -t markdown_mmd-citations --metadata link-citations=true

WEAVE = julia makeweave.jl
vpath %.jmd jmd



jmdin := $(wildcard jmd/*.jmd)
jmdmd := $(patsubst jmd/%.jmd, md/%.md,$(jmdin))
jmdout := $(patsubst jmd/%.jmd, build/%.md,$(jmdin))

docin := $(wildcard src/*.md)
docout := $(patsubst src/%.md, build/%.md,$(docin))


mdout := $(jmdout) $(rmdout)

nbout := $(patsubst jmd/%.jmd, build/%.ipynb,$(jmdin))


build/figure : figure
	ln -s figure build/figure

build/figures : md/figures
	ln -s md/figures jmd/figures

jmd : $(jmdout)
nb : $(nbout)
doc: $(docout)

$(jmdmd) : md/%.md : %.jmd #makeweave.jl
	$(WEAVE) $< $@  weave

$(mdout) : build/%.md : md/%.md
	$(PANDOC) $< -o $@

$(nbout) : build/%.ipynb : %.jmd
	$(WEAVE) $< $@  notebook

$(docout) : build/%.md :  src/%.md
	julia -e "using Documenter, NeuralNetworkEconomics, DocumenterMarkdown; makedocs( modules=[NeuralNetworkEconomics],  format=Markdown(), clean=false,  pages=[  \"Home\" => \"index.md\" ], repo=\"https://github.com/schrimpf/NeuralNetworkEconomics.jl/blob/{commit}{path}#L{line}\",  sitename=\"NeuralNetworkEconomics.jl\",  authors=\"Paul Schrimpf <paul.schrimpf@gmail.com>\")"

site: site/index.html

site/index.html: $(rmdout) $(jmdout) $(docout) mkdocs.yml
	mkdocs build

all: site/index.html $(nbout)

deploy: site/index.html
	mkdocs gh-deploy
