solstice-dev-setup
============================================================

Repository that contains base scripts to setup development environment for Solstice project.

It contains code to automatize the clone of all the Plot Twist and ArtellaPipe repos available.

Requirements
################

* Windows 10
* `Python 2.7.16 <https://www.python.org/ftp/python/2.7.16/python-2.7.16.amd64.msi>`_: We are using this version of Python as our core version. Install it in your machine and make sure that you add it in your Windows system path.
* `virtualenv <https://virtualenv.pypa.io/en/latest//>`_: We will need it to create a virtualenv with proper requirements to launch our code.
* Autodesk Maya: A big part of our pipeline it's oriented to be used inside DCCs. Our core target DCC is Maya but currently our pipeline supports different DCCs such as Houdini or Nuke. Depending of the department you are going to work with you will need to download more or less DCCs. The only that is mandatory for all departments is Maya.

1) Install Solstice & ArtellaPipe (optional) repositories
##############################################################

1. **Clone/Download** this repo in any folder in your local drive.
2. Launch **clone_solstice_repos.bat**. This batch file will create a new environment variable, will install all dependencies and will download all the Plot Twist available GitHub repositories inside this folder.
3. **Optional**. If you are going to work with ArtellaPipe related code you can download all ArtellaPipe repositories launching **clonde_artellapipe_repos.bat**.
4. Once your repos are installed you can move them anywhere in your local drive.

2) Setup Solstice development virtual environment
#######################################################

1. **Clone/Download** this repo in any folder in your local drive (skip this step is you have already done previously).
2. Inside dev/win folder you will find a file named **dev_requirements.txt**. In this file is where you will need to specify all the Plot Twist repositories you are going to work with. You need to make sure that you write -e before writing the repository path to indicate pip that you are going to use a local requirement. Here you can find the basic template that you will need to use (make sure that you replace the paths in the example by yours):

.. code-block::

    -e D:/dev/solstice/solstice
    -e D:/dev/solstice/solstice-config
    -e D:/dev/solstice/solstice-bootstrap

Once your dev_requirements.txt file is setup, you just need to launch **dev_setup.bat** file. This file will create a new
virtual environment named **solstice_dev** and will install all the dependencies specified in dev_requirements.txt.

.. note::

    Once the virtual environment is created if you add new requirements to **dev_requirements.txt**, you can launch **update_requirements.bat** to install new added requirements to the already created virtual environment.

    This batch file can also to update your requirements to their latest available version.

3) Launch Plot Twist Pipeline in Maya
##########################################

Once our Plot Twist virtual environment is created we can start using it inside Maya.

1. Launch Maya.
2. Open Maya Script Editor and in a Python tab copy the code that you can find **dev/solstice_dev.py** file.

.. code-block:: python

    import os
    import sys
    import maya.cmds as cmds

    # Path where you Plot Twist development virtual environment package are located.
    # For example: D:\dev\solstice\solstice\dev\win\solstice\Lib\site-packages'
    dev_path = r''
    paths_to_add = [dev_path]
    for f in os.listdir(dev_path):
        if f.endswith('.egg-link'):
            file_path = os.path.join(dev_path, f)
            with open(file_path) as f:
                new_path = f.readline().rstrip()
                if os.path.isdir(new_path):
                    paths_to_add.append(new_path)

    for p in paths_to_add:
        if os.path.isdir(p) and p not in sys.path:
            sys.path.append(p)

    # =============================================================

    import artellapipe
    import solstice.loader
    solstice.loader.init(True, dev=True)

    # Example of how to launch a tool
    # artellapipe.ToolsMgr().run_tool('artellapipe-tools-artellamanager', do_reload=True, debug=False)


First we add the path where we have installed all our requirements into into Python sys.path. Also, when using -e flag
in requirements.txt all our local requirements are installed in that folder as .egg-link files. Those files are text files
that contains the path where the repository is located in your local machine. We use that info to add also all those paths
into Python sys.path

After that we just initialize Plot Twist loader. This loader basically setup Plot Twist project and also initializes all
ArtellaPipe related code.
