.. _windows-latest:

Building ROS 2 on Windows
=========================

.. contents:: Table of Contents
   :depth: 2
   :local:

This guide is about how to setup a development environment for ROS2 on Windows.

Prerequisites
-------------

First follow the steps for `Installing Prerequisites <Eloquent_windows-install-binary-installing-prerequisites>` on the Binary Installation page.

Stop and return here when you reach the "Downloading ROS 2" section.

Additional Prerequisites
^^^^^^^^^^^^^^^^^^^^^^^^

When building from source you'll need a few additional prerequisites installed.

Install Additional Prerequisites from Chocolatey
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

First install git:

.. code-block:: bash

   > choco install -y git

You will need to append the Git cmd folder ``C:\Program Files\Git\cmd`` to the PATH (you can do this by clicking the Windows icon, typing "Environment Variables", then clicking on "Edit the system environment variables".
In the resulting dialog, click "Environment Variables", the click "Path" on the bottom pane, then click "Edit" and add the path).

Then install ``patch``:

.. code-block:: bash

   > choco install -y patch

You may need to close the cmd prompt and open a new one, but at this point you should be able to run ``git``\ , ``python``\ , ``cmake``\ , and ``patch.exe --version``.

Installing Developer Tools
--------------------------

Now we are ready to install some our tools that we use to help in developing ROS 2.

Let's start with ``vcstool``:

.. code-block:: bash

   > pip install -U vcstool

You can test it out by just running ``vcs`` (you should be able to do this in the same cmd prompt).

Next, install ``colcon``:

.. code-block:: bash

   > pip install -U colcon-common-extensions

You can test it out by just running ``colcon`` (you should be able to do this in the same cmd prompt).

Also, you should install ``curl``:

.. code-block:: bash

   > choco install -y curl

Install dependencies
--------------------

Next install the latest version of ``setuptools`` and ``pip``:

.. code-block:: bash

   > <PATH_TO_PYTHON_EXECUTABLE> -m pip install -U setuptools pip

Where ``PATH_TO_PYTHON_EXECUTABLE`` looks like: ``c:\python37\python.exe``

Then you can continue installing other Python dependencies:

.. code-block:: bash

   > pip install -U catkin_pkg cryptography EmPy ifcfg lark-parser lxml numpy pyparsing pyyaml

Next install testing tools like ``pytest`` and others:

.. code-block:: bash

   > pip install -U pytest pytest-mock coverage mock

Next install linters and checkers like ``flake8`` and others:

.. code-block:: bash

   > pip install -U flake8 flake8-blind-except flake8-builtins flake8-class-newline flake8-comprehensions flake8-deprecated flake8-docstrings flake8-import-order flake8-quotes mypy pep8 pydocstyle

Next install cppcheck:

.. code-block:: bash

   > choco install -y cppcheck

Next install xmllint:

* Download the `64 bit binary archives <https://www.zlatkovic.com/pub/libxml/64bit/>`__ of ``libxml2`` (and its dependencies ``iconv`` and ``zlib``) from https://www.zlatkovic.com/projects/libxml/
* Unpack all archives into e.g. ``C:\xmllint``
* Add ``C:\xmllint\bin`` to the ``PATH``.

Install Qt5
^^^^^^^^^^^

This section is only required if you are building rviz, but it comes with our default set of sources, so if you don't know, then assume you are building it.

First get the installer from Qt's website:

https://www.qt.io/download

Select the Open Source version and then the ``Qt Online Installer for Windows``.

Run the installer and install Qt5.
We recommend you install it to the default location of ``C:\Qt``, but if you choose somewhere else, make sure to update the paths below accordingly.
When selecting components to install, the only thing you absolutely need for bouncy and later is the appropriate MSVC 64-bit component under the ``Qt`` -> ``Qt 5.10.0`` tree.
We're using ``5.10.0`` as of the writing of this document and that's what we recommend since that's all we test on Windows, but later version will probably work too.
For bouncy and later, be sure to select ``MSVC 2017 64-bit``. For ardent use ``MSVC 2015 64-bit``.
After that, the default settings are fine.

Finally, set the ``Qt5_DIR`` environment variable in the ``cmd.exe`` where you intend to build so that CMake can find it:

.. code-block:: bash

   > set Qt5_DIR=C:\Qt\5.10.0\msvc2017_64
   : You could set it permanently with ``setx -m Qt5_DIR C:\Qt\5.10.0\msvc2017_64`` instead, but that requires Administrator.

.. note::

   This path might change based on which MSVC version you're using or if you installed it to a different directory.

RQt dependencies
~~~~~~~~~~~~~~~~

.. code-block:: bash

   > pip install -U pydot PyQt5

Getting the Source Code
-----------------------

Now that we have the development tools we can get the ROS 2 source code.

First setup a development folder, I use ``C:\dev\ros2_eloquent``:

