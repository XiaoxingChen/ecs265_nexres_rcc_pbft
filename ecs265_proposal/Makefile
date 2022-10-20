FILE = proposal
# $(FILE).pdf: $(FILE).tex header.tex design.tex prelim.tex arch.tex guarantees.tex invoker.tex attacks.tex intro.tex back.tex discuss.tex proofs.tex related.tex eval.tex impl.tex abstract.tex concl.tex
$(FILE).pdf: $(FILE).tex sec1_statement.tex sec2_background.tex
	pdflatex $(FILE)

newcite: reference.bib $(FILE).pdf 
	bibtex $(FILE)
	pdflatex $(FILE)
	pdflatex $(FILE)
    
force:
	pdflatex $(FILE)
	bibtex $(FILE)
	pdflatex $(FILE)
	pdflatex $(FILE)

f:
	pdflatex $(FILE)
	pdflatex $(FILE)

clean:
	rm -f $(FILE).aux $(FILE).bbl $(FILE).blg $(FILE).dvi $(FILE).log $(FILE).pdf $(FILE).ps $(FILE).out