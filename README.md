# cold_pulses
# Detection of cold water intrusion in a weakly-stratified environment.
*Robin Guillaume-Castel - 2020*

 

This package allows you to accurateley detect individual cold pulses events in a time series over several depths.

## Before starting
### Input files
Before getting into the algorithm, you will need to prepare input files that will be used. Several (two or more) csv input files are necessary to make the algorithm work. They should fit the folowing criteria:
- The files should show data from the same location
- The files should show data from different depth levels
- If more than two depth levels are used, the depths should be equally spaced
- Each file should be in two columns representing time and temperature, in this order.

To make the algorithm work at its best, the files should preferably:
- not show a strong daily variability
- not show a strong vertical stratification
Visual inspection could be used to observe those criteria.

We suggest using depths that are multiples of 5 to create equally spaced data. Round your files to the nearest one.
### Preparing working directory
Once your files are ready, you should create a working directory in your computer.
Then, download the 'cold_pulse_detection.py' file available in the [example_input folder](https://github.com/rguilcas/cold_pulses/tree/master/example_input). This is the python script that we will use to launch the algorithm from command line.

Put the file in the working directory you created. 

Create a new folder with the name of your choice in the working directory and put your csv files in your directory.

The stucture of your working directory should be:
- 'working_directory'
 - 'cold_pulse_detection.py'
 - 'new_folder'
  - 'csv_file1.csv'
  - 'csv_file2.csv'
  - ...

Open the 'cold_pulse_detection.py' file in a text editor and modify the information accordingly with your csv files and 'new_folder' names.

The 'cold_pulse_detection.py' file is made of four sections, two of which should me adapted to your needs:
- 'INPUT DATA', **should be modified**: This is where input variables such as file names and depths should be entered. 
- 'ALGORITHM PARAMETERS', **should be modified**: This is where algorithm numerical parameters can be changed.
- 'CREATE CONFIGURATION DATA': This is where configuration data are created from 'INPUT DATA' and 'ALGORITHM PARAMETERS';
- 'RUN ALGORITHM': This is where the algorithm is run.

Note that most of the fields should be in quotation marks as this is how string should be in Python.

The 'INPUT_DATA' section contains:
- 'INPUT_DIR', *quotation marks needed*: Directory where the csv files are stored. You should replace the value by the name you gave to 'new_folder'.
- 'OUTPUT_NAME', *quotation marks needed*: Name that will be used for output files. We recommend using a new name for each run.
- 'BOT', ***True** or **False** without quotation marks*: Should be **True** if you want to detect deep pulses, **False** if not.
- 'TOP', ***True** or **False** without quotation marks*: Should be **True** if you want to detect surface pulses, **False** if not.
- 'PREPARE_CSV', ***True** or **False** without quotation marks*: Should be **True** if your csv files have not been prepared yet, **False** if not. This will create a NetCDF file 'prepared_csv.nc' in your 'new_folder'. Should be **True** for the first run with new files.
- 'FILE_NAMES', *quotation marks needed*: Python *list* containing names of your csv files. Do not forget to add *.csv* at the end of each file name. A list should be in square braquets, with comas to separate each file name.
    
    FILE_NAME = ['file_name1.csv',
    		 'file_name2.csv']

- 'FILE_DEPTHS', *no quotation mark needed*: Python *list* containing loggers depths for each of your csv files. Depths should be in the same order as the 'FILE_NAME' field. For example:

    FILE_DEPTHS = [15,
                   25]
	
indicates that *file_name1.csv* is from a 15 meters deep logger and *file_name1.csv* is from a 25 meters deep one.
- 'TIME_FILE_NAME', *quotation marks needed*: Name of the file that will be used for time interpolation. All other files will be interpolated over this file's time steps to create the NetCDF file. It needs to be one of the files in the 'FILE_NAME' field. Do not forget to add *.csv* at the end of the file name.

### Preparing your Python environment

***If Python is already installed on your machine and is ready to use from command line, skip this step***

Once your input files are ready, you need to install Python on your machine. We suggest downlading the [Anaconda environment] ( https://www.anaconda.com/). If you are using Windows, make sure to tick the box 'Add anaconda to your PATH' when installing it. If you are using Mac, this should be automatic.

!!!WARNING!!!
Make sure python is installed on your machine, along with the pip library (automatically installed on most python GUI). 
If it isn't, we suggest downloading and installing anaconda first: https://www.anaconda.com/. If on windows, you will be asked to add anaconda to your path, where you will have to tick the box.

Once python is installed ad the config_file modified, open a command prompt (anaconda prompt in windows) and go to your working directory.

If it is the first time you use the package, install the cold_pulses package using:

	pip install git+http://github.com/rguilcas/cold_pulses

Then for any other time, use from your working directory:

	python cold_pulse_detection.py

This will start the script and output two to three files in the output_dir chosen.

The files available are:
- one or two csv (bot_stats and/or top_stats) files. These files give specific information on all individual pulses detected (top or bottom pulses depending on the file)

The columns contained are:

	start_time			The starting time step of the pulse

	duration			The duration of the pulse (in minutes)

	gammaD1, gammaD2, ... 		The Degree Cooling Hours of the pulse (°C.h) for depth level 1, level 2, ... 

	dropD1, dropD2, ... 		The maximum temperature drop of the pulse for depth level 1, level 2, ...

	init_tempD1, init_tempD2,...    The initial temperature of the pulse for depth level 1, level 2, ...

	start, end 			The start and end indexes of the pulse in the time series

- one netcdf file containing time series of different parameters
		
The fields available are:
			
	temp : time series of the temperature in several depths

	dch_top (only if top was True in the config file): instantaneous degree cooling hours for top pulses at different depths

	dch_bot (only if bot was True in the config file): instantaneous degree cooling hours for bottom pulses at different depths

	pulse_temp_top (only if top was True in the config file): temperature time series where all temperature values outside of top pulses are nans

	pulse_temp_bot (only if bot was True in the config file): temperature time series where all temperature values outside of bottom pulses are nans

