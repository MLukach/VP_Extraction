# DRUID
Repo for the Vertical Profile generation code, used in the DRUID, BioDar and PESTdar projects

This repo contains a refactored version of the QVP (quasi-vertical profile) extraction code which has been extended to allow static CVP (column vertical profile) extraction. Code has also been added that allows dynamic CVP extraction, however this code is not yet executable.

The program has also had functionality added to be able to read ODIM formatted HDF5 files. Due to some issues with assumptions made by the ODIM reading function in pyart, a modified version of the read_odim_h5 function has been included in the program which subsets the input file by pulse type and time.This is necessary as the pyart ODIM reader is capable of reading ODIM files generated by the [Nimrod2ODIM](https://github.com/cemac/Radar_ODIM_conv) script, however aggregated files generated by the [hdf5 aggregator](https://github.com/ncasuk/nimrod_hdf5_aggregator) are not read properly due to the aggregated nature of the files themselves. While this is an acceptable fix, it may not be entirely future proof. There may also be an argument for including these changes in a PR to the ARM-pyart repo.

Included in this repo is an environment file which can be used for running the program using python 3.8

## Usage

Program can be invoked with: `python vp_extraction.py profile_type from_directory to_directory -s eventT000000 -e eventT235959 [OPTIONALS]`

Provided arguments should be profile type, input directory (`from_directory`), and the output directory (`to_directory`).

It is assumed, that the events subdirectories are in the input directory.

The name of the output file will be generated automatically based on the date from the start (-s) option and on the elevation angle. It will look like 20170517_QVP_20deg.nc and will be placed in the output directory at the end of the run.

Options
The possible 8 options in the function include:
	   -d, —debug : output debug messages during program execution;
	   -v, --verbose: print messages (debug,info,warning,...) about the program execution to the console (stderr);
	   -s , --start time (included):  date and time in format: yyyymmdd'T'HHMMSS;
	   -e , --end time (included): date and time in format: yyyymmdd'T'HHMMSS;
	   -f , --fields for QVP generation of variables for QVP calculation;
	   -z , --zoom time-interval: Time interval to zoom, Formatted as (yyyymmdd'T'HHMMSS,yyyymmdd'T'HHMMSS);
	   -c , --count threshold: Minimal number of points for the mean value calculation at each range;
	   -a , --azimuth bounds to exclude: Azimuths, that should be excluded in the mean value calculation. Format [star1,end1;start2,end2];

-s and -e options allow to make a QVP over several days, providing input directory and assuming each day (event - YYYYmmdd) is inside a subdirectory with a corresponding name (e.g. ./20170203/ and ./20170204/ in the directory ./). For the 90 deg. elevation subfolder ver/ is assumed after the event subdirectory (e.g. ./20170203/ver/). The format of these options is: '20170517T000000'. First part before “T” is the date, the second part is the time (HHMMSS). Setting up these options to -s $eventT000000 -e $eventT235959, where $event is the date will collect all the nc-files from the event’s subdirectory.

-f option is the list of variables to be included in QVP file. It’s assumed, that all these variables can be found in the input nc-files. (e.g. current default list is ['PhiDP','RhoHV','SQI','W','dBZ','ZDR','KDP','V']) Field list should be provided as a space-separated list (i.e. 'PhiDP RhoHV SQI W dBZ ZDR KDP V')

-z option allows to zoom into a part of the day event. Might be redundant as it was used before adding -s and -e options.

-c option is the count threshold that is used as a minimum number of non-NAN azimuthal values to be used for the calculation of an azimuthal mean. The value used here should be proportional (e.g. 2/3) to the remaining number of azimuthal directions if -z option is used. Another way to ignore it, is to set it to 1, so that any azimuth with at least one non-NaN value will be used.

-a option provides azimuth bounds to exclude from the average calculation. Possible values are from 0 to 359 (e.g. 45,185). This option can be used to remove certain azimuthal directions from the calculation if there is an obstacle or a known artefact influencing the observations.  


and that the elevation angle is in the list of elevations in the volume radar data file.