Based on tutorial <a href="https://www.youtube.com/watch?v=UhSEsoIUROA
">"Upload an image in Spring Boot; Save to Disk with IntelliJ IDEA"</a> by Brandon Jones

Create form in HTML file with method="post", enctype="multipart/form-data", action"<endpoint in spring boot app>".
As a child of this form, write an input with type="file" and name="imageFile" (the name will corespond to a RequestParam in the spring boot application). Write an additional input child of type submit.

To pass the file from the client to the spring boot application, set a contorller method with the endpoint set in 
the HTML file, this controller must recieve post requests. One of the parameters for this method will be
a multipart file with the following anotation "@RequestParam("imageFile")", imageFile maps it to the input 
element in the above paragraph.

Set Path path = Paths.get(location + "<file-name>"), this determines the name and location of the file once it is written. location is a string that holds the directory the file will be stored in, I defined it in my 
application.yaml file. Take the multipart file and File.write(path, multipartfile).


