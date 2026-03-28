# request-path-lab

Layered troubleshooting lab validating DNS, TLS, HTTP, edge behavior, and browser behavior.

## Goal

The purpose of this lab is to validate the request path step by step instead of assuming that a successful page load tells the whole story.

This repo supports the broader usdevlab portfolio by documenting a practical workflow for:

- confirming DNS resolution  
- inspecting HTTP response behavior  
- validating TLS and SNI behavior  
- checking browser-side behavior  
- connecting signals across layers into one clear explanation  

## Why This Matters

In support engineering, systems often behave across multiple layers at the same time. A request may resolve correctly at DNS, negotiate TLS successfully, and still behave unexpectedly at the HTTP, edge, origin, or browser layer.

The fastest path to clarity is:

1. identify the layer  
2. validate that layer directly  
3. compare expected versus actual behavior  
4. keep only the explanation supported by evidence  

## Lab Workflow

### 1. DNS Validation
Use dig to confirm where the request begins and whether the domain resolves to the expected destination.

### 2. HTTP and Edge Validation
Use curl to inspect headers, status codes, redirects, and response metadata.

### 3. TLS Validation
Use openssl s_client to inspect certificate presentation, handshake behavior, and whether SNI changes the result.

### 4. Browser Validation
Use browser DevTools to confirm how the request behaves in a real client environment.

## Core Commands and Sample Output

# DNS
dig usdevlab.online

; <<>> DiG <<>> usdevlab.online
;; ANSWER SECTION:
usdevlab.online.   300   IN   A   172.67.172.231

# HTTP / Edge
curl -I https://usdevlab.online

HTTP/2 200
server: cloudflare
cf-cache-status: HIT
cf-ray: 86b123abcde12345-CDG

# TLS (with SNI)
openssl s_client -connect usdevlab.online:443 -servername usdevlab.online

SSL handshake has read 3000 bytes and written 400 bytes
Certificate chain
Verify return code: 0 (ok)


