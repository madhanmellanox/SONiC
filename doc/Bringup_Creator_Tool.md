Bringup Creator Tool
====================
Bringup  Creator is a python based tool that is used to generate the files required for bringing up a Mellanox Spectrum based platform (switch). There are certain files created as part of default SKU folder and there are few files that are created for the entire platform irrespective of the SKU and they are placed outside the SKU folders. 

The common files that are placed outside the SKU folders are:
* default_sku
* pcie.yaml
* platform.json (generated using Vadym’s script)
* platform_components.json
* platform_reboot
* platform_wait
* pmon_daemon_control.json
* port_peripher_config.j2
* sensors.conf
* system_health_monitoring_config.json
* thermal_policy.json

The files that are part of plugins folder are:
* eeprom.py
* fanutil.py
* psuutil.py
* sfplpmget.py
* sfplpmset.py
* sfpreset.py
* sfputil.py

The files that are part of (default) sku folder are:
* buffers.json.j2
* buffers_defaults_t0.j2
* buffers_defaults_t1.j2
* buffers_dynamic.json.j2
* hwsku.json
* pg_profile_lookup.ini
* port_config.ini
* qos.json.j2
* sai.profile
* sai_xxx.xml

# bringup_gen CLI

bringup_gen CLI is a simple CLI which takes an input folder where the input files that are required to generate the bringup related files for that platform. The usage of bringup_gen CLI is shown below:

madhan@arc-build-server:/builds2/madhan/bringup_tool$ sudo ./bringup_gen.py -h
usage: bringup_gen.py [-h] [-v] [-i INPUT]

Generate files for Bringing up a platform

optional arguments:
  -h, --help            show this help message and exit
  -v, --version         show program's version number and exit
  -i INPUT, --input INPUT
                        Input folder containing files that are input for Bringup tool

The tool generates all the files required for bring up and saves them in an output folder similar to that folder for the platform on the switch say, x86_64-mlnx_msn3700-r0 and on the switch it will be placed in /usr/share/device/mellanox/x86_64-mlnx_msn3700-r0/

If there are any files missing in the input folder, we will skip the generation of files that require the particular input file, but will proceed to generate other files whose input files exist in the input folder.

# Input files

The following are the input files that are required to generate files for bring up of a platform.
* sai_platform_info.xls
* buffer_spectrum.xls
* platfom.json
* platform_info.xml

sai_platform_info.xls file is the file that contains SAI port related information like local port, SDK/SAI port, module, lanes etc. This is a file which is obtained from the FW team.

buffer_spectrum.xls file is the file that contains QOS related values for all kinds of Spectrum ASICs. This file is input for all QOS related files that are required for bringup. This file is obtained from Architecture team.

platform.json file is one of the required files for Bringup and it serves as an input file also here. There is already a script file to generate platform.json per platform

platform_info.xml is the input files which contains required information for generating several output files required for bringup. An example of fields is breakout mode and port speed for generating SAI_xxx.xml file.
Since platform_info.xml file contains required fields for multiple files, the content of the platform_info.xml file is shown below.

\<platform_info><br>
\<default_sku> ACS-MSN4600C \</default_sku><br>
\<platform> 4600C \</platform><br>
\<org> Microsoft \</org><br>
\</platform_info><br>
\<sai_xml><br>
    \<device-mac-address>00:02:03:04:05:00\</device-mac-address><br>
    \<issu-enabled>1\</issu-enabled><br>
    \<number-of-physical-ports>64\</number-of-physical-ports><br>
    \<port-info><br>
        \<port><br>
            \<local-port>3\</local-port><br>
            \<port-speed>50\</port-speed><br> 
        \</port><br>
        \<port><br>
            \<local-port>5\</local-port><br>
            \<port-speed>100\</port-speed><br>
        \</port> <br>
    \</port-info><br>
    \<breakout-defns><br>
        \<breakout-defn><br>
            \<port-speed> 100 \</port-speed><br>
            \<breakout-modes>3</breakout-modes><br>
        \</breakout-defns><br>
    \</breakout-defns><br>
    \<port-speed-defns><br>
        \<port-speed-defn><br>
            \<port-speed>1\</port-speed><br>
            \<bitmask>2\</bitmask><br>
        \</port-speed-defn><br>
        \<port-speed-defn><br>
            \<port-speed>10\</port-speed><br>
            \<bitmask>16\</bitmask><br>
        \</port-speed-defn><br>
        \<port-speed-defn><br>
            \<port-speed>40\</port-speed><br>
            \<bitmask>32\</bitmask><br>
        \</port-speed-defn><br>
        \<port-speed-defn><br>
            \<port-speed>50\</port-speed><br>
            \<bitmask>384\</bitmask><br>
        \</port-speed-defn><br>
        \<port-speed-defn><br>
            \<port-speed>100\</port-speed><br>
            \<bitmask>1536\</bitmask><br>
        \</port-speed-defn><br>
        \<port-speed-defn><br>
            \<port-speed>200\</port-speed><br>
            \<bitmask>4096\</bitmask><br>
        \</port-speed-defn><br>
    \</port-speed-defns><br>
