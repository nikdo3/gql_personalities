# ISDatabase
Database backend for university site. Project is based on SQLAlchemy and GraphQL (strawberry federated).
<br/><br/>
This project contains only SQLAlchemy models and GraphQL endpoint to provide data from the postgres database running in separate container. To successfully start this application you need to have a running postgres database (for instance in docker container).
<br/><br/>

There are two supported ways to start the application:
<br/><br/>

Start the app without the docker
- is quite complicated as it is part of federation API, prefer use in docker
- you need to have running postgres database which is ready to use
- change the ComposeConnectionString inner constants in DBDefinition.py to your postgres address (see used pattern)
- to start the app outside of docker use the following command:
uvicorn main:app --reload
- after application startup you can access the graphQL UI on ip given by uvicorn - remember to add /gql (example: http://127.0.0.1:8000/gql)
- by default the app creates some random database after every startup (not all tables are populated with data)
<br/><br/>

Start the app inside the docker using docker-compose.yml (recommended)
- to start the app as a docker container you first need to create the gql_core image - to do this use following command:
docker build -t gql_core .
- this image contains our solution with SQLAlchemy models and GraphQL endpoint
- (optional) you can run the container standalone on any port you want by using: docker run -p your_port:8001 gql_core
- use docker-compose.yml file to set up postgres database and gql_core using this command:
docker-compose up
- docker will use given compose file to create two containers (isdatabase_gql_entry_point and isdatabase_database)
- gql_entry_point is based on the gql_core image and provides the GraphQL endpoint (contains all our code)
- database container is based on postgres 13.2 image and provides a database (image will be downloaded if necessary)
- postgres is automatically set up by docker-compose.yml - there you can edit variables such as database name, username and password - these will be used by gql to acess the database
- these two containers are able to exchange data between each other on closed docker network
- only gql endpoint is available for other device outside of docker network - to access the GraphQL UI open http://localhost:82/gql on your device
<br/><br/>

- in this version of our project the database is populated with random data (not all databse is populated - for testing purposes only)
<br/><br/>

## Usefull commands

```bash
uvicorn main:app --env-file environment.txt --port 8001
```

```bash
pytest --cov-report term-missing --cov=src --log-cli-level=INFO -x
```

test pto vkládání dat:
mutation MyMutation {
  certificateInsert(
    certificate: {level: "1", id: "bf92dfb7-f999-47e5-9d3a-9ce14bbcbae0", startdate: "2016-06-15T00:00:00", userId: "09baa07b-5b9a-4b4f-b230-be41ef9f021d", certificateTypeId: "9ef403b8-f75d-4a7a-a013-65e6706f8d8a", enddate: "2024-12-09T07:58:30.821886"}
  ) {
    ... on InsertError {
      input
    }
  }
}
