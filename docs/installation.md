## Requirements

* An [Islandora](https://islandora.ca/) repository using Drupal 9.2 or higher, with the [Islandora Workbench Integration](https://github.com/mjordan/islandora_workbench_integration) module enabled. If you are using Drupal 9.1 or earlier, please refer to the "Using Drupal 9.1 or earlier" section below.
* Python 3.6 or higher
* The following Python libraries:
    * [ruamel.yaml](https://yaml.readthedocs.io/en/latest/index.html)
    * [Requests](https://2.python-requests.org/en/master/)
    * [progress_bar](https://pypi.org/project/progress_bar/)
    * [openpyxl](https://pypi.org/project/openpyxl/)
    * If you want to have these libraries automatically installed, you will need Python's [setuptools](https://pypi.org/project/setuptools/)

Islandora Workbench has been installed and used on Linux, Mac, and Windows.

!!! warning
    Some systems have both Python 2 and Python 3 installed. It's a good idea to check which version is used when you run `python`. To do this, run `python --version`, which will output something like "Python 2.7.17" or "Python 3.6.9". If `python --version` indicates you're running version 2, try running `python3 --version` to see if you have version 3 installed.

## Installing Islandora Workbench

If you already have the required Python libraries installed, simply cloning Islandora Workbench from GitHub is all you need to do:

`git clone https://github.com/mjordan/islandora_workbench.git`

This will create a directory named `islandora_workbench` where you will run the `./workbench` command.

If you don't already have the required libraries installed, clone this repo as above, and then use `setup.py`:

`sudo python3 setup.py install`

After you run this, all of the required Python libraries will be installed.

## Updating Islandora Workbench

Since Islandora Workbench is under development, you will want to update it often. To do this, run the following `git` command:

`git pull origin main`

After you pull in the latest changes using `git`, it's a good idea to rerun the setup tools in case new Python libraries have been added since you last ran the setup tools (same command as above):

`sudo python3 setup.py install`

## Using Drupal 9.1 or earlier

When ingesting media in Drupal versions 8.x - 9.1, Islandora Workbench has two significant limitations/bugs that you should be aware of:

* Approximately 10% of media creation attempts will likely fail. Workbench will log these failures. Additional information is available in this [issue](https://github.com/Islandora/documentation/issues/1481).
* A file with a filename that already exists in Islandora will overwrite the existing file, as reported in this [issue](https://github.com/Islandora/documentation/issues/1790).

To avoid these issues, you need to be running Drupal version 9.2 or higher.
