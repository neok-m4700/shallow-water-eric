include make_local.inc
# make_local.inc can be a link to make_zahir.inc or make_turing.inc
# makefile for FEM code for zahir (IBM Power4)
# put this makefile at SFEMaNS/APPLICATIONS/MYAPPLICATION
# don't change its name

RACINE = ../../..
SPLIB_DIR = /homegpfs/rech/nor/rnor522/SPLIB

RACINE_MHD = $(RACINE)/MHD_POSTPROC

MPICMP=mpxlf_r  -c 
MPILNK=mpxlf_r 
MONOCMP=xlf_r -c -I$(RACINE)/FEMSUB_OBJET
MONOLNK=xlf_r 


COMMONOPT = -I$(SPLIB_DIR)  -qfree=f90 $$FFTW3_I4S4D8
RELEASEOPT = -O $(COMMONOPT)
DEBUGOPT = -qnooptimize -g -qfullpath $(COMMONOPT)

LIB_TIME =  

TA_OPT = -L/usr/local/prod/traceanalyzer/lib64 -lVT

FEM_OBJ =  dyn_line.o \
           def_type_mesh.o \
	   associate_gauss.o \
	   gauss_points_2d_p1.o \
	   gauss_points_2d_p2.o \
	   chaine_caractere.o \
	   dir_nodes.o \
	   prep_mesh.o \
	   prep_mesh_interface.o \
	   prep_mesh_periodic.o \
	   sub_plot.o \
	   bessel.o \
	   condlim_test_conv.o \
	   condlim.o \
	   fem_s_direct_axi.o \
	   fem_tn_axi.o \
	   fem_tn_navier_mhd.o \
	   tn_par.o \
	   fem_sparsekit_axi.o \
	   st_sparsekit.o \
	   restart.o \
	   fft_parallel.o \
	   post_interp.o \
	   user_time.o

LOC_OBJ =  


GAUSS_OBJ = gauss_points_2d_p1.o\
	   gauss_points_2d_p2.o

SPLIB_OBJ = $(SPLIB_DIR)/splib.a \
	$(SPLIB_DIR)/orderings/reorder.o \
	$(SPLIB_DIR)/solve_splib.o

BLAS_LIB = -lessl


default:
	@echo "Configuration: RELEASE"
	make release

debug : 
	@if [ ! -d DEBUG ] ; then mkdir DEBUG ;  ln make_local.inc DEBUG/make_local.inc ; fi 
	@cd DEBUG ; make -f ../makefile OPT="$(DEBUGOPT)" CMP="$(MPICMP)" LNK="$(MPILNK)" test.exe

release : 
	@if [ ! -d RELEASE ] ; then mkdir RELEASE ; ln make_local.inc RELEASE/make_local.inc ; fi 
	@cd RELEASE ; make -f ../makefile OPT="$(RELEASEOPT)" CMP="$(MPICMP)" LNK="$(MPILNK)" test.exe

profile : 
	@if [ ! -d PROFILE ] ; then mkdir PROFILE ;  ln make_local.inc PROFILE/make_local.inc ; fi 
	@cd PROFILE ; make -f ../makefile OPT="$(RELEASEOPT) -p" CMP="$(MPICMP)" LNK="$(MPILNK)" test.exe

gprofile : 
	@if [ ! -d GPROFILE ] ; then mkdir GPROFILE ; ln make_local.inc GPROFILE/make_local.inc ; fi 
	@cd GPROFILE ; make -f ../makefile OPT="$(RELEASEOPT) -pg " CMP="$(MPICMP)" LNK="$(MPILNK)" test.exe

# Trace Analyser
ta : 
	@if [ ! -d RELEASE ] ; then mkdir RELEASE ; ln make_local.inc RELEASE/make_local.inc ; fi 
	@cd RELEASE ; make -f ../makefile OPT="$(RELEASEOPT)" CMP="$(MPICMP)" LNK="$(MPILNK)" test_ta.exe

