# DPLAFest 2017 DPLA Metadata Analysis Workshop

Welcome to the open repository, documentation and materials for the DPLAfest 2017 DPLA Metadata Analysis Workshop.

**Table of Contents for this Document**

- [About the Workshop](#about-the-workshop)
- [Workshop Schedule](#workshop-schedule)
- [Contact Before, During, After the Workshop](#contact-before-during-after-the-workshop)
- [Our Expectations of You](#our-expectations-of-you)
- [Participant Requirements (Installation Requests)](#participant-requirements)


## About the Workshop

The DPLAfest 2017 DPLA Metadata Analysis Bootcamp for Hubs & Partners will be held **1 PM to 5 PM, Wednesday, April 19, 2017 at the Chicago Public Library’s Harold Washington Library Center** (enter at 401 S. Plymouth Street and read more about space logistics [here](https://dp.la/info/get-involved/dplafest/april-2017/travel/)). We will meet for the workshop in the Reception Room. The workshop will be followed by a small reception for Hub members in the South Hall.

This workshop is a registration-only event for DPLA Hubs & Partners. For registration information or other logistical questions before the event, please email Gretchen Gueguen, DPLA Data Services Coordinator and workshop organizer: [gretchen (at) dp (dot) la](mailto:gretchen@dp.la).

We ask that all participants come to the workshop ready to dive in by reviewing the information in this document. If you want a sneak peak of the workshop's contents, feel free to also watch this repository (you'll be notified of updates). To watch this repository, sign into GitHub, go this repository's home URL, and click the following button:

![Image of Watching a GitHub Repository Button Dropdown](images/WatchingGHRepo.png)

## Workshop Schedule

Time | Lesson | Leader | Lesson Materials
-----|--------|--------|-----------------
**1-1:15 PM** | Intros & Welcome, Housekeeping, etc. (15 minutes)| [Gretchen](mailto:gretchen@dp.la) | n/a
**1:15-1:45 PM** | DPLA MAP 4.0 and the basic requirements/crosswalk & mapping goals (30 minutes) | [Gretchen](mailto:gretchen@dp.la) | [http://bit.ly/dpla-metadata-bootcamp](http://bit.ly/dpla-metadata-bootcamp)
**1:45-2:15 PM** | Command line basics (30 minutes) | [Audrey](mailto:audrey@dp.la) | [Bash_Exercises.md](Bash_Exercises.md)
**2:15-2:45 PM** | Harvesting OAI feed at the command line (30 minutes) | Christina | [Metadata_Harvest.md](Metadata_Harvest.md)
**2:45-3 PM** | break (15 minutes) | n/a | n/a
**3-4:30 PM** | Metadata analysis (90 minutes, including working time) | Christina | TBD
**4:30-5 PM** | Other DPLA metadata analysis tools to explore (30 minutes) | [Gretchen](mailto:gretchen@dp.la) | [http://bit.ly/dpla-metadata-bootcamp](http://bit.ly/dpla-metadata-bootcamp)

## Contact Before, During, After the Workshop

If you have questions or concerns leading up to or after the workshop, you can get in touch in a number of ways:

- Please email Gretchen Gueguen, DPLA Data Services Coordinator and workshop organizer, particularly with logistical or registration questions: [gretchen (at) dp (dot) la](mailto:gretchen@dp.la).
- If you feel comfortable doing so, please open an issue on this GitHub repository, particularly with any questions dealing with workshop preparation or any installation issues. This allows multiple workshop leaders to respond as able, and other participants can also learn (since we're sure the same questions will come up multiple times): https://github.com/dpla/Metadata-Analysis-Workshop/issues (this will require that you login or create a free account with GitHub).
- Alternatively, you can email all the workshop leaders - [Gretchen](mailto:gretchen@dp.la), [Christina Harlow](mailto:cmharlow@gmail.com), and [Audrey Altman](mailto:audrey@dp.la) - with questions if you feel uncomfortable using GitHub issues.

During the workshop, we will indicate the best ways to get help or communicate a question/comment - however, this workshop is intended to be informal, so feel free to speak up or indicate you have a question at any time.

## Our Expectations of You

To keep this workshop a safe and inclusive space, we ask that you review and follow the [DPLAfest 2017 Code of Conduct](https://dp.la/info/get-involved/dplafest/april-2017/dplafest-2017-code-of-conduct/) and [the Recurse Center Social Rules (aka Hacker School Rules)](https://www.recurse.com/manual#sub-sec-social-rules).

## Participant Requirements

We request that all participants **bring their own laptop**, preferably one you have installation or admin privileges on, to the workshop. The laptop should have the software listed below installed as well as an up-to-date modern web browser (basically, not Internet Explorer). If you have any issues with the requested set-up, please contact us ASAP using the [communication methods detailed above](#contact-before-during-after-the-workshop).

### Bash Shell (Required)
Bash is a commonly-used shell that gives you the power to do simple tasks more quickly.

Platform | Installation Instructions | Link to Installation Help
---------|---------------------------|--------------------------
Windows  | Cygwin or Git for Windows (which also installs a bash shell). | See here for installing [Cygwin](https://www.cygwin.com/cygwin-ug-net/setup-net.html), and see [here](https://git-for-windows.github.io/) for installing Git for Windows. We recommend Git for Windows if you also want to install Git (see recommendations, below)
Mac OS X | Comes pre-installed - look for Terminal (found in /Applications/Utilities) | n/a
Linux    | Comes pre-installed - The default shell is usually Bash, but if your machine is set up differently you can run it by opening a terminal and typing bash. | n/a

#### Bash Shell Install Test

Open Cygwin, Git for Windows, Terminal or your default Linux shell and test your bash install by running two simple commands (do not include the $ symbol):

```bash
$ cd
$ ls -A
```

The output should look similar (though your listed files will be different) to this:

![Bash output of test change directory and list commands](images/BashTest.png)

### Python & Pip (Required)
Python is a programming language that is helpful for scripting work with data (among other uses). Pip is a package management library for Python - i.e., it installs bits of existing Python code for you easily so you can run more scripts.

We won't be learning Python in this workshop, but how to run and work with a set of existing Python scripts.

Regardless of how you choose to install it, please make sure you have **Python version 2.7.x** or greater for Python 2 (Python 3 will mostly likely not work).

Platform | Installation Instructions | Link to Installation Help
---------|---------------------------|--------------------------
Windows  | Install Python 2.7.x (which comes with Pip) by using the installer from the the Python website (the website should auto-detect your OS). | https://www.python.org/downloads/
Mac OS X | Python 2.7 & Pip is usually pre-installed on Macs OS X. If not, or you want to get a later version, download the installer for 2.7.x from the Python website (the website should auto-detect your OS). | https://www.python.org/downloads/
Linux  | Install Python 2.7.x (which comes with Pip) by using the installer from the the Python website (the website should auto-detect your OS). | https://www.python.org/downloads/

#### Python & Pip Install Test

Open Cygwin, Git for Windows, Terminal or your default Linux shell and test your Python install by running these commands (do not include the $ symbol):

```bash
$ python --version
$ pip --version
```

The output should look similar (though your versions might be slightly different) to this:

![Bash output of Python and Pip version commands](images/PythonTest.png)

### Python Virtual Environments (Recommended)
Virtualenv (or Virtual Environments) is a Python library that allows you to create isolated environments on your computer to install packages. This protects your system Python needs from any changes in your Python experimentation, as well as having other useful aspects.

If you installed a Bash shell, Python & Pip as outlined above, you can then use Pip to install virtualenv.

Platform | Installation Instructions | Link to Installation Help
---------|---------------------------|--------------------------
Windows, Mac OS X, or Linux  | In your Bash shell: `pip install virtualenv` | https://python-guide-pt-br.readthedocs.io/en/latest/dev/virtualenvs/

#### Python VirtualEnv Install Test

Open Cygwin, Git for Windows, Terminal or your default Linux shell and test your Python VirtualEnv install by running these commands (do not include the $ symbol):

```bash
$ virtualenv test
$ ls
```

The output should look similar to this, as we've created a test Python virtualenv called 'test' and then show it's presence in our working directory:

![Bash output of test python virtualenv creation](images/VenvTest.png)

### Git & GitHub Account (Recommended)
Git is a version control system, and GitHub is a hosting platform for Git Repositories.

These are not required, but we strongly recommend them for ease of pulling down the workshop data, scripts, & documentation.

Platform | Installation Instructions | Link to Installation Help
---------|---------------------------|--------------------------
Windows  | Download & Run the Git for Windows [installer](https://git-for-windows.github.io/). After that installation completes, follow only [step 2 forward on the GitHub setup site](https://help.github.com/articles/set-up-git/#platform-windows) to finish your Git/GitHub installation & setup. | https://git-for-windows.github.io/, https://help.github.com/articles/set-up-git/#platform-windows
Mac OS X | Install and set up Git following the directions in the link. Note - this may require you download XCode Tools first, which can be a lengthy (but not complicated) download. | https://help.github.com/articles/set-up-git/#platform-mac
Linux    | If Git is not already available on your machine you can try to install it via your distro's package manager. For Debian/Ubuntu run sudo apt-get install git and for Fedora run sudo yum install git. | https://help.github.com/articles/set-up-git/#platform-linux

#### Git & GitHub Install Test

Open Cygwin, Git for Windows, Terminal or your default Linux shell and test your Git install and GitHub setup by running these commands (do not include the $ symbol):

```bash
$ cd
$ git --version
$ mkdir workshop-test
$ cd workshop-test
$ git clone https://github.com/dpla/Metadata-Analysis-Workshop.git
$ ls
```

The output should look similar to this. We've requested our Git version (`git --version`), then created a folder for our workshop outputs (`mkdir workshop-test`), changed (`cd workshop-test`) into that folder, cloned the Git Repository from GitHub for this workshop (`git clone ...`), then listed our files to show the presence of that clone Git repository (`ls`):

![Bash output of test python virtualenv creation](images/GitTest.png)
