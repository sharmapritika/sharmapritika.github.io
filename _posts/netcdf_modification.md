To convert the nc coords from (0, 360) to (-180,180)

Source : [link](https://stackoverflow.com/questions/53345442/about-changing-longitude-array-from-0-360-to-180-to-180-with-python-xarray/53561230#53561230)
```
lon_name = 'longitude'  # whatever name is in the data

# Adjust lon values to make sure they are within (-180, 180)
ds['_longitude_adjusted'] = xr.where(
    ds[lon_name] > 180,
    ds[lon_name] - 360,
    ds[lon_name])

# reassign the new coords to as the main lon coords
# and sort DataArray using new coordinate values
ds = (
    ds
    .swap_dims({lon_name: '_longitude_adjusted'})
    .sel(**{'_longitude_adjusted': sorted(ds._longitude_adjusted)})
    .drop(lon_name))

ds = ds.rename({'_longitude_adjusted': lon_name})

ds_new = ds.fillna(1.e+36)
ds_new.longitude.attrs["axis"] = "X"
ds_new.longitude.attrs["units"] = "degrees_east"
ds_new.longitude.attrs["standard_name"] = "Longitude"
ds_new.latitude.attrs["axis"] = "Y"
ds_new.time.attrs["axis"] = "T"
```
---

Reverse the dimension of lat : [90, 85, ..., -85, -90] becomes [-90, -80, ..., 85, 90]

Source: [link](https://stackoverflow.com/questions/54677161/xarray-reverse-an-array-along-one-coordinate)

```
ds = ds.reindex(lat=ds.lat[::-1])
```
---

When you get Error : `AttributeError: NetCDF: String match to name in use` while saving the netcdf file,
Do: (source: [link](https://github.com/Unidata/netcdf4-python/issues/1020))
`ds.to_netcdf(path+filename_save,format='NETCDF3_64BIT')`

---
