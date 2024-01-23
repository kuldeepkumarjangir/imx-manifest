Step 1:- Environment setup for yocto build from official guide
  Essential Yocto Project host packages:-
	  $sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential \ 
	   chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils \ 
	   iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev \ 	 
	   python3-subunit mesa-common-dev zstd liblz4-tool file locales -y 

Step 2:- Download Yocto Kirkstone based on Freescale Community BSP 4.0

	$ mkdir -p ~/bin
	$ curl https://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
	$ chmod a+x ~/bin/repo
	$ export PATH=~/bin:$PATH
	Make a new folder/Directory under /home/fabrizio
	$ mkdir ~/yocto
	$ cd ~/yocto

Step 3:- Download Manifest with a release tag

  $ repo init -u git@ec2-63-35-80-216.eu-west-1.compute.amazonaws.com:krib/imx8qm-os-manifest.git -m krib-5.15.32-2.0.0-qt5.xml -b feature/meta-qt5-support
  $ repo sync -j$(nproc)

Step 4:- Setup yocto build image

	MACHINE=imx8qmmek :- supported machine for our yocto build
	DISTRO=fsl-imx-wayland :- Here we are selecting the DISTRO wayland
	build :- build directory
	
	The command is only mandatory for the very first build setup
	
	$ cd ~/yocto
	$ MACHINE=imx8qm DISTRO=fsl-imx-wayland source init-krib-environment.sh -b build
	
	we can skip the full setup if we already have the build setup and just run
	
	$ cd ~/yocto
	$ source setup-environment build

Step 5:- local.conf customization
	we have to customize our local.conf under the following directory  
	/home/fabrizio/yocto/build/conf
	
	mainly, we have to add the following instructions:
	
	# Switch to Debian packaging and include package-management in the image
	PACKAGE_CLASSES = "package_deb"

	INHERIT += "rm_work" (to save some space on my hard disk it deletes the packages after installing that packages properly)

	# Needed for ffmpeg ( we indicate that we are willing to accept packages with proprietary, commercial licenses that may have different terms than open-source licenses.)
	LICENSE_FLAGS_ACCEPTED="commercial"     (for example in our case faad2 needed this flag)
Step 6:- Added QtWebengine
  /home/fabrizio/yocto/sources/meta-krib/recipes-external/packagegroup/
    QtWebengine recipe is add to the build path under the above directory 
    filename- packagegroup-qt6-krib.bb

    the properties for qtwebengine are enabled by creating a qtwebengine_git.bbappend and add the packages in configuartion path
    /home/fabrizio/yocto/sources/meta-krib/recipes-external/qt5/
  Step 7:- Change the Qt Version by modifying the repice qt5-git.bb adding new version PV and SRCREV
    /home/fabrizio/yocto/sources/meta-qt5/recipes-qt/qt5/
    
