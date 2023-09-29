# Intermediate-javascript-week1
Day 1 Frames and windows
Popups and Window Methods
 A popup window is one of the oldest methods to show the additional documents to the user.Most modern browsers are configured to open new tabs instead of separate windows.Popups exist from really ancient times. The initial idea was to show another content without closing the main window. As of now, there are other ways to do that: we can load content dynamically with fetch and show it in a dynamically generated <div>. So, popups are not something we use every day. A popup is a separate window with its own independent JavaScript environment. So opening a popup with a third-party non-trusted site is safe.
 window.open
 The syntax to open a popup is: window.open(url, name, params).

Window features:
menubar (yes/no) – shows or hides the browser menu on the new window.
toolbar (yes/no) – shows or hides the browser navigation bar (back, forward, reload etc) on the new window.
location (yes/no) – shows or hides the URL field in the new window. FF and IE don’t allow to hide it by default.
status (yes/no) – shows or hides the status bar. Again, most browsers force it to show.
resizable (yes/no) – allows to disable the resize for the new window. Not recommended.
scrollbars (yes/no) – allows to disable the scrollbars for the new window. Not recommended.
There is also a number of less supported browser-specific features, which are usually not used. Check window.open in MDN for examples.

Accessing window from popup
 A popup may access the “opener” window as well using window.opener reference. It is null for all windows except popups.
 
 Closing a popup
 To check if a window is closed: win.closed.
Technically, the close() method is available for any window, but window.close() is ignored by most browsers if window is not created with window.open(). So it’ll only work on a popup.
The closed property is true if the window is closed. That’s useful to check if the popup (or the main window) is still open or not. A user can close it anytime, and our code should take that possibility into account.

Scrolling and resizing
 There are methods to move/resize a window:
win.moveBy(x,y)
Move the window relative to current position x pixels to the right and y pixels down. Negative values are allowed (to move left/up).
win.moveTo(x,y)
Move the window to coordinates (x,y) on the screen.
win.resizeBy(width,height)
Resize the window by given width/height relative to the current size. Negative values are allowed.
win.resizeTo(width,height)
Resize the window to the given size.
There’s also window.onresize event.

Focus/Blur on a window
 When we open a popup, it’s might be a good idea to run a newWindow.focus() on it. Just in case, for some OS/browser combinations it ensures that the user is in the new window now.
If we want to track when a visitor actually uses our web-app, we can track window.onfocus/onblur. That allows us to suspend/resume in-page activities, animations etc. But please note that the blur event means that the visitor switched out from the window, but they still may observe it. The window is in the background, but still may be visible.

DAY 2 Cross-Window communication
Same Origin
 A "same-origin URL" refers to a URL (Uniform Resource Locator) that has the same origin as the current web page. In the context of web security and the same-origin policy, a URL's origin consists of three parts:
Protocol
Host
Port

Windows on subdomains: document.domain
 In web development, when you have a webpage hosted on a subdomain (e.g., sub.example.com) and you want to interact with a parent window or frame that is on a different subdomain (e.g., example.com), you typically run into issues related to the same-origin policy. The same-origin policy restricts interactions between different domains for security reasons.Setting document.domain:

The document.domain property can be used to change the domain of a page to a common parent domain for both the parent and child frames or windows. This is typically used when you have control over both the parent and child domains.

For example, if you have a parent window at https://example.com and a child iframe at https://sub.example.com, you can set both the parent and child's.Cross-Origin Communication:

Once you've set document.domain on both the parent and child to the same value, you can now communicate between them using JavaScript, even though they are technically on different subdomains.

This enables you to perform actions like accessing properties and calling functions in the parent window from the child iframe and vice versa, as long as the domains match.

It's important to note that this approach only works when you have control over both the parent and child domains. You cannot use this technique to interact with arbitrary third-party domains. Additionally, be cautious when using document.domain because it can have security implications, and it should only be used when you have a specific need for cross-origin communication between subdomains that you trust.

