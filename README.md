# aws-eb-multicontainer
Amazon Web Services (AWS) Elasticbeanstalk  (EB) Multi-container 


To use:

1. Create an ElasticBeanstalk environment with Docker Multi Container option.
2. Upload `site-artifacts` to S3
3. Create CodeCommit repositories for your applications
4. Add the CodeCommit repositories as the git remote origin for each application and `git push origin main` to each. Be sure to include the `buildspec.yml` in the repo.
4. Create ECR repositories for your applications
5. Create CodePipeline for `example-com`
  - Ensure `CodePipelineServiceRole` has (requires going to IAM):
    - AmazonEC2ContainerRegistryPowerUser
    - AmazonS3FullAccess
  - Create CodeBuild Project (this will be used in other CodePipelines, so give it a universal name like `example-builder`)
    - Ensure checkbox for Docker privledges are checked
    - Be sure to add the needed environment variables to build project
    - Ensure `CodeBuildServiceRole` has (requires going to IAM):
      - AmazonEC2ContainerRegistryPowerUser
      - AmazonS3FullAccess 
  - Deploy the environment to the Elasticbeanstalk environment created in first step
  - After creating CodePipeline, it should work up till the deployment stage, which won't work because the other applications (app.example.com, backend.example.com) have to be built and deployed.
6. Repeat creating a CodePipeline for `app-example-com` and `backend-example-com`.
  - Reuse the the same `CodePipelineServiceRole`
  - Reuse the same CodeBuild project
  - After creating and initiating Pipelines for each, the last deploy should succeed.
7. Go to `Route 53` and create a hosted zone.
8. Update domain register Nameservers to point to the Route 53 hosted zone (propagation can take up to two days).
9. Go `Route 53` hosted zone and create an `A Record` with value `example.com` that points to an Alias of the Elasticbeanstalk environment you created in step 1.
10. Add `A Records` for `app.example.com` and `backend.example.com`, pointing each to the same Elasticbeanstalk Alias
11. Create ACM certificate
12. Go to EC2 > Load Balancers
  - Add Listener for `https` on port `443`
  - Associate the SSL created in prior step
  - Ensure security group for LB allows access on port 443
  - Redirect traffic on port 80 to port 443
13. Profit (or start coding your project and then profit)


