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

In order to download the HazDat CKAN container AWS credentials are needed.

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
cd $HOME
git clone --recursive https://github.com/HazDat/ckan.git
cd ckan/contrib/docker
```

Set up the default configs:
```{bash}
ln -s env.template .env
```

Download and run the CKAN site with:
```{bash}
docker-compose up -d
```

This will take a while, once it's done check that all containers are running with:
```{bash}
docker ps
```

and see whether the CKAN site is running by visiting: [http://localhost:5000](http://localhost:5000).

## Customising CKAN templates

First it is worth looking at the [documentation for creating CKAN themes](https://docs.ckan.org/en/2.8/theming/index.html). In particular [this page](https://docs.ckan.org/en/2.8/theming/css.html) on customising the CSS.

Next we need to get access to the HazDat theme files. Set up an alias to the directory where the templates files are kept:

```{bash}
export VOL_CKAN_EXT=$(docker volume inspect docker_ckan_extensions | jq -r -c '.[] | .Mountpoint')
echo $VOL_CKAN_EXT
```

Fix up permissions so that we can easily read and write the files in `$VOL_CKAN_EXT`. This can get a little tricky!

For Mac we first need to install bindfs:

```{bash}
brew cask install osxfuse
brew install bindfs
```

Now use `bindfs` to make the templates directory accessible:

```{bash}
cd $HOME/ckan/contrib
mkdir ckan_extensions
sudo chown -R $(whoami):docker $VOL_CKAN_EXT
sudo bindfs --map=900/$(whoami) $VOL_CKAN_EXT ckan_extensions
```

Now all of the files needed to create/modify the HazDat theme can be found in `ckan_extensions/ckanext-hazdat_theme/ckanext/hazdat_theme`. Any changes to CSS or HTML files in here should be immediately visible upon reloading development URL [http://localhost:5000](http://localhost:5000).

For example edit `$HOME/ckan/contrib/ckan_extensions/ckanext-hazdat_theme/ckanext/hazdat_theme/public/hazdat_theme.css` and change the masthead colour and reload the site to see the effect.

