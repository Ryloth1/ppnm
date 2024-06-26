CODE = $(filter %.cs,$^)
LIBS = $(addprefix -reference:,$(filter %.dll,$^)) # macros
MKEXE = mcs -target:exe -out:$@ $(LIBS) $(CODE)
MKLIB = mcs -target:library -out:$@ $(LIBS) $(CODE)

default: OutA.svg OutB.txt OutB.svg OutC.svg

OutC.svg: dataC.txt Makefile
	echo '\
		set terminal svg background "white" ;\
		set out "$@" ;\
		set xlabel "x";\
		set ylabel "y";\
		set size ratio -1;\
		set title "Figure-8 solution";\
		plot[-1.3:2] "$<" using 1:2 with lines t "Planet 1"\
		, "$<" using 3:4 with lines t "Planet 2"\
		, "$<" using 5:6 with lines t "Planet 3"\
		'|tee logC.gpi | gnuplot


OutB.svg: dataBNC.txt dataBNE.txt dataBGR.txt Makefile
	echo '\
                set terminal svg background "white" ;\
                set out "$@" ;\
                set xlabel "x";\
                set ylabel "y";\
		set size ratio -1;\
		set grid;\
                set term svg size 1200, 600 ;\
		set multiplot layout 1,3 columns;\
                set title "Circular Newtonian orbit";\
		plot "dataBNC.txt" using (1/$$2)*cos($$1):(1/$$2)*sin($$1) with lines notitle;\
		set title "Elliptic Newtonian orbit";\
                plot "dataBNE.txt" using (1/$$2)*cos($$1):(1/$$2)*sin($$1) with lines notitle;\
		set title "Relativistic precession in GR";\
                plot "dataBGR.txt" using (1/$$2)*cos($$1):(1/$$2)*sin($$1) with lines notitle;\
		unset multiplot;\
                '|tee logB.gpi | gnuplot

OutA.svg: dataAHarm.txt dataAPend.txt Makefile
	echo '\
		set terminal svg background "white" ;\
		set out "$@" ;\
		set xlabel "t";\
		set grid;\
		set size 1,1;\
		set title "Solution to Harmonic oscillator and damped pendulum";\
		plot "dataAHarm.txt" using 1:2 with l t "Harmonic oscillator"\
		,"dataAPend.txt" using 1:2 with l t "theta(t)"\
		,"dataAPend.txt" using 1:3 with l t "omega(t)"\
		'|tee logA.gpi | gnuplot

dataAHarm.txt: mainA.exe
	mono $< 1> $@ 2> dataAPend.txt

dataBNC.txt: mainB.exe Makefile
	mono $< -u0:1 1> $@ 2>> OutB.txt

dataBNE.txt: mainB.exe Makefile
	mono $< -u0:1 -uprime0:-0.5 1> $@ 2>> OutB.txt

dataBGR.txt: mainB.exe Makefile
	mono $< -u0:1 -uprime0:-0.5 -epsilon:0.01 -phiL:20 1> $@ 2>> OutB.txt

OutB.txt: 
	echo 'Test of ODE solver with no path-recording:' > $@

dataC.txt: mainC.exe
	mono $< > $@

mainA.exe: mainA.cs extlib.dll; $(MKEXE)

mainB.exe: mainB.cs extlib.dll; $(MKEXE)

mainC.exe: mainC.cs extlib.dll; $(MKEXE)

extlib.dll: ../../lib/vector.cs ../../lib/genlist.cs \
	../../lib/spline.cs ../../lib/matrix.cs \
	../../lib/qrgs.cs ../../lib/linsol.cs \
	../../lib/ode.cs;  $(MKLIB)


clean:
	$(RM) *.dll *.exe [Oo]ut* [Dd]ata* [Ll]og*