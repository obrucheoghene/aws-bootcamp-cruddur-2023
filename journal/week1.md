# Week 1 — App Containerization

## Required Homework

### 1. Containerize Application (Dockerfiles, Docker Compose)
Here are the steps I took to containerize the Cruddur application.
I started our by containerizing each of the application

### Steps I followed to containerize Backend-flask
a. I created a `Dockerfile` in `backend-flask` directory

```dockerfile
FROM python:3.10-slim-buster

WORKDIR /backend-flask

COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt

COPY . .

ENV FLASK_ENV=development

EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```
b. I ran the command below to build `backend-flask` docker image 
 ```sh
 docker build -t  backend-flask ./backend-flask
 ```
 Here is the result showing that a `backend-flask` image has been created
 ![Backend-Flask Docker Image Build](./assets/backend-img-build.png)
 
c. After which I ran the container with the command below

```sh
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
```
This command started the container on port `4567` and set environment variable for `FRONTEND_URL` and `BACKEND_URL` to `*`

Preview container environment variable
 ![Backend-Flask Container ENV](./assets/backend-image-env-set.png)

Preview port container is running on
 ![Backend-Flask Container port](./assets/backend-running-port.png)
 
 d. I appended `/api/activities/home` to the address url showing in the above port preview and opened it on the browser.
 Here was the the response I received.
  ![Backend-Flask Api Response](./assets/backend-running-response.png)


### Steps I followed to containerize Frontend-React-js

a. I installed the npm packages by running the commands below
```sh
cd frontend-react-js
npm i
```

b. I created a `Dockerfile` in `frontend-react-js` directory

```dockerfile
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```

c. To build `frontend-react-js` docker image, I navigated to the app root directory using `cd ..` then I ran the command below

 ```sh
 docker build -t frontend-react-js ./frontend-react-js
 ```

d. I started the `frontend-react-js` container on port `3000` with the command below

```sh
docker run -p 3000:3000 -d frontend-react-js
```

Preview of port where the `frontend-react-js` container is running on
 ![frontend-react-js Container port](./assets/frontend-backend-port.png)
 
 
 d. I opened the address url running on port 3000 on the browser to preview the application frontend.
 
   ![frontend-react-js Preview ](./assets/frontend-react-js-preview.png)

As you could have noticed in the above preview, the `frontend-react-js` has no data from the `backend-flask` This is because both of the containers are not connected yet to communicate with each other.

### Using Docker Compose to Manage Multiple container
Inorder to spin up the containers for `frontend-react-js` and `backend-flask` images at once, I created a `docker-compose.yml` file with content as shown below

```yml
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
```

I installed the npm packages required by the `frontend-react-js`
```sh
cd frontend-react-js
npm i
```

On the root directory of the project I executed the command below

```sh
docker compose up
```
Here is the preview of `frontend-react-js` and `backend-flask` containers running on port `3000` and `4567` respectively

 ![frontend-react-js and Backend-flask Containers running](./assets/docker-compose-images-running.png)

Here is the preview of the application on the browser
 ![Application opened in the browser](./assets/cruddle-app-docker-compose.png)


### 2. Document the Notification Endpoint for the OpenAI Document 

  Here is my documentation of the notification endpoint in the `openapi-3.0.yml` file
  
  ```yml
    /api/activities/notifications:
    get:
      description: Return of activities for all of those that I follow
      tags:
        - activities
      parameters: []
      responses:
        '200':
          description: Return array of activities
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Activity'
  ```
  
  Preview of the Notifications endpoint on swagger editor

   ![Notification endpoint on swagger editor](./assets/open-api-notification-spec.png)


### 3. Write a Flask Backend Endpoint for Notifications

- On the `services` folder of the `backend-flask` I created a `notifications_activities.py` file with the content below

```py
from datetime import datetime, timedelta, timezone
class NotificationsActivities:
  def run():
    now = datetime.now(timezone.utc).astimezone()
    results = [{
      'uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
      'handle':  'Wilfred Cloud',
      'message': '@ AWS bootcamp with Andrew Brown',
      'created_at': (now - timedelta(days=2)).isoformat(),
      'expires_at': (now + timedelta(days=5)).isoformat(),
      'likes_count': 5,
      'replies_count': 1,
      'reposts_count': 0,
      'replies': [{
        'uuid': '26e12864-1c26-5c3a-9658-97a10f8fea67',
        'reply_to_activity_uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
        'handle':  'Worf',
        'message': 'This post has honor!',
        'likes_count': 0,
        'replies_count': 0,
        'reposts_count': 0,
        'created_at': (now - timedelta(days=2)).isoformat()
      }],
    }
    ]
    return results
```
- On `app.py` I imported the `notifications_activities.py`

```py
from services.notifications_activities import *
```

- Finally I created the notifications endpoint route

```py
@app.route("/api/activities/notifications", methods=['GET'])
def data_notifications():
  data = NotificationsActivities.run()
  return data, 200
```

Here is the preview of my Flask Backend Endpoint Notifications

 ![Flask Backend Endpoint Notifications](./assets/notification-endpoint-implementation.png)

 On my browser, I appended `/api/activities/notifications` to the application URL to recieve the json response on the Notifications api.

 Here is the preview of the response

 ![ Notifications Api Response](./assets/notification-api-response.png)


### 4. 	Write a React Page for Notifications

- I created  `NotificationsFeedPage.js` and `NotificationsFeedPage.css`

 ![NotificationsFeedPage](./assets/notificationfeedpage.png)


- Imported the `NotificationsFeedPage.js` in the `App.js`

```js
import NotificationsFeedPage from './pages/NotificationsFeedPage';
```

- Created the route for the `NotificationsFeedPage`

```js
  {
    path: "/notifications",
    element: <NotificationsFeedPage />
  }
```

- On the Cruddur app opened in browser I clicked on `Notifications` on the sidebar to open the `NotificationsFeedPage`

Here is the preview

 ![NotificationsFeedPage](./assets/notification-feed-page.png)


### 5. 	Run DynamoDB Local Container and ensure it works

- I integrated the DynamoDB Local image config into our existing `docker-compose.yml` file

```yml
services:
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
```

- I ran `docker-compose up` to run the container

Preview of DynamoDB local running
![DynamoDB local](./assets/dynamodb-container.png)

- Using in commands listed in [100DaysofCloudChallenge](https://github.com/100DaysOfCloud/challenge-dynamodb-local)

  - Created a `Music` table

  ```sh
  aws dynamodb create-table \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --table-class STANDARD
  ```

  ![DynamoDB local](./assets/dynamodb-createtable.png)

   - Created an Item

    ```sh
    aws dynamodb put-item \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --item \
        '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}}' \
    --return-consumed-capacity TOTAL  
    ```

    ![DynamoDB local](./assets/dynamodb-createitem.png)

    - List Tables

    ```sh
    aws dynamodb list-tables --endpoint-url http://localhost:8000
    ```
    ![DynamoDB List Tables](./assets/dynamodb-listtables.png)

    - Get records

    ```sh
    aws dynamodb scan --table-name Music --query "Items" --endpoint-url http://localhost:8000
    ```
    ![DynamoDB Get Records](./assets/dynamodb-scantable.png)

### 6. Run Postgres Container and ensure it works