# reuse the MPI-compiled objects and link them with no MPI
mono : 
	@if [ ! -d RELEASE ] ; then mkdir RELEASE ; ln make_local.inc RELEASE/make_local.inc ; fi
	@cd RELEASE ; make -f ../makefile OPT="$(RELEASEOPT)" CMP="$(MPICMP)" LNK="$(MONOLNK)" test_mono.exe

# use a separate, MPI-free directory for a true monoproc
monoproc : 
	@if [ ! -d MONOPROC ] ; then mkdir MONOPROC ; ln make_local.inc MONOPROC/make_local.inc ; fi
	@cd MONOPROC ; make -f ../makefile OPT="$(RELEASEOPT)" CMP="$(MONOCMP)" LNK="$(MONOLNK)" test_monoproc.exe

cleanall: 
	rm -rf DEBUG RELEASE MONOPROC 

####################################
# Version MPI
####################################

test.exe: $(FEM_OBJ) $(SPLIB_OBJ) $(LOC_OBJ) interpol_suite.o
	($(LNK) $(OPT) $(LIB_TIME) -o test.exe interpol_suite.o $(FEM_OBJ) $(SPLIB_OBJ) $(LOC_OBJ) $(BLAS_LIB)) 

run1:
	mpirun -np 1 ./test.exe
run2:
	mpirun -np 2 ./test.exe


####################################
# Version mono proceceur
####################################

test_mono.exe: $(FEM_OBJ) $(SPLIB_OBJ) $(LOC_OBJ) interpol_suite.o mpi.o $(RACINE)/FEMSUB_OBJET/mpif.h 
	($(LNK) $(OPT) $(LIB_TIME) -o test_mono.exe interpol_suite.o mpi.o $(FEM_OBJ) $(SPLIB_OBJ) $(LOC_OBJ) $(BLAS_LIB); )

test_monoproc.exe: $(FEM_OBJ) $(SPLIB_OBJ) $(LOC_OBJ) interpol_suite.o mpi.o $(RACINE)/FEMSUB_OBJET/mpif.h 
	($(LNK) $(OPT) $(LIB_TIME) -o test_monoproc.exe interpol_suite.o mpi.o $(FEM_OBJ) $(SPLIB_OBJ) $(LOC_OBJ) $(BLAS_LIB); )

####################################
# Version pour Trace analyser
####################################
test_ta.exe:  $(FEM_OBJ) $(SPLIB_OBJ) $(LOC_OBJ) interpol_suite.o
	($(LNK) $(OPT) $(TA_OPT) $(LIB_TIME) -o test_ta.exe interpol_suite.o $(FEM_OBJ) $(SPLIB_OBJ) $(LOC_OBJ) $(BLAS_LIB))

####################################
####################################

# $< Represents either the full name of a prerequisite that made a target out-of-date (inference rule), or the full name of a target (.DEFAULT rule).
# $* Represents the file name section of a prerequisite that made a target out-of-date (in an inference rule) without a suffix.

mpi.o: $(RACINE)/FEMSUB_OBJET/mpi.f90
	$(CMP) $(OPT) $< -o $(*).o

interpol_suite.o:  $(FEM_OBJ)  ../interpol_suite.f90 
	($(CMP) $(OPT) $(LIB_TIME) ../interpol_suite.f90)

#                    * * * * * * * * * * * *
#                    *   FEM OBJECTS       *
#                    * * * * * * * * * * * *
#

gauss_points_2d_p1.o: $(RACINE)/GAUSS_POINTS_OBJET/gauss_points_2d_p1.f90
	$(CMP) $(OPT) $< -o $(*).o

gauss_points_2d_p2.o: $(RACINE)/GAUSS_POINTS_OBJET/gauss_points_2d_p2.f90
	$(CMP) $(OPT) $< -o $(*).o
 
associate_gauss.o: $(RACINE)/FEMSUB_OBJET/associate_gauss.f90
	$(CMP) $(OPT) $< -o $(*).o

