# BandUP v3.0.0-beta.5-modified
This is a modified code, by Zhi Wang @ CU Boulder, 2018 Jan 10, based
on BandUP v3.0.0-beta.5. 
It only supports VASP.
The original BandUP code of v3.0.0-beta.5 can be found at:
https://github.com/band-unfolding/bandup/releases/tag/v3.0.0-beta.5
        
<!-- ============================================================ -->
## System requirements:

   * The same requirements as the original BandUP.
   * https://github.com/band-unfolding/bandup#system-requirements
    
   * To do orbital-projected unfolding, you may also need vaspkit
   * https://sourceforge.net/projects/vaspkit/


<!-- ============================================================ -->
## To compile the modified BandUP:

   * 1. Enter the folder and extract all files
             
            cd BandUp_3.0.0-beta5_mod_ZhiWang
            tar bandup-3.0.0-beta.5.tar.gz
    
   * 2. Find and REPLACE 
    
            band_unfolding_routines_mod.f90
            main_BandUP.f90
            
      under ./bandup-3.0.0-beta.5/src/ with the modified files.
      
   * 3. Run the "build" script:
   
            ./build
            
      to get the executable binary 'bandup'

   * 4. Compile read_spec.f90 to get the executable binary 'read_spec' (or whatever name you like)
        

<!-- ============================================================ -->
## To unfold the supercell band structure:

   * 1. Prepare
   
            a. The supercell VASP POSCAR, renamed to 'supercell_lattice.in'
            b. The primitive cell VASP POSCAR, renamed to 'prim_cell_lattice.in'
            c. The KPOINTS file that contains the band path in primitive cell, renamed to 'KPOINTS_prim_cell.in'
      
      Lattice vectors in (a) and (b) must be related by an integer matrix, i.e., A = MB, where M is an integer matrix

   * 2. In the same folder that contains 1(a-c), run

            bandup kpts-sc-get

   * 3. Find the generated 'KPOINTS_supercell.out', rename to 'KPOINTS' then use it as the input to VASP band calculation

   * 4. Get
   
            (a) OUTCAR
            (b) WAVECAR
            
      from VASP band calculation

   * 5. Link or copy iv(a-b) to the same folder that contains i(a-c)

   * 6. In the same folder, create a file named 'energy_info.in' with 4 lines:
   
            line 1: fermi energy
            line 2: -1.0
            line 3: 1.0
            line 4: 0.01
      
   * 7. In the same folder, run

            bandup unfold
    
   * 8. Wait until the code exits, check if
   
            unfolded_spectral_function.dat
            unfolded_parameters.dat
            
      have been generated.
         
            In an older version, you needed to modify these files, but now you don't need to touch them.

   * 9. In the same folder, run

            ./read_spec

      and check if 'unfolded_bands.dat' has been generated

<!-- ============================================================ -->
## To do orbital-projected unfolding

   * For those who found BandUP problematic when doing the orbital projected unfolding (like me), you may want to have an alternative. Here is one:
    
   * a. Follow steps i-viii noted above; remember to set LORBIT >= 10 when you do the non SCF unfolding VASP run.
           
   * b. Before step 9, use vaspkit to get the orbital-projected band structure WITH folding (BAND.dat).
   
            Vaspkit can only do this with a line-style KPOINTS, so you need to use a "dummy" KPOINTS file.
            Make sure it has the same NUMBER of k-points as the real file; the coordinates of k are not important.
           
   * c. Delete all comment lines in "BAND.dat" file.

   * d. Rename the modified BAND.dat into
    
            folded_orbital_projection.dat
            
      Then put it together with unfolded_spectral_function.dat
           
   * e. Now run
    
            ./read_spec

      and the code will find the new file and ask you which column you want to use for the orbital projection. The column index starts from 1
      
            1: k-point coordinates, which is useless;
            2: s orbital
            3: p orbital (or 3-5 if LORBIT = 11)
            4: d orbital (or 6-10 if LORBIT = 11)
            Note: The current code only supports a single number, not a range :(
           
   * Now you have the 'unfolded_bands.dat' containing only one specific orbital.

<!-- ============================================================ -->
## To plot the EBS:

   * Open 'contour_plot.py', find the required libraries and change the input parameters as you need

   * In the folder that contains 'unfolded_bands.dat', run

            python contour_plot.py
