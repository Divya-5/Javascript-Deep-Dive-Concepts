# Mutation observer

The MutationObserver interface provides the ability to watch for changes being made to the DOM tree.
MutationObserver is a built-in object that observes a DOM element and fires a callback when it detects a change.

Syntax

MutationObserver is easy to use. 

## Constructor

MutationObserver()- Creates and returns a new MutationObserver which will invoke a specified callback function when DOM changes occur.

observe()- Configures the MutationObserver to begin receiving notifications through its callback function when DOM changes matching the given options occur.

First, we create an observer with a callback-function:

`let observer = new MutationObserver(callback);`

And then attach it to a DOM node:

`observer.observe(node, config);`

config is an object with boolean options “what kind of changes to react on”:

childList – changes in the direct children of node,
subtree – in all descendants of node,
attributes – attributes of node,
attributeFilter – an array of attribute names, to observe only selected ones.
characterData – whether to observe node.data (text content),

Few other options:

attributeOldValue – if true, pass both the old and the new value of attribute to callback, otherwise only the new one (needs attributes option),
characterDataOldValue – if true, pass both the old and the new value of node.data to callback, otherwise only the new one (needs characterData option).

MutationRecord objects have properties:
I) type – mutation type, one of

1. "attributes": attribute modified
2. "characterData": data modified, used for text nodes,
3. "childList": child elements added/removed,

II) target – where the change occurred: an element for "attributes", or text node for "characterData", or an element for a "childList" mutation,
III) addedNodes/removedNodes – nodes that were added/removed,
IV) previousSibling/nextSibling – the previous and next sibling to added/removed nodes,
V) attributeName/attributeNamespace – the name/namespace (for XML) of the changed attribute,
VI) oldValue – the previous value, only for attribute or text changes, if the corresponding option is set attributeOldValue/characterDataOldValue.

Eg:
`<ol contenteditable>`\
`  <li>Press enter</li>`\
`</ol>`\

// Select the node that will be observed for mutations
`const targetNode = document.getElementById("some-id");`\

// Options for the observer (which mutations to observe)
`const config = { attributes: true, childList: true, subtree: true };`\

// Callback function to execute when mutations are observed
`const callback = (mutationList, observer) => {`\
 ` for (const mutation of mutationList) {`\
  `  if (mutation.type === "childList") {`\
    ` console.log("A child node has been added or removed.");`\
 ` } else if (mutation.type === "attributes") {`\
     `console.log(`The ${mutation.attributeName} attribute was modified.`);`\
  `}`\
  `}`\
`};`\

// Create an observer instance linked to the callback function
`const observer = new MutationObserver(callback);`\

// Start observing the target node for configured mutations
`observer.observe(targetNode, config);`\

// Later, you can stop observing
`observer.disconnect();`\

For example, here’s a <div> with a contentEditable attribute. That attribute allows us to focus on it and edit.

`<div contentEditable id="elem">Click and <b>edit</b>, please</div>`\

<script>
`let observer = new MutationObserver(mutationRecords => {`\
`  console.log(mutationRecords); // console.log(the changes)`\
`});`\

// observe everything except attributes
`observer.observe(elem, {`\
 ` childList: true, // observe direct children`\
  `subtree: true, // and lower descendants too`\
 ` characterDataOldValue: true // pass old data to callback`\
`});`\
</script>

If we run this code in the browser, then focus on the given <div> and change the text inside <b>edit</b>, console.log will show one mutation:

`mutationRecords = [{`\
`type: "characterData",`\
`oldValue: "edit",`\
`target: <text node>,`\
`// other properties empty`\
`}];`\

If we make more complex editing operations, e.g. remove the <b>edit</b>, the mutation event may contain multiple mutation records:

mutationRecords = [{
type: "childList",
target: <div#elem>,
removedNodes: [<b>],
nextSibling: <text node>,
previousSibling: <text node>
// other properties empty
}, {
type: "characterData"
target: <text node>
// ...mutation details depend on how the browser handles such removal
// it may coalesce two adjacent text nodes "edit " and ", please" into one node
// or it may leave them separate text nodes
}];

So, MutationObserver allows to react on any changes within DOM subtree.

Example:

`<div class="parent" id="parent" >`\
`<div class="child" contenteditable>Child 1</div>`\
`<div class="child" contenteditable>Child 2</div>`\
`<div class="child" contenteditable>Child 3</div>`\
`</div>`\

<script>
   ` const mutationObserver= new MutationObserver(entries => {`\
`    console.log(entries)`\
`    })`\
   ` const parent=document.querySelector(".parent")`\
    `mutationObserver.observe(parent,{ childlist:true  })`\
   ` parent.children[0].remove`\
 </script>


MutationRecord = [{
type: "childList",
target: <div#parent.parent>,
removedNodes: NodesList[div.child],
addedNodes:NodesList[]
attributeName:null
attributeNameSpace:null
nextSibling: text
previousSibling: text
oldValue:null
}];


# Attribute:true

<script>
  `  const mutationObserver= new MutationObserver(entries => {`\
   ` console.log(entries)`\
  `  })`\
   ` const parent=document.querySelector(".parent")`\
   ` mutationObserver.observe(parent,{ attribute:true, attributeOldValue:true, attributeFilter:["id"] })`\
   ` parent.id= "New Id"`\
</script>


 MutationRecord = [{
type: "attributes"
target: <div#New Id.parent>
removedNodes: NodesList[]
addedNodes:NodesList[]
attributeName:id
attributeNameSpace:null
nextSibling:null
previousSibling:null
oldValue:parent
}];

