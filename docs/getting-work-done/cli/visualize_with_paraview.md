# Using Paraview



## Install Paraview 5.13.0 on your local workstation

```
wget --output-document /tmp/paraview.tar.gz "https://www.paraview.org/paraview-downloads/download.php?submit=Download&version=v5.13&type=binary&os=Linux&downloadFile=ParaView-5.13.0-MPI-Linux-Python3.10-x86_64.tar.gz"
mkdir ${HOME}/paraview
tar -xvzf /tmp/paraview.tar.gz --directory ${HOME}/paraview --strip-components 1

echo "export PATH=\$PATH:${HOME}/paraview/bin" >> ${HOME}/.bashrc
echo "export LD_LIBRARY_PATH=\$LD_LIBRARY_PATH:${HOME}/paraview/lib" >> ${HOME}/.bashrc

source ${HOME}/.bashrc
```

Verify that paraview starts by running `paraview`

## Set up the Armory PVSC file

Paraview can use an XML file called a "ParaView Server Connection" (PVSC) file to help automate remote paraview server connections. Fluid Numerics has created a PVSC file for you to make it easy to connect to Paraview server running on our cluster. You can use the following command to create this file on your system (this file will be saved at `${HOME}/paraview/armory.pvsc`).

```
cat > ${HOME}/paraview/armory.pvsc <<EOL
<Servers> 
  <Server name="Fluid Numerics Armory" resource="csrc://localhost:port">
   <CommandStartup>
     <Options>
       <Option name="TERM" label="Terminal Client" save="true">
        <Enumeration default="xterm">
          <Entry value="/opt/X11/bin/xterm" label="/opt/X11/bin/xterm (Linux/OSX)" />
          <Entry value="/usr/bin/xterm" label="/usr/bin/xterm (Linux/OSX)" />
        </Enumeration>
       </Option>
       <Option name="SSH_EXE" label="SSH Executable" save="true">
         <File default="/usr/bin/ssh"/>
       </Option>
       <Option name="SSH_USER" label="SSH Username" save="true">
         <String default=""/>
       </Option>
       <Option name="LOGIN_IP" label="Login Node IP Address" save="true">
         <String default="port.armory.fluidnumerics.com"/>
       </Option>
       <Option name="REMOTESCRIPT" label="The remote script that generates the job submission">
         <String default="/opt/paraview/armory/submit-paraview.sh"/>
       </Option>
       <Option name="PV_SERVER_PORT" label="Server Port: ">
         <Range type="int" min="11000" max="11050" step="1" default="11000"/>
       </Option>
       <Option name="SLURM_PARTITION" label="Slurm Partition" save="true">
         <String default="gpu"/>
       </Option>
       <Option name="NUMPROC" label="Number Of Processes" save="true">
         <Range type="int" min="1" max="48" step="4" default="12"/>
       </Option>
       <Option name="MEMORY" label="Memory (GB) per mpi-task" save="true">
         <Range type="int" min="1" max="8" step="2" default="2"/>
       </Option>
       <Option name="NUMHOURS" label="Number Of Hours to reserve" save="true">
         <Range type="int" min="1" max="8" step="1" default="4"/>
       </Option>
     </Options>
     <Command exec="$TERM$" delay="5">
      <Arguments>
         <Argument value="-T"/>
         <Argument value="Paraview"/>
         <Argument value="-e"/>
         <Argument value="$SSH_EXE$"/>
         <Argument value="-t"/>
         <Argument value="-R"/>
         <Argument value="\$PV_SERVER_PORT$:localhost:\$PV_SERVER_PORT$"/>
         <Argument value="\$SSH_USER$@$LOGIN_IP$"/>
         <Argument value="env -i"/>
         <Argument value="\$REMOTESCRIPT$"/>
         <Argument value="\$PV_SERVER_PORT$"/>
         <Argument value="\$SLURM_PARTITION$"/>
         <Argument value="\$NUMPROC$"/>
         <Argument value="\$MEMORY$"/>
         <Argument value="\$NUMHOURS$"/>
      </Arguments>
     </Command>
   </CommandStartup>
 </Server>
</Servers> 
EOL
```

Save the file and close your text editor.