\</sai_xml><br>
\<port_peripher_config><br>
\<gearbox_present>yes\</gearbox_present><br>
\<gearbox_model>MELLANOX-GEARBOX-4600C</gerabox_model><br>
\</port_peripher_config><br>
\<platform_components><br>
\<number_of_cplds>3\</number_of_cplds><br>
\</platform_components><br>


# Generating default_sku file

This file contains the name of the default sku, this input is taken from platform_info.xml file and the file is generated.

# Generating pcie.yaml file

This file contains information about PCIE configurations on the switch. This file can be generated by running "sudo pcieutil generate" on the switch. A file will be created with name pcie.yaml file and the information about the command will be provided there. Once, the bringup tool output is placed on the switch, this file will be replaced by executing the command and generating the output file.

# Generating platform.json file

platform.json file is one of the input files, so it is generated at first. We already have per platform scripts to generate platform.json file.

# Generating platform_components.json file

This file contains information about the components of the platform like ONIE, SSD, BIOS and CPLD. The number of CPLDs on a platform may vary. So the information about number of CPLDs is extracted from platform_info.xml file and this file is generated.

# Generating platform_reboot file

This file in the switch for most of the platforms point to a symbolic link of 2700 platform_reboot file. So, in our output folder we will create a platform_reboot file and the information to create a symbolic link to 2700 platform_reboot file will be provided there.

# Generating platform_wait file

This file in the switch for most of the platforms point to a symbolic link of 2700 platform_wait file. So, in our output folder we will create a platform_wait file and the information to create a symbolic link to 2700 platform_wait file will be provided there.

# Generating pmon_daemon_control.json file

This file in the switch for most of the platforms point to a symbolic link of 2700 pmon_daemon_control.json file. So, in our output folder we will create a pmon_daemon_control.json file and the information to create a symbolic link to 2700 pmon_wait_control.json file will be provided there.

# Generating port_peripher_config.j2 file

This file may be present in the platform bringup files or not depending on whether the platform contains gearbox or not. The information about the gearbox is obtained from port_info.xml file and this file is generated. 

# Generating sensors.conf file

This file contains information about sensors. The input for this file comes from hardware documents and has to be read carefully and derive at human readable names. This file can not be automated.

# Generating system_health_monitoring_config.json file

This file in the switch for most of the platforms point to a symbolic link of 2700 system_health_monitoring_config.json file. So, in our output folder we will create a system_health_monitoring_config.json file and the information to create a symbolic link to 2700 system_health_monitoring_config.json file will be provided there.

# Generating thermal_policy.json file

This file in the switch for most of the platforms point to a symbolic link of 2700 thermal_policy.json file. So, in our output folder we will create a thermal_policy.json file and the information to create a symbolic link to 2700 thermal_policy.json file will be provided there.

# Generating eeprom.py file 

This file in the switch for most of the platforms point to a symbolic link of 2700 eeprom.py file. So, in our output folder and under plugins folder, we will create a eeprom.py file and the information to create a symbolic link to 2700 eeprom.py file will be provided there.

# Generating fanutil.py file

This file in the switch for most of the platforms point to a symbolic link of 2700 fanutil.py file. So, in our output folder and under plugins folder, we will create a fanutil.py file and the information to create a symbolic link to 2700 fanutil.py file will be provided there.

