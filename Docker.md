# Creating a Docker Image #

Start by installing the Docker desktop tools found here: https://docs.docker.com/install/. Download the correct installer for your operating system and run the installation.

Now, once Docker is running on your local machine, enter the following text:

> docker login images.sbgenomics.com

You'll then be prompted to enter your username. You can enter it either in the same format as when logging in to the Platform (including uppercase, periods etc.) **OR** as the email address you used to register for the account on the Platform.

Then you'll be asked to enter your password. **Note: This is NOT your Seven Bridges password.**
This is an auto-generated token. To get your authentication token, click on the **developer dashboard** from the menu at the top of the page. Select "Authentication token" from the drop down list and generate a token. Highlight the token text, go back to your command line, and paste it. **Note: You won't be able to see the text once pasted in the terminal.**

Congratulations! You're now logged in! (You won't have to do this every time)


### Here's where the fun begins... ###

The text below runs a Docker container on your local machine. <image> can be any image in Docker Hub or the Seven Bridges image registry that you have uploaded or have permission to access. The -it flag makes the container run interactively so we can test it out. In my example below, I'm running ubuntu, version 20.04.

  > docker run -it ubuntue:20.04

  From here on out, we're basically testing what we want to happen on by using a docker container on our local machine. This allows us to troubleshoot in real-time, and work out any quirks.
  
> apt-get update       # This is to make sure we have the latest versions of what we need
  
> apt-get install wget    # This is a tool we can install and use to download docker images off the web
  
> apt-get install build-essential     # This is a useful group of tools that is good to have available
  
> cd opt    # Changing directories to /opt/ is best practice
  
> wget https://github.com/samtools/samtools/releases/download/1.15.1/samtools-1.15.1.tar.bz2    # Using the wget tool we installed to download Samtools
  
> ls    # Checking to see what format Samtools is in. Looks like it's compressed, so it needs to be unzipped.
  
> tar -xf samtools-1.15.1.tar.bz2     # This command unzips Samtools. 

> ls    # Checking to make sure Samtools was actually unzipped :P
  
> cd samtools-1.15.1    # Moving into that directory
  
> ./configure 
  
> make 
  
> make install
  
> ls    # You should see "Samtools" as an installed tool
  
> samtools    # This is to test that Samtools actually got installed and works
  
> history 
exit
