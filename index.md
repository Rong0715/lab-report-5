# A Review on Lab Report 2: StringServer
Lab Report 2 is by far the most interesting lab report for me. It offers a simple and thought-provoking perspective on how much we can do with the knowledges that we currently know -- I used to think after taking several Java programmimg courses all I can do was writing bunch of for loops and making sure my code output matches the sample output on the assignment instruction. But after this report and its corresponding lab, I understand, for the first time operating one myself, how powerful servers are as tools for us to store data, interact with other people, and develop interesting functions with fairly high potential.
### What did I do in Lab Report 2?
In this report, I wrote a web server called `StringServer` that keeps track of a single string that gets added to by incoming requests using path like this:
`/add-message?s=<string>`.
The code I wrote to in order to perform as purpose is below:
``` Java
import java.io.IOException;
import java.net.URI;

class Handler implements URLHandler {

    String dialogue = "";

    public String handleRequest(URI url) {
        if (url.getPath().equals("/")) {
            return String.format("It's an empty path.");
        } else {
            if (url.getPath().contains("/add-message")) {
                String[] parameters = url.getQuery().split("=");
                if (parameters[0].equals("s")) {
                    dialogue += "\n" + parameters[1];
                    return dialogue;
                }
            }
            return "404 Not Found!";
        }
    }
}

class StringServer {
    public static void main(String[] args) throws IOException {
        if(args.length == 0){
            System.out.println("Missing port number! Try any number between 1024 to 49151");
            return;
        }

        int port = Integer.parseInt(args[0]);

        Server.start(port, new Handler());
    }
}

```

The `Handler` class contains operations to process the given URL. It firstly gets the path part of the URL, and check if it's equal to `"/"`, which means the path is empty. If so, the message `"It's an empty path."` will be printed on the screen. If the path contains further information, the program moves on to check if the path contains the sub string `"/add-message"` which is used by the user to prompt the program to perform add-message task. If the path does not contain this keyword, the message `"404 Not Found!"` will be printed on the screen. If the path does contain this keyword, the program will then split the query part of the path by `"="` and stored all the splited sub strings in the array `parameters`. The message the user wants to print on the screen would be the second element inside the erray, we can access it using `parameters[1]` and add it to the `String` object `dialogue`. The program will return this String object and the other files will help print this message on the screen.
### Add username and time stamp to the dialogue!
One of the many cool things we can implement using this approach is that we can make the dialogue more like a real chat tool, as the server allows machines which are connected to the server to run the file at the same time and modify the dialogue.
We can implement this feature by change this part of the above code:
``` Java
if (url.getPath().contains("/add-message")) {
    String[] parameters = url.getQuery().split("=");
    if (parameters[0].equals("s")) {
        dialogue += "\n" + parameters[1];
        return dialogue;
    }
}
```
Instead of simply adding messages in this form `/add-message?s=<string>`, we can use this form `/add-message?s=<username>=<string>` to include username as well. This way we could also store username inside the `parameters` array.
``` Java
String[] parameters = url.getQuery().split("=");
if (parameters[0].equals("s")) {
    dialogue += "\n" + parameters[1] + ": " + parameters[2];
    return dialogue;
}
```
So that if the user gives a path looking like this `/add-message?s=Rong=Hello`, the output message will be like this:
`Rong: Hello`.
We can furthermore add time stamp at the end of the message, in order to do that, `java.time.format.DateTimeFormatter` and `java.time.LocalDateTime` can be imported to help.
``` Java
import java.time.format.DateTimeFormatter;  
import java.time.LocalDateTime;   
import java.io.IOException;
import java.net.URI;

class Handler implements URLHandler {

    String dialogue = "";

    public String handleRequest(URI url) {
        if (url.getPath().equals("/")) {
            return String.format("It's an empty path.");
        } else {
            if (url.getPath().contains("/add-message")) {
                String[] parameters = url.getQuery().split("=");
                if (parameters[0].equals("s")) {
                    DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss");  
                    LocalDateTime now = LocalDateTime.now();  
                    dialogue += "\n" + parameters[1] + ": " + parameters[2] + " (" + dtf.format(now) + ")";
                    return dialogue;
                }
            }
            return "404 Not Found!";
        }
    }
}
```
With this code, we can connect to the same server port and chat with our friends! Except I don't have any friends so I tested the code all by myself :( 
```
Rong: Hello (2023/03/13 17:43:48)
Chandler: Hi, can I BE more bored writing this lab report? (2023/03/13 17:44:36)
Joey: How you doin'? (2023/03/13 17:44:58)
```
### Conclusion
Cool stuff. If you like this lab report, smash the full score button and don't forget to subscribe to my Github account for more lab reports. 
