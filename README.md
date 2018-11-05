# CKAN for HazDat

CKAN for [ds.hazdat.io](ds.hazdat.io)

See the original/upstream [CKAN README.rst](README.rst).

## Running CKAN

### Get necessary software

First install the necessary software on your computer. Open a terminal and paste:
```{bash}
pip install awscli --upgrade --user
```

This will install `awscli` which is needed to download files from AWS. Try running it:
```{bash}
aws
```

If this doesn't work then you may need to edit first add `$HOME/.local/bin` to your path:
```{bash}
export PATH=$PATH:$HOME/.local/bin
aws
```

Next, install Docker. Here are instructions on how to [Install Docker for Mac](https://docs.docker.com/docker-for-mac/install/). Once Docker is installed run it and follow the prompts, however there is no need to log in or create a Docker Id. Afterwards, check that it works by running the following terminal command:
```{bash}
docker version
```

### Set-up AWS credentials

In order to download the HazDat CKAN container an AWS credentials are needed.

Now, in the terminal type:

```{bash}
aws configure
```

Contact me to get credentials. Set the default region to be `ap-southeast-1`.

### Download and run CKAN Docker containers

First log in to the AWS container service:
```{bash}
aws ecr get-login --no-include-email
```

This will print a lot of stuff, copy it and paste it back as a command, for example:
```{bash}
docker login -u AWS -p <stuff> https://994001419236.dkr.ecr.ap-southeast-1.amazonaws.com
```

Next get the the CKAN setup configuration:
```{bash}
git clone --recursive https://github.com/HazDat/ckan.git
cd ckan/contrib/docker
```

Set up the default configs:
```{bash}
ln -s env.template .env
```

Download and run the CKAN site with:
```{bash}
docker-compose up
```

This will take a while, once it's done (output has stopped), open a new window and check that all containers are running with:
```{bash}
docker ps
```

See whether the CKAN website is running by visiting: [http://localhost:5000](http://localhost:5000).

## Customising CKAN templates

There is documentation for creating CKAN themes [here](https://docs.ckan.org/en/2.8/theming/index.html).

All of the files needed to create the HazDat theme can be found in `extensions/ckanext-hazdat_theme/ckanext/hazdat_theme`.

Once a file has been modified you need to restart CKAN before the changes can be viewed:

```{bash}
docker-compose restart ckan
```

