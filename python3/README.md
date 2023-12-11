# Documentation for `gcr.io/distroless/python3`

## Image Contents

This image contains a minimal Linux, Python-based runtime.

Specifically, the image contains everything in the [base image](../../base/README.md), plus:

* Python 3 and its dependencies.
* No shell and no support for ctypes

## Build your own image  

I've placed two two options to build with the newer version of [zlib1g](https://packages.debian.org/sid/zlib1g), which should resolve the following [vulnerability issue](https://security-tracker.debian.org/tracker/CVE-2023-45853):  
 - non-root deb11 with python3.9.2 (sits under the `/experimental` folder)  
 - non-root dev12 with python3.11  

To build an image you need to run build from the root dir. (look at the python [BUILD file](./BUILD)):  
```
bazel build //:python_deb12_local_build
```  

In order to add more options - you need to define new targets in the main `BUILD`, like that:  
```
oci_tarball(
    name = "python_root_deb12_arm64_local_build",
    image = "//python3:python3_root_arm64_debian12",
    repotags = [],
)
```

After that you can load the image to docker:  
```
docker load --input $(bazel cquery --output=files //:python_deb12_local_build)
```  
Add a tag:  
```
docker tag XXX distroless/python3-debian12-zlib1g1-3:1.0.0
```  
And push to the registry:  
```
docker push distroless/python3-debian12-zlib1g1-3:1.0.0
```  

## Usage

The entrypoint of this image is set to "python", so this image expects users to supply a path to a .py file in the CMD.

See the Python [Hello World](../../examples/python3/) directory for an example.
