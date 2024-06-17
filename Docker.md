# Creating a Docker Image #

Start by installing the Docker desktop tools found here: https://docs.docker.com/install/. Download the correct installer for your operating system and run the installation.

Now, once Docker is running on your local machine, enter the following text:

```unix assembly
docker login images.sbgenomics.com
  ``` 

You'll then be prompted to enter your username. You can enter it either in the same format as when logging in to the Platform (including uppercase, periods etc.) **OR** as the email address you used to register for the account on the Platform.

Then you'll be asked to enter your password. **Note: This is NOT your Seven Bridges password.**
This is an auto-generated token. To get your authentication token, click on the **developer dashboard** from the menu at the top of the page. Select "Authentication token" from the drop down list and generate a token. Highlight the token text, go back to your command line, and paste it. **Note: You won't be able to see the text once pasted in the terminal.**

Congratulations! You're now logged in! (You won't have to do this every time)


### Here's where the fun begins... ###

The text below runs a Docker container on your local machine. <image> can be any image in Docker Hub or the Seven Bridges image registry that you have uploaded or have permission to access. The -it flag makes the container run interactively so we can test it out. In my example below, I'm running ubuntu, version 20.04.

 ```unix assembly
 docker run -it ubuntu:20.04
  ``` 

  From here on out, we're basically testing what we want to happen on by using a docker container on our local machine. This allows us to troubleshoot in real-time, and work out any quirks.
  
Each line here is a command. Try running one at a time to make sure everything works properly.  
  
  ```unix assembly
apt-get update   #  This is to make sure we have the latest versions of what we need

apt-get install wget    # This is a tool we can install and use to download docker images off the web
  
apt-get install build-essential     # This is a useful group of tools that is good to have available
  
cd opt    # Changing directories to /opt/ is best practice
  
wget https://github.com/samtools/samtools/releases/download/1.15.1/samtools-1.15.1.tar.bz2    # Using the wget tool we installed to download Samtools
  
ls    # Checking to see what format Samtools is in. Looks like it's compressed, so it needs to be unzipped.
  
tar -xf samtools-1.15.1.tar.bz2     # This command unzips Samtools. 

ls    # Checking to make sure Samtools was actually unzipped :P
  
cd samtools-1.15.1    # Moving into that directory
  
./configure 
  
make            # build executable program or library from source code
  
make install     # the make program takes the binaries from the previous step and copies them into some appropriate locations so that they can be accessed
  
ls    # You should see "Samtools" as an installed tool
  
samtools    # This is to test that Samtools actually got installed and works
  
history   # This is so you can see all of the commands you ran. We can use these to construct the Dockerfile

exit  # To exit the docker container
  
  ```
  
### Now to create the actual Dockerfile ###

Open your computer's text editor and create a new file.  Save it to your desktop as "Dockerfile" with no .txt or extention.
In this new text file, we're going to scroll back up in our terminal window to where we typed "history" and copy and paste our commands. 
  
The text file should look something like this:

``` Unix Assembly
  
FROM ubuntu:20.04

RUN apt-get update

RUN apt-get install -y wget \
  build-essential \
  libncurses-dev \
  zlib1g-dev \
  libbz2-dev \
  liblzma-dev

### Download and uncompress samtools library ###

WORKDIR /opt/
RUN wget https://github.com/samtools/samtools/releases/download/1.15.1/samtools-1.15.1.tar.bz2

RUN tar -xf samtools-1.15.1.tar.bz2

### Compiling samtools ###

WORKDIR samtools-1.15.1
RUN ./configure
RUN make
RUN make install

COPY Dockerfile /opt/

  ```
### Hoorah! You've made it! The final steps are to build and push the Dockerfile to the appropriate location on the Seven Bridges Platform ###
  
Go back to your terminal and type the following:
```Unix Assembly
  
cd Desktop/     # Or wherever you saved the Dockerfile
ls              # You should see the file here
docker build -t cgc.images.sbgenomics.com/USER_NAME/samtools-0-15-1:0 .   #Change USER_NAME to your own username. This builds the docker image from the file we created.
docker push cgc.images.sbgenomics.com/USER_NAME/samtools-0-15-1:0        #Again, change USER_NAME to your own. This command puts the docker image onto our platform.

  
