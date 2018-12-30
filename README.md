# developer.zetkin.org
Portal for developers with guides and documentation. Built using Jekyll.

## Building and running
You can run this with Docker:

```
$ docker run --rm -ti -v $PWD:/srv/jekyll -p 4000:4000 jekyll/jekyll jekyll serve -i
```

If you prefer not to use Docker, you'll need to go to https://jekyllrb.com/ and follow the installation guidlines and step-by-step tutorial.

## Site structure
In _data/resources.yml the data for the API is found. This file is generated with a script that is run in the zetkin/core project. 
