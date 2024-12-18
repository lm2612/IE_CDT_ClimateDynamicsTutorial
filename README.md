# Climate Dynamics

Imagine you work for the Met Office and a client (e.g., from agriculture, utilities companies, finance) has asked you predict the temperature in Oxford, UK on 19th November 2025 and on 19th November 2100. You have access to data from climate model simulations (CMIP) available on Jasmin at `/badc/cmip6/data/CMIP6/CMIP/` or downloaded from [the Climate Data Store (cds)](https://cds.climate.copernicus.eu/datasets/projections-cmip6?tab=download). You are welcome to use data from any other source if you wish. The different timescales require different approaches.

Think about: 
* What data and methods would you use here?
* How would you approach the different timescales differently?
* Are there any assumptions you would make?
* What information would you provide to the client? 
* What sources of uncertainty do you expect in your prediction?

You can either work locally or on Jasmin. Using Jasmin is easier and recommended as you will have more options, but the CMIP6 archive server has been down recently so the backup option is to run these locally using pre-processed files. 

### Set up: local
To use these notebooks on your local macbook, you will need to have a few packages. You can use the same conda environment `iecdt_eda` you set up in week 3:
```zsh
conda env update -n iecdt_eda --file environment.yaml  
conda activate iecdt_eda
```

### Set up: Jasmin
Go to [https://notebooks.jasmin.ac.uk/](https://notebooks.jasmin.ac.uk/) and start a jupyter notebook. Packages should already be installed on `jaspy`. You should automatically have access to the CMIP data at `/badc/cmip6/data/CMIP6/CMIP/`. 

## Time series approach

To predict the temperature at a specific location on 19th November 2025, we can time series data to inform us about the historical temperatures in this location.

It is recommended to use jasmin. The jupyter notebook [jasmin/PredictTimeseriesSetupJasmin.ipynb](jasmin/PredictTimeseriesSetupJasmin.ipynb) will help you open the dataset. This data comes from a historical simulation of the Met Office coupled climate model UKESM. 

```python
data_path = "/badc/cmip6/data/CMIP6/CMIP/MOHC/UKESM1-0-LL/historical/r1i1p1f2/day/tas/gn/v20190627/"
filename = f"{data_path}tas_day_UKESM1-0-LL_historical_r1i1p1f2_gn_19500101-20141230.nc"
ds = xr.open_mfdataset(filename)
```

In case there are issues on jasmin, there is some CMIP data already pre-processed in the `./local/data/` directory. This contains monthly averaged data from the historical simulation of the Met Office climate model HadGEM. The jupyter notebook [local/PredictTimeseriesSetupLocal.ipynb](local/PredictTimeseriesSetupLocal.ipynb) will help you get started with this data.


## Spatial pattern approach

For longer term predictions in 2100, we need to consider global warming. Assume that in 2100, the global mean temperature will be ~1.5K warmer than today (or ~3.0K warmer than pre-industrial levels) [IPCC, 2021]. The jupyter notebook [jasmin/PredictSpatialPatternSetupJasmin.ipynb](jasmin/PredictSpatialPatternSetupJasmin.ipynb) will help you open two climate model datasets: one from a pre-industrial climate simulation and one from a 4xCO2 climate simulation. 

```python
# Pre-industrial (PI) simulation
data_path_PI = "/badc/cmip6/data/CMIP6/CMIP/MOHC/UKESM1-0-LL/piControl/r1i1p1f2/Amon/tas/gn/v20190410/"
filename_PI = f"{data_path_PI}tas_Amon_UKESM1-0-LL_piControl_r1i1p1f2_gn_196001-204912.nc"
ds_PI = xr.open_dataset(filenames_PI)

# 4xCO2 simulation
data_path_4xCO2 = "/badc/cmip6/data/CMIP6/CMIP/MOHC/UKESM1-0-LL/abrupt-4xCO2/r1i1p1f2/Amon/tas/gn/v20190406/"
filename_4xCO2 = f"{data_path_4xCO2}tas_Amon_UKESM1-0-LL_abrupt-4xCO2_r1i1p1f2_gn_195001-199912.nc"
ds_4xCO2 = xr.open_dataset(filename_4xCO2)
```

In case there are issues on jasmin, I have also added the time mean of this data into the `./local/data/` directory of this repository. See [local/PredictSpatialPatternSetupLocal.ipynb](local/PredictSpatialPatternSetupLocal.ipynb).

With a technique called "Pattern Scaling", you can interpolate between these two to estimate the global map of climate change for a given CO2 forcing scenario (Santer, 1990, Mitchell, 2003, Tebaldi & Arblaster, 2014). Pattern scaling assumes that the pattern of warming remains constant but scales linearly by a scaler variable, such as global mean temperature. The steps are:

1. Take temperature difference between two scenarios
2. Normalise pattern by global mean temperature change - this gives the pattern of warming per 1K of warming (so should be centered around 1).
3. Scale the pattern by the new global mean temperature change


## Other approaches

Think about other approaches you could take to predict the temperature at some time point in the future. How would you blend both temporal and spatial data to improve your predictions? How would you leverage artificial intelligence (AI) techniques?


## References
* Santer, B. D., Wigley, T. M., Schlesinger, M. E., & Mitchell, J. F. (1990). Developing climate scenarios from equilibrium GCM results.
* Mitchell, T. D. (2003). Pattern scaling: an examination of the accuracy of the technique for describing future climates. Climatic change, 60(3), 217-242.
* Tebaldi, C., & Arblaster, J. M. (2014). Pattern scaling: Its strengths and limitations, and an update on the latest model simulations. Climatic Change, 122, 459-471.
* IPCC, 2021: Summary for Policymakers. In: Climate Change 2021: The Physical Science Basis. Contribution of Working Group I to the Sixth Assessment Report of the Intergovernmental Panel on Climate Change [Masson-Delmotte, V., P. Zhai, A. Pirani, S.L. Connors, C. Péan, S. Berger, N. Caud, Y. Chen, L. Goldfarb, M.I. Gomis, M. Huang, K. Leitzell, E. Lonnoy, J.B.R. Matthews, T.K. Maycock, T. Waterfield, O. Yelekçi, R. Yu, and B. Zhou (eds.)]. In Press.

