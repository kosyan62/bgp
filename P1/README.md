First, we need to build two images for the project.

`docker build -t router -f ./_mgena_router .`  
`docker build -t host -f ./_mgena_host .`

After we import the images 'router' and 'host' into GNS3, configure them and run the project.

The host consists of a simple busybox image which reduces load of the vm.
The router consists of the configured frr.

Now we're able to connect to them using the auxiliary console.
