# Notes On: Node.js Use Cases

Understanding where Node.js is going to return the most value to both developers and users, based on it's objective capabilities.

## Traditional Web Serving

Traditionally, serving users on the web means allotting them a thread and some memory, which means the number of concurrent users your server can support has an obvious limitation; You would need enough memory to serve as many users as exist multiplied by how much memory each user is allocated.

So a server with 8GB of memory, allocating each user 2MB of memory could only support 4000 concurrent connections.

## Node.js Web Serving

Node circumvents the issue above by working on a single thread, with operations executing as part of an event loop.

This event loop is callback based. So Node (typically) won't stop and wait on some operation to complete. Instead it will continue through its event loop, queueing more callbacks, and waiting for operations to explicilty declare "I'm done".

## Remember: Single Threaded

There is a caveat to Node being single threaded, that being that any *really* heavy compution tasks will bring the event loop to a grinding halt as the thread is in heavy use running calculations.

So whilst Node *can* excel within data heavy applications, that is only true if Node doesn't need to do extensive computation on said data.

## High Value Node Uses

- Chat client
  - Low computation requirements, lots of data throughput, event based.
- Exposing document (object) oriented databases
  - No need to convert data from a JSON object to something Node will understand, whether that be in reading or writing.
- Monitoring dashboards
  - Situations where you need real time data reported in, well, real time.
- Data streaming
  - Node allows you to stream data, and process that stream rather than wait for *all* of the data to be present.

## Low/No Value Node Uses

- Heavy computation
  - The single thread will get overwhelemed quickly. [Cluster](https://nodejs.org/api/cluster.html) is an option.

## Node.js Success Stories

- [Paypal](https://www.paypal-engineering.com/2013/11/22/node-js-at-paypal/)
  - Built twice as fast*
  - 33% fewer lines of code*
  - 40% fewer files
  - double the requests per second in testing
  - 35% decreased response time

**versus fallback Java application built in parallel*

- [Walmart](https://venturebeat.com/2012/01/24/why-walmart-is-using-node-js/)
  - Serve richer experience to low end devices as front end code executed on back end.
  - Options for hybrid application and the consistency across devices.

## Sources/Further Reading

- [Just What is Node.js?](https://www.scribd.com/document/54504131/What-is-node-JS-developerWorks)
- [Why Use Node.js?](https://www.toptal.com/nodejs/why-the-hell-would-i-use-node-js)
