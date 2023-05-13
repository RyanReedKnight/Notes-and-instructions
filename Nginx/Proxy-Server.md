# My experience using Nginx to connect two EC instances.
  I am documenting my experience setting up a proxy server via Nginx. My goal is to make this the tutorial I wish I had when I was setting this up for the first time. This assumes you have been through a tutorial on how to use 
Nginx as a reverse proxy, and are familiar with the basic configuration files that are required.
# What I was trying to accomplish.
  I had two EC2 instances set up on AWS, EC2 instance A needed to serve a React website, and to direct Axios calls from the website to EC2 instance B.
EC2 instance B hosted a Srping Boot application, which sends images to EC2 instance A when it recieves a request to do so.

# The wrong way of doing this
  Supose the addresses of the EC2 instances are ```ec2-A.com``` and ```ec2-B.com```, and supose the context path for the Spring Boot application is ```/photo-api```.
The first time I attempted this, I had Axios fetch photos directly from ec2-B.com, so that a function fetching a photo might look something like this
```
getPhotoBytes = async (photoKey:string):Promise<ArrayBuffer> => {
        const res = await axios.get<ArrayBuffer>(`http://ec2-B.com/photo-api/files/${photoKey}`,
        {responseType:'arraybuffer'}
        )
        return res.data;
}
```
  The config I would use when setting up Nginx for pass the calls to ec2-B.com (), would be as follows (note the 
  Spring Boot app is configured to run on port 5000). 
 ```
 # ec2-B.conf
 server {

        listen  80;
        listen [::]:80;
        server_name ec2-B.com;

        location /photo-api {
                proxy_pass "http://ec2-B.com:5000/photo-api";
                proxy_set_header Host ryan-knights-photo-website.com;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-Proto $scheme;
                add_header 'Access-Control-Allow-Origin' 'http://ec2-B' always;
                add_header 'Access-Control-Allow-Methods' 'GET';
                add_header 'Access-Control-Allow-Headers' 'Content-Type, application/json';
                add_header 'Access-Control-Allow-Headers' 'Content-Type, multipart/form-data';
                add_header 'Access-Control-Allow-Headers' 'Content-Type, text/plain';
        }
}

 ```
  I tried variations of this multiple times, but was never successfull, because it violated the cross-orign 
  resource policy of the browser, after a lot of frustration, I tried a different approach.
  
 # The correct way (or at least the way that worked)
  To not violate the cross origin resource policiy of the browser, I had Axios make it's requests to 
  the domain the web page was being hosted on (ec2-A.com), and then used Nginx to direct those calls to
  ec2-B.com. So the aforemention Axios function would look something like this
  ```
  getPhotoBytes = async (photoKey:string):Promise<ArrayBuffer> => {
        const res = await axios.get<ArrayBuffer>(`http://ec2-A.com/photo-api/files/${photoKey}`,
        {responseType:'arraybuffer'}
        )
        return res.data;
}
  ```
 Now instead of having a separate config file for ec2-B.com, the proxy pass will take place in the ec2-A.conf file,
 ```
 server {

        listen  80      default_server;
        server_name     ec2-A.com;

        location / {
                root    /usr/share/nginx/html;
                index   index.html      index.htm;
        }
        # Direct Axios calls to ec2-B.com, to be recieved by the Spring Boot application.
        location /photo-api {
                proxy_pass http://ec2-B.com:5000/photo-api;
                proxy_set_header Host ec2-A.com;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-Proto $scheme;
                add_header 'Access-Control-Allow-Origin' 'http://ec2-B.com' always;
                add_header 'Access-Control-Allow-Methods' 'GET';
                add_header 'Access-Control-Allow-Headers' 'Content-Type, application/json';
                add_header 'Access-Control-Allow-Headers' 'Content-Type, multipart/form-data';
                add_header 'Access-Control-Allow-Headers' 'Content-Type, text/plain';
        }
}

 ```
The browser no longer needs to make calls to a different endpoint than the one it is served from,
and CORS violations are no longer an issue.
  
 
 
 
