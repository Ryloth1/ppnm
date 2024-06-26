CODE = $(filter %.cs,$^)
LIBS = $(addprefix -reference:,$(filter %.dll,$^)) # macros
MKEXE = mcs -target:exe -out:$@ $(LIBS) $(CODE)
MKLIB = mcs -target:library -out:$@ $(LIBS) $(CODE)
TIME = time --output=$@ --append --format "$$N %e %U"

default:  Out.HydrogenRmax.svg Out.HydrogenDr.svg Out.times.svg Out.HydrogenWavef.svg OutA.txt


Out.HydrogenRmax.svg: out.HydrogenRmax.txt Makefile
	echo '\
		set terminal svg background "white" ;\
		set out "$@" ;\
		set xlabel "rmax";\
		set ylabel "Energy";\
		set title "Convergence of energy wrt. rmax, dr = 0.05";\
		plot "$<" using 1:3 with lp notitle\
		'|tee logRmax.gpi | gnuplot

Out.HydrogenDr.svg: out.HydrogenDr.txt Makefile
	echo '\
            set terminal svg background "white" ;\
            set out "$@" ;\
            set xlabel "dr";\
            set ylabel "Energy";\
			set xrange [0.2:0.01] ;\
            set title "Convergence of energy wrt. dr rmax = 10";\
            plot "$<" using 2:3 with lp notitle \
            '|tee logDr.gpi | gnuplot

Out.HydrogenWavef.svg: out.HydrogenWavef.txt Makefile
	echo '\
            set terminal svg background "white" ;\
            set out "$@" ;\
            set xlabel "r";\
            set ylabel "Psi";\
            set xrange [0:20] ;\
            set title "Wavefunctions";\
            f1(x) = 2*x*exp(-x);\
			f2(x) = x*(1-x/2)*exp(-x/2)/sqrt(2) ;\
			f3(x) = 2*x*(1-2*x/3 + 2*x*x/27)*exp(-x/3)/sqrt(27) ;\
			plot "$<" using 1:2 with p pt 3 ps 0.3 title "n = 1" \
			,"$<" using 1:3 with p pt 3 ps 0.3 title "n = 2" \
			,"$<" using 1:4 with p pt 3 ps 0.3 title "n = 3" \
			,f1(x) with lines lt rgb "red"  title "u1(r)"\
            ,f2(x) with lines lt rgb "brown" title "u2(r)"\
            ,f3(x) with lines lt rgb "grey0" title "u3(r)"\
            '|tee logwavf.gpi | gnuplot

Out.times.svg: out.times.txt out.times_opt.txt Makefile
	echo '\
		set terminal svg background "white" ;\
		set out "$@" ;\
		set xlabel "Matrix size N";\
		set ylabel "Decomposition time";\
		set key left top ;\
		set title "Running time as function of matrix size";\
		f(x)=b+(x/a)**c;\
		b=1; a=90; c=3;\
        fo(x)=bo+(x/ao)**co;\
        bo=1; ao=90; co = 3;\
		fit f(x) "out.times.txt" via a,b;\
		fit fo(x) "out.times_opt.txt" via ao,bo;\
		plot "out.times.txt" with lp title "Jacobi algorithm"\
		,"out.times_opt.txt" with lp title "Optimized Jacobi algorithm" \
		,f(x) title sprintf("fit: (n/%.f)^{%.2f}+%.3f",a,c,b) \
        ,fo(x) title sprintf("fit: (n/%.f_opt)^{%.2f}+%.3f",ao,c,bo) \
		'|tee logtime.gpi |gnuplot

OutA.txt: mainA.exe
	mono $< > $@

Ns := $(shell seq 100 25 300)
out.times.txt: time.exe Makefile
	for N in $(Ns); do \
		time -ao log.$$N -f "$$N %e" mono $< -msize:$$N 1>out 2>err & \
    done; wait
	>$@
	for N in $(Ns); do cat log.$$N >> $@; done
	$(RM) log*

out.times_opt.txt: time_opt.exe Makefile
	for N in $(Ns); do \
		time -ao log.$$N -f "$$N %e" mono $< -msize:$$N 1>out 2>err & \
    done; wait
	>$@
	for N in $(Ns); do cat log.$$N >> $@; done
	$(RM) log* 

out.HydrogenRmax.txt: mainB.exe Makefile
	for R in $$(seq 4 1 10); do \
		mono $< -rmax:$$R -dr:0.05 1>> log.$$R & \
	done; wait
	>$@
	for R in $$(seq 4 1 10); do cat log.$$R >> $@; done
	$(RM) log*

out.HydrogenDr.txt: mainB.exe Makefile 
	for r in $$(seq 0.01 0.01 0.2); do \
                mono $< -rmax:10 -dr:$$r 1>> log.$$r;\
        done; wait
	>$@
	for r in $$(seq 0.01 0.01 0.2); do cat log.$$r >> $@; done
	$(RM) log*

out.HydrogenWavef.txt: mainB.exe Makefile
	mono $< -rmax:25 -dr:0.05 -Wavef_conv:Wavef > $@

mainA.exe: mainA.cs extlib.dll; $(MKEXE)

mainB.exe: mainB.cs extlib.dll; $(MKEXE)

time.exe: time.cs extlib.dll; $(MKEXE)

time_opt.exe: time_opt.cs extlib.dll; $(MKEXE)

extlib.dll: ../../lib/matrix.cs ../../lib/vector.cs ../../lib/jacobi.cs; $(MKLIB)

clean: 
	$(RM) *.dll *.exe [Oo]ut* [Ll]og* *.[Ll]og