# characterData
<script>
   ` const mutationObserver= new MutationObserver(entries => {`\
  `  console.log(entries)`\
  `  })`\
   ` const parent=document.querySelector(".parent")`\
   ` mutationObserver.observe(parent.children[0].childNodes[0],{characterData:true, characterDataOldValue:true })`\
  `  parent.id= "New Id"`\
</script>


MutationRecord = [{
type: "characterData"
target:text
removedNodes: NodesList[]
addedNodes:NodesList[]
attributeName:null
attributeNameSpace:null
nextSibling:null
previousSibling:null
oldValue:"Child 1"
}];

# Subtree
<script>
 ` const mutationObserver= new MutationObserver(entries => {`\
   ` console.log(entries)`\
`    })`\
   ` const parent=document.querySelector(".parent")`\
   ` mutationObserver.observe(parent,{subtree:true, characterData:true, characterDataOldValue:true })`\
</script>

MutationRecord = [{
type: "characterData"
target:text
removedNodes: NodesList[]
addedNodes:NodesList[]
attributeName:null
attributeNameSpace:null
nextSibling:null
previousSibling:null
oldValue:"Child 1"
}];

<script>
`const mutationObserver= new MutationObserver(entries => {`\
`console.log(entries)`\
`})`\
`const parent=document.querySelector(".parent")`\
`mutationObserver.observe(parent,{subtree:true, attributes:true })`\
`parent.children[0].id= "Test"`\
</script>


 MutationRecord = [{
type: "characterData"
target:div#Test.child
removedNodes: NodesList[]
addedNodes:NodesList[]
attributeName:id
attributeNameSpace:null
nextSibling:null
previousSibling:null
oldValue:null
}];


## Usage for integration

When such thing may be useful?

Imagine the situation when you need to add a third-party script that contains useful functionality, but also does something unwanted, e.g. shows ads <div class="ads">Unwanted ads</div>.

Naturally, the third-party script provides no mechanisms to remove it.

Using MutationObserver, we can detect when the unwanted element appears in our DOM and remove it.

There are other situations when a third-party script adds something into our document, and we’d like to detect, when it happens, to adapt our page, dynamically resize something etc.

MutationObserver allows to implement this.

## Usage for architecture
There are also situations when MutationObserver is good from architectural standpoint.

Let’s say we’re making a website about programming. Naturally, articles and other materials may contain source code snippets.

Such snippet in an HTML markup looks like this:

...
`<pre class="language-javascript"><code>`\
`  // here's the code`\
`  let hello = "world";`\
`</code></pre>`\
...

For better readability and at the same time, to beautify it, we’ll be using a JavaScript syntax highlighting library on our site, like Prism.js. To get syntax highlighting for above snippet in Prism, Prism.highlightElem(pre) is called, which examines the contents of such pre elements and adds special tags and styles for colored syntax highlighting into those elements, similar to what you see in examples here, on this page.

When exactly should we run that highlighting method? Well, we can do it on DOMContentLoaded event, or put the script at the bottom of the page. The moment our DOM is ready, we can search for elements pre[class*="language"] and call Prism.highlightElem on them:


`document.querySelectorAll('pre[class*="language"]').forEach(Prism.highlightElem);`\

Everything’s simple so far, right? We find code snippets in HTML and highlight them.

Now let’s go on. Let’s say we’re going to dynamically fetch materials from a server. 
For now it only matters that we fetch an HTML article from a webserver and display it on demand:

let article = /* fetch new content from server */
`articleElem.innerHTML = article;`\

The new article HTML may contain code snippets. We need to call Prism.highlightElem on them, otherwise they won’t get highlighted.

Where and when to call Prism.highlightElem for a dynamically loaded article?

We could append that call to the code that loads an article, like this:
let article = /* fetch new content from server */
`articleElem.innerHTML = article;`]

`let snippets = articleElem.querySelectorAll('pre[class*="language-"]');`\
`snippets.forEach(Prism.highlightElem);`\

…But, imagine if we have many places in the code where we load our content – articles, quizzes, forum posts, etc. Do we need to put the highlighting call everywhere, to highlight the code in content after loading? That’s not very convenient.

And what if the content is loaded by a third-party module? For example, we have a forum written by someone else, that loads content dynamically, and we’d like to add syntax highlighting to it. No one likes patching third-party scripts.

Luckily, there’s another option.

We can use MutationObserver to automatically detect when code snippets are inserted into the page and highlight them.

So we’ll handle the highlighting functionality in one place, relieving us from the need to integrate it.


## Additional methods

There’s a method to stop observing the node:

observer.disconnect() – stops the observation.
When we stop the observing, it might be possible that some changes were not yet processed by the observer.Stops the MutationObserver instance from receiving further notifications until and unless observe() is called again.

observer.takeRecords() – Removes all pending notifications from the MutationObserver's notification queue and returns them in a new Array of MutationRecord objects. It gets a list of unprocessed mutation records – those that happened, but the callback has not handled them.
These methods can be used together, like this:// get a list of unprocessed mutations
// should be called before disconnecting,
// if you care about possibly unhandled recent mutations
let mutationRecords = observer.takeRecords();

// stop tracking changes
observer.disconnect();
...


 Records returned by observer.takeRecords() are removed from the processing queue
The callback won’t be called for records, returned by observer.takeRecords().
Garbage collection interaction
Observers use weak references to nodes internally. That is, if a node is removed from the DOM, and becomes unreachable, then it can be garbage collected.

The mere fact that a DOM node is observed doesn’t prevent the garbage collection.


## Summary
MutationObserver can react to changes in DOM – attributes, text content and adding/removing elements.

We can use it to track changes introduced by other parts of our code, as well as to integrate with third-party scripts.

MutationObserver can track any changes. The config “what to observe” options are used for optimizations, not to spend resources on unneeded callback invocations.