.. code-block:: bash

   > md \dev\ros2_eloquent\src
   > cd \dev\ros2_eloquent

Get the ``ros2.repos`` file which defines the repositories to clone from:

.. code-block:: bash

   # CMD
   > curl -sk https://raw.githubusercontent.com/ros2/ros2/eloquent/ros2.repos -o ros2.repos

   # PowerShell
   > curl https://raw.githubusercontent.com/ros2/ros2/eloquent/ros2.repos -o ros2.repos

Next you can use ``vcs`` to import the repositories listed in the ``ros2.repos`` file:

.. code-block:: bash

   # CMD
   > vcs import src < ros2.repos

   # PowerShell
   > vcs import --input ros2.repos src

Getting a DDS Vendor
--------------------

You'll also need a DDS Vendor available for ROS to build against.
There is currently support for eProsima FastRTPS, ADLINK's OpenSplice, and RTI's Connext DDS.
The source distribution of ROS 2 includes FastRTPS, so it will always build unless explicitly ignored.

ADLINK OpenSplice
^^^^^^^^^^^^^^^^^

If you would like to also build against OpenSplice, you will need to first download the latest version of `OpenSplice <https://github.com/ADLINK-IST/opensplice/releases>`__.
Then run something like the following command before building ROS 2, to set up the OpenSplice environment:

.. code-block:: bash

   call "C:\opensplice69\HDE\x86_64.win64\release.bat"

where the exact paths may need to be slightly altered depending on where you selected to install OpenSplice.

RTI Connext 5.3
^^^^^^^^^^^^^^^

If you would like to also build against RTI Connext DDS there are options available for `university, purchase or evaluation <../Install-Connext-University-Eval>`

After installing, use the RTI Launcher to load your license file.

Then before building ROS 2, set up the Connext environment:

.. code-block:: bash

   call "C:\Program Files\rti_connext_dds-5.3.1\resource\scripts\rtisetenv_x64Win64VS2017.bat"

Note that this path might need to be slightly altered depending on where you selected to install RTI Connext DDS, and which version of Visual Studio was selected.
The path above is the current default path as of version 5.3.1, but will change as the version numbers increment in the future.

If you want to install the Connext DDS-Security plugins please refer to `this page <../Install-Connext-Security-Plugins>`.

If you don't install any additional DDS vendors, ROS 2 will default to using eProsima's Fast-RTPS as the middleware.

Building the ROS 2 Code
-----------------------

.. _windows-dev-build-ros2:

To build ROS 2 you will need a Visual Studio Command Prompt ("x64 Native Tools Command Prompt for VS 2019") running as Administrator.

FastRTPS is bundled with the ROS 2 source and will always be built unless you put an ``AMENT_IGNORE`` file in the ``src\eProsima`` folder.

To build the ``\dev\ros2_eloquent`` folder tree:

.. code-block:: bash

   > colcon build --merge-install

.. note::

   We're using ``--merge-install`` here to avoid a ``PATH`` variable that is too long at the end of the build.
   If you're adapting these instructions to build a smaller workspace then you might be able to use the default behavior which is isolated install, i.e. where each package is installed to a different folder.

.. note::

   If you are doing a debug build use ``python_d path\to\colcon_executable`` ``colcon``.
   See `Extra stuff for debug mode`_ for more info on running Python code in debug builds on Windows.

Testing and Running
-------------------

Note that the first time you run any executable you will have to allow access to the network through a Windows Firewall popup.

You can run the tests using this command:

.. code-block:: bash

   > colcon test --merge-install

.. note::

   ``--merge-install`` should only be used if it was also used in the build step.

Afterwards you can get a summary of the tests using this command:

.. code-block:: bash

   > colcon test-result

To run the examples, first open a clean new ``cmd.exe`` and set up the workspace.
This is done by sourcing the ``local_setup.bat`` file, which will automatically set up the environment for any DDS vendors that support was built for.
Then execute the examples, e.g.:

.. code-block:: bash

   > call install\local_setup.bat
   > ros2 run demo_nodes_py talker

In a separate shell you can do the same, but instead run the ``listener``\ :

.. code-block:: bash

   > call install\local_setup.bat
   > ros2 run demo_nodes_py listener

For more explanations see the `Python Programming </Tutorials/Python-Programming>` demo or `other tutorials </Tutorials>`.

.. note::

   It is not recommended to build in the same cmd prompt that you've sourced the ``local_setup.bat``.

Alternative DDS Sources
-----------------------

The demos will attempt to build against any detected DDS vendor.
The only bundled vendor is eProsima's Fast RTPS, which is included in the default set of sources for ROS 2.
To build for other vendors, make sure that your chosen DDS vendor(s) are exposed in your environment when you run the build.
If you would like to change which vendor is being used see: `Working with Multiple RMW Implementations </Tutorials/Working-with-multiple-RMW-implementations>`

