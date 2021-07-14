---
layout: post 
title: NCO tools for netcdf operations
---

<a href="https://sharma-bharat.github.io/" style="float: right;">*Homepage*</a> \
<a href="https://sharma-bharat.github.io/Posts.html" style="float: right;">*Posts*</a>

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

op_typ key values: ‘sbt’, ‘-’, ‘dff’, ‘diff’, ‘sub’, ‘subtract’, ‘subtraction’
```
file3=file1-file2
ncdiff 1.nc 2.nc 3.nc
ncbo --op_typ=- 1.nc 2.nc 3.nc
```

### for addition

op_typ key values: ‘add’, ‘+’, ‘addition’
```
file3=file1+file2
ncadd 1.nc 2.nc 3.nc
ncbo --op_typ=+ 1.nc 2.nc 3.nc
```

### for multiplication

op_typ key values: ‘mlt’, ‘*’, ‘mult’, ‘multiply’, ‘multiplication’
```
file3=file1*file2
ncmult 1.nc 2.nc 3.nc
ncbo --op_typ=* 1.nc 2.nc 3.nc
```

### for division

op_typ key values: ‘dvd’, ‘/’, ‘divide’, ‘division’
```
file3=file1/file2
ncdivide 1.nc 2.nc 3.nc
ncbo --op_typ=/ 1.nc 2.nc 3.nc
```

In case you want to compute the flux density of gpp, i.e. GPP (in gC mon-1) **divide** area (m2) = gpp (gC m-2 month-1). if GPP is 3-d file and area is 2-d, `ncbo` commands work well provided the variable name of the area file is renames to `gpp`.

``` ncbo --op_typ=/ CESM2_ssp585_r1i1p1f1_gpp_gCm-2_64x128.nc AREA64.nc  gpp_flux_density.nc```


--------------
## Averaging an ensemble of realizations:
>```nces 85_01.nc 85_02.nc 85_03.nc 85_04.nc 85_05.nc 85.nc
nces 85_0[1-5].nc 85.nc```
### Ensemble/Variable averaging:
>`ncea bnu_gpp_regrid.nc can_gpp_regrid.nc ccsm_gpp_regrid.nc mod_av_gpp_regrid.nc`
-----
## Renaming the attribute(long_name) of a variable "CVEG" to "LEAFC+WOODC"
>http://nco.sourceforge.net/nco.html#ncatted \
`ncatted -a long_name,CVEG,o,c,LEAFC+WOODC cesm1bgc_pftcon_leafc.nc`

-----
## Adding an attribute
>http://dvalts.io/2018/05/14/More-NCO-fun.html \
`ncatted -a standard_name,col_fire_closs,a,c,"col_fire_closs anomalies" cesm1bgc_pftcon_col_fire_closs_anomalies.nc`

----
## Deleting an attribute
>`ncatted -a stardard_name,col_fire_closs,d,, cesm1bgc_pftcon_col_fire_closs_anomalies.nc`

----
## Filling NaN in place of some _FillingValue or vice versa
>http://nco.sourceforge.net/nco.html#nan 
* from _FillingValue to NaN\
`ncatted -a _FillValue,,o,f,NaN bnu_nep_regrid.nc`
* from Nan to _filling value\
`ncatted -a _FillValue,,m,f,1.0e36 in.nc`
* e.g.\
`cp gpp_ccsm_hist test.nc #test file has gpp variable`
* setting the fill value to 1e33\
`ncatted -a _FillValue,gpp,o,f,1.e33 test.nc `\
--info -a:attribute; o: overwrite;f:float \
* setting the missing value to 1e33 \
`ncatted -a missing_value,gpp,o,f,1.e33 test.nc`

----
## Concatenation
>for concatenating the historical and the rcp simulations i used nco concatenation tool called **ncrcat** \
eg.:\
`ncrcat ccsm_gpp_hist_regrid.nc ccsm_gpp_rcp_regrid.nc ccsm_gpp_regrid.nc`\
the concatenation result of there two files are stored in the rightmost file

### Concatenate nc files over lats
>http://forum.marine.copernicus.eu/discussion/205/how-to-mergeconcatenate-cmems-netcdf-files-tips/p1	
* Adding a record dimension only to the first file
    * `ncks -O --mk_rec_dmn recorddimension in.nc out.nc`
    * `mv decomp_trend_ccsm_gpp_000.nc rd_decomp_trend_ccsm_gpp_000.nc`
    * `ncks -O -h --mk_rec_dmn lat rd_decomp_trend_ccsm_gpp_000.nc rd_decomp_trend_ccsm_gpp_000.nc`
* Conacatenating files
    * `ncrcat -h outfrom1.nc file_1.nc file_2.nc file_outfrom1_1_2.nc `\
    (use [-h] if you dont want to have histroy file)
    * `ncrcat -h rd_decomp_trend_ccsm_gpp_000.nc decomp_trend_ccsm_gpp_*.nc decomp_trend_ccsm_gpp.nc`
* Fixing the record dimension
    * `ncks -O -h --fix_rec_dmn lat decomp_seasonal_ccsm_nep.nc decomp_seasonal_ccsm_nep.nc`
* Check your file
    * `ncdump -h decomp_trend_ccsm_gpp.nc |less`
---
## Masking the values of the original file using the land fraction nc file

`ncks -A sftlf.nc gpp.nc` \
it will copy the variable from sftlf.nc to gpp.nc; so gpp.nc will have variable(s) from sftlf.nc \
\where the land frac is 0 or less set the corresponding gpp variable to missing value `ncap2 -s "where(sftlf<=0) gpp=gpp.get_miss();" test.nc testfixfill.nc`

---
## Extracting Variable(s)

* to extract only one variable from a nc file
    * `ncks -C -v gpp mod_av_gpp_regrid.nc mod_av_gpp_regrid_prep.nc`
* to extract two or more variable from a nc file
    * `ncks -C -v gpp,time,lat,lon bnu_gpp_regrid.nc bnu_gpp_regrid_sel.nc`
* to copy two or more variable from a nc file to other
    * `ncks -A -v gpp,time,lat,lon bnu_gpp_regrid.nc bnu_gpp_regrid_sel.nc`

---
## Splitting nc files

```
ncks -d time,0,729 hgt.2006.nc hgt.2006-01.nc
ncks -d time,730,1459 hgt.2006.nc hgt.2006-02.nc 
```
e.g.
```
ncks -d time,1560,1919 bnu_gpp_regrid_sel.nc bnu_gpp_regrid_sel_time.nc
ncks -d lat,0,179 bnu_gpp_regrid_sel_time.nc bnu_gpp_regrid_sel_time_l1.nc
ncks -d time,12,1151 b.e10.BRCP85BDRD.pftcon.f09_g16.001.clm2.h0.SMOIST.200501-210012.nc rcp.nc
```
---
## Change the type of the variable

`ncap2 -s 'var2=double(var1)' in.nc out.nc`

---
