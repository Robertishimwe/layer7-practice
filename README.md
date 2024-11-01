# Layer7 Gateway JavaScript Assertions: A Complete Course

## Table of Contents
1. Introduction
2. Basics of JavaScript Assertions
3. Context and Variables
4. Working with Messages
5. Logging and Debugging
6. XML Handling
7. Error Handling
8. Best Practices
9. Common Use Cases
10. Advanced Topics

## 1. Introduction

JavaScript assertions in Layer7 API Gateway allow you to write custom logic to manipulate messages, access context variables, and implement complex business rules. This course will teach you everything from basics to advanced techniques.

### Prerequisites
- Basic JavaScript knowledge
- Basic understanding of Layer7 Gateway
- Access to Layer7 Policy Manager

## 2. Basics of JavaScript Assertions

### Adding a JavaScript Assertion
1. In Policy Manager, right-click where you want to add the assertion
2. Navigate to Include -> Programmable Assertions -> Execute JavaScript
3. The JavaScript Properties dialog will appear

### Basic Structure
```javascript
// Get data from context
var someValue = context.getVariable("some.variable");

// Process data
var result = someValue + " processed";

// Set response
var responseMsg = context.getVariable("response");
responseMsg.setContent(result, "text/plain");
```

### Key Components
- Script Name: Identifies your JavaScript code (max 128 characters)
- Execution Timeout: Maximum time for script execution (default: 1500ms)
- Strict Mode: Enforces stricter JavaScript parsing (recommended)

## 3. Context and Variables

### Reading Variables
```javascript
// Basic variable reading
var username = context.getVariable("request.authenticateduser.name");

// Reading HTTP parameters
var paramValue = context.getVariable("request.http.parameter.paramName");

// Reading message content
var requestMsg = context.getVariable("request");
var content = requestMsg.getContent();
```

### Setting Variables
```javascript
// Create/update a variable
context.setVariable("my.custom.variable", "some value");

// Create a message variable
var newMsg = context.getOrCreateMessageVariable("my.new.message");

// Create a multivalued variable
var multiValue = context.getOrCreateMultivaluedVariable("my.multi.value");
multiValue.add("value1");
multiValue.add("value2");
```

## 4. Working with Messages

### HTTP Request Message
```javascript
var request = context.getVariable("request");

// Get HTTP method
var method = request.getMethod();

// Get URL
var url = request.getUrl();

// Get parameters
var params = request.getParameters();

// Get headers
var headers = request.getHeaders();
```

### HTTP Response Message
```javascript
var response = context.getVariable("response");

// Set content
response.setContent("Hello World", "text/plain");

// Set status code
response.setStatusCode(200);

// Set headers
response.setHeader("Custom-Header", "value");
```

### Working with Cookies
```javascript
// Read cookies
var cookies = request.getCookies();

// Add a cookie
var cookie = {
    name: "sessionId",
    value: "123456",
    path: "/",
    domain: ".example.com",
    maxAge: 3600
};
response.addCookie(cookie);
```

## 5. Logging and Debugging

### Log Levels
```javascript
// Available log levels:
// LOG_LEVEL.SEVERE
// LOG_LEVEL.WARNING
// LOG_LEVEL.INFO
// LOG_LEVEL.CONFIG
// LOG_LEVEL.FINE
// LOG_LEVEL.FINER
// LOG_LEVEL.FINEST

// Basic logging
logger.log(LOG_LEVEL.INFO, "Processing request...");

// Debug logging
logger.log(LOG_LEVEL.FINE, "Debug value: " + someVariable);

// Error logging
logger.log(LOG_LEVEL.SEVERE, "Error occurred: " + error.message);
```

## 6. XML Handling

### Parsing XML
```javascript
// Parse XML string
var xmlString = '<?xml version="1.0"?><root><child>value</child></root>';
var xmlDoc = XML.parse(xmlString);

// Access root element
var root = xmlDoc.root;

// Find elements using XPath
var elements = xmlDoc.find("//child");
```

### Modifying XML
```javascript
// Add new element
var newChild = XML.parse('<newChild>new value</newChild>').root;
root.appendChild(newChild);

// Add attribute
root.addAttribute("id", "123");

// Remove element
root.removeChild(someChild);
```

### Iterating Over XML
```javascript
// Iterate over children
root.eachChild(function(child, index) {
    logger.log(LOG_LEVEL.INFO, "Child " + index + ": " + child.name);
});

// Iterate over attributes
root.eachAttribute(function(attr, index) {
    logger.log(LOG_LEVEL.INFO, attr.name + "=" + attr.value);
});
```

## 7. Error Handling

### Try-Catch Blocks
```javascript
try {
    // Risky operation
    var value = context.getVariable("might.not.exist");
    processValue(value);
} catch(e) {
    // Log error
    logger.log(LOG_LEVEL.SEVERE, "Error: " + e.message);
    
    // Set error response
    var response = context.getVariable("response");
    response.setContent("An error occurred", "text/plain");
    response.setStatusCode(500);
}
```

