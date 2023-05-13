# Compiling and installing kernel from source on Pop!_OS
I used the <a href="https://www.youtube.com/watch?v=WiZ05pnHZqM&t=1809s
">this</a> tutorial as a starting point, though some adjustments were necessary since I am using
a different distribution of Linux than that used in the tutorial.

1. Download the release candidate kernel from kerenl.org as a tar and extract
	it to a working directory, or clone the git repository.

2. Copy the current kernel's config file from /boot/config-<kernel name>-generic 
	to .config to the source directory. The location of the config file will vary 
	between different distributions.

3. Run "make oldconfig" to adjust .config accordingly to the new kernel.

4. Install necessary dependencies listed
	<a href="https://www.kernel.org/doc/html/v4.12/process/changes.html">here</a>.
	Note the on Pop OS, and I assume other Debian like distributions, it is
	necessary to install libelf-dev and libssl-dev.

5. In the .config file, set both CONFIG_SYSTEM_TRUSTED_KEYS to "".

6. Run "sudo make INSTALL_MOD_STRIP=1 module_install" If you do not include
	the INSTALL_MOD_STRIP=1 option, the kernel will be huge, over 1 GB,
	and will fill up your EFI partiton (perhaps this is only a major problem 
	for systems using systemd-boot and/or systems with full disk encryption).

7. Run "sudo make install" in source directory to install the kernel.

