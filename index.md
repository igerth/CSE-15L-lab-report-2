# Lab Report 2 - Servers and Bugs (Week 3)

**Part 1:** I wrote a mini web server called `StringServer` that supports the path and behavior that is displayed in the following screenshots. It keeps track of a single string that gets added to by incoming requests, which look like `/add-message?s=<string>`. First, here is my code which I will refer back to when describing two examples of `/add-message`.
```
import java.io.IOException;
import java.net.URI;
import java.util.*;


class Handler implements URLHandler {
  List<String> messages = new ArrayList<>();
  public String handleRequest(URI url) {
    if (url.getPath().equals("/add-message")) {
      String[] message = url.getQuery().split("=");
      if (message[0].equals("s")) {
        messages.add(message[1]);
        String print = "";
        for (int i = 0; i < messages.size(); i++) {
          print += (messages.get(i) + "\n" );
        }
        return print;
      } else {
        return "Bad Argument";
      }
    } else if (url.getPath().equals("/")){
      return "Input Message to Add";
    } else {
      return "Bad Argument";
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

## Examples:
My first request was `/add-message?s=Hello`, which then added the word `Hello` to the webpage.

![Image](https://github.com/igerth/CSE-15L-lab-report-2/blob/main/Screenshot%202023-01-30%20at%201.19.10%20PM.png?raw=true)

Inside the `Handler` class, the first if statement runs inside the `HandleRequest` method. It then uses the `getPath` method to get the path of the URL, and because the path equals `/add-message`, it uses the `getQuery` method and makes a String array with the first element `s` being to the left of the `=` and the second element `Hello` being to the right. Because the left-hand side equals `s`, the right-hand side `Hello` gets added to the `messages` ArrayList I created to be then concatenated into one string, with each message seperated by `\n`, and then returned. 

The most important method in this is the `handleRequest` method inside of the `Handler` class, which takes the URL and processes the request in the URL path to return the string of inputs. Within the method, there are values such as the `message` array, which splits the query at the `=`, and then the `messages` ArrayList where the message string in the query is added, and then finally the `print` string, which is a concatenation of all the messages seperated by the newline character. 

From this specific request, the `Hello` message is added to the `messages` ArrayList, which changes the value of `messages` since it was empty before this call. The `print` string is also changed from and empty string to `Hello`, since `Hello` is the message we need to return. 

My second request was `/add-message?s=How are you`, which then added the phrase `How are you` in a new line after `Hello`. 

![Image](https://github.com/igerth/CSE-15L-lab-report-2/blob/main/Screenshot%202023-01-30%20at%201.19.30%20PM.png?raw=true)

The methods called are exactly similar to when they were called in the previous example with `Hello`, however, `How are you` now gets added to the `messages` ArrayList and the `print` string now prints both `"Hello" + "\n" + "How are you"`. 

