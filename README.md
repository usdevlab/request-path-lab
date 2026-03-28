request-path-lab

Layered troubleshooting lab validating DNS, TLS, HTTP, edge behavior, and browser behavior.

Goal

The purpose of this lab is to validate the request path step by step instead of assuming that a successful page load tells the whole story.

This repo supports the broader usdevlab portfolio by documenting a practical workflow for:
	•	confirming DNS resolution
	•	inspecting HTTP response behavior
	•	validating TLS and SNI behavior
	•	checking browser-side behavior
	•	connecting signals across layers into one clear explanation

Why This Matters

In support engineering, systems often behave across multiple layers at the same time. A request may resolve correctly at DNS, negotiate TLS successfully, and still behave unexpectedly at the HTTP, edge, origin, or browser layer.

The fastest path to clarity is:
	1.	identify the layer
	2.	validate that layer directly
	3.	compare expected versus actual behavior
	4.	keep only the explanation supported by evidence

Lab Workflow

1. DNS Validation

Use dig to confirm where the request begins and whether the domain resolves to the expected destination.

2. HTTP and Edge Validation

Use curl to inspect headers, status codes, redirects, and response metadata.

3. TLS Validation

Use openssl s_client to inspect certificate presentation, handshake behavior, and whether SNI changes the result.

4. Browser Validation

Use browser DevTools to confirm how the request behaves in a real client environment.

Core Commands and Sample Output

```bash

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

```

Troubleshooting Scenario 1: Stale Content (Caching)

Issue

A user reports that a website is not updating after changes are made. The page loads successfully, but content appears stale.

Hypothesis

The issue may be related to caching at the browser, edge, or origin layer.

Validation Steps
	1.	DNS
Confirm the domain resolves to the expected edge location.
	2.	HTTP
Inspect headers using curl to check cache-related signals such as cf-cache-status.
	3.	TLS
Confirm HTTPS behavior is normal and not affecting content delivery.
	4.	Browser
Use DevTools to inspect network requests and disable cache to compare behavior.

Observations
	•	DNS resolves to Cloudflare edge IPs
	•	HTTP response includes cf-cache-status: HIT
	•	Browser continues to serve cached content even after refresh

Conclusion

The issue is caused by cached content being served from the edge or browser. A hard refresh or cache purge is required to retrieve the latest version.

⸻

Troubleshooting Scenario 2: TLS / SNI Failure

Issue

A user reports that HTTPS requests fail intermittently, or the wrong certificate is presented when accessing the site.

Hypothesis

The issue may be related to incorrect TLS configuration or missing Server Name Indication during the handshake.

Validation Steps
	1.	DNS
Confirm the domain resolves to the expected destination.
	2.	TLS without SNI
Run openssl without specifying a server name to observe default certificate behavior.
	3.	TLS with SNI
Run openssl with the servername flag to verify correct certificate presentation.
	4.	Compare Results
Check whether the certificate or handshake behavior changes depending on SNI usage.

Observations
	•	TLS handshake without SNI returns an unexpected or default certificate
	•	TLS handshake with SNI returns the correct certificate
	•	Connection behavior differs based on whether SNI is included

Conclusion

The issue is caused by missing or incorrect SNI during the TLS handshake. Proper SNI configuration ensures the correct certificate is served.

⸻

Key Takeaway

A good troubleshooting workflow does not stop at “the site loads.”

It asks:
	•	where does the request begin
	•	what layers are involved
	•	what signals confirm each layer
	•	where does behavior change
	•	what evidence supports the explanation

Related Links
	•	https://usdevlab.online
	•	https://usdevlab.online/v2/labs.html
	•	https://usdevlab.online/v2/docs.html
	•	https://usdevlab.online/v2/case-study.html
	•	https://github.com/usdevlab
