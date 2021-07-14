---
layout: post 
title: How to mask fill values in existing netcdf file?
---

<a href="https://sharma-bharat.github.io/" style="float: right;">*Homepage*</a> \
<a href="https://sharma-bharat.github.io/Posts.html" style="float: right;">*Posts*</a>

## How to mask fill values in existing netcdf file?
#### In case *ncdump -h* command does not recognises your fill or missing values you can do the following.

Mask the NaNs in fillvalue of the file (with a variable `gpp`) using `xarray`.

``` 
import xarray as xr
ds = xr.open_dataset("abc.nc")
ds_new = ds.fillna(1.e+36)
\# ds_new = ds.fillna(1.e+36).drop_vars("areacella") # incase you also want to remove a variable.
ds_new.gpp.attrs["_FillValue"] = 1.e+36
ds_new.gpp.attrs["missing_value"] = 1.e+36
ds_new.to_netcdf(f"abc_new.nc", unlimited_dims={'time':True})

```