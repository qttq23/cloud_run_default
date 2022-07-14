
1. overview
cloud run:  
- is a docker container.
- you build an image and give it to cloud run.
- cloud run will run it when requests come up and stop it when no more requests.
- cloud run can automatically scale to 0 or up to 1000, depends on number of requests.

cloud build:  
- used to build your source code to an docker image.
- cloud build uses Cloud Storage to store your source code in tgz format.

cloud artifact registry:  
- used to store images built by cloud build.
- cloud run will get images from artifact registry to run them.

(
https://cloud.google.com/run/docs/quickstarts/build-and-deploy/deploy-nodejs-service
)

2. gcloud cli install
(google cloud build requires the google cloud cli)
https://cloud.google.com/run/docs/quickstarts/build-and-deploy/deploy-nodejs-service#before-you-begin
	
3. common flows
first, you need to create a `artifact repo` because `cloud build` will store built images in there.  
then you build your server app. `cloud build` will zip your source code (tgz format) and put it in `cloud storage`.  
`cloud build` then pulls that tgz file to build image.  
the built image will be put to `artifact repo`.  
`cloud run` will run images from `artfact repo`.  
  
## create artifact repo once:
create in GCconsole > Artifact Registry (recommended)

or from cli:  
gcloud artifacts repositories create server-nodejs --repository-format=docker --location=asia-southeast1 --description="Docker repository for server_nodejs"

view repo:  
view in google cloud console > artifact registry

or from cli:  
gcloud artifacts repositories list

(
https://cloud.google.com/build/docs/build-push-docker-image#create_a_docker_repository_in
)

## build:  
go to the folder of app to be build (where Dockerfile is located):  
gcloud builds submit --tag asia-southeast1-docker.pkg.dev/web-revise/server-nodejs/server-nodejs-api1:lastest

(format:  
gcloud builds submit --tag LOCATION-docker.pkg.dev/PROJECT_ID/REPOSITORY/IMAGE_NAME

reference:   
https://cloud.google.com/build/docs/building/build-containers#use-dockerfile
)

cloud build will upload source code to Google Cloud Storage in tgz format.  
then it use that tgz to build image. the output image will be placed in Google Artifact Registry.  

view build details: https://cloud.google.com/build/docs/build-push-docker-image#view_build_details


# deploy:

for the first time, you should use `GC console` to create and deploy `Service in GCRun` manually.  
because you will need to set up environment variables.  
later time, after you've set the proper env, you can either use the `GCconsole` or `CLI` to deploy service (more accurately, deploy a revision).  
if you run `CLI` for the first time and it fails, you may have to go to `GC console` and set up new Revision with env variables manually.  

deploy from console manually (recommended for first time deploy):  
https://cloud.google.com/run/docs/deploying#service  
remember to set environment vars in tab VARIAbLES&SECRET.  


deploy from CLI:  
gcloud run deploy SERVICE --image IMAGE_URL

(eg:   
gcloud run deploy server-nodejs-api1 --image asia-southeast1-docker.pkg.dev/web-revise/server-nodejs/server-nodejs-api1:lastest --region asia-southeast1
)

# set custom domain:
google cloud run provides free DV SSL cert for every services as well as its custom domains.  
https://cloud.google.com/run/docs/mapping-custom-domains#run