Troubleshooting
---------------

CMake error setting modification time
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you run into the CMake error ``file INSTALL cannot set modification time on ...`` when installing files it is likely that an anti virus software or Windows Defender are interfering with the build. E.g. for Windows Defender you can list the workspace location to be excluded to prevent it from scanning those files.

260 Character Path Limit
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   The input line is too long.
   The syntax of the command is incorrect.

You may see path length limit errors when building your own libraries, or maybe even in this guide as ROS2 matures.

Run ``regedit.exe``, navigate to ``Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem``, and set ``LongPathsEnabled`` to 0x00000001 (1).

Hit the windows key and type ``Edit Group Policy``.
Navigate to Local Computer Policy > Computer Configuration > Administrative Templates > System > Filesystem.
Right click ``Enable Win32 long paths``, click Edit.
In the dialog, select Enabled and click OK.

Close and open your terminal to reset the environment and try building again.

CMake Packages Unable to Find asio, tinyxml2, tinyxml, eigen, or log4cxx
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We've seen, but been unable to identify the root cause, that sometimes the chocolatey packages for ``asio``, ``tinyxml2``, etc. do not add important registry entries and that will cause CMake to be unable to find them when building ROS 2.

It seems that uninstalling the chocolatey packages (with ``-n`` if the uninstall fails the first time) and then reinstalling them will fix the issue.

patch.exe Opens a New Command Window and Asks for Administrator
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This will also cause the build of packages which need to use patch to fail, even you allow it to use administrator rights.

The solution, for now, is to make sure you're building in a Visual Studio command prompt which has been run as administrator.
On some machines canceling the prompt without selecting "Yes" will also work.

Failed to load FastRTPS shared library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

FastRTPS requires ``msvcr20.dll``, which is part of the ``Visual C++ Redistributable Packages for Visual Studio 2013``.
Although it is usually installed by default in Windows 10, we know that some Windows 10 like version don't have it installed by default (e.g.: Windows Server 2019).
In case you haven't it installed, you can download it from `here <https://www.microsoft.com/en-us/download/details.aspx?id=40784>`_.

Extra stuff for Debug mode
--------------------------

If you want to be able to run all the tests in Debug mode, you'll need to install a few more things:


* To be able to extract the Python source tarball, you can use PeaZip:

.. code-block:: bash

   > choco install -y peazip


* You'll also need SVN, since some of the Python source-build dependencies are checked out via SVN:

.. code-block:: bash

   > choco install -y svn hg


* You'll need to quit and restart the command prompt after installing the above.
* Get and extract the Python 3.7.3 source from the ``tgz``:

  * https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tgz
  * To keep these instructions concise, please extract it to ``C:\dev\Python-3.7.3``

* Now, build the Python source in debug mode from a Visual Studio command prompt:

.. code-block:: bash

   > cd C:\dev\Python-3.7.3\PCbuild
   > get_externals.bat
   > build.bat -p x64 -d


* Finally, copy the build products into the Python37 installation directories, next to the Release-mode Python executable and DLL's:

.. code-block:: bash

   > cd C:\dev\Python-3.7.3\PCbuild\amd64
   > copy python_d.exe C:\Python37 /Y
   > copy python37_d.dll C:\Python37 /Y
   > copy python3_d.dll C:\Python37 /Y
   > copy python37_d.lib C:\Python37\libs /Y
   > copy python3_d.lib C:\Python37\libs /Y
   > for %I in (*_d.pyd) do copy %I C:\Python37\DLLs /Y


* Now, from a fresh command prompt, make sure that ``python_d`` works:

.. code-block:: bash

   > python_d
   > import _ctypes

* Once you have verified the operation of ``python_d``, it is necessary to reinstall a few dependencies with the debug-enabled libraries:

.. code-block:: bash

   > python_d -m pip install --force-reinstall https://github.com/ros2/ros2/releases/download/numpy-archives/numpy-1.16.2-cp37-cp37dm-win_amd64.whl
   > python_d -m pip install --force-reinstall https://github.com/ros2/ros2/releases/download/lxml-archives/lxml-4.3.2-cp37-cp37dm-win_amd64.whl

* To verify the installation of these dependencies:

.. code-block:: bash

   > python_d
   # No import errors should appear when executing the following lines
   > from lxml import etree
   > import numpy

* When you wish to return to building release binaries, it is necessary to uninstall the debug variants and use the release variants:

.. code-block:: bash

   > python -m pip uninstall numpy lxml
   > python -m pip install numpy lxml

* To create executables python scripts(.exe), python_d should be used to invoke colcon

.. code-block:: bash

   > python_d path\to\colcon_executable build

* Hooray, you're done!

Troubleshooting
---------------

Troubleshooting techniques can be found `here </Troubleshooting>`.
