# 1Hsolv_fieldfixshim

(This README is not yet complete)

Topspin macro to adjust field and shim on proton signal of non-deuterated NMR solvents

1Hsolv_fieldfixshim is a Topspin AU program that iteratively shims and sets the  
field according to the 1H signal of the solvent. It reads the solvent from the current
datafile and according to the settings for that particular solvent, it identifies a 
key multiplet (usually the tallest or most upfield tall multiplet) for shimming and 
"referencing" the field. This can be set as the shim routine in the ICON-NMR 
configuration for protonated solvents, with the lock program set to 'LOCK-OFF'. 

## Prerequisites

This has only been tested on Topspin 2, 3 and 4. There are two flags that may need to 
be adjusted from the defaults if running in Topspin 2.

## Installing

Place the file in your <TSHOME>\exp\stan\nmr\au\src\user directory.

## Getting Started

### Setting up your solvent table

Open up edsolv. Add any of the following solvents that you wish, if not present:
Acetic-H4, Acetone-H6, C6H6, CH2Cl2, CH3CN, CHCl3, Dioxane-H8, DMF-H7, DMSO-H6, 
Ethanol-H6, H2O, Isopropanol-H8, Methanol-H4, Pyr-H5, TFE-H3, THF-H8, Tol-H8

Add more if desired. It may be convenient to copy and edit the original 2H 
versions (freezing points will be approximate). Next, edit the solvents and 
uncheck "lock solvent" for each.

### Determining fieldbyHz value

Insert a sample that produces a tall, sharp peak. Tune/match and shim. Turn 
the lock and lock sweep off. Open up the BSMS display (bsmsdisp) and navigate 
to the lock panel. Collect a series of 1H spectra with a variety of field
values. Record the peak position in Hz at each field value. Do a linear regression
to obtain the slope (units of field/Hz) of the line. It should be close to 0.14 
field units/Hz. Replace the value of fieldbyHz in the script (around line 39) with
this value.

### Setting TS version flags

Open up 1Hsolv_fieldfixshim in a text editor and look at the first few
lines of code. You can generally use the two default flags:

#define READPEAKSDEFINED 1 
#define BSMSDOUBLEDEFINED 1 

for Topspin 3 and up. For Topspin 2, you will probably need to set these
to 0. 

If you are curious which to use, you can look in the aulib files.
Check <TSHOME>/prog/include/lib/auliba.h for the text 'BsmsDouble' and
<TSHOME>/prog/include/lib/aulibp.h for the text 'readPeakList'. (Substitute
the directory of your Topspin installation for <TSHOME>). Note that
in Linux you can run 

grep BsmsDouble <TSHOME>/prog/include/lib/auliba.h
grep readPeakList <TSHOME>/prog/include/lib/aulibp.h 

at the terminal and check if there is output. If the command is present in 
the aulib file, set the flag to '1'. Otherwise set it to '0'. 

Practically, if the program fails to compile with one flag, try the alternative.

### Usage

In Topspin, insert your sample, turn the lock-sweep off, specify the (protonated) 
solvent, and tune/match. You can then run 1Hsolv_fieldfixshim in the command line
and the macro will set the field and shim. There will be a 1H spectrum in expno
90X where X is expno you were in when you ran the program.

In ICON-NMR,



## Contributing
Pull requests are welcome. 

## Authors

  - **Alex Greenwood** - *provided script* -
    [Greenwoodad](https://github.com/Greenwoodad)

## License
[MIT](https://choosealicense.com/licenses/mit/)
