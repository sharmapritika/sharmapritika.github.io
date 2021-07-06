## How to regrid the netcdf files?
### Here we will discuss two most common methods that are used for regridding: Tempestremap and ncremap 

[TempestRemap](https://github.com/ClimateGlobalChange/tempestremap)[newtab] (by Paul Ulrich) is a conservative, consistent and monotone remapping package for arbitrary grid geometry with support for finite volumes and finite elements.

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