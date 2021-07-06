<a href="https://sharma-bharat.github.io/" style="float: right;">*Homepage*</a> \
<a href="https://sharma-bharat.github.io/Posts.html" style="float: right;">*Posts*</a>

## How to regrid the netcdf files?
### Here we will discuss two most common methods that are used for regridding: Tempestremap and ncremap 

1.[TempestRemap](https://github.com/ClimateGlobalChange/tempestremap) (by Paul Ulrich) is a conservative, consistent and monotone remapping package for arbitrary grid geometry with support for finite volumes and finite elements.

```
./bin/GenerateRLLMesh --lon 720 --lat 360 --file grid_360x720.g
./bin/GenerateRLLMesh --lon 360 --lat 180 --file grid_180x360.g
./bin/GenerateOverlapMesh --a grid_360x720.g --b grid_180x360.g --out overlap_grid_360to180.g

./bin/GenerateOfflineMap --in_mesh grid_360x720.g --out_mesh grid_180x360.g \
                        --ov_mesh overlap_grid_360to180.g --in_np 2 \
                        --out_map map_tempest_360x720_to_180x360.20210701.nc

./bin/ApplyOfflineMap  --map map_tempest_360x720_to_180x360.20210701.nc --var gpp\
                         --in_data GPP_ANN_CRUNCEPv6_Monthly_1980_2013_Updated.nc\
                         --out_data GPP_ANN_CRUNCEPv6_Monthly_1980_2013_tempest_180x360.nc
```



2.[ncremap](https://acme-climate.atlassian.net/wiki/spaces/DOC/pages/754286611/Regridding+E3SM+Data+with+ncremap#Intermediate-Regridding-II%3A-TempestRemap) has many options of regridding that includes conservative, biliniear, nearest and also sub grade scale (sgs). **sgs** is especially useful incase the climate variable is dependent on landfrac.

Options: 
default: bilinear
-a aave: conserve
-P sgs: includes sub grade scale

```
export NCO_PATH_OVERRIDE='No'                                                               
export HDF5_USE_FILE_LOCKING=FALSE
module load ncl

ncks --rgr grd_ttl='Equi-Angular grid 192x288'#latlon=192,288#lat_typ=uni#lon_typ=grn_wst \
     --rgr scrip=192x288_SCRIP.20210701.nc \
        CESM2_ssp585_r1i1p1f1_gpp_gCm-2.nc \
        test_foo_cesm.nc

ncks --rgr grd_ttl='Equi-Angular grid 64x128'#latlon=64,128#lat_typ=uni#lon_typ=grn_wst \
     --rgr scrip=64x128_SCRIP.20210701.nc \
         CESM2_ssp585_r1i1p1f1_gpp_gCm-2.nc \
         test_fooo_cesm.nc

ncremap -a aave -P sgs \
            -s 192x288_SCRIP.20210701.nc \
            -g 64x128_SCRIP.20210701.nc \
            -m map_aave_sgs_nco_192x288_and_64x128.20210701.nc \
            CESM2_ssp585_r1i1p1f1_gpp_gCm-2.nc \
            CESM2_ssp585_r1i1p1f1_gpp_gCm-2_nco_sgs_aave_64x128.nc
```

### Some points to keep in mind:
* The variable should be assumed such that in any location within a grid, the values are same. For instance, flux density, precipitation, and temperature follow the rule. 
* Use density (g m-2 s-1) variables rather than mass flux (g s-1) as conservative remapping means the area-weighting summary or average. For GPP in gC/mon, the rule is broken.
* the variable type should be *double* of dimensions.
* run *ncdump -h* on the source file, if the masked data does not show as "- -", mask the data properly. 
* The units of area variable are stredians. To get the area in km2 use a multiplying facor of 6371*6371 km^2.
* If the data is of high resolution, use *tempestremap*.

If you have the access to Cori Nersc, visit the dir : **/global/cfs/cdirs/m2467/bharat/Regridding_Techniques**. It has observation and model data and scripts designed to regrid them to coarser resolution. The file `Regridding_Example.ipynb` is designed to test the output of your regrided file and compare with source file.

### The comparison of regridded data vs original data:
* For a random time of 1981-12-31 for Flux ANN, Total GPP:
     * Original (360x720)                 : 7.24 PgC
     * Regrid Tempest (180x360)           : 6.14 PgC (when mask was NaN)
     * Regrid ncremap -conserve (180x360) : 6.74 PgC (when mask was NaN)
     * Regrid ncremap -bilinear (180x360) : 7.02 PgC (when mask was NaN)
     * Regrid (FV) ncremap -conserve (180x360) : 7.24 PgC (when mask was "- -")

* For a random time of 1851-12-15 for CESM2, Total GPP:
     * Original (192x288)                : 7.51 PgC
     * Regrid Tempest (64x128)           : 7.47 PgC
     * Regrid ncremap -conserve (64x128) : 7.47 PgC
     * Regrid ncremap -bilinear (64x128) : 7.47 PgC
     * Original w/landfrac (192x288)          : 6.18 PgC
     * Regrid ncremap -conserve -sgs (64x128) : 6.15 PgC
