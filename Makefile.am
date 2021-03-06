# -*- makefile-automake -*-

ACLOCAL_AMFLAGS = -Im4

CUSTOMSOURCES = \
	poppler-sharp/custom/Document.custom

EXTRA_DIST = \
	poppler-sharp/poppler-api.raw \
	poppler-sharp/poppler.metadata \
	sources/poppler-sharp.dll.config \
	$(CUSTOMSOURCES)

GENDIR = poppler-sharp/generated/
GENSOURCES = $(GENDIR)*.cs
GENSTAMP = $(GENDIR)stamp

CLEANFILES = \
	$(GENSOURCES) \
	$(GENSTAMP) \
	poppler-sharp/poppler-api.xml \
	poppler-sharp.dll

pkglib_DATA = poppler-sharp.dll sources/poppler-sharp.dll.config
pkgconfigdir = $(libdir)/pkgconfig
pkgconfig_DATA = sources/poppler-sharp.pc

if ENABLE_AUTOGEN_API
poppler-sharp/poppler-api.raw: sources/poppler-sharp.sources
	gapi2-parser $<
endif

poppler-sharp/poppler-api.xml: poppler-sharp/poppler-api.raw \
                               poppler-sharp/poppler.metadata
	[ -d $(dir $@) ] || mkdir -p $(dir $@)
	cp $< $@
	chmod u+w $@
	gapi2-fixup \
		--api=$@ \
		--metadata=$(lastword $^)

$(GENSOURCES): $(GENSTAMP)
$(GENSTAMP): poppler-sharp/poppler-api.xml $(CUSTOMSOURCES)
	gapi2-codegen \
		--outdir=poppler-sharp/generated \
		--customdir=$(srcdir)/poppler-sharp/custom \
		$(GTKSHARP_CFLAGS) \
		--generate $<
	touch $@

poppler-sharp.dll: $(GENSOURCES)
	$(MCS) \
		-pkg:gtk-sharp-2.0 \
		-r:Mono.Cairo \
		-t:library \
		-out:$@ \
		-unsafe \
		$^
