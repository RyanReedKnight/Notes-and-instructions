24 FEB 2023
Objective: to connect two podman containers, in this case,
one containing a Spring Boot instance, and the other an associated 
PostgreSQL database.

Method: The objective will be acomplished using a pod, a feature of podman 
that allows two containers to share ports. The pod is created first, and then integrated
with the two containers.

Step 1, create the pod: enter the following command to create the pod.

```
	podman pod create \
	--name pokedoom-pod \
	-p 5432:5432 \
	-p 8080:8080
```

* --name identifies the pod.
* port 5342 is mapped for the PostgreSQL database.
* port 8080 is mapped the the Spring Boot application.
* Note: So far it does not apear to be necisary to map the port for the front end,
	this makes sense since the front end the front end makes calls to port 8080 
	when interacting with the backend.
* Note: These ports will not be mapped in the following steps, the mapping is done.

Step 2A, creating podman container for PostgreSQL database: enter the following command.

```
	podman run \
        --name WhateverYouWantToCallYourDatabaseContainer \
        --pod pokedoom-pod \
        -e POSTGRES_PASSWORD=WhateverYourPasswordIs \
        -e POSTGRES_USER=WhateverYourUserNameIs \
        -d postgres
```

Step 2B, creating podman container for Spring Boot application: 
	enter the following command, it is assumed the image has been built already, good, straitforward instructions on 
	how to build the image are availible on Spring's website.

```	
	podman run \
	--pod pokedoom-pod \
	-e DB_PASSWD=WhateverYourPasswordIs \
	-e DB_URL=WhateverYourPostgreSQL_URL_Is \
	-e DB_USERNAME=WhateverYourUserNameIs \
	-e FRONT_END_ADDRESS=WhateverYourFrontEndAddressIs \
	-e JWT_SECRET=WhateverYourJWTSecretIs \
	WhateverYourImageIsCalled
```
* The environment variables being passed in are specific to the Spring Boot 
		application I am working with.
		
You should now have a working Spring Boot aplication, assuming there are not issues elsewhere.
