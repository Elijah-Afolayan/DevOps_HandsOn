## PROJECT 3: MERN STACK IMPLEMENTATION  
- MERN Web stack consists of following components:
MongoDB: A document-based, No-SQL database used to store application data in a form of documents.

- ExpressJS: A server side Web Application framework for Node.js.

- ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.

- Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

### **sample Diagram of the setup**


STEP 0  Spin up and Ubuntu instance in AWS cloud and  SSH into the instance.
![](image/Project3_CreateEc2.png)

![](image/project3_EC2ready.png)

![](image/Project3_SSH_EC2.png)

***STEP 1 – BACKEND CONFIGURATION***  
- Update ubuntu run {`sudo apt update`} 
![](image/Project3_UpdateEC2.png)
- Upgrade ubuntu run {`sudo apt upgrade`}
![](image/project3_upgrade_EC2.png)  
- Location of Node.js software from Ubuntu repositories. run {`curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`}  
![](image/project3_nodej_repo_locate.png)

- Install Node.js with the command `sudo apt-get install -y nodej`
![](image/project3_install_nodejs.png)  

- Verify Nodejs installation run `node -v` 
- Verify the node installation `run npm -v` 
![](image/project3_verify_Nodejs_install.png)

### Application Code Setup
    - Create new directory for To-Do project called **Todo**
        - `mkdir Todo`
    - Verify Todo directory creation
        - `ls` or `ls -lih`
    - Change current directory to the created one:
        - `cd Todo`
![](image/project3_mkdir_todo.png)



- Initialise project directory(**Todo**) by running `npm init`. A new file named **package.json** will be created which contain information about  application and the dependencies that it needs to run.
![](image/Project3_init_Todo.png)  
![](image/project3_verify_jsoncreated.png)

STEP 2 INSTALLING EXPRESSJS
- From Todo directory use **npm** to install expressjs `npm install express`  
- Now create a file  called **index.js** with command `touch index.js` 

![](image/project3_write_Indexjs.png)  

- Run `ls` command to confirm that index.js file is successfully created
![](image/project_express_install.png)

- Install the dotenv module with the below command
    - `npm install dotenv`
- Edit the index.js file with the command and past the belwo application code 
    - `vim index.js`  

`const express = require('express');
 require('dotenv').config();

 const app = express();

 const port = process.env.PORT || 5000;

 app.use((req, res, next) => {
 res.header("Access-Control-Allow-Origin", "\*");
 res.header("Access-Control-Allow-Headers", "Origin,  X-Requested-With, Content-Type, Accept");
 next();
 });

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});`

![](image/project3_verify_runningServer.png)

- Created an inbound rule to open TCP port 5000 from AWS console.
![](image/project3_openport_5000.png)

Routes
- Actions requirement for To-Do application
    - Create a new task
    - Display list of all tasks
    - Delete a completed task
- Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET and  DELETE.

- *For each task, we need to create routes that will define various endpoints that the To-do app will depend on. So let us create a folder routes* 
    - `mkdir routes`  
    - Tip: You can open multiple shells in Putty or Linux/Mac to connect to the same EC2

- Change directory to routes folder.
    - cd routes
- Create a file api.js with the command below
    - touch api.js

![](image/project3_mkdir_routes.png)  

- Open the file with the command below copy the
Copy below code in the file. (Do not be overwhelmed with the code)

`const express = require ('express');
 const router = express.Router();

 router.get('/todos', (req, res, next) => {

 });

 router.post('/todos', (req, res, next) => {

 });

 router.delete('/todos/:id', (req, res, next) => {

 })`

 module.exports = router;
   - vim api.js
![](image/projwct3_write_api.png)

## STEP 3 Create Models
- Change directory back to Todo folder with cd .. and install Mongoose
    - npm install mongoose  

![](image/project3_install_mongoose.png)  
- Create a new folder models :
    - mkdir models
- Change directory into the newly created ‘models’ folder with
    - cd models
- Inside the models folder, create a file and name it todo.js
    - touch todo.js

All 3 command can be combined in one line as bellow 
- mkdir models && cd models && touch todo.js

![](image/project3_mkdir_models.png)

update api.js file in routes directory with new code 

![](image/project3Update_api.png)

STEP 4 MONGODB DATABASE installation
 - Sign up for a free account on Mlab 

 - Created a cluster and a database and set up the required parameteres and extract your connection string. sample *DB = mongodb+srv://username:password@network-address/dbname?retryWrites=true&w=majority*
![](image/project3_connectn_String.png)
 - Update .env file with the connection string. 
 - Update index.js file and then run comand node index.js to  connect to MongoDb.
 ![](image/project3_connectMongoDb.png)

- Download and install postman on your machine
- Open Postman and Create a POST request to the API
    - Set header key *Content-Type*  then Value as *application/json*
    - Create your data input '{"action": "enter data to store in here or send here"}'  

![](image/project3_Postman_post.png) 
- Open Postman and Create a GET request to the API
    - Set header key *Content-Type*  then Value as *application/json*  

![](image/project3_postmanGet.png)  

- Open Postman and Create a DELETE request to the API
    - Set header key *Content-Type*  then Value as *application/json*  
    
![](image/project3_postmanDelete.png)