# Generating psuutil.py file

This file in the switch for most of the platforms point to a symbolic link of 2700 psuutil.py file. So, in our output folder and under plugins folder, we will create a psuutil.py file and the information to create a symbolic link to 2700 psuutil.py file will be provided there.

# Generating sfplpmget.py file

This file in the switch for most of the platforms point to a symbolic link of 2700 sfplpmget.py file. So, in our output folder and under plugins folder, we will create a sfplpmget.py file and the information to create a symbolic link to 2700 sfplpmget.py file will be provided there.

# Generating sfplpmset.py file

This file in the switch for most of the platforms point to a symbolic link of 2700 sfplpmset.py file. So, in our output folder and under plugins folder, we will create a sfplpmset.py file and the information to create a symbolic link to 2700 sfplpmset.py file will be provided there.

# Generating sfpreset.py file

This file in the switch for most of the platforms point to a symbolic link of 2700 sfpreset.py file. So, in our output folder and under plugins folder, we will create a sfpreset.py file and the information to create a symbolic link to 2700 sfpreset.py file will be provided there.

# Generating sfputil.py file

This file in the switch for most of the platforms point to a symbolic link of 2700 sfputil.py file. So, in our output folder and under plugins folder, we will create a sfputil.py file and the information to create a symbolic link to 2700 sfputil.py file will be provided there.

# Generating buffers.json.j2 file

This file in the switch for most of the platforms point to a symbolic link of 2700 buffers.json.j2 file. So, in our output folder, we will create a buffers.json.j2 file and the information to create a symbolic link to 2700 buffers.json.j2 file will be provided there.


# Generating buffers_defaults_t0.j2 file

This file contains QoS fields like Ingress Lossless pool size, Ingress Lossy pool size, egress Lossless pool size, Egress Lossy pool size etc for t0 topology. This fields are computed and stored in buffer_spectrum.xls file. All the settings in the file like ASIC type, gearbox, buffer-defaults-t0 / buffer-defaults-t1 etc are done before extracting the QOS values from the xls file.

# Generating buffers_defaults_t1.j2 file

This file contains QoS fields like Ingress Lossless pool size, Ingress Lossy pool size, egress Lossless pool size, Egress Lossy pool size etc for t1 topology. This fields are computed and stored in buffer_spectrum.xls file. All the settings in the file like ASIC type, gearbox, buffer-defaults-t0 / buffer-defaults-t1 etc are done before extracting the QOS values from the xls file.

# Generating buffers_dynamic.json.j2 file

This file in the switch for most of the platforms point to a symbolic link of 2700 buffers_dynamic.json.j2 file. So, in our output folder, we will create a buffers_dynamic.json.j2 file and the information to create a symbolic link to 2700 buffers_dynamic.json.j2 file will be provided there.


# Generating hwsku.json file

We already have per platform scripts to generate hwsku.json file.

# Generating pg_profile_lookup.ini file

pg_profile_lookup.ini is a standard file. They are 2 types one is for MSFT and other one is for all other type of SKU. The values in the file never change. With the assumption that default sku is for Microsoft, the file can be generated. 

# Generating port_config.ini file

This file contains fields like port name, port speed, lanes, alias name etc that are related to port information. This file for the default sku can be generated from platform.json file and the default sku name can be obtained from platform_info.xml file. So, with the help of these 2 input files, this file can be generated.

# Generating qos.json.j2 file

This file in the switch for most of the platforms point to a symbolic link of 2700 qos.json.j2 file. So, in our output folder, we will create a qos.json.j2 file and the information to create a symbolic link to 2700 qos.json.j2 file will be provided there.

# Generating sai.profile file

This file contains information about the SAI_xxx.xml file for the default SKU. Since, it is default sku, SAI_xxx.xml is going to be like SAI_4600C.xml. So, this file can be generated by extracting platform information from platform_info.xml file.

# Generating sai_xxx.xml file

The name of the file sai_xxx.xml file for default SKU is going to be like sai_4600C.xml. The file contains information about SAI port related information like local port, SDK/SAI port, breakout mode, port speed etc. This file can be generated by extracting information from sai_platform_info.xls and platform_info.xls file.