# Repos 

PayloadsAllTheThings/XSS Injection

hacktricks/pentesting-web/xss-cross-site-scripting/

hacktricks/pentesting-web/xssi-cross-site-script-inclusion.md

hacktricks/pentesting/pentesting-web/xss-to-rce-electron-desktop-apps.md

fuzzdb/attack/xss/

sammiie/bug-bounty-reference

# My Notes

**Example 1**

No filter pop was generated easily 

Payload = `<script>alert(1)</script>`

**Example 2**

The word script was filtered. We can exploit by playing with the cases

Payload = `<ScRipt>alert(1)</ScRipt>`


**Example 3**

The word script was filter for all cases (lower and upper)

Payload = `<scri<script>pt>alert(1)</scr</script>ipt>`

http://10.0.2.10/xss/example3.php?name=%3Cscr%3Cscript%3Eipt%3Ealert(1)%3C/scr%3C/script%3Eipt%3E

**Example 4**

 developer decided to completely block the word script: if the request matches script, the execution stops
 
 Fortunately (or unfortunately depending on what side you are on), there are a lot of ways to get JavaScript to be run (non-exhaustive list):

with the `<a` tag and for the following events: `onmouseover` (you will need to pass your mouse over the link), `onmouseout`, `onmousemove`, `onclick` ...

with the `<a` tag directly in the URL: `<a href='javascript:alert(1)'...` (you will need to click the link to trigger the JavaScript code and remember that this won't work since you cannot use script in this example).

with the `<img` tag directly with the event onerror: `<img src='zzzz' onerror='alert(1)' />`.

with the `<div` tag and for the following events: onmouseover (you will need to pass your mouse over the link), onmouseout, onmousemove, onclick...

**Payload** = `<img src=x onerror=alert(1) />`

              <a href="http://none" onmouseover=alert(1) >pop</a>
              
              <a href="https://example.com/lol%22onmouseover=%22prompt(1);%20img.png">Click</a>  (url encoding)
              
              <svg/onload=alert``>
              
              
 **Example 5**
 
In this example, the `<script>` tag is accepted and gets echoed back. But as soon as you try to inject a call to alert, the PHP script stops its execution. The problem seems to come from a filter on the word **alert**.

Using JavaScript's **eval** and **String.fromCharCode()**, you should be able to get an alert box without using the word alert directly. **String.fromCharCode()** will decode an integer (decimal value) to the corresponding character.

You can write a small tool to transform your payload to this format using your favorite scripting language.
Using this trick and the ascii table, you can easily generate the string: alert(1) and call eval on it.

Another easier bypass is to use the functions `prompt` or `confirm` in Javascript. They are less-known, but will give you the same result

Payload = 

          <svg/onload=prompt``>
          
          //For some reason you can use unicode to encode "alert" but not "(1)"
          
          <img src onerror=\u0061\u006C\u0065\u0072\u0074(1) />
          
          <img src onerror=\u{61}\u{6C}\u{65}\u{72}\u{74}(1) />
          
          <iframe src=javascript:'\x3c\x73\x76\x67\x20\x6f\x6e\x6c\x6f\x61\x64\x3d\x61\x6c\x65\x72\x74\x28\x31\x29\x3e' />
          
          //check hacktricks/pentesting-web/xss-cross-site-scripting/
          
**Example 6**

Here, the source code of the HTML page is a bit different. If you read it, you will see that the value you are sending is echoed back inside JavaScript code.

![image](https://user-images.githubusercontent.com/16500435/103246788-ffacbd00-4964-11eb-9f88-4b36c0fbdb9e.png)

The input name variable is between `<script>` as seen in the code review below, so we can just close the double quote and use `\\` to comment the reset of code.

![image](https://user-images.githubusercontent.com/16500435/103265234-6655c900-49ad-11eb-9100-98e02fc97605.png)


Payload= `";alert("xss");//`

**Example 7**

This example is similar to the one before. This time, you won't be able to use special characters, since they will be HTML-encoded. As you will see, you don't really need any of these characters.

This issue is common in PHP web applications, because the well-known function used to HTML-encode characters **(htmlentities)** does not encode single quotes `(')`, unless you told it to do so, using the **ENT_QUOTES** flag.

Code review

![image](https://user-images.githubusercontent.com/16500435/103266140-41faec00-49af-11eb-91ca-4db2840f31a3.png)

The developer uses `htmlentities()` to encode special characters. However, it does not encode single quotes `'`, so that I can use single quote to close it and comment the rest of the code

Payload = `';alert(1);//`

**Example 8**

Here, the value echoed back in the page is correctly encoded. However, there is still a XSS vulnerability in this page. To build the form, the developer used and trusted PHP_SELF which is the path provided by the user. It's possible to manipulate the path of the application in order to:

call the current page (however you will get an HTTP 404 page);
get a XSS payload in the page.
This can be done because the current configuration of the server will call /xss/example8.php when any URL matching /xss/example8.php/... is accessed. You can simply get your payload inside the page by accessing /xss/example8.php/[XSS_PAYLOAD]. Now that you know where to inject your payload, you will need to adapt it to get it to work and get the famous alert box.

Trusting the path provided by users is a common mistake, and it can often be used to trigger XSS, as well as other issues. This is pretty common in pages with forms, and in error pages (404 and 500 pages).

Code review

![image](https://user-images.githubusercontent.com/16500435/103271170-c8b4c680-49b9-11eb-979e-21cead5ddee1.png)

![image](https://user-images.githubusercontent.com/16500435/103271444-90fa4e80-49ba-11eb-8667-c91132357625.png)


The developer does not valid the parpmeter PHP_SELF so that I can bypass it.

Payload = `" onmouseover="alert('xss')"` i.e `http://10.0.2.10/xss/example8.php/%22%20onmouseover=%22alert('xss')`

Code after payload

![image](https://user-images.githubusercontent.com/16500435/103271294-1f220500-49ba-11eb-83ad-051d46380a73.png)

**Example 9**

This example is a DOM-based XSS. This page could actually be completely static and still be vulnerable.

In this example, you will need to read the code of the page to understand what is happening. When the page is rendered, the JavaScript code uses the current URL to retrieve the anchor portion of the URL (#...) and dynamically (on the client side) write it inside the page. This can be used to trigger a XSS vulnerability, if you use the payload as part of the URL.

Code Review

![image](https://user-images.githubusercontent.com/16500435/103274660-7a57f580-49c2-11eb-928c-a8c2a61be9fe.png)

The user input is after `#`. This is a DOM-based XSS vuln

Payload = `<script>alert(1)</script>`

**Didn't work in Firefox but worked in Edge and IE**



        