Iframe: wrong document pitfall
 The "wrong document" pitfall in web development refers to a situation where you mistakenly access or manipulate elements or properties in the Document Object Model (DOM) that belong to a different document or context than the one you intended. This can lead to unexpected behavior, errors, or security vulnerabilities in your web application. Here are some common scenarios and pitfalls related to this issue:

Accessing Elements in iframes:
If you have multiple iframes on a page, it's important to ensure that you're working with the correct iframe's content. Accidentally accessing elements in the wrong iframe can lead to unexpected results.

Cross-Origin Security:
The same-origin policy restricts interactions between documents from different origins (domains, subdomains, protocols). Attempting to access or manipulate elements or data in a different origin's iframe can result in security errors.

Collection: window.frames
 An alternative way to get a window object for <iframe>– is to get it from the named collectionwindow.frames:

By number: window.frames[0] – the window object for the first frame in the document.
By name: window.frames.iframeName – the window object for the frame withname="iframeName".

Navigation links are:
window.frames – the collection of “children” windows (for nested frames).
window.parent – the reference to the “parent” (outer) window.
window.top – the reference to the topmost parent window.

The “sandbox” iframe attribute
 The sandbox attribute allows for the exclusion of certain actions inside an <iframe> in order to prevent it from executing untrusted code. It “sandboxes” the iframe by treating it as coming from another origin and/or applying other limitations.

There’s a “default set” of restrictions applied for <iframe sandbox src="...">. But it can be relaxed if we provide a space-separated list of restrictions that should not be applied as a value of the attribute, like this: <iframe sandbox="allow-forms allow-popups">.

In other words, an empty "sandbox" attribute puts the strictest limitations possible, but we can put a space-delimited list of those that we want to lift.

Here’s a list of limitations:
allow-same-origin
allow-top-navigation
allow-forms
allow-scripts
allow-popups

Cross Window Messaging
 The postMessage interface allows windows to talk to each other no matter which origin they are from.

So, it’s a way around the “Same Origin” policy. It allows a window from john-smith.com to talk to gmail.comand exchange information, but only if they both agree and call corresponding JavaScript functions. That makes it safe for users.

DAY 3-CLICKJACKING ATTACK
Introduction to Clickjacking
 Clickjacking, also known as a "UI redress attack" or "user interface manipulation," is a type of malicious attack where an attacker tricks a user into clicking on something different from what the user perceives. It typically involves overlaying or embedding a legitimate website or web application within a hidden or opaque iframe and then placing deceptive elements, such as buttons or forms, on top of it. When the user interacts with what they see, they are actually interacting with the hidden website, and their actions are captured by the attacker.

 Old school defenses (weak)
 The oldest defense is a bit of JavaScript which forbids opening the page in a frame (so-called “framebusting”).
Blocking top-navigation
We can block the transition caused by changing top.location in beforeunload event handler.
Sandbox attribute
One of the things restricted by the sandbox attribute is navigation. A sandboxed iframe may not change top.location.

X-Frame-Options
 The server-side header X-Frame-Options can permit or forbid displaying the page inside a frame.
The header may have 3 values:

DENY
Never ever show the page inside a frame.

SAMEORIGIN
Allow inside a frame if the parent document comes from the same origin.

ALLOW-FROM domain
Allow inside a frame if the parent document is from the given domain.

Samesite cookie attribute
 The samesite cookie attribute can also prevent clickjacking attacks. cookie with such attribute is only sent to a website if it’s opened directly, not via a frame, or otherwise. More information can be accessed using this link Cookies, document.cookie.


 DAY 4-ARRAYBUFFER AND BINARY ARRAY
 ArrayBuffer
 An ArrayBuffer in JavaScript is a built-in object that represents a generic, fixed-size binary data buffer. It allows you to work with binary data directly, without the need for a higher-level data type like an array or string. ArrayBuffer is particularly useful when dealing with binary data in various contexts, such as networking, file I/O, or working with low-level data protocols like WebGL and WebAssembly.

Here are some key characteristics and concepts related to ArrayBuffer:
Fixed Size
Raw Binary Data
View Objects
Usage Examples

