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

### My first request was `/add-message?s=Hello`, which then added the word `Hello` to the webpage.
![Image](https://github.com/igerth/CSE-15L-lab-report-2/blob/main/Screenshot%202023-01-30%20at%201.19.10%20PM.png?raw=true)

### My second request was `/add-message?s=How are you`, which then added the phrase `How are you` in a new line after `Hello`. 
![Image](https://github.com/igerth/CSE-15L-lab-report-2/blob/main/Screenshot%202023-01-30%20at%201.19.30%20PM.png?raw=true)

