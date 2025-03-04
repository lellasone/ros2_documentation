.. redirect-from::

   Installation/Linux-Development-Setup
   Installation/Ubuntu-Development-Setup

Ubuntu (source)
===============

.. contents:: Table of Contents
   :depth: 2
   :local:


System requirements
-------------------
The current Debian-based target platforms for {DISTRO_TITLE_FULL} are:

- Tier 1: Ubuntu Linux - Noble (24.04) 64-bit
- Tier 3: Debian Linux - Bullseye (11) 64-bit

As defined in `REP 2000 <https://www.ros.org/reps/rep-2000.html>`_.

System setup
------------

Set locale
^^^^^^^^^^

.. include:: ../_Ubuntu-Set-Locale.rst

Enable required repositories
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. include:: ../_Apt-Repositories.rst

Install development tools
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   sudo apt update && sudo apt install -y \
     python3-pip \
     python3-pytest-cov \
     python3-flake8-blind-except \
     python3-flake8-class-newline \
     python3-flake8-deprecated \
     python3-pytest-repeat \
     python3-pytest-rerunfailures \
     ros-dev-tools

Build ROS 2
-----------

Get ROS 2 code
^^^^^^^^^^^^^^

Create a workspace and clone all repos:

.. code-block:: bash

   mkdir -p ~/ros2_{DISTRO}/src
   cd ~/ros2_{DISTRO}
   vcs import --input https://raw.githubusercontent.com/ros2/ros2/{REPOS_FILE_BRANCH}/ros2.repos src

.. _linux-development-setup-install-dependencies-using-rosdep:

Install dependencies using rosdep
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. include:: ../_Apt-Upgrade-Admonition.rst

.. code-block:: bash

   sudo rosdep init
   rosdep update
   rosdep install --from-paths src --ignore-src -y --skip-keys "fastcdr rti-connext-dds-6.0.1 urdfdom_headers"

.. include:: ../_rosdep_Linux_Mint.rst

Install additional RMW implementations (optional)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The default middleware that ROS 2 uses is ``Fast DDS``, but the middleware (RMW) can be replaced at build or runtime.
See the :doc:`guide <../../How-To-Guides/Working-with-multiple-RMW-implementations>` on how to work with multiple RMWs.

Build the code in the workspace
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you have already installed ROS 2 another way (either via Debians or the binary distribution), make sure that you run the below commands in a fresh environment that does not have those other installations sourced.
Also ensure that you do not have ``source /opt/ros/${ROS_DISTRO}/setup.bash`` in your ``.bashrc``.
You can make sure that ROS 2 is not sourced with the command ``printenv | grep -i ROS``.
The output should be empty.

More info on working with a ROS workspace can be found in :doc:`this tutorial <../../Tutorials/Beginner-Client-Libraries/Colcon-Tutorial>`.

.. code-block:: bash

   cd ~/ros2_{DISTRO}/
   colcon build --symlink-install

.. note::

   If you are having trouble compiling all examples and this is preventing you from completing a successful build, you can use the ``--packages-skip`` colcon flag to ignore the package that is causing problems.
   For instance, if you don't want to install the large OpenCV library, you could skip building the packages that depend on it using the command:

   .. code-block:: bash

      colcon build --symlink-install --packages-skip image_tools intra_process_demo

Setup environment
-----------------

Set up your environment by sourcing the following file.

.. code-block:: bash

   # Replace ".bash" with your shell if you're not using bash
   # Possible values are: setup.bash, setup.sh, setup.zsh
   . ~/ros2_{DISTRO}/install/local_setup.bash

.. _talker-listener:

Try some examples
-----------------

In one terminal, source the setup file and then run a C++ ``talker``\ :

.. code-block:: bash

   . ~/ros2_{DISTRO}/install/local_setup.bash
   ros2 run demo_nodes_cpp talker

In another terminal source the setup file and then run a Python ``listener``\ :

.. code-block:: bash

   . ~/ros2_{DISTRO}/install/local_setup.bash
   ros2 run demo_nodes_py listener

You should see the ``talker`` saying that it's ``Publishing`` messages and the ``listener`` saying ``I heard`` those messages.
This verifies both the C++ and Python APIs are working properly.
Hooray!

Next steps
----------

Continue with the :doc:`tutorials and demos <../../Tutorials>` to configure your environment, create your own workspace and packages, and learn ROS 2 core concepts.

Alternate compilers
-------------------

Using a different compiler besides gcc to compile ROS 2 is easy. If you set the environment variables ``CC`` and ``CXX`` to executables for a working C and C++ compiler, respectively, and retrigger CMake configuration (by using ``--force-cmake-config`` or by deleting the packages you want to be affected), CMake will reconfigure and use the different compiler.

Clang
^^^^^

To configure CMake to detect and use Clang:

.. code-block:: bash

   sudo apt install clang
   export CC=clang
   export CXX=clang++
   colcon build --cmake-force-configure

Stay up to date
---------------

See :doc:`../Maintaining-a-Source-Checkout` to periodically refresh your source installation.

Troubleshoot
------------

Troubleshooting techniques can be found :ref:`here <linux-troubleshooting>`.

Uninstall
---------

1. If you installed your workspace with colcon as instructed above, "uninstalling" could be just a matter of opening a new terminal and not sourcing the workspace's ``setup`` file.
   This way, your environment will behave as though there is no {DISTRO_TITLE} install on your system.

2. If you're also trying to free up space, you can delete the entire workspace directory with:

   .. code-block:: bash

      rm -rf ~/ros2_{DISTRO}
