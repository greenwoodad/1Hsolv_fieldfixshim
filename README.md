# 1Hsolv_fieldfixshim

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
versions (boiling/freezing points will be approximate). Next, edit the solvents  
and uncheck "lock solvent" for each.

### Determining fieldbyHz value

Insert a sample that produces a tall, sharp 1H peak. Tune/match and shim. Turn 
the lock and lock sweep off. Open up the BSMS display (bsmsdisp) and navigate 
to the lock panel. Collect a series of 1H spectra with a variety of field
values. Record the peak position in Hz at each field value. Do a linear regression
to obtain the slope (units of field/Hz) of the line. It should be close to 0.14 
field units/Hz. Replace the value of fieldbyHz in the script (around line 39) with
this value.

### Setting TS version flags

Open up 1Hsolv_fieldfixshim in a text editor and look at the first few
lines of code. You can generally use the two default flags:

```sh
#define READPEAKSDEFINED 1 
#define BSMSDOUBLEDEFINED 1 
```

for Topspin 3 and up. For Topspin 2, you will probably need to set these
to 0. 

If you are curious which to use, you can look in the aulib files.
Check <TSHOME>/prog/include/lib/auliba.h for the text 'BsmsDouble' and
<TSHOME>/prog/include/lib/aulibp.h for the text 'readPeakList'. (Substitute
the directory of your Topspin installation for <TSHOME>). Note that
in Linux you can run 

```sh
grep BsmsDouble <TSHOME>/prog/include/lib/auliba.h
grep readPeakList <TSHOME>/prog/include/lib/aulibp.h 
```

at the terminal and check if there is output. If the command is present in 
the aulib file, set the flag to '1'. Otherwise set it to '0'. 

Practically, if the program fails to compile with one flag, try the alternative.

### Adding additional solvents

If desired, additional solvents can be added by editing lines 42-48 of the macro:

1) Add the new solvent to your solvent table if it is not already there.
2) Increase the integer nomofsolvs on line 42 by 1 and the lengths of the arrays
   on lines 44-48 as well.
3) Add another char variable on line 43-- it will look something like:
   r[64]="New Solvent" where "New Solvent" is the name of the solvent in your
   solvent table. 
4) Add the char r (or s, or t, depending on how many you have added) to the end
   of the solvlist array on line 44. 
5) Consider (collect if necessary) the 1H spectrum of your solvent. Provide
   the number of multiplets that could possibly be the tallest depending on 
   variation in the shimming at the end of the numsolvpeakslist array on line 45.
   This is usually (but not always) 1.
6) Add a value to the end of the highorlowlist array on line 46. This should be
   1 if you have an un-ambiguously tallest peak, 2 if you want to reference and
   shim on the most downfield of your "tall" peaks, and 3 if you want to
   reference and shim on the most upfield of them.
7) Add a value to the end of the multiplicitylist array on line 47. This should
   be 1 if the multiplet selected in step 6 is a singlet, triplet, pentet etc. and
   2 if the multiplet is a doublet, quartet, etc.
8) Add the shift to the end of the solvpeaklist array on line 48. This is the
   value you want to reference your selected multiplet to be. Note that the shift
   of your solvent neat (as opposed to dilute in CDCl3) may not be common knowledge,
   and you may choose to collect a spectrum of TMS in your solvent to determine
   this shift. Note also that I have not bothered to do this for the currently
   provided shift array!
    
## Usage

Running manually in Topspin, insert your sample, turn the lock-sweep off, specify 
the (protonated) solvent, and tune/match. You can then run 1Hsolv_fieldfixshim in
the command line and the macro will set the field and shim. There will be a 1H 
spectrum in expno 90X where X is the expno you were in when you ran the program.

In ICON-NMR, you can edit the solvent-specific locking and shimming programs in
the configuration. Set the lock program for each of your protonated solvents to
'LOCK-OFF' and the shim program to '1Hsolv_fieldfixshim.'



## Contributing
Pull requests are welcome. 

## Authors

  - **Alex Greenwood** - *provided script* -
    [Greenwoodad](https://github.com/Greenwoodad)

## License
[MIT](https://choosealicense.com/licenses/mit/)