TypedArray
 A TypedArray in JavaScript is a built-in object that provides a way to work with binary data in a structured and type-safe manner. TypedArrays are designed to work with ArrayBuffer objects, which represent a fixed-size binary data buffer. TypedArrays allow you to view and manipulate the data stored in an ArrayBuffer as arrays of various numeric data types, such as integers and floating-point numbers.

Here are some key points about TypedArrays:

Type Safety: TypedArrays enforce strict type checking, meaning that the elements of the array have a fixed data type. This helps ensure that you work with the correct data types and avoid unintended type conversions.

Performance: TypedArrays are designed for efficiency and can be more performant than regular JavaScript arrays when working with binary data. They are optimized for tasks like data processing, network communication, and working with low-level APIs like WebGL.

Supported Data Types: JavaScript provides several TypedArray types, each corresponding to a different numeric data type. Common TypedArray types include:

Int8Array: 8-bit signed integers
Uint8Array: 8-bit unsigned integers
Int16Array: 16-bit signed integers
Uint16Array: 16-bit unsigned integers
Int32Array: 32-bit signed integers
Uint32Array: 32-bit unsigned integers
Float32Array: 32-bit floating-point numbers (single-precision)
Float64Array: 64-bit floating-point numbers (double-precision)
Creation and Usage: To create a TypedArray, you typically pass an ArrayBuffer or another TypedArray to its constructor. Once created, you can access and manipulate the data within the TypedArray using array-like syntax

Out-of-bounds behaviour
 If you attempt to write an out-of-bounds value into a TypedArray in JavaScript, the behavior can vary depending on the specific TypedArray type and the JavaScript engine you are using. In general, writing an out-of-bounds value can lead to unexpected results, and it's considered an unsafe practice.

 TypedArray methods
 There are two additional methods:

arr.set(fromArr, [offset]) copies all elements from fromArr to the arr, starting at position offset (0 by default).
arr.subarray([begin, end]) creates a new view of the same type from begin to end (exclusive). That’s similar to slice method (that’s also supported), but doesn’t copy anything – just creates a new view, to operate on the given piece of data.
These methods allow us to copy typed arrays, mix them, create new arrays from existing ones, and so on.

DataView
 DataView is a special super-flexible “untyped” view over ArrayBuffer. It allows accessing the data on any offset in any format.

For typed arrays, the constructor dictates what the format is. The whole array is supposed to be uniform. The i-th number is arr[i].
With DataView we access the data with methods like .getUint8(i) or .getUint16(i). We choose the format at method call time instead of the construction time.
The syntax:

new DataView(buffer, [byteOffset], [byteLength])

buffer – the underlying ArrayBuffer. Unlike typed arrays, DataView doesn’t create a buffer on its own. We need to have it ready.
byteOffset – the starting byte position of the view (by default 0).
byteLength – the byte length of the view (by default till the end of buffer).

DAY5-SPRINT
Day 5 Reflections
 In JavaScript, window events refer to events that occur within the context of the browser's window or global environment. These events are related to various interactions and changes that can happen in a web page or web application. Window events allow you to respond to user actions, browser actions, and other changes in the environment. They are typically handled using event listeners. Here are some common window events in JavaScript:

DOMContentLoaded: This event is fired when the HTML document has been completely loaded and parsed, without waiting for stylesheets, images, and other external resources to finish loading. It's often used to trigger scripts that need to run as soon as the DOM structure is ready.
load: This event is fired when all resources on the web page (including images and stylesheets) have finished loading. It's used for actions that depend on the entire page being loaded.
resize: The resize event is triggered when the browser window is resized. It's often used to adjust the layout or perform other responsive design-related tasks.
scroll: The scroll event is fired when the user scrolls the web page. It can be used to create effects like parallax scrolling or lazy loading of content.
unload: The unload event is triggered when the user navigates away from the page or closes the browser tab/window. It can be used for cleanup tasks or for displaying a confirmation dialog to prevent accidental navigation away from the page.
beforeunload: Similar to unload, the beforeunload event is fired before the page is about to be unloaded. It can be used to display a confirmation message to the user to confirm leaving the page.
online and offline: These events are triggered when the user's device goes online (connected to the internet) or offline (disconnected). They are used to handle network-related scenarios in web applications.
