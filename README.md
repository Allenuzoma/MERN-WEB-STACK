# MERN-WEB-STACK


The MERN stack is a popular web development stack that consists of four key technologies:

MongoDB: A NoSQL database used to store application data in a flexible, JSON-like format.
Express.js: A web application framework for Node.js that simplifies building server-side applications and APIs.
React: A JavaScript library for building user interfaces, particularly for single-page applications (SPAs) with dynamic views.
Node.js: A JavaScript runtime that allows you to run JavaScript on the server side, enabling full-stack JavaScript development.
Together, these technologies enable developers to build modern, scalable web applications using JavaScript from frontend to backend.

The purpose of this project is deploy a simple To-Do application that creates To-Do lists.


**Step 0: Installing the AWS EC2 Instance**
To deploy a simple To-Do application that creates To-Do lists.
Try to connect to the instance by selecting "connect" and seeing the various ways to connect



![ssh connection procedure on aws](https://github.com/user-attachments/assets/d3dd9f11-0d39-4ce1-a8fe-1f14628d8cb2)


On windows powershell enter the command 
ssh -i "Allen-key.pem" ubuntu@ec2-3-84-234-240.compute-1.amazonaws.com


![after connecting to instance](https://github.com/user-attachments/assets/9cdca3f4-3549-4f7a-b0dc-5265802818af)


**Step 1: Backend Configuration**
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

**Step 2: Application Code Setup**
Create new directory for the Todo app


mkdir Todo

Enter the directory:


cd Todo



Enter the command to initialize the project and generate a package.json file which will contain information and dependencies about our Todo app.



npm init 



![npm init 1](https://github.com/user-attachments/assets/9975c3b1-d7df-40fe-9249-ec0293f85712)


![npm init 2](https://github.com/user-attachments/assets/c92eb929-7fb9-4ce3-92e2-ff13bb632c23)


Verify a package .json file is generated by typing ls

**Step 3: Installation of ExpressJs and creation of routes directory**

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


When we open a browser and enter out public ip address with the port 5000 attached we see our index.js output



    ![website after installing expressjs ](https://github.com/user-attachments/assets/16aa2ff6-bd2e-4205-8a4e-12a7eec14c22)


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

**Test Backend Code without front end using RESTful API**
We have cofigured our backend code and have also configured our database but we do not have our frontend UI configured yet, we would need React.js for that. Before thern we can test our backend code using RESTful API. We would need an API development client to test our code. We would use Postman app for this purpose.

We download and install the postman app. Sign up and start to initiate HTTP requests:


![post man after signin](https://github.com/user-attachments/assets/3d46ca0b-5fde-4959-b096-1074d54359eb)


![postman 2](https://github.com/user-attachments/assets/e111915d-d4de-4197-9873-21ebe2196d8d)

We would initiate a post request by selecting POST and entering our API using the format http://<public IP address or public DNS>:5000/api/todos 
Enter the body tab --> raw-->JSON and enter the following statement as a task which will be stored in the application database:

          {
              "action":"Finish your projects ASAP"
          }




![http post request on postman](https://github.com/user-attachments/assets/973c3e91-55d6-4840-a2f7-317dffae569b)

The task is stored in the database and can be retrieved using the GET request as seen below:



![postman GET request](https://github.com/user-attachments/assets/fb034c67-20f3-435f-b78b-5644fed7ef77)

To delete the task we would add the task id to the http url as thus http://<public IP address or public DNS>:5000/api/todos/<request ID>

![postman DELETE request](https://github.com/user-attachments/assets/54da5c3c-01bc-4618-8752-854c89295a9a)



We use the GET request again and can see the task have been deleted.


![postman DELETE request RESULT](https://github.com/user-attachments/assets/333ee0ab-61e0-4f82-acba-2672ceeb30fa)


**Step 2: Frontend Creation**

Next we would create a user interface for a web client(browser) which we would use to interact with the API.
We would use the react command as a scaffold for our app.
We will go back to our root directory Todo$ and use the create-react-app client:

      $npx create-react-app client

![npx create-react-app client installation](https://github.com/user-attachments/assets/3e8265f9-4426-48eb-baec-fd657177aa77)

NOTE: The installation using npx led to a prolonged delay which eventually led to the collapse of my EC2 instance and resulted in me launching a new instance on AWS and repeating the entire process from the installation of node.js and its dependencies up to the frontend part. You will observe a new public IP address and public DNS from this point onwards. Every other component like the mongo db cluster and the postman communication remains the same.
To prevent a repeat of the above-mentioned scenario, I have decided to use Yarn package installer instead of npx for installing the react client using the command:
yarn create react-app client

![yarn create react-app client](https://github.com/user-attachments/assets/888f2fb6-3f0e-456d-9fbe-518d31cc6dcc)


![installing create react app success](https://github.com/user-attachments/assets/6aa20c85-0493-42f0-a670-0cc220845625)


We can confirm the presence of the Client directory in the main root folder.
We can also see other components like the SRC directory which will be of importance to us shortly.


![client directory present](https://github.com/user-attachments/assets/c12cbe7a-6df7-48f0-8949-98171e7f8c6a)



Now we would need to create dependencies before we test the react app we just installed.
We would install Concurrently and Nodemon. Concurrently is a dependency that allows us run more than one command simultaneously on the same terminal..
We would also install nodemon which runs and monitor the server and automatically restart the server when there is a new change.

![installing concurrently and nodemon](https://github.com/user-attachments/assets/15a60448-c261-4948-a9ce-f7c880283393)ls

We would update our package.json file to reflect the newly installed dependencies concurrently and nodemon.

{
  "name": "todo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {"start": "node index.js",
    "start-watch": "nodemon index.js",
    "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "dotenv": "^16.4.5",
    "express": "^4.21.0",
    "mongoose": "^8.7.0"
  },
  "devDependencies": {
    "concurrently": "^9.0.1",
    "nodemon": "^3.1.7"
  }
}



![package json in todo dir updated to reflect concurrently and nodemon](https://github.com/user-attachments/assets/c8f9948a-bb86-4883-8745-e4f082485925)

We can see from the image above that the script part of the package.json code has been corrected.

In the Client folder we locate the package.json file and edit and uodate with the following code:
  "proxy": "http://localhost:5000"

This keypair is added for the purpose of being able to access the application with a browser using the url "http://localhost:5000" instead of "http://localhost:5000/api/todos"

![package json file in Client dir](https://github.com/user-attachments/assets/ef7a6979-9e27-4c08-b194-96c4f5903189)

In the Todo directory, run the command:
npm run dev

![npm run dev 1](https://github.com/user-attachments/assets/7c1d52a9-7084-4181-898e-28a6b76bc116)


![npm run dev 2](https://github.com/user-attachments/assets/d3f9a32b-ebe4-4104-834d-18b5809a3cbd)

We can see that we can now view client on our browser using the url http://localhost:3000 but we have to add an incoming rule for port 3000 in our virtual machine.

![curl localhost 3000 s](https://github.com/user-attachments/assets/cf8bdd40-8015-4ab5-aeae-7bd8601a15d9)

![application on client using url 13 40 9 37 on port 3000](https://github.com/user-attachments/assets/46a050b2-340b-4d90-bd9a-39942a13fa48)



**Creating React**
We would create the main react app next. We would use re-useable components. React makes our code modular.
We would use 2 Stateful and 1 Stateful component.

- On our Todo directory --> Client directory --> Src directory, Create Components directory and create 3 components in it. The files are Input.js, ListTodo.js and Todo.js:

  
        mkdir Components&&cd Components&&touch Input.js ListTodo.js Todo.js


* We would intall AXIOS. Axios is an promise based HTTP client for the browser and node.js
+ To install AXIOS, we would go to the client directory and use the command:

  
        npm install axios


  ![npm install axios](https://github.com/user-attachments/assets/7aaa83d4-7754-4b87-929d-d8bd85cc0d58)

+ Next we will open the Todo directory --> Client directory --> Src directory --> Components directory and copy the following code using the Index.js file:


                       import React, { Component } from 'react';
                import axios from 'axios';
                
                class Input extends Component {
                  state = {
                    action: '',
                  };
                
                  addTodo = () => {
                    const task = { action: this.state.action };
                
                    if (task.action && task.action.length > 0) {
                      axios
                        .post('/api/todos', task)
                        .then((res) => {
                          if (res.data) {
                            this.props.getTodos();
                            this.setState({ action: '' });
                          }
                        })
                        .catch((err) => console.log(err));
                    } else {
                      console.log('input field required');
                    }
                  };
                
                  handleChange = (e) => {
                    this.setState({
                      action: e.target.value,
                    });
                  };
                
                  render() {
                    let { action } = this.state;
                    return (
                      <div>
                        <input type="text" onChange={this.handleChange} value={action} />
                        <button onClick={this.addTodo}>add todo</button>
                      </div>
                    );
                  }
                }
                
                export default Input; 




  ![Input js](https://github.com/user-attachments/assets/101ce727-03a0-49ce-bea6-1159b04ee637)


  + Next we will copy the following command to the ListTodo.js file:
          
          
          import React from 'react';
          
          const ListTodo = ({ todos, deleteTodo }) => {
            return (
              <ul>
                {todos && todos.length > 0 ? (
                  todos.map((todo) => {
                    return (
                      <li key={todo._id} onClick={() => deleteTodo(todo._id)}>
                        {todo.action}
                      </li>
                    );
                  })
                ) : (
                  <li>No todo(s) left</li>
                )}
              </ul>
            );
          };
          
          export default ListTodo;    
              

                  
  ![ListTodo js in Components directory](https://github.com/user-attachments/assets/29b3578b-53f5-4293-b108-339f34622714)


  + Next we will copy the following command to the Todo.js file


  ![Todo js in components](https://github.com/user-attachments/assets/77618dd3-c125-46c5-bb57-73b7fa0f1d37)


                                  import React, { Component } from 'react';
                                  import axios from 'axios';
                                  import Input from './Input';
                                  import ListTodo from './ListTodo';
                                  
                                  class Todo extends Component {
                                    state = {
                                      todos: [],
                                    };
                                  
                                    componentDidMount() {
                                      this.getTodos();
                                    }
                                  
                                    getTodos = () => {
                                      axios
                                        .get('/api/todos')
                                        .then((res) => {
                                          if (res.data) {
                                            this.setState({
                                              todos: res.data,
                                            });
                                          }
                                        })
                                        .catch((err) => console.log(err));
                                    };
                                  
                                    deleteTodo = (id) => {
                                      axios
                                        .delete(`/api/todos/${id}`)
                                        .then((res) => {
                                          if (res.data) {
                                            this.getTodos();
                                          }
                                        })
                                        .catch((err) => console.log(err));
                                    };
                                  
                                    render() {
                                      let { todos } = this.state;
                                  
                                      return (
                                        <div>
                                          <h1>My Todo(s)</h1>
                                          <Input getTodos={this.getTodos} />
                                          <ListTodo todos={todos} deleteTodo={this.deleteTodo} />
                                        </div>
                                      );
                                    }
                                  }
                                  
                                  export default Todo;          
                                  
+ Next we would do a little tweak on the App.js file in a bid to delete the react logo:



            import React from 'react';
            import Todo from './components/Todo';
            import './App.css';
            
            const App = () => {
              return (
                <div className="App">
                  <Todo />
                </div>
              );
            };
            
            export default App;

            

  ![app js to delete logo](https://github.com/user-attachments/assets/f6ad9562-e07a-4ff6-96e2-f5ba32a02e1d)

            

  ![app js to delete logo](https://github.com/user-attachments/assets/30d53a27-764a-4e8e-a5ea-ff46545e732d)


  Next we go to the App.css and copy the command to it:


        .App {
          text-align: center;
          font-size: calc(10px + 2vmin);
          width: 60%;
          margin-left: auto;
          margin-right: auto;
        }
        
        input {
          height: 40px;
          width: 50%;
          border: none;
          border-bottom: 2px #101113 solid;
          background: none;
          font-size: 1.5rem;
          color: #787a80;
        }
        
        input:focus {
          outline: none;
        }
        
        button {
          width: 25%;
          height: 45px;
          border: none;
          margin-left: 10px;
          font-size: 25px;
          background: #101113;
          border-radius: 5px;
          color: #787a80;
          cursor: pointer;
        }
        
        button:focus {
          outline: none;
        }
        
        ul {
          list-style: none;
          text-align: left;
          padding: 15px;
          background: #171a1f;
          border-radius: 5px;
        }
        
        li {
          padding: 15px;
          font-size: 1.5rem;
          margin-bottom: 15px;
          background: #282c34;
          border-radius: 5px;
          overflow-wrap: break-word;
          cursor: pointer;
        }
        
        @media only screen and (min-width: 300px) {
          .App {
            width: 80%;
          }
        
          input {
            width: 100%
          }
        
          button {
            width: 100%;
            margin-top: 15px;
            margin-left: 0;
          }
        }
        
        @media only screen and (min-width: 640px) {
          .App {
            width: 60%;
          }
        
          input {
            width: 50%;
          }
        
          button {
            width: 30%;
            margin-left: 10px;
            margin-top: 0;
          }
        }





  ![app css in src](https://github.com/user-attachments/assets/e0bb6822-e1a4-44f1-82a3-2388c188d82b)



  + Next we will add the following code to index.css



      
       body {
        margin: 0;
        padding: 0;
        font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        box-sizing: border-box;
        background-color: #282c34;
        color: #787a80;
      }
      
      code {
        font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New", monospace;
      }


  ![index css final](https://github.com/user-attachments/assets/1feaed30-5759-467b-9a43-b2e9a4884d62)


  + All is set. Now we will go to the Todo directory and run the command to restart the node server:
            npm run dev


   ![final results npm run dev](https://github.com/user-attachments/assets/80d5163f-fd76-4632-a135-4cde53c9c10b)

  
  + We can now go to the browser and enter the url http://localhost:3000 or http://13.40.9.37:3000 to access the application.


    ![final results of app running  on browser](https://github.com/user-attachments/assets/334c4fd3-bf89-4dfe-b7fa-02b859d4ef2b)



    ![final results of app running  on browser 2](https://github.com/user-attachments/assets/9d65bc86-780f-4c7b-a8e6-52137668ff00)


    ![final results of app running  on browser 3](https://github.com/user-attachments/assets/e5787f0e-4510-495d-bc10-1965efb1cb85)


+ We can see that our Todo application is up and running.
        

 


























