# ImageMagick for AWS Lambda

Scripts to compile ImageMagick utilities for AWS Lambda instances powered by Amazon Linux 2023, such as the `nodejs22.x` runtime.

## Prerequisites

* Docker desktop
* Unix Make environment
* AWS command line utilities (just for deployment)

## Compiling the code

* start Docker services
* `make all`

There are two `make` scripts in this project.

* [`Makefile`](Makefile) is intended to run on the build system, and just starts a Docker container matching the AWS Linux 2 environment for Lambda runtimes to compile ImageMagick using the second script.
* [`Makefile_ImageMagick`](Makefile_ImageMagick) is the script that will run inside the container, and actually compile binaries. 

The output will be in the `result` dir.

### Configuring the build

By default, this compiles a version expecting to run as a Lambda layer from `/opt`. You can change the expected location by providing a `TARGET` variable when invoking `make`.

The default Docker image used is `public.ecr.aws/lambda/nodejs:22`. To use a different base, provide a `DOCKER_IMAGE` variable when invoking `make`.

[See list of official AWS Lambda images here](https://gallery.ecr.aws/lambda).

Modify the versions of libraries or ImageMagick directly in [`Makefile_ImageMagick`](Makefile_ImageMagick).

### Experimenting

* `make bash` to open an interactive shell with all the build directories mounted
* `make libs` to make only the libraries, useful to test building additional libraries without building ImageMagick itself

### Bundled libraries

This is not a full-blown ImageMagick setup you can expect on a regular Linux box, it's a slimmed down version to save space that works with the most common formats. You can add more formats by including another library into the build process in [`Makefile_ImageMagick`](Makefile_ImageMagick).

These libraries are currently bundled:

* libpng
* libtiff
* libjpeg
* openjpeg2
* libwebp

## Deploying to AWS as a layer

Run the following command to deploy the compiled result as a layer in your AWS account.

```sh
make deploy DEPLOYMENT_BUCKET=<YOUR BUCKET NAME>
```

### configuring the deployment

By default, this uses imagemagick-layer as the stack name. Provide a `STACK_NAME` variable when
calling `make deploy` to use an alternative name.

### example usage

An example project is in the [example](example) directory. It sets up two buckets, and listens to file uploads on the first bucket to convert and generate thumbnails, saving to the second bucket. You can deploy it from the root Makefile using:

```sh
make deploy-example DEPLOYMENT_BUCKET=<YOUR BUCKET NAME>
```



## Info on scripts

For more information, check out:

* https://imagemagick.org/script/install-source.php
* http://www.linuxfromscratch.org/blfs/view/cvs/general/imagemagick.html

## Author

Gojko Adzic <https://gojko.net>

## License

* These scripts: [MIT](https://opensource.org/licenses/MIT)
* ImageMagick: https://imagemagick.org/script/license.php
* Contained libraries all have separate licenses, check the respective web sites for more information
