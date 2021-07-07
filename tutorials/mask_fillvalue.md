## How to mask fill values in existing netcdf file?
#### In case *ncdump -h* command does not recognises your fill or missing values you can do the following.

* Read the file (with a variable `gpp`) using `xarray` .
``` 
import xarray as xx
ds = xr.ods
ds_new = ds.fillna(1.e+36)
# ds_new = ds.fillna(1.e+36).drop_vars("areacella") # incase you also want to remove a variable.
ds_new.gpp.attrs["_FillValue"] = 1.e+36
ds_new.gpp.attrs["missing_value"] = 1.e+36
ds_new.to_netcdf(f"abc_new.nc", unlimited_dims={'time':True})

```