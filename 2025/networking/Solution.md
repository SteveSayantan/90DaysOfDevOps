# Week 1: Networking Challenge Solution


### Task 1: Write examples of how each layer applies to real-world scenarios
Checkout this article written by me on [hashnode](https://devops-bites.hashnode.dev/exploring-the-tcpip-model-essential-guide-to-network-basics) .

---
### Task 2:  Create a blog, article, GitHub page, or README listing these protocols and explaining their relevance to DevOps workflows.
Checkout this article written by me on [hashnode](https://devops-bites.hashnode.dev/exploring-the-tcpip-model-essential-guide-to-network-basics) .

---
### Task 3: Write a step-by-step guide or blog on how to create and configure Security Groups.
Would be covered in the upcoming weeks.

---
### Task 4: Create a cheat sheet or short guide explaining the purpose and usage of Networking Commands

- **1️⃣ `ping` - Check Network Connectivity**

  The `ping` command sends **ICMP Echo Requests** to a target to check if it’s reachable.

  #### **Example Usage:**
  ```sh
  ping google.com
  ```
  #### **Scenario: Checking Internet Connectivity**  
  We’re having trouble loading websites. To check if our **internet connection is working**, run:
  ```sh
  ping 8.8.8.8
  ```
  - If replies come back → The internet is fine.  
  - If no replies (timeout) → We have a network issue.

- **2️⃣ `traceroute` / `tracert` - Trace Network Route**  
  - **Linux/macOS:** `traceroute` 
  - **Windows:** `tracert`  
  
  This command **traces the path** packets take from our system to a destination, showing the routers (hops) along the way.
  
  #### **Example Usage:**
  ```sh
  traceroute google.com    # Linux/macOS
  tracert google.com       # Windows
  ```
  #### **Scenario: Debugging Slow Internet Issues**  
  Our website is **loading slowly**, but `ping` works fine. Run `traceroute` to check **where the delay occurs**:
  ```sh
  traceroute example.com
  ```
  If we see **timeouts or high latency** at a particular hop, that router may be **causing delays**.

- **3️⃣ `netstat` - View Network Connections**

  The `netstat` command shows **active network connections, ports, and listening services**.

  #### **Example Usage:**
  ```sh
  netstat -tulnp    # Linux (shows listening ports)
  netstat -ano      # Windows (shows active connections)
  ```
  #### **Scenario: Checking Which Ports Are in Use**  
  You suspect that **your web server isn't running** on port 80. Run:
  ```sh
  netstat -tulnp | grep :80   # Linux
  netstat -ano | findstr :80  # Windows
  ```
  - If the port **isn't listed**, the server is **not running**.  
  - If the port **is occupied by another process**, you may need to stop that process.
  
  #### Flags Explained:
  - `-t`: shows only active TCP connections (excluding UDP).
  
  - `-u`: shows only active UDP connections
  
  - `-l`: shows listening ports (i.e. services actively listening for connections).
  
  - `-n`: Displays IP addresses and port numbers instead of resolving them into hostnames and service names. E.g. `localhost:http` is displayed as `127.0.0.1:80`
  
  - `-p`: Displays the process (PID) and program name using each port.


- **4️⃣ `curl` - Fetch Data from URLs**

  The `curl` command is one of the most essential tools for a DevOps engineer, as it helps interact with APIs, test services, debug network issues, and automate deployments.
  
  #### **Example Usage:**
  
  - `curl example.com` : performs GET request to http://example.com and logs the response body, since we haven't specified the protocol
  
  - `curl -i https://example.com` : performs GET request to https://example.com and logs the response body with response headers due to `-i` .
  
  - `curl -I https://example.com`  : performs HEAD request to https://example.com and logs only the response headers due to `-I`.
  
  - `curl https://jsonplaceholder.typicode.com/users |jq` : logs the response body (JSON data) of the GET request using jq
  
  - `curl -L http://apple.com`  : `-L` makes curl redo the request to the new place in case of a redirect; Otherwise, it would return the redirect response body and not the actual one.
  
  - `curl -o page.html https://example.com` : `-o` is used to save the response body (here, HTML) in a file page.html, instead of displaying in terminal
  
  - `curl -v https://example.com -o /dev/null` : The response body (here, HTML) is sent to `/dev/null`, instead of displaying in terminal. Only the verbal message is displayed due to `-v`.
  
  - `curl https://example.com -H "Magic: disco"` : `-H` adds *Magic: disco* header to the regular request headers.
    - We remove an internal header by giving a replacement without content on the right side of the colon, as in: `-H "Host:"`. This removes **Host** header from the regular request headers.
  
    - If we want to send a header with no-value then its header must be terminated with a semicolon, such as `-H "User-agent;"` to send `"User-agent:"`.
  
  - `curl https://example.com -d "name=curl"`: `-d` sends the specified data in a POST request to the HTTP server using the content-type **application/x-www-form-urlencoded**. This flag can be used multiple times. Using `'-d name=daniel -d skill=lousy'` would generate a post chunk that looks like `'name=daniel&skill=lousy'`.

  - `curl https://example.com -d @file_name`: `-d` sends the content of the file *file_name* as the body of a POST request using the content-type **application/x-www-form-urlencoded**. For example,
  
     - `curl -d @data.json -H 'content-type:application/json' https://jsonplaceholder.typicode.com/posts`: Makes a POST request to the server with the content of *data.json* . If **content-type** header is not explicitly set, it would be **application/x-www-form-urlencoded** . Since, we are sending JSON, we don't want that here.
  
          ```js
          //data.json
          {"name":"steve","role":"dev","isMarried":false}
          ```
  - `curl -T data.json -H 'content-type:application/json' https://hello.requestcatcher.com/test` : `-T` sends a PUT request to the server with the content of *data.json* . Here, we also set the **content-type** header using `-H` flag.
      ```js
      //data.json
      {"name":"steve","role":"dev","isMarried":false}
      ```
  
  - `curl -u username:password https://api.example.com/secure-data` : The `-u` flag passes the username and password to use for server authentication (e.g. basic authentication).

  - `curl -c cookies.txt -d "username=admin&password=secret" https://example.com/login` : `-c` saves the session cookies to *cookies.txt*.
  
     - `curl -b cookies.txt https://example.com/dashboard` : `-b` sends the saved cookies in *cookies.txt* with a request.

- **5️⃣ `dig` / `nslookup` - Query DNS Information**  
  - **Linux/macOS:** Use `dig`  
  - **Windows:** Use `nslookup`  

  These commands query **DNS records** to find IP addresses, domain details, and troubleshoot name resolution.
  
  #### **Example Usage:**
  ```sh
  dig google.com       # Linux/macOS
  nslookup google.com  # Windows
  ```
  #### **Scenario: Checking Domain Name Resolution**  
  Our app fails to connect to `api.example.com`. To check if DNS is resolving it correctly:
  ```sh
  dig api.example.com
  nslookup api.example.com
  ```
  - If no IP is returned, the **DNS server might be down**.  
  - If the IP is incorrect, our **DNS configuration might be wrong**.

### References
  - [curl man page](https://linux.die.net/man/1/curl)
  - [curl tutorial on FreeCodeCamp](https://youtu.be/I6id1Y0YuNk?si=c-HXQzcHF-NU4Jfb)
