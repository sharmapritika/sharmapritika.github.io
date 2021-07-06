<a href="https://sharma-bharat.github.io/" style="float: right;">*Homepage*</a>

## Adition and substraction
when variable names are different
1. rename the variable names in the original file


`ncrename -v(variable) original_name,new_name in.nc(file_nc)`

```
ncrename -v gpp,nep can_gpp.nc
ncrename -v ra,nep can_ra.nc
ncrename -v ra,nep can_rh.nc
```
2. substract/add using any of the following section's methods, `NEP = GPP - Ra -Rh`
```
ncdiff can_gpp.nc can_ra.nc can_gpp-ra.nc
ncdiff can_gpp-ra.nc can_rh.nc can_nep.nc
```

**Note: variable names must be same**

### for substraction
>`file3=file1-file2`\
`ncdiff 1.nc 2.nc 3.nc`

### for addition
>`file3=file1+file2`\
`ncadd 1.nc 2.nc 3.nc`\
`ncbo --op_typ=add 1.nc 2.nc 3.nc`

### for multiplication
>`file3=file1*file2`\
`ncmult 1.nc 2.nc 3.nc`

### for division
>`file3=file1/file2`\
op_typ key values: ‘dvd’, ‘/’, ‘divide’, ‘division’\
`ncdivide 1.nc 2.nc 3.nc`\
`ncbo --op_typ=/ 1.nc 2.nc 3.nc`


--------------
\
## Averaging an ensemble of realizations:
>`nces 85_01.nc 85_02.nc 85_03.nc 85_04.nc 85_05.nc 85.nc`
`nces 85_0[1-5].nc 85.nc`

-----
\
## Renaming the attribute(long_name) of a variable "CVEG" to "LEAFC+WOODC"
http://nco.sourceforge.net/nco.html#ncatted \
`ncatted -a long_name,CVEG,o,c,LEAFC+WOODC cesm1bgc_pftcon_leafc.nc`


