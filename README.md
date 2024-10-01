# MERN-WEB-STACK
To deploy a simple To-Do application that creates To-Do lists.
Try to connect to the instance by selecting "connect" and seeing the various ways to connect



![ssh connection procedure on aws](https://github.com/user-attachments/assets/d3dd9f11-0d39-4ce1-a8fe-1f14628d8cb2)


On windows powershell enter the command 
ssh -i "Allen-key.pem" ubuntu@ec2-3-84-234-240.compute-1.amazonaws.com


![after connecting to instance](https://github.com/user-attachments/assets/9cdca3f4-3549-4f7a-b0dc-5265802818af)


Step 1: Backend Configuration
update and upgrade ubuntu


![sudo apt update and upgrade ](https://github.com/user-attachments/assets/18fdad0e-16c3-4917-94de-a6e85f87f627)

Verify the location of the node.js software in Ubuntu: 
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -


![image](https://github.com/user-attachments/assets/08d819dc-3337-43f0-8c49-692ad66d7a60)

Install nodejs and npm on the ubuntu sserver using:
sudo apt-get install -y nodejs

Verify the node version installed:
node -v


![image](https://github.com/user-attachments/assets/4dc2d185-f857-495f-ba8c-bcde37f0ccae)


Verify the npm package manager installed:


![image](https://github.com/user-attachments/assets/891dd523-e519-4d2a-8b6c-f03086964dc4)

**Application Code Setup**
Create new directory for the Todo app
mkdir Todo

Enter the directory:
cd Todo
Enter the command to initialize the project and generate a package.json file which will contain information and dependencies about our Todo app.
npm init 

![npm init 1](https://github.com/user-attachments/assets/9975c3b1-d7df-40fe-9249-ec0293f85712)


![npm init 2](https://github.com/user-attachments/assets/c92eb929-7fb9-4ce3-92e2-ff13bb632c23)


Verify a package .json file is generated by typing ls

**Install ExpressJs and create the routes directory**
install expressJs using npm:
npm install express


![image](https://github.com/user-attachments/assets/0bfe1bfa-1725-4609-b00e-4fa20d5024ca)

Create index.js file:
touch index.js


Install the dotenv module:
npm install dotenv

![image](https://github.com/user-attachments/assets/db5f837c-92dc-4948-ba2d-3938adbfca15)


Use nano editor to open the index.js file

Copy the following code into the index.js file:

const express = require('express');

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Content-Type, Accept');
  next();
});

app.use((req, res, next) => {
  res.send('Welcome to Express');
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});



![image](https://github.com/user-attachments/assets/92f271d1-1cdb-43bd-a453-d0151cb857dc)


Next we start our server by running:
nano index.js

![image](https://github.com/user-attachments/assets/7ef12bc2-1baa-4ade-b2d1-383e3de7d2be)

We can see that the server is running on port 5000. We have to create an inbound rule for this port in the security group of our instance.


![image](https://github.com/user-attachments/assets/796a229b-4315-47f8-86c7-8ba143ddac48)


**Step 3: Creating the routes**
Our app is a ToDo app which will be required to create a task, display list of tasks and delete completed task.
Each task will be associated to an endpoint and will use one of the HTTP request methods: POST, GET and DELETE.
For each task, we will create a route.

Create a folder called routes:

mkdir routes

Enter the directory:

cd routes

Create a new file
touch api.js

Copy the following code into the file:

const express = require('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {
  // get placeholder
});

router.post('/todos', (req, res, next) => {
  // post placeholder
});

router.delete('/todos/:id', (req, res, next) => {
  // delete placeholder
});

module.exports = router;


![nano api js](https://github.com/user-attachments/assets/770ea55d-94d2-4ab1-8e98-dc0d8d68645c)



**Creation of Models**
We have to create models. Models make javascript applications interactive. We will use models to define the database schema so as to be able to define the fields in the document in the mongodb database which we would be using.
We will have to install mongoose, which is a node.js package which makes working with mongodb easier.
Go back to the Todo directory by using cd ..:

Install mongoose using the command: 
npm install mongoose

Create a folder named "models":
mkdir models

Enter the folder and create a new file "todo.js":
cd models&&touch todo.js


![install mongoose, creation of models and todo js in models](https://github.com/user-attachments/assets/b91f90d8-dc72-4107-9faf-f46b08f76b02)

Using a text editor like nano open and paste this code:

const mongoose = require('mongoose');
const Schema = mongoose.Schema;

// Create schema for todo
const TodoSchema = new Schema({
  action: {
    type: String,
    required: [true, 'The todo text field is required'],
  },
});

// Create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;

![todo js code in models](https://github.com/user-attachments/assets/cbc03202-42e5-4115-af15-87757419e847)

We have now created our model. What we would do next will be to update our route in api.js to make use of the new model we just created. 
Go back to our routes directory in the Todo directory and clear what we have in the api.js file and replace with the code:

const express = require('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {
  // This will return all the data, exposing only the id and action field to the client
  Todo.find({}, 'action')
    .then((data) => res.json(data))
    .catch(next);
});

router.post('/todos', (req, res, next) => {
  if (req.body.action) {
    Todo.create(req.body)
      .then((data) => res.json(data))
      .catch(next);
  } else {
    res.json({
      error: 'The input field is empty',
    });
  }
});

router.delete('/todos/:id', (req, res, next) => {
  Todo.findOneAndDelete({ _id: req.params.id })
    .then((data) => res.json(data))
    .catch(next);
});

module.exports = router;



![updating api js to to make use of the model created](https://github.com/user-attachments/assets/5c49fe1d-f39b-4664-afd7-84bcf5ee384b)

**MongoDB Database**
Next we need a database where we can store our applications data. 
MongoDB database is the required database and this can be gotten using mLab which offers MongoDB database as a service.
We would visit the mLab website where we would sign up and create a database cluster using this URL:
https://www.mongodb.com/products/try-free/platform/atlas-signup-from-mlab 

![mongodb webpage](https://github.com/user-attachments/assets/1a24e066-e8ba-4f51-b162-6750febc1e8f)

Create a free db cluster and launch it

Go to the network tab of the db cluster created.

Add IP access list entry. Add 0.0.0.0/0 to include all IP addresses.
Set the entry to be deleted in one week.
![add ip access list entry](https://github.com/user-attachments/assets/689e6a59-3ed6-4f9a-a5b3-841c29a574ba)

![network access tab of db cluster](https://github.com/user-attachments/assets/9ad91cf8-2f24-47a6-b645-d6252054eb13)

Go to cluster overview page
![overview of db cluster](https://github.com/user-attachments/assets/2d6d9d43-fe5e-4744-9ed5-7e91edae2c6e)


Choose the collections tab
![collections tab in db cluster overview](https://github.com/user-attachments/assets/12776ba9-828c-451e-91e8-daf30196577b)

in the index.js file in the Todo directory, we would create the process .env file which would allow us to access the environmental variables but first we will need to create the .env file itself first.
Go to the Todo dir and create .env file using the command:
    touch .env
Enter the file using a nano editor.
Now we will copy the connection string into the .env file, this ensures that the connection to the database is established.
Copy the connection string from the database cluster.
The connection string can be gotten from the tab you can navigate from the database cluster page 
Cluster--> Connect-->Create a db user-->Connect your application-->Select driver(node.js)

![cluster connect](https://github.com/user-attachments/assets/0568a429-1c56-48dc-8867-f773fe356985)

Create a db user and password
![cluster connect 2](https://github.com/user-attachments/assets/0f5eeef9-0a3c-4a5c-8270-05a097afde54)

Choose the connection method to be by driver as this contains the node.js method which we are using in the stack
![choose a connection method](https://github.com/user-attachments/assets/e146c444-e38a-44dc-8dcd-82cea0cc2de9)

Ensure to select the node.js driver
![connection string display page](https://github.com/user-attachments/assets/9abbdfef-5039-4ee3-8c50-688da9419262)


Copy the connection string displayed.
This is the format of the connection string: 

mongodb+srv://allen_uzoma:<db_password>@mern-db-cluster.bsio6.mongodb.net/?retryWrites=true&w=majority&appName=MERN-DB-CLUSTER


Replace the <db_password> with the one you chose while creating the db user steps earlier.

Next we would copy the db connection string into the .env file using the command:
DB = 'mongodb+srv://allen_uzoma:<db_password>@mern-db-cluster.bsio6.mongodb.net/?retryWrites=true&w=majority&appName=MERN-DB-CLUSTER'

![nano env with db connection string](https://github.com/user-attachments/assets/a4a7c961-a3ec-4590-ab03-23c8cb9a09e4)

Next we would update the index.js file in the Todo directory to reflect the use of the .env file which we just copied the database string into. Enter the following command into the index.js file using a nano editor:

const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

// Connect to the database
mongoose
  .connect(process.env.DB, { useNewUrlParser: true })
  .then(() => console.log(`Database connected successfully`))
  .catch((err) => console.log(err));

// Since mongoose's Promise is deprecated, we override it with Node's Promisemongoose.Promise = global.Promise;

app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Cont>  next();
});


![index js updated after creating the  env file](https://github.com/user-attachments/assets/69d5aeb0-4eb8-4a25-a7e1-2d93b4b35254)

We used environmental variables to store information in line with security best practice to separate configuration and secret data from application code, by ensuring we do not copy ourconnection strings inside the index.js application file.

Next, in the Todo directory, we would restart the server using the command:
node index.js

![node index js after connecting the db ](https://github.com/user-attachments/assets/f8a6c120-cdd2-44e5-9f95-46e74d7d2933)

We can see that our database is connected successfully, meaning our Backend is configured. We will test it now.
























