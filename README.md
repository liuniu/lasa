# lasa
Lua Asynchronous Socket Api


# Table of Contents

* [lasa](#lasa)
* [lasa.thread](#lasa.thread)
* [lasa.sleep](#lasa.sleep)
* [lasa.timer](#lasa.timer)
* [lasa.socket.tcp](#lasa.socket.tcp)
* [lasa.socket.udp](#lasa.socket.udp)
* [lasa.http](#lasa.http)
* [lasa.websocket](#lasa.websocket)
* [lasa.semaphore](#lasa.semaphore)   
* [lasa.detach](#lasa.detach)
* [lasa.now](#lasa.now)
* [lasa.uuid](#lasa.uuid) 
* [lasa.sha1](#lasa.sha1)
* [lasa.md5](#lasa.md5)
* [lasa.md5_bin](#lasa.md5_bin)
* [lasa.hmac_sha1](#lasa.hmac_sha1)
* [lasa.monotonictime](#lasa.monotonictime)
* [lasa.encode_base64](#lasa.encode_base64)
* [lasa.decode_base64](#lasa.decode_base64)
* [lasa.escape_uri](#lasa.escape_uri)
* [lasa.unescape_uri](#lasa.unescape_uri)
* [lasa.log](#lasa.log)
* [lasa_exe](#lasa_exe)

## <a name="lasa"></a>lasa

### <a name="lasa_exe"></a>lasa_exe

**syntax:** *int lasa_exe(int argc, char **argv, char *luacode, struct loader_libs *g_loader_libs)*

> C function, execuate lua code or file, if use static library (for ios), set g_loader_libs which includes luaopen_name functions.

> ```
struct loader_libs g_loader_libs[] = {
    /* {"module",   luaopen_module}, */
    {NULL,       NULL},
};
```


### <a name="lasa.detach"></a>lasa.detach

**syntax:** *lasa.detach(conf)*

> Detach a thread for running a lua code or lua file. 

> `conf` is a Lua table which can be specified as the argument to this method.

> * `name`

>     Thread name.

> * `content_by_lua`

>     Lua code string.
> * `content_by_file`

>     Lua file.    


### <a name="lasa.now"></a>lasa.now

**syntax:** *secs = lasa.now()*

> Returns a floating-point number for the elapsed time in seconds (including milliseconds as the decimal part) from the epoch for the current time stamp from the system.

### <a name="lasa.monotonictime"></a>lasa.monotonictime

**syntax:** *micro= lasa.monotonictime()*

> Returns a floating-point number for monotonicctime(microseconds).

    
### <a name="lasa.encode_base64"></a>lasa.encode_base64

**syntax:** *newstr = lasa.encode_base64(str, no_padding?)*

> Encodes <code>str</code> to a base64 digest.

            	
### <a name="lasa.decode_base64"></a>lasa.decode_base64

**syntax:** *newstr = lasa.decode_base64(str)*

> Decodes the <code>str</code> argument as a base64 digest to the raw form. Returns <code>nil</code> if <code>str</code> is not well formed.


### <a name="lasa.hmac_sha1"></a>lasa.hmac_sha1

**syntax:** *digest = lasa.hmac_sha1(secret_key, str)*

> Computes the [http://en.wikipedia.org/wiki/HMAC HMAC-SHA1] digest of the argument <code>str</code> and turns the result using the secret key <code><secret_key></code>.
The raw binary form of the <code>HMAC-SHA1</code> digest will be generated, use `lasa.encode_base64`, for example, to encode the result to a textual representation if desired.

 
### <a name="lasa.sha1"></a>lasa.sha1

**syntax:** *newstr = lasa.sha1(str)*

> Returns the string form of the SHA-1 digest of the <code>str</code> argument.
	
### <a name="lasa.md5"></a>lasa.md5

**syntax:** *digest = lasa.md5(str)*

> Returns the hexadecimal representation of the MD5 digest of the <code>str</code> argument.
	
### <a name="lasa.md5_bin"></a>lasa.md5_bin

**syntax:** *digest = lasa.md5_bin(str)*

> Returns the binary form of the MD5 digest of the <code>str</code> argument.
	
### <a name="lasa.uuid"></a>lasa.uuid

**syntax:** *id = lasa.uuid()*

> Returns a uuid string. 

### <a name="lasa.escape_uri"></a>lasa.escape_uri

**syntax:** *newstr = lasa.escape_uri(str)*

>  Escape str as a URI component

### <a name="lasa.unescape_uri"></a>lasa.unescape_uri

**syntax:** *newstr = lasa.unescape_uri(str)*

> Unescape str as an escaped URI component.

### <a name="lasa.log"></a>lasa.log

**syntax:** *lasa.log(level, "tag", "msg")*

> Use `__android_log_print` to print log with the setting of `level`.

> The `level` values are as follows. 

```

typedef enum android_LogPriority {
    ANDROID_LOG_UNKNOWN = 0,
    ANDROID_LOG_DEFAULT,    /* only for SetMinPriority() */
    ANDROID_LOG_VERBOSE,
    ANDROID_LOG_DEBUG,
    ANDROID_LOG_INFO,
    ANDROID_LOG_WARN,
    ANDROID_LOG_ERROR,
    ANDROID_LOG_FATAL,
    ANDROID_LOG_SILENT,     /* only for SetMinPriority(); must be last */
} android_LogPriority;

```

## <a name="lasa.thread"></a>lasa.thread

### lasa.thread.spawn

**syntax:** *co = lasa.thread.spawn(func, arg1, arg2,...)*

> Spawns a new user `light thread` with the Lua function `func` as well as those optional arguments `arg1`, `arg2`, and etc. Returns a Lua thread (or Lua coroutine) object represents this "light thread".
Before `lasa.thread.spawn` returns, the `func` will be called with those optional arguments until it returns, aborts with an error, or gets yielded due to I/O operations.
After `lasa.thread.spawn` returns, the newly-created "light thread" will keep running asynchronously usually at various I/O events.
When the user "light thread" terminates with a Lua error, however, it will not abort other running "light threads" like the `main thread` does.

> A `light thread` will keep running exclusively on the CPU until

> * a (nonblocking) I/O operation cannot be completed in a single run. 
* User "light threads" can create "light threads" themselves.
* The "parent coroutine" can call `lasa.thread.wait` to wait on the termination of its child "light thread".
* You can call coroutine.status() and coroutine.yield() on the "light thread" coroutines.

> The status of the "light thread" coroutine can be "zombie" if

> * the current "light thread" already terminates (either successfully or with an error), 
* its parent coroutine is still alive, and
* its parent coroutine is not waiting on it with `lasa.thread.wait`.


    
### lasa.thread.wait

**syntax:** *ok = lasa.thread.wait(thread1, thread2, thread3, ...)*

> Waits on one or more child "light threads" and returns the results of the first `light thread` that terminates (either successfully or with an error).
The arguments `thread1`, `thread2`, and etc are the Lua thread objects returned by earlier calls of `lasa.thread.spawn`.
The return values have exactly the same meaning as [[#coroutine.resume|coroutine.resume]], that is, the first value returned is a boolean value indicating whether the "light thread" terminates successfully or not, and subsequent values returned are the return values of the user Lua function that was used to spawn the "light thread" (in case of success) or the error object (in case of failure).
Only the direct `parent coroutine` can wait on its child `light thread`, otherwise a Lua exception will be raised.


### lasa.thread.kill

**syntax:** *ok，err = lasa.thread.kill(thread)*

> Kills a running "light thread" created by `lasa.thread.spawn`. Returns a true value when successful or `nil` and a string describing the error otherwise.
According to the current implementation, only the parent coroutine (or `light thread`) can kill a thread. 
    
    
*The following example demonstrates the use of `lasa.thread`*


*And it will generate the following output:*

        		
## <a name="lasa.sleep"></a>lasa.sleep

**syntax:** *ret = lasa.sleep(timeout)*

> Sleeps for the specified seconds without blocking. One can specify time resolution up to 0.001 seconds (i.e., one milliseconds).


## <a name="lasa.timer"></a>lasa.timer

### lasa.timer.at

**syntax:** *tmr, err = lasa.timer.at(delay, callback, user_arg1, user_arg2, ...)*


> Creates an lasa timer with a user callback function as well as optional user arguments.
The first argument, <code>delay</code>, specifies the delay for the timer,
in seconds. One can specify fractional seconds like <code>0.001</code> to mean 1
millisecond here. <code>0</code> delay can also be specified, in which case the
timer will immediately expire when the current handler yields
execution.
The second argument, <code>callback</code>, can
be any Lua function, which will be invoked later in a background
"light thread" after the delay specified. The user callback will be
called automatically by the lasa core with the arguments <code>premature</code>,
<code>user_arg1</code>, <code>user_arg2</code>, and etc, where the <code>premature</code>
argument takes a boolean value indicating whether it is a premature timer
expiration or not, and <code>user_arg1</code>, <code>user_arg2</code>, and etc, are
those (extra) user arguments specified when calling <code>lasa.timer.at</code>
as the remaining arguments.
 
> When a timer expires, the user Lua code in the timer callback is
running in a "light thread" detached completely from the original
request creating the timer. The timer also can be kill by `lasa.timer.kill`.


### lasa.timer.kill

**syntax:** *lasa.timer.kill(tmr)*

> Kill a timer.


## <a name="lasa.semaphore"></a>lasa.semaphore

### lasa.semaphore.new

**syntax:** *sema, err = lasa.semaphore.new(n?)*

> Creates and returns a new semaphore instance that has `n` (default to `0`) resources.

### sema:wait

**syntax:** *ok, err = sema:wait(timeout)*

> Requests a resource from the semaphore instance.

> Returns `true` immediately when there is resources available for the current running "light thread".
Otherwise the current "light thread" will enter the waiting queue and yield execution.
The current "light thread" will be automatically waken up and the `wait` function call
will return `true` when there is resources available for it, or return `nil` and a string describing
the error in case of failure (like `"timeout"`).

> The `timeout` argument specifies the maximum time this function call should wait for (in seconds).

> When the `timeout` argument is 0, it means "no wait", that is, when there is no readily available
"resources" for the current running "light thread", this `wait` function call returns immediately
`nil` and the error string `"timeout"`.

> You can specify millisecond precision in the timeout value by using floating point numbers like 0.001 (which means 1ms).

> "Light threads" created by different contexts (like request handlers) can wait on the same semaphore instance without problem.


### sema:post

**syntax:** *sema:post(n?)*

> Releases `n` (default to `1`) "resources" to the semaphore instance.

> This will not yield the current running "light thread".

> At most `n` "light threads" will be waken up when the current running "light thread" later yields (or terminates).

### sema:count

**syntax:** *count = sema:count()*

> Returns the number of resources readily available in the `sema` semaphore instance (if any).

> When the returned number is negative, it means the number of "light threads" waiting on this semaphore.

## lasa.socket

### <a name="lasa.socket.tcp"></a>lasa.socket.tcp

**syntax:** *tcpsock = lasa.socket.tcp()*

> Creates and returns a TCP or stream-oriented unix domain socket object (also known as one type of the "cosocket" objects). 
    
### tcpsock:bind

**syntax:** *ok，err = tcp_socket:bind(ip, port)*

    
### tcpsock:accept

**syntax:** *tcpsockclient, err = tcpsock:accept()*

    
### tcpsock:receive

**syntax:** *data, err, partial = tcpsock:receive(size)*

**syntax:** *data, err, partial = tcpsock:receive(pattern?)*

> Receives data from the connected socket according to the reading pattern or size.

> This method is a synchronous operation just like the send method and is 100% nonblocking.

> In case of success, it returns the data received; in case of error, it returns nil with a string describing the error and the partial data received so far.

> If a number-like argument is specified (including strings that look like numbers), then it is interpreted as a size. This method will not return until it reads exactly this size of data or an error occurs.

> If a non-number-like string argument is specified, then it is interpreted as a "pattern". The following patterns are supported:

> * `*a`: reads from the socket until the connection is closed. No end-of-line translation is performed;
 
> * `*l`: reads a line of text from the socket. The line is terminated by a Line Feed (LF) character (ASCII 10), optionally preceded by a Carriage Return (CR) character (ASCII 13). The CR and LF characters are not included in the returned line. In fact, all CR characters are ignored by the pattern.
If no argument is specified, then it is assumed to be the pattern `*l`, that is, the line reading pattern.

> Timeout for the reading operation is controlled by sock:settimeout, and default timout is 30 seconde. For example:

> ```
 sock:settimeout(1000)  -- one second timeout
 local line, err, partial = sock:receive()
 if not line then
     print("failed to read a line: ", err)
     return
 end
 print("successfully read a line: ", line)
 ```
> It is important here to call the settimeout method before calling this method.

### tcpsock:receiveuntil

**syntax:** *iterator = tcpsock:receiveuntil(pattern, options?)*

> This method returns an iterator Lua function that can be called to read the data stream until it sees the specified pattern or an error occurs.

> Here is an example for using this method to read a data stream with the boundary sequence --abcedhb:

> ```
 local reader = sock:receiveuntil("\r\n--abcedhb")
 local data, err, partial = reader()
 if not data then
     print("failed to read the data stream: ", err)
 end
 print("read the data stream: ", data)
 ```
> When called without any argument, the iterator function returns the received data right before the specified pattern string in the incoming data stream. So for the example above, if the incoming data stream is 'hello, world! -agentzh\r\n--abcedhb blah blah', then the string 'hello, world! -agentzh' will be returned.

> In case of error, the iterator function will return nil along with a string describing the error and the partial data bytes that have been read so far.

> The iterator function can be called multiple times and can be mixed safely with other cosocket method calls or other iterator function calls.

> The iterator function behaves differently (i.e., like a real iterator) when it is called with a size argument. That is, it will read that size of data on each invocation and will return nil at the last invocation (either sees the boundary pattern or meets an error). For the last successful invocation of the iterator function, the err return value will be nil too. The iterator function will be reset after the last successful invocation that returns nil data and nil error. Consider the following example:

> ```
 local reader = sock:receiveuntil("\r\n--abcedhb")
  while true do
     local data, err, partial = reader(4)
     if not data then
         if err then
             print("failed to read the data stream: ", err)
             break
         end
         print("read done")
         break
     end
     print("read chunk: [", data, "]")
 end
```

> Then for the incoming data stream 'hello, world! -agentzh\r\n--abcedhb blah blah', we shall get the following output from the sample code above:

> ```
read chunk: [hell]
read chunk: [o, w]
read chunk: [orld]
read chunk: [! -a]
read chunk: [gent]
read chunk: [zh]
read done
```
> Note that, the actual data returned might be a little longer than the size limit specified by the size argument when the boundary pattern has ambiguity for streaming parsing. Near the boundary of the data stream, the data string actually returned could also be shorter than the size limit.

> Timeout for the iterator function's reading operation is controlled by the lua_socket_read_timeout config directive and the settimeout method. And the latter takes priority. For example:

> ```
 local readline = sock:receiveuntil("\r\n")
 sock:settimeout(1000)  -- one second timeout
 line, err, partial = readline()
 if not line then
     print("failed to read a line: ", err)
     return
 end
 print("successfully read a line: ", line)
 ```
 
> It is important here to call the settimeout method before calling the iterator function (note that the receiveuntil call is irrelevant here).

> This method also takes an optional options table argument to control the behavior. The following options are supported:

> * `inclusive`

>    The inclusive takes a boolean value to control whether to include the pattern string in the returned data string. Default to false. For example,

> ```
 local reader = tcpsock:receiveuntil("_END_", { inclusive = true })
 local data = reader()
 print(data)
 ```
 
> Then for the input data stream "hello world _END_ blah blah blah", then the example above will output hello world _END_, including the pattern string _END_ itself.
    
### tcpsock:send

**syntax:** *bytes, err = tcpsock:send(data)*

> Sends data without blocking on the current TCP or Unix Domain Socket connection.

> This method is a synchronous operation that will not return until all the data has been flushed into the system socket send buffer or an error occurs.

> In case of success, it returns the total number of bytes that have been sent. Otherwise, it returns nil and a string describing the error.
> It is important here to call the settimeout method before calling this method.

> In case of any connection errors, this method always automatically closes the current connection.
    
### tcpsock:connect

**syntax:** *ok, err = tcpsock:connect(host, port, options_table?)*

> Attempts to connect a TCP socket object to a remote server or to a stream unix domain socket file without blocking.

> It is important here to call the settimeout method before calling this method.

### lasa.socket.connect

> This function is a shortcut for combining lasa.socket.tcp() and the connect() method call in a single operation. It is actually implemented like this:

> ```
 local sock = ngx.socket.tcp()
 local ok, err = sock:connect(...)
 if not ok then
     return nil, err
 end
 return sock
```

### tcpsock:sslhandshake (TODO)

**syntax:** *session, err = tcpsock:sslhandshake(reused_session?, server_name?, ssl_verify?, send_status_req?)*
   
### tcpsock:close

**syntax:** *ok, err = tcpsock:close()*

> Closes the current TCP or stream unix domain socket. It returns the 1 in case of success and returns nil with a string describing the error otherwise.

### tcpsock:peek

**syntax:** *ok = tcpsock:peek()*   

> Check there are data in the receive buffer. 

    
### tcpsock:settimeout

**syntax:** *tcpsock:settimeout(time)*

> Set the timeout value in milliseconds for subsequent socket operations (connect, receive, and iterators returned from receiveuntil). 
>
> If the parameter time is zero or negative, the timeout value will use the default timeout value.

### <a name="lasa.socket.udp"></a>lasa.socket.udp

**syntax:** *udpsock = lasa.socket.udp()*

> Creates and returns a UDP or datagram-oriented unix domain socket object (also known as one type of the "cosocket" objects). The following methods are supported on this object:
    
### udpsock:setpeername

**syntax:** *ok, err = udpsock:setpeername(host, port)*

**syntax:** *ok, err = udpsock:setpeername("unix:/path/to/unix-domain.socket")* (TODO)

> Attempts to connect a UDP socket object to a remote server or to a datagram unix domain socket file. Because the datagram protocol is actually connection-less, this method does not really establish a "connection", but only just set the name of the remote peer for subsequent read/write operations.
> 
> Both IP addresses and domain names can be specified as the <code>host</code> argument. 


### udpsock:setsockname

    
### udpsock:send

**syntax:** *ok, err = udpsock:send(data)*

> Sends data on the current UDP or datagram unix domain socket object.
In case of success, it returns <code>1</code>. Otherwise, it returns <code>nil</code> and a string describing the error.

> The input argument <code>data</code> is a Lua string.



### udpsock:receive

**syntax:** *data, err = udpsock:receive(size?)*

> Receives data from the UDP or datagram unix domain socket object with an optional receive buffer size argument, <code>size</code>.

> This method is a synchronous operation and is 100% nonblocking.

> In case of success, it returns the data received; in case of error, it returns <code>nil</code> with a string describing the error.

> If the <code>size</code> argument is specified, then this method will use this size as the receive buffer size. But when this size is greater than <code>8192</code>, then <code>8192</code> will be used instead.

### udpsock:close

**syntax:** *ok, err = udpsock:close()*

> Closes the current UDP or datagram unix domain socket. It returns the <code>1</code> in case of success and returns <code>nil</code> with a string describing the error otherwise.

> Socket objects that have not invoked this method (and associated connections) will be closed when the socket object is released by the Lua GC (Garbage Collector) or the current client HTTP request finishes processing.

    
### udpsock:settimeout

**syntax:** *udpsock:settimeout(time)*

> Set the timeout value in milliseconds.

    
## <a name="lasa.websocket"></a>lasa.websocket

### lasa.websocket.client
### client:new

**syntax:** *wb, err = client:new()*

**syntax:** *wb, err = client:new(opts)*

> Instantiates a WebSocket client object.

> In case of error, it returns nil and a string describing the error.

> An optional options table can be specified. The following options are as follows:
> 
> * `timeout`

>    Specifies the network timeout threshold in milliseconds. You can change this setting later via the set_timeout method call. Note that this timeout setting does not affect the HTTP response header sending process for the websocket handshake; you need to configure the send_timeout directive at the same time. Default is 30 seconds.

### lasa.websocket.server.new

**syntax:** *wb, err = lasa.websocket.server.new("host:port")*

**syntax:** *wb, err = lasa.websocket.server.new(param)*

> `param` is a Lua table as follows:
> 
> * `host`
>    
>    host is a string, including the server's host and port, e.g: "127.0.0.1:8000"
> * `ssl`
>    
>    ssl is a Lua table which represents the server's ssl information including ssl certificate and ssl private key.
>
>    e.g: { ssl_cert = s_ssl_cert_path, ssl_key = s_ssl_key_path }

### wb.accept

**syntax:** *wbc, err = wb:accept()*
    
### wb:connect

**syntax:** *ok, err = wb:connect("ws://host:port/path")*

**syntax:** *ok, err = wb:connect("wss://host:port/path")* TODO

**syntax:** *ok, err = wb:connect("ws://host:port/path", options)*

**syntax:** *ok, err = wb:connect("wss://host:port/path", options)* TODO

> Connects to the remote WebSocket service port and performs the websocket handshake process on the client side.

> Before actually resolving the host name and connecting to the remote backend, this method will always look up the connection pool for matched idle connections created by previous calls of this method.

> An optional Lua table can be specified as the last argument to this method to specify various connect options:

> * `protocols`

>    Specifies all the subprotocols used for the current WebSocket session. It could be a Lua table holding all the subprotocol names or just a single Lua string.
> * `origin`

>    Specifies the value of the `Origin` request header.
> * `ssl_verify` TODO

>    Specifies whether to perform SSL certificate verfication during the
SSL handshake if the `wss://` scheme is used.
 
### wb:send_text

**syntax:** *bytes, err = wb:send_text(text)*

> Sends the `text` argument out as an unfragmented data frame of the `text` type. Returns the number of bytes that have actually been sent on the TCP level.

> In case of errors, returns `nil` and a string describing the error.

    
### wb:send_binary

**syntax:** *bytes, err = wb:send_binary(data)*

> Sends the `data` argument out as an unfragmented data frame of the `binary` type. Returns the number of bytes that have actually been sent on the TCP level.

> In case of errors, returns `nil` and a string describing the error.


### wb:send_ping

**syntax:** *bytes, err = wb:send_ping()*

**syntax:** *bytes, err = wb:send_ping(msg)* TODO

> Sends out a `ping` frame with an optional message specified by the `msg` argument. Returns the number of bytes that have actually been sent on the TCP level.

> In case of errors, returns `nil` and a string describing the error.

> Note that this method does not wait for a pong frame from the remote end.


### wb:send_pong

**syntax:** *bytes, err = wb:send_pong()*

**syntax:** *bytes, err = wb:send_pong(msg)* TODO

> Sends out a `pong` frame with an optional message specified by the `msg` argument. Returns the number of bytes that have actually been sent on the TCP level.

> In case of errors, returns `nil` and a string describing the error.


### wb:send_close

**syntax:** *bytes, err = wb:send_close()*

**syntax:** *bytes, err = wb:send_close(code, msg)* TODO

> Sends out a `close` frame with an optional status code and a message.

> In case of errors, returns `nil` and a string describing the error.

### wb:send_frame

**syntax:** *bytes, err = wb:send_frame(fin, opcode, payload)*

> Sends out a raw websocket frame by specifying the `fin` field (boolean value), the opcode, and the payload.

> For a list of valid opcode, see 

> http://tools.ietf.org/html/rfc6455#section-5.2

> In case of errors, returns `nil` and a string describing the error.

> To control the maximal payload length allowed, you can pass the `max_payload_len` option to the `new` constructor.

> To control whether to send masked frames, you can pass `true` to the `send_masked` option in the `new` constructor method. By default, unmasked frames are sent.


### wb:recv_frame

**syntax:** *data, typ, err = wb:recv_frame()*

> Receives a WebSocket frame from the wire.

> In case of an error, returns two `nil` values and a string describing the error.

> The second return value is always the frame type, which could be one of `continuation`, `text`, `binary`, `close`, `ping`, `pong`, or `nil` (for unknown types).

> For `close` frames, returns 3 values: the extra status message (which could be an empty string), the string "close", and a Lua number for the status code (if any). For possible closing status codes, see

> http://tools.ietf.org/html/rfc6455#section-7.4.1

> For other types of frames, just returns the payload and the type.

> For fragmented frames, the `err` return value is the Lua string "again".

    
### wb:close

**syntax:** *ok, err = wb:close()*

> Closes the current WebSocket connection. If no `close` frame is sent yet, then the `close` frame will be automatically sent.

    
### wb:set_timeout

**syntax:** *wb:set_timeout(ms)*

> Sets the timeout delay (in milliseconds) for the network-related operations.

    
## <a name="lasa.http"></a>lasa.http

### lasa.http.client.new

**syntax:** *hc, err = lasa.http.client.new()*
> new a http client.
    
### hc:request

**syntax:** *ok, resp = hc:request(req)*
> Send http request.
> 
> `req` is a Lua table as follows:
> 
> * `url`
>    
>     e.g: "https://github.com", "http://google.com"
> * `body`
>    
>    http payload, if it's nil the method is 'GET', others is 'POST'.

> * `timeout`

>    request timeout in seconds.

> * `headers`

>     http headers is a Lua table. You can define http header by yourself. e.g. headers = {['Content-Type']='application/x-www-form-urlencoded'}

```
local httpc = lasa.http.client
local hc = httpc:new()
local ok, resp, chunked = hc:request({
        url     = "http://github.com",
        headers = {['Content-Type']='application/x-www-form-urlencoded'},
        body    = "hello world."
        timeout = 30,
    })
```

> `resp` is a Lua table as follows:

> * `body`

>     The response body.

> * `code`

>     The http status code.

> * `headers`

>     The table of http headers.

> * `status`

>     the http status message.

> `chunked` is a ccloser function

```
local httpc = lasa.http.client
local hc = httpc:new()
local ok, resp, chunked = hc:request({
        url = 'http://www.163.com'
        })
    if chunked then
        while true do 
            local resp = chunked()
            if not resp or not resp.body then break end
            print(#resp.body)
        end
    end
```
       
### hc:close

**syntax:** *ok, err = hc:close()*
> close http client.




