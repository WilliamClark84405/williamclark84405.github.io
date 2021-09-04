<!--
## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/WilliamClark84405/williamclark84405.github.io/edit/main/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/WilliamClark84405/williamclark84405.github.io/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
-->
# ESP-32 Control Point Software Project
## Programming Guide and Notes
### Revision 1.0 v1 update 1

## Creating FLASH based web served base64 images

```
#include <WiFi.h>
#include <ESPAsyncWebServer.h>

const char* ssid = "my-SSID";
const char* password = "my-Password";

AsyncWebServer server(80);

const char index_html[] PROGMEM = R"rawliteral(
<!DOCTYPE HTML><html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
  <h2>ESP Image Web Server</h2>
  <img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEU == base64 image file truncated for brevity == lFTkSuQmCC">
</body>  
</html>)rawliteral";


void setup(){
  // Serial port for debugging purposes
  Serial.begin(115200);
  
  // Connect to Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi..");
  }

  // Print ESP32 Local IP Address
  Serial.println(WiFi.localIP());

  // Route for root / web page

  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send_P(200, "text/html", index_html);
  });

  // Start server
  server.begin();
}
 
void loop(){
  
}
```
## Continue
ESP32-Super Documentation Book

Compiler errors
	Compiler errors are your friends - strive for 100% clean, no errors or warnings of any type. Any errors the compiler flags are potential problems. Compiling code with the 'strict' compiler flag with undoubtedly create much stronger and fewer future problems when the code is released into usage. 
	
	Compiler errors may not be entirely truthful or apparent as some combinations of errors will pass through the compiler process as undetected but may not be what the programmer intended. Many types are compatible within compilation between functions for example, but deliver incorrect results. There is a division between proper syntax errors and process (logic or runtime) errors. The compiler can detect many types of compiler syntax errors but can not know the mind of the programmer and her/his intent in producing an algorithm for a particular action.
	Example snippit: Error reported to the wrong code segment then where it actually occured
	Error:/home/nvidia/Documents/Arduino/ESP32-Super/core0.ino: In function 'void Task1code(void*)':
core0:38:16: error: 'cpTriggers' was not declared in this scope
     cpTriggers();
     