### Validation
```javascript
function validateInput(value) {
    if (!value) {
        throw new Error("Value is required");
    }
    if (typeof value !== 'string') {
        throw new Error("Value must be a string");
    }
    return true;
}
```

## 8. Best Practices

### 1. Always Use Error Handling
```javascript
try {
    // Your code here
} catch(e) {
    logger.log(LOG_LEVEL.SEVERE, "Error: " + e.message);
    throw e; // Re-throw if you want policy to fail
}
```

### 2. Set Appropriate Content Types
```javascript
// For JSON
response.setContent(JSON.stringify(obj), "application/json");

// For XML
response.setContent(xmlDoc, "application/xml");

// For Text
response.setContent(text, "text/plain");
```

### 3. Use Logging Effectively
```javascript
// Start of processing
logger.log(LOG_LEVEL.INFO, "Starting process for request: " + requestId);

// Debug information
logger.log(LOG_LEVEL.FINE, "Intermediate value: " + value);

// Completion
logger.log(LOG_LEVEL.INFO, "Process completed successfully");
```

### 4. Clean Up Resources
```javascript
// Set variables to null when done
xmlDoc = null;
response = null;
```

## 9. Common Use Cases

### 1. Request Transformation
```javascript
// Transform request data
var request = context.getVariable("request");
var content = request.getContent();

var transformed = {
    newFormat: content.oldFormat,
    timestamp: new Date().toISOString()
};

request.setContent(JSON.stringify(transformed), "application/json");
```

### 2. Response Enrichment
```javascript
// Add additional data to response
var response = context.getVariable("response");
var content = JSON.parse(response.getContent());

content.metadata = {
    processedAt: new Date().toISOString(),
    version: "1.0"
};

response.setContent(JSON.stringify(content), "application/json");
```

### 3. Custom Authentication
```javascript
// Validate custom token
var token = context.getVariable("request.http.header.X-Custom-Token");

if (!token || !validateToken(token)) {
    var response = context.getVariable("response");
    response.setStatusCode(401);
    response.setContent("Invalid token", "text/plain");
    throw new Error("Authentication failed");
}
```

## 10. Advanced Topics

### Working with Multivalued Variables
```javascript
// Create multivalued variable
var multi = context.getOrCreateMultivaluedVariable("multi.values");

// Add values
multi.add("value1");
multi.add("value2");

// Iterate over values
for (var i in multi) {
    logger.log(LOG_LEVEL.INFO, "Value: " + multi[i]);
}
```

### Custom XML Processing
```javascript
// Complex XML transformation
function transformXML(inputXML) {
    var doc = XML.parse(inputXML);
    var transformed = XML.parse('<new/>').root;
    
    doc.root.eachChild(function(child) {
        if (child.type === XML.NODE_TYPE.ELEMENT_NODE) {
            var newChild = processNode(child);
            transformed.appendChild(newChild);
        }
    });
    
    return transformed;
}
```

### Performance Optimization
```javascript
// Cache frequently used values
var cachedValue = context.getVariable("cached.value");
if (!cachedValue) {
    cachedValue = expensiveOperation();
    context.setVariable("cached.value", cachedValue);
}

// Minimize XML parsing
var xmlDoc = null;
try {
    xmlDoc = context.getVariable("parsed.xml");
} catch(e) {
    xmlDoc = XML.parse(xmlString);
    context.setVariable("parsed.xml", xmlDoc);
}
```

## Exercise Examples

### Exercise 1: Basic Message Transformation
```javascript
// Task: Convert a JSON request to XML response
var request = context.getVariable("request");
var jsonContent = JSON.parse(request.getContent());

var xmlString = '<?xml version="1.0"?><root>' +
    '<name>' + jsonContent.name + '</name>' +
    '<value>' + jsonContent.value + '</value>' +
    '</root>';

var response = context.getVariable("response");
response.setContent(xmlString, "application/xml");
```

### Exercise 2: Custom Validation
```javascript
// Task: Validate request parameters
var request = context.getVariable("request");
var params = request.getParameters();

try {
    validateRequired(params.id, "ID");
    validateRequired(params.name, "Name");
    validateEmail(params.email);
    
    // If valid, process request
    processRequest(params);
} catch(e) {
    var response = context.getVariable("response");
    response.setStatusCode(400);
    response.setContent(e.message, "text/plain");
}
```

### Exercise 3: Complex XML Processing
```javascript
// Task: Transform SOAP request to REST response
var request = context.getVariable("request");
var soapDoc = request.getContent();

// Extract data from SOAP
var data = extractFromSOAP(soapDoc);

// Create REST response
var response = context.getVariable("response");
response.setContent(JSON.stringify(data), "application/json");
response.setHeader("Content-Type", "application/json");
```

## Conclusion

JavaScript assertions are powerful tools in Layer7 Gateway that allow you to implement complex business logic and transformations. Remember these key points:

1. Always handle errors appropriately
2. Use logging for debugging and monitoring
3. Set proper content types and headers
4. Follow best practices for performance
5. Test thoroughly before deployment

For further learning:
- Review Layer7 API Gateway documentation
- Practice with the provided exercises
- Start with simple transformations and gradually move to complex scenarios
- Keep security in mind when processing data
