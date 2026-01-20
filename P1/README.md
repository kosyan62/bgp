First, we need to build two images for the project.

`docker build -t router -f ./_mgena_router .`  
`docker build -t host -f ./_mgena_host .`

After this we import the images 'router' and 'host' into GNS3, change some settings and run the project.

After import machines into project, we're able to connect to them using the auxiliary console.