user_time.o: $(RACINE)/FEMSUB/user_time.f90
	$(CMP) $(OPT) $< -o $(*).o

chaine_caractere.o: $(RACINE)/FEMSUB/chaine_caractere.f90 
	$(CMP) $(OPT) $< -o $(*).o

dyn_line.o: $(RACINE)/FEMSUB/dyn_line.f90
	$(CMP) $(OPT) $< -o $(*).o

dir_nodes.o: $(RACINE)/FEMSUB/dir_nodes.f90 dyn_line.o
	$(CMP) $(OPT) $< -o $(*).o

def_type_mesh.o: $(RACINE)/FEMSUB_OBJET/def_type_mesh.f90
	$(CMP) $(OPT) $< -o $(*).o

prep_mesh.o: $(RACINE)/FEMSUB_OBJET/prep_mesh.f90 $(GAUSS_OBJ)
	$(CMP) $(OPT) $< -o $(*).o

prep_mesh_periodic.o: $(RACINE)/FEMSUB_OBJET/prep_mesh_periodic.f90 $(GAUSS_OBJ)
	$(CMP) $(OPT) $< -o $(*).o

prep_mesh_interface.o: $(RACINE)/FEMSUB_OBJET/prep_mesh_interface.f90
	$(CMP) $(OPT) $< -o $(*).o

fem_s_direct_axi.o: $(RACINE)/FEMSUB_OBJET/fem_s_direct_axi.f90 $(GAUSS_OBJ)
	$(CMP) $(OPT) $< -o $(*).o

fem_tn_axi.o: $(RACINE)/FEMSUB_OBJET/fem_tn_axi.f90 $(GAUSS_OBJ)
	$(CMP) $(OPT) $< -o $(*).o

fem_sparsekit_axi.o: $(RACINE)/FEMSUB_OBJET/fem_sparsekit_axi.f90 $(GAUSS_OBJ)
	$(CMP) $(OPT) $< -o $(*).o

st_sparsekit.o: $(RACINE)/FEMSUB/st_sparsekit.f90 
	$(CMP) $(OPT) $< -o $(*).o

sub_plot.o: $(RACINE)/FEMSUB/sub_plot.f90 
	$(CMP) $(OPT) $< -o $(*).o

condlim.o: ../condlim.f90
	$(CMP) $(OPT) $< -o $(*).o

condlim_test_conv.o: $(RACINE)/MHD_DATA_TEST_CONV/condlim_test_conv.f90
	$(CMP) $(OPT) $< -o $(*).o

post_proc.o: $(RACINE_MHD)/post_proc.f90
	$(CMP) $(OPT) $< -o $(*).o

post_interp.o: $(RACINE_MHD)/post_interp.f90
	$(CMP) $(OPT) $< -o $(*).o

fft_parallel.o: $(RACINE)/FEMSUB_OBJET/fft_parallel.f90
	$(CMP) $(OPT) $< -o $(*).o

fem_tn_navier_mhd.o: $(RACINE)/FEMSUB_OBJET/fem_tn_navier_mhd.f90
	$(CMP) $(OPT) $< -o $(*).o

tn_par.o: fem_tn_navier_mhd.o $(RACINE)/FEMSUB_OBJET/tn_par.f90
	$(CMP) $(OPT) $< -o $(*).o

restart.o: $(RACINE)/FEMSUB_OBJET/restart.f90
	$(CMP) $(OPT) $< -o $(*).o

sub_ns.o: $(RACINE)/FEMSUB_OBJET/sub_ns.f90
	$(CMP) $(OPT) $< -o $(*).o

maxwell_update_time.o: $(RACINE)/FEMSUB_OBJET/maxwell_update_time.f90
	$(CMP) $(OPT) $< -o $(*).o

initialisation.o: $(RACINE)/FEMSUB_OBJET/initialisation.f90
	$(CMP) $(OPT) $< -o $(*).o

bessel.o: $(RACINE)/FEMSUB/bessel.f90
	$(CMP) $(OPT) $< -o $(*).o