Scope errors are frequently fromm the first non-compilable code line after something has gone awry in the first compiler pass that sets memory allocations and resolves functions to the memory mappings. Therefore, the error will comliain about an error and point to the first unresolvable return address error that may be nowhere near the actual error. These arrors are frquently from errors in the code hierarchy (where closing parenthesis code levels are not matched. Leaving out eiher a matching set of parenthesis will cause this error. Frequently in the last code segment you have edited rather that where the comiler has thrown the error.

The incorrect matching parenthsis happened in a much later code segment. See error below that caused an compilation error in a statement hundreds of lines earlier but next in the compilation pass. This is not a comilation error. The comiler is logically processing the code as it passes through the compiler. It is humans that have a hard time finding the real error as neither sets of code look related--but to the compiler is related in the most recent compiler pass.
=BEGIN SNIPPIT=
	 subValue = request.substring(equalMark + 1, ampersandMark);                   // Store for error diagnostics webpage value (webpageNextMark -> webpageEndMark position)
    configuration.webpageValue = subValue;
    Serial.println(subValue);

    ampersandMark = request.indexOf("&");
      
    subRequest = request.substring(ampersandMark);                                // Parse phrase '&' to next ' ' EOL
    subSearch = request.substring(ampersandMark + 1);                             // 'Get' line past '&' to next '&', complete 'Get' couple
    if (isStringValid(subSearch) == 1 // <- ERROR! No matching semicolon to end statement
      
    equalMark = subRequest.indexOf('=');
	=END SNIPPIT
	
	Errors may produce function prototypes for matching types within code tht may help resolve errors.
	
	

	Memory Management:
		Variables: 
		Stack: Make sure there is enough overhead in the stack to accomadate all of the running functions return addresses. For each function called a return address is 'pushed' onto a special memory segment called the stack. When the 'return' instruction is executed after a function runs, the oringinal code return address is 'pushed' into the MCU active code address register and execution is contunued from that point. There are other items that can be stored and present in stack memeory. How do you accomadate enough space on the stack? Some comilers and IDE's use profiling tools to determine 'worst case' usuage so that ample strage can be set for stack space. Lacking these tools, programmers using the Arduino IDE generally just register very large sections of memory dedicated to the stack. Small programs with few functions (< 100 instructions) can be safe with 1KB, medium (< 1000 instructions) 10KB, large (< 10000) 100KB, and huge 1MB for every 10,000 instructions. Out-of-control loops or recrsive functions that dig into the stack and run out of space will run and then reboot as memory is consumed and new stack memeory allocations can not be made or the hardware watchdog timer is exhausted without reset by the MCU.    
		Heap:
		
Using the Serial.println() function to troubleshoot (debug) and develop code sections
 
    // Get MULTIPLE HTTP Get responses and trim succeeding responses to process one-at-a-time later in webpagePutNVS, make sure it formatted as configuration 'Get'
    // Simple Menu with multiple Get responses =MORE=
    // GET /attributes?webBackGround=&webAlignment=&basicscheme=Pride+Month HTTP/1.1 LAST PARAMETER ONLY
    // GET /attributes?webBackGround=Black&webAlignment=Left&basicscheme=Night HTTP/1.1 FULL LINE
    } else if (request.indexOf('&') && request.indexOf('=') && request.indexOf('?')) {
    Serial.println("MULTIPLE HTTP FIRST responses");                                    // TWO parameters returned - Simple menu configuration
    Serial.println("Configuration '?=&=&= and var= and val=' Sequence Get");
      
    subrequest = request;   
    
      while (subrequest.indexOf(' ') > 0) {                                                // Parse Get line until end ' ' found
      // Continue if more parameters attached past two    // =EDIT HERE=
        int questionMark = request.indexOf('?');                                        // Parsing HTTP Get response string
        int equalMark = request.indexOf('=', questionMark);
        int ampersandMark = request.indexOf("&", equalMark);                            // MULTIPLE HTTP Get response has '&' character termination
        int spaceMark = request.indexOf(" ", secondEqualMark);
        
        // Extract first set(s) of Get parameters returned
        subrequest = request.substring(questionMark, equalMark);
        Serial.println("subrequest ->");
        Serial.println(subrequest);
        Serial.println("<- subrequest");            
        Serial.println(questionMark);        // TESTING                                 // Test for var and val
        Serial.println(equalMark);
        Serial.println(ampersandMark);
        Serial.println(spaceMark);

        configuration.webpageParam = configuration.webpageRequest.substring(questionMark + 1, equalMark); // Store for error diagnostics webpage value (webpageNextMark -> webpageEndMark position)      
        configuration.webpageValue = configuration.webpageRequest.substring(equalMark + 1, ampersandMark); // Store for error diagnostics webpage value (webpageNextMark -> webpageEndMark position)

        Serial.println("configuration.webpageParam:");
        Serial.println(configuration.webpageParam);
        Serial.println("configuration.webpageValue:");
        Serial.println(configuration.webpageValue);
      }
    // Alternate Menu Configuration items
    } else if (request.indexOf('&') && request.indexOf('=') && request.indexOf('?')) {
    Serial.println("MULTIPLE HTTP SECOND responses");                                        // TWO parameters returned - Simple menu configuration
    Serial.println("Configuration '&?= and var= and val=' Sequence Get");
    
    // Continue if more parameters attached past two
      int questionMark = request.indexOf('?');                                        // Parsing HTTP Get response string
      int equalMark = request.indexOf('=', questionMark);
      int ampersandMark = request.indexOf("&", equalMark);                            // MULTIPLE HTTP Get response has '&' character termination
      int secondEqualMark = request.indexOf("=", ampersandMark);                      // 
      int spaceMark = request.indexOf(" ", secondEqualMark);
      // First set of Get parameters returned
      configuration.webpageParam = configuration.webpageRequest.substring(equalMark + 1, ampersandMark); // Store for error diagnostics webpage value (webpageNextMark -> webpageEndMark position)
      
      Other Tricks:
      Take the monotony and extended manual editing out of repeated programming segments. Repeated code sections can be completed by writing short (bash, shell, python, etc.) scripts that can, rather than using functions, merge in long sections of formatted coded parameters into the final programming code quickly. Scipting lines that create scripts, that create scripts that output C++ code that are merged into your 'C' program. Note: Scripting extended code segments can be hazordous. It is best used for repeated code segments that can be easily scanned by the programmers eye and checked for errors. Scripted ot programmed source code can quickly go awry if not checked line-by-line before execution!
      
Example: Merge in a list of parmeeters to create a bash script that creates a shell file that produces "C" code for compilation.

File a1.txt contains: the reated "C' segment required
=SNIPPIT=
  if (configuration.webpageParam == "XXX") {
    configuration.XXX = configuration.webpageValue;
    preferences.putString("XXX:", configuration.XXX);
  }
=SNIPPIT=

File b1.txt contains: the parameters to be merged into the repeated segments of file "a1.txt" from the RAM memory parameters list
=SNIPPIT=      
  ulong sense3ACTime = 60;
  UInt sense3ACValue = 0; 
  UInt sense4AC = 256;
  ulong sense4ACTime = 60;
  UInt sense4ACValue = 0; 
=SNIPPIT=

Running the following in a bash interpreter
cat b1.txt | awk '{print "cat a1.txt | sed -e *s/XXX/"$1"/g* >> doit.sh"}' | sed -e "s/*/'/g" >> doit1.sh

... merges the parameters of file b1.txt into a1.txt

Output "bash" script file doit1.sh
=SNIPPIT=
cat a1.txt | sed -e 's/XXX/sense3ACTime/g' > doit.sh
cat a1.txt | sed -e 's/XXX/sense3ACValue/g' > doit.sh
cat a1.txt | sed -e 's/XXX/sense4AC/g' > doit.sh
cat a1.txt | sed -e 's/XXX/sense4ACTime/g' > doit.sh
cat a1.txt | sed -e 's/XXX/sense4ACValue/g' > doit.sh
=SNIPPIT=

Running './doit.sh > output.c' (after making executable: 'sudo chmod +x doit.sh') creates the following file 'output.c'

output.c file, produces four lines of formatted code for each value in column one of the inout file
=SNIPPIT=
	if (configuration.webpageParam == "sense3ACTime") {
    configuration.sense3ACTime = configuration.webpageValue;
    preferences.putString("sense3ACTime:", configuration.sense3ACTime);
  }
  if (configuration.webpageParam == "sense3ACValue") {
    configuration.sense3ACValue = configuration.webpageValue;
    preferences.putString("sense3ACValue:", configuration.sense3ACValue);
  }
  if (configuration.webpageParam == "sense4AC") {
    configuration.sense4AC = configuration.webpageValue;
    preferences.putString("sense4AC:", configuration.sense4AC);
  }
  if (configuration.webpageParam == "sense4ACTime") {
    configuration.sense4ACTime = configuration.webpageValue;
    preferences.putString("sense4ACTime:", configuration.sense4ACTime);
  }
  if (configuration.webpageParam == "sense4ACValue") {
    configuration.sense4ACValue = configuration.webpageValue;
    preferences.putString("sense4ACValue:", configuration.sense4ACValue);
  }
=SNIPPIT=

Troubleshooting HTML pages:
Hints: 
1. Add docuement type (required):
<meta name='viewport' content='width=device-width, initial-scale=1.0'><meta charset='UTF-8'>

2. Keep the HTML contents sections in order
Add style sheet information
external, inline, or 
</style>

3. Add head information
</head>

4. Add body information
<body>

5. Comment pages for troubleshooting 
<!--webpageAttributes-->

6. Add descriptive titles to link and body text
<h1 title='Main Menu Items'><a href='h1configlink'>Menu Main Titles</a></h1>
<h2 title='CP Active Status and Submenu Items - Whats On?'><a href='h2configlink'>CP Status Items</a></h2>
<h3 title='Subheadings or Current Configuration items'><a href='h3configlink'>Menu Subheadings</a></h3>
<h4 title='Informational Items such as current temperature and humidity'><a href='h4configlink'>Information Alerts</a></h4>
<h5 title='Submenu items such enable passive infared (PIR) detection to turn on lights'><a href='h5configlink'>Submenu Selections</a></h5>
<h6 title='Lesser Menu Items such as to select a CP switch time on parameters'><a href='h6configlink'>Lesser Menu Items</a></h6>
<p  title='Informational CP status items such as time and date at the bottom of CP web pages'><a href='pconfiglink'>Informational Items</a></p>

7. Add end tags in reverese order to main body tags
</body>
</html>


Arduino Preprocessor Directives Tutorial
Jan Gromes 



Now we have a basic idea what really goes into compiling Arduino sketch, but out of all the compilation stages described above, we’re going to only focus on the second one: the preprocessor.
 
Preprocessor basics
In the above text, I mentioned that preprocessor is essentially very simple: it just takes in text input, searches from some keywords, does some operations according to what it finds, and then outputs a different text. Even though it’s very simple, it’s also extremely powerful, because it allows you to do things that would otherwise be very complicated – if not impossible – in plain C/C++ language.
The preprocessor works by searching for lines that start with the hash sign (#) and have some text after it. This is called preprocessor directive and it’s a sort of “command” for the preprocessor. The full list of all supported directives with detailed documentation can be found here:
https://gcc.gnu.org/onlinedocs/cpp/Index-of-Directives.html#Index-of-Directives.
In the following text, I will focus mainly on #include, #define and conditional directives, since these are the most useful on Arduino, but if you want to know more about some more “exotic” directives, like #assert or #pragma, this is the place to get official information.
 
Adding extra code: #include directive
This is probably the best-known preprocessor directive, not only amongst Arduino enthusiasts, but in C/C++ programming in general. The reason is simple: it’s used to include libraries. But how exactly does it happen? The exact syntax is as follows:
#include <file>
or
#include "file"
The difference between the two is subtle and mainly comes down to where exactly the preprocessor searches for the file. In the first case, the preprocessor searches only in directories specified by the IDE. In the second case, the preprocessor first looks through the folder containing the source, and only if the file isn’t there, it moves on the same directories it would search in the first case. Since the folder containing libraries is specified in Arduino IDE, there’s no major difference between the two when including a library.
When the preprocessor finds the file, it simply copy-pastes the contents into the source code in place of the #include directive. However, if no such file can be found in any of the directories, it will raise a fatal error and stop the compilation.
It’s important to remember that preprocessor only works with text – it doesn’t really understand what all those fancy letters and numbers mean. And most importantly, it does zero higher-level checks on what was included and how many times. Let’s take a look what can happen if you use incorrectly written library.
#include <ExampleLibrary.h>

void setup() {

}

#include <ExampleLibrary.h>

void loop() {

}
There’s really not much going on in the Arduino sketch. Notice that we’re including a file called “ExampleLibrary.h”, and that we’re including it twice.
//This is an example library

int a = 0;

//End of example library
And this is what’s inside “ExampleLibrary.h”. Again, not much going on, except for one integer variable. So what happens when we try to compile this Arduino sketch?

The error shows that the variable a is being declared twice, which causes the compilation to fail. This is what the source code looks like after preprocessor is done.
//This is an example  library

int a = 0;

//End of example library

void setup() {

}

//This is an example  library

int a = 0;

//End of example library

void loop() {

}
It is obvious now that no library should get included more than once, but how do you achieve this without having to rely on the user? The standard solution is to wrap the entire library in the following construct:
#ifndef _EXAMPLE_LIBRARY_H
#define _EXAMPLE_LIBRARY_H

//This is an example  library

int a = 0;

//End of example library

#endif
Now, when the library is included for the first time, the preprocessor checks whether there is something defined with the name “_EXAMPLE_LIBRARY_H”. Since nothing like that exists yet, it proceeds on the next line and defines a constant called “_EXAMPLE_LIBRARY_H”. Then, the library code is copied into the sketch. 
When including the library for the second time, the preprocessor checks for constant named “_EXAMPLE_LIBRARY_H” again. This time however, the constant is already defined from the previous #include, so nothing is added to the sketch. Now, the compilation finishes successfully. The #ifdef and #endif are conditional directives, which will be discussed later on.
 
Defining things: #define directive
In the previous example, we used the #define directive to create a constant, which determined whether to include a library or not. In the official documentation, anything defined by #define directive is called macro, so I will stick to that terminology in this article. The syntax of this directive is following:
#define macro_name macro_body
Most Arduino beginners are somewhat confused by macros. If I define a macro like the following:
#define X 10
what is the exact difference from declaring some variable like below?
int Y = 10;
Again, it all comes down to the fact that preprocessor only works with text. When it finds the #define directive, it will search the rest of the source code and replace all occurrences of “X” with “10”. This means that unlike variables, the value of macro will never change. Also, you have to keep in mind that preprocessor only searches the rest of the source code after the line with #define on it. Let’s see what happens when we try to use a macro before it is defined.
int Y = X;
#define X 10
int Z = X;

void setup() {
 
}

void loop() {
 
}
Compiling the above code will give us the following error:

The code after preprocessing will look like this:
int Y = X;
int Z = 10;

void setup() {

}

void loop() {
 
}
The first line contains X, which is interpreted as a variable, however, that variable was never declared, so the compilation stops.
Even though the most common use of of #define directive is to create named constants, it can do much more than just that. For example, let’s say you want to know which of two given numbers is smaller. You could write a function that will do just that.
int min(int a, int b) {
 if(a < b) {
   return(a);
 }
 return(b);
}
Or in a simpler way with a ternary operator:
int min(int a, int b) {
 return((a < b) ? a : b);
}
However, both of these functions will be compiled and will take up precious program storage space. We can achieve the same effect with the following function-like macro, that will take much less program space.
#ifndef MIN
 #define MIN(A, B)     (((A) < (B)) ? (A) : (B))
#endif
Now, each occurrence of “MIN(A, B)”, will be replaced with “(((A) < (B)) ? (A) : (B))” where “A” and “B” can be either a number, or a variable. Notice that the #define is wrapped in the same protective construct that prevents user from defining the macro twice.
When creating macros, you have to keep in mind that once again, they are processed as text. That’s why in the definition above, pretty much everything is wrapped in brackets. Try and guess the result of the following operation.
#ifndef MULTIPLY
 #define MULTIPLY(A, B)     A * B
#endif

//some code...

int result = MULTIPLY(2 - 0, 3);
The value of result should be 6, since 2 – 0 is 2 and 2 * 3 is 6 right? What if I told you, that the result will be 2? The following is what actually gets compiled:
int result = 2 - 0 * 3;
Since multiplication has priority over subtraction, it is obvious now that the result has to be 2, because 3 * 0 is 0 and 2 – 0 is still 2. The correct version will look like this:
#ifndef MULTIPLY
 #define MULTIPLY(A, B)     ((A) * (B))
#endif
 
Conditional compilation: #if directives
In the previous examples, I used #ifndef a directive, which allowed me to check, whether library was already included. This directive can be used to achieve something that would be impossible in the terms of C/C++ language only: conditional syntax. These directives have the following syntax:
#if expression

  //compile this code

#elif different_expression
 
 //compile this different code

#else
 
 //compile this entirely different code

#endif
The most common way to use conditional syntax is to check whether a macro has been defined. To do that you can use several specialized directives:
#ifndef macro_name

  //compile this code if macro_name does not exist

#endif
We’re already familiar with the above, since we used this directive to check whether a library was already included. You can also use this condition:
#ifdef macro_name

  //compile this code if macro_name exists

#endif
The above is a just shorthand for #if defined, which can be used to test multiple macros in a single condition. Note that every condition has to end with #endif directive, to specify which parts of the code are affected by the condition, and which ones are not.
Let’s take a look at a practical example. Suppose you have written a library and you want it to work correctly on both Arduino UNO and Arduino Mega. This seems like a good idea, right? Portable code is always easier to use than having to adapt already working library for a different board. But what if , for example, your library uses the SPI bus? This bus is located on pins 11 – 13 on Arduino UNO, but on Mega, it is moved to pins 50 – 52.
How can you tell the compiler that it should use the correct pins, no matter what board you’re currently uploading into? You guessed it – conditional syntax! Based on what board you have selected in the Arduino IDE in the “Tools” > “Board” menu, the IDE will define different macros, allowing you to select parts of code that will only compile for a specific board! This is incredibly powerful because it allows you to do something like this:
#if defined(__AVR_ATmega168__) || defined(__AVR_ATmega328P__)
 
 //this will compile for Arduino UNO, Pro and older boards
 int _sck = 13;
 int _miso = 12;
 int _mosi = 11;

#elif defined(__AVR_ATmega1280__) || defined(__AVR_ATmega2560__)
 
 //this will compile for Arduino Mega
 int _sck = 52;
 int _miso = 50;
 int _mosi = 51;

#endif
See the beauty of it? With only three lines of code, we have made a multi-platform, portable library! On a side note, this is exactly how the RohmMultiSensor library (from Lightweight Arduino Library for ROHM Sensor Evaluation Kit) knows which parts of code should be compiled for each selected sensor. If you take a peek inside the header file RohmMultiSensor.h, you will only see several #ifdef and some #include directives. Since all sensor-specific code is stored in separate .cpp files, it is easy to add new sensors to the library – just create another file, then create the same #ifdef – #include – #endif structure the other sensors use. Done!
Providing feedback: #warning and #error directives
The last directives we’re going to cover are #warning and #error. Both of them are really self-explanatory, so here’s the syntax:
#warning "message"
and
#error "message"
When preprocessor finds these directives, it will print the message into the Arduino IDE console. The difference between the two is that after #warning, compilation proceeds as normal, while #error stops the compilation altogether. 
We can use this in our previous example:
#if defined(__AVR_ATmega168__) || defined(__AVR_ATmega328P__)

  //this will compile for Arduino UNO, Pro and older boards
 int _sck = 13;
 int _miso = 12;
 int _mosi = 11;
 
#elif defined(__AVR_ATmega1280__) || defined(__AVR_ATmega2560__)

 //this will compile for Arduino Mega
 int _sck = 52;
 int _miso = 50;
 int _mosi = 51;
 
#else

 #error “Unsupported board selected!”

#endif
This way, when the user tries to compile the library for some other Arduino board (e.g. Yún, LilyPad, etc.) the compilation will fail, rather than not defining the SPI pins at all.
