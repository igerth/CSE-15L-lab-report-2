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

All relevant arguments and values remain the same for the methods and fields as well. 

The values that do change for this specific request are the `messages` ArrayList, which now has the string `How are you` appended to it, as well as the `print` string, which starts as an empty string and is changed to a concatenation of both messages, `"Hello" + "\n" + "How are you"`. 

**Part 2:** For this part, I will choose the ArrayExamples file to debug. 
- A failure-inducing input for the buggy program. This is for the `reverseInPlace` method: 
```
public class ArrayTests {
	@Test 
	public void testReverseInPlace() {
    int[] myInput = {1, 2, 3, 4, 5};
    ArrayExamples.reverseInPlace(myInput);
    assertArrayEquals(new int[]{5, 4, 3, 2, 1}, myInput);
	}
```
An input that doesn’t induce a failure (Referencing a test that was provided):
```
public void testReverseInPlace() {
    int[] input1 = { 3 };
    ArrayExamples.reverseInPlace(input1);
    assertArrayEquals(new int[]{ 3 }, input1);
	}
```
The symptom. I noticed that the `reverseInPlace` method was reversing the first half of the array, but not reversing the second half. Evident in this screenshot:

![Image](https://github.com/igerth/CSE-15L-lab-report-2/blob/main/Screenshot%202023-01-30%20at%204.55.35%20PM.png?raw=true)

The bug with the before-and-after code change required to fix it:
```
static void reverseInPlace(int[] arr) {
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = arr[arr.length - i - 1];
    }
  }
```
```
static void reverseInPlace(int[] arr) {
    int[] store = new int[arr.length];
    for(int i = 0; i < arr.length; i += 1) {
      store[i] = arr[arr.length - i - 1];
    }
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = store[i];
    }
  }
```
Why the fix addresses the issue: Before, the for loop was updating the input array in real-time, meaning that it worked for the first half of the array, but when it got to the second half of the array, it was calling back to the first half of the array and taking those already reversed values. So if the input array was `{ 1, 2, 3, 4, 5}` the output would be `{ 5, 4, 3, 4, 5}`. To fix this, I created a shallow copy of the input array so that in the for loop, the reversed input array could be copied while the loop iterated over the input array. This combats the previus issue. Then, I deep-copied the `store` array to the input array. 

**Part 3:** In summary, one of my favorite parts from the previous two labs was learning about webservers and how to handle URL requests. I liked the customization we could come up with in handling different requests. I had no idea how this was done before and I am glad I was able to do this in lab. 
