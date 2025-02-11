aws cognito-idp initiate-auth --auth-flow USER_PASSWORD_AUTH --client-id xyz --auth-parameters USERNAME=zyx,PASSWORD=xyz,SECRET_HASH=xyz

CRUD With Lambda & DyanmoDB with Buildcode

Make sure all the service you’ll create in same region
Create DynamoDB Table:
	GO to dynamodb console
	Click create table
	Add table name & Partition key rest left as it is.
	Click on create table
Setup AWS Secret Manager
	Go to Secret Manager Console
	Click on create new secret
	From Secret type select other type of secret.
	Add Key Value: tableName : yourDbName
	Encryption type aws/secretmanager
	Click on next
	Set Secret name
	Click on next
	Left as it is and click on store
Create Role from IAM;
	Go to iam console
	Click on roles from left navigation
	Click on create role
	Choose AWS service for trust entity type
	From use case select Lambda
	Click next
	Attach Necessary policy
		SecretsManagerReadWrite
		AmazonDynamoDBFullAccess
		AmazonS3ReadOnlyAccess
	Click on next
	Set Role Name
	Click on create role
	
Create Lambda functions for each route (you can create only as per application req.)	GO to lambda console
	Click on create function
	Select author from scratch
	Set function name
	Change execution role to existing role and select above created role.
	Click on create function
Create S3 Bucket for code
	Go to s3 console
	Create general purpose bucket
	Set name bucket name
	Click on create
Create API Gatewayy
	Go to API Gateway console
	Click create API
	Choose Http API from Type and click on build
	Set API Name.
	Click next next and create
	Go to your API
	Create your routes & methods
	Click on route 
	Click on attache integration
	Create or attach integration
	Select lambda function from integration type
	Select your lambda function from integration target
	Click on create
Create Cognito if authenticate your API
	GOTO cognito console
	Click on Create user pool
	Select traditional web applications from application type
	Set name of application
	In configuration option select email & username
	select email from required attributes
	click on create
	from user pool click created user pool
	from left navigation click on users
	click on create user
	set required detail name email password
	click on create user.
NOTE: command to get token:	
Run into AWS CLI:
	aws cognito-idp initiate-auth --auth-flow USER_PASSWORD_AUTH --client-id <YOUR CLIENT ID FROM USER POOL>
--auth-parameters USERNAME=<username>,PASSWORD=<password>,SECRET_HASH=<SECRET_HASH>

ALLOW_USER_PASSWORD_AUTH in authentication flow by editing you user pool
FOR Secret hash: SECRET_HASH = HMAC-SHA256(Base64Encode(ClientId + username), ClientSecret);

Now GOTO API Gateway and click API
Select route which you want to authenticate
	Click on Create and attach authorizer
	Set name
	For issuer URL and Audience, you will get it from Cognito


Create Code Build for Code deployment
	GO to Codebuild Console
	Click on create project
	Set project name
	Select source (GitHub)
	Select Default source credentials
	Click on manage default credentials
		Select personal access token (get it from GITHUB)
			Create PAT in GITHUB
				Go to setting 
				Click on developer setting
				Click on Personal access token
				Generate personal access token
				Select Repo, workflow, admin:repo_hook
	Set github repository
	check rebuild every type
	build type – single build
	provisional modal: on demand
	env image: managed image
	compute : EC2
	OS: linux
	Runtime:standard
	Image: aws/codebuild/amazonlinux-x86_64-standard:5.0
	Image version: always use latest image
	Build specification: use a buildspec file
	In artifact select S3
	Click on create

	
		
		
