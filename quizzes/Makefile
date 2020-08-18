QUIZZES:= simplequiz.zip programmatic.zip

all: $(QUIZZES)

clean:
	rm -f $(QUIZZES)

%.zip: %.md
	text2qti --run-code-blocks --pandoc-mathml $<
	rm -f _text2qti_cache.zip
