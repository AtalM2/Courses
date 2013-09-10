markdown = $(shell ls *.md)
pdf = $(markdown:.md=.pdf)
onstaged = $(html:%=onstage.html\#%)

all: $(pdf)

%.pdf: %.tex
	xelatex $<

%.pdf: %.md
	~/.cabal/bin/pandoc \
		-Ssf markdown+raw_tex \
		-t latex \
		--latex-engine xelatex \
		--template template.tex \
		$< \
		-o $@

update:
	while :; do inotifywait -qqe modify $(markdown); make; done

.PHONY: all view
