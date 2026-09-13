# LIVE SUBDOMAINS ENUMERATION

**Professional Automated Reconnaissance Framework for Passive Subdomain Discovery and Live Service Identification**

![Version](https://img.shields.io/badge/version-3.0-blue.svg)
![Language](https://img.shields.io/badge/language-Bash-green.svg)
![License](https://img.shields.io/badge/license-Educational-orange.svg)
![Status](https://img.shields.io/badge/status-Production%20Ready-success.svg)

---

## Executive Summary

This repository contains an advanced, production-grade Bash shell script designed for comprehensive subdomain enumeration and live service discovery during authorized penetration testing engagements. The framework implements a six-phase reconnaissance methodology that integrates multiple industry-standard tools to provide exhaustive attack surface mapping and asset discovery capabilities.

Developed as part of penetration testing coursework (Assignment #2, PN Test A2 - 64999), this tool demonstrates mature software engineering practices including robust error handling, multi-domain batch processing, dependency validation, and structured reporting mechanisms.

---

## Table of Contents

- [Features](#features)
- [Technical Architecture](#technical-architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Methodology](#methodology)
- [Tool Integration](#tool-integration)
- [Output Specification](#output-specification)
- [Examples](#examples)
- [Troubleshooting](#troubleshooting)
- [Performance Considerations](#performance-considerations)
- [Security & Legal](#security--legal)
- [Contributing](#contributing)
- [Author](#author)

---

## Features

### Core Capabilities

✓ **Multi-Domain Batch Processing**
- Simultaneous enumeration of multiple target domains
- Array-based input handling for robust multi-target operations
- Automatic domain normalization and validation

✓ **Intelligent Domain Normalization**
- Automatic removal of protocol schemes (HTTP/HTTPS/FTP)
- Subdomain prefix filtering (www, www2, etc.)
- Port and path stripping
- Case normalization
- Whitespace and special character handling

✓ **Comprehensive Passive Enumeration**
- Subfinder integration for certificate transparency analysis
- Assetfinder for public source aggregation
- Findomain for rapid passive discovery
- Optional Amass integration for advanced DNS enumeration

✓ **DNS Resolution & Validation**
- DNSX-based DNS verification of discovered subdomains
- A-record and CNAME resolution
- Filtering of non-resolvable hosts
- Response analysis and validation

✓ **Live Service Detection**
- HTTPX-based HTTP/HTTPS probing
- Status code detection and classification
- Page title extraction
- Technology stack detection (web frameworks, CMS, etc.)
- Multi-threaded probing with configurable concurrency

✓ **Results Deduplication**
- ANEW-based duplicate elimination
- Unique result retention across multiple enumeration sources
- Consolidated output generation

✓ **Professional Reporting**
- Structured, timestamped output format
- Scan metadata and execution summaries
- Segmented results by target domain
- Statistical analysis and summary metrics

### Quality Assurance

✓ **Dependency Management**
- Automated prerequisite checking
- Helpful installation guidance for missing tools
- Pre-flight validation before execution

✓ **Error Handling**
- Graceful input validation
- Non-blocking subdomain processing
- Comprehensive error logging
- Signal-based cleanup (SIGINT/SIGTERM)

✓ **Output Integrity**
- Isolated stderr for diagnostic messaging
- Clean stdout for piping and automation
- Temporary file management and cleanup
- No terminal pollution during execution

---

## Technical Architecture

### System Design

```
┌─────────────────────────────────────────────────────────────┐
│           INPUT COLLECTION & NORMALIZATION                   │
│  (live_urls.txt → Domain Extraction & Validation)            │
└────────────────┬────────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────────┐
│          PASSIVE ENUMERATION PHASE (Phase 3)                 │
│  ┌──────────────┬──────────────┬──────────────┐             │
│  │  Subfinder   │ Assetfinder  │  Findomain   │ (+ Amass)   │
│  └──────────────┴──────────────┴──────────────┘             │
└────────────────┬────────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────────┐
│     DEDUPLICATION & AGGREGATION (ANEW)                       │
│  (Unique subdomain set generation)                           │
└────────────────┬────────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────────┐
│           DNS VERIFICATION PHASE (Phase 4)                   │
│  (DNSX Resolution & A-Record Validation)                     │
└────────────────┬────────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────────┐
│      LIVE HOST VERIFICATION PHASE (Phase 5)                  │
│  (HTTPX Probing with Status Codes & Tech Detection)         │
└────────────────┬────────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────────┐
│         REPORTING & OUTPUT GENERATION                        │
│  (subdomain_results.txt with Structured Metadata)           │
└─────────────────────────────────────────────────────────────┘
```

### Execution Flow

1. **Initialization**
   - Dependency verification
   - Configuration validation
   - Output file preparation with metadata headers

2. **Domain Processing Loop**
   - Input line reading with comment/empty line filtering
   - Domain extraction and normalization
   - Format validation (RFC-compliant domain regex)
   - Sequential enumeration per valid domain

3. **Enumeration Phase**
   - Parallel tool execution (Subfinder, Assetfinder, Findomain)
   - Temporary file management per domain
   - Deduplication via ANEW

4. **Validation Phase**
   - DNS resolution via DNSX
   - Non-resolvable subdomain filtering
   - Response code and record analysis

5. **Probing Phase**
   - Multi-threaded HTTPX execution (default: 50 threads)
   - Service detection and classification
   - Final result compilation

6. **Finalization**
   - Temporary resource cleanup
   - Summary statistics generation
   - Output file verification

---

## Prerequisites

### System Requirements

| Component | Requirement | Details |
|-----------|-------------|---------|
| **OS** | Linux/Unix | Ubuntu 18.04+, Debian 10+, CentOS 8+, Kali Linux |
| **Shell** | Bash | Version 4.0+ |
| **Memory** | 2GB+ | Recommended for large-scale enumeration |
| **Disk Space** | 500MB+ | For tool installation and output files |
| **Network** | Outbound HTTPS | Access to enumeration APIs and DNS servers |

### Required Tools

All tools must be installed and available in system PATH:

| Tool | Version | Purpose | Installation |
|------|---------|---------|--------------|
| **Subfinder** | Latest | Certificate transparency enumeration | Go-based tool |
| **Assetfinder** | Latest | Public source aggregation | Go-based tool |
| **Findomain** | Latest | Fast passive enumeration | Go-based or Rust binary |
| **DNSX** | Latest | DNS resolution & validation | Go-based tool |
| **HTTPX** | Latest | HTTP service probing | Go-based tool |
| **ANEW** | Latest | Result deduplication | Go-based tool |

### Go Installation (For Tool Dependencies)

```bash
# Install Go 1.19+
wget https://go.dev/dl/go1.21.0.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
```

---

## Installation

### Automated Installation (Recommended)

```bash
# Clone repository
git clone https://github.com/Idanish123/LIVE-SUBDOMAINS-ENUMERATION.git
cd LIVE-SUBDOMAINS-ENUMERATION

# Make script executable
chmod +x subdomain_enum.sh

# Verify and install dependencies
./subdomain_enum.sh
# Script will detect missing tools and provide installation commands
```

### Manual Tool Installation

```bash
# Subfinder
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest

# Assetfinder
go install -v github.com/tomnomnom/assetfinder@latest

# Findomain
go install -v github.com/Findomain/Findomain@latest

# DNSX
go install -v github.com/projectdiscovery/dnsx/cmd/dnsx@latest

# HTTPX
go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest

# ANEW
go install -v github.com/tomnomnom/anew@latest

# Ensure Go binaries are in PATH
export PATH=$PATH:$(go env GOPATH)/bin
echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> ~/.bashrc
```

### Debian/Ubuntu Package-Based Installation

```bash
# Update package lists
sudo apt-get update

# Install Go
sudo apt-get install -y golang-go

# Install tools via Go
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
go install -v github.com/tomnomnom/assetfinder@latest
go install -v github.com/Findomain/Findomain@latest
go install -v github.com/projectdiscovery/dnsx/cmd/dnsx@latest
go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
go install -v github.com/tomnomnom/anew@latest
```

### Verification

```bash
# Test script execution
./subdomain_enum.sh

# Expected output:
# [✓] All dependencies satisfied
# [-] Error: live_urls.txt not found!
```

---

## Usage

### Quick Start

```bash
# 1. Create input file
cat > live_urls.txt << EOF
example.com
https://www.example.org
http://test.example.net:8080/path
EOF

# 2. Run enumeration
./subdomain_enum.sh

# 3. Review results
cat subdomain_results.txt
```

### Standard Execution

```bash
# Basic execution with default configuration
./subdomain_enum.sh
```

### Advanced Configuration

Edit configuration variables in the script header:

```bash
# ========== CONFIGURATION SECTION ==========
INPUT_FILE="live_urls.txt"              # Input file path
OUTPUT_FILE="subdomain_results.txt"     # Output file path
TEMP_DIR="/tmp/subdomain_enum_$$"       # Temporary directory
THREADS=50                               # HTTPX thread count
TIMEOUT=10                               # HTTP request timeout (seconds)

# Tool toggles (1=enabled, 0=disabled)
USE_SUBFINDER=1     # Certificate transparency sources
USE_ASSETFINDER=1   # Public asset databases
USE_FINDOMAIN=1     # Fast passive enumeration
USE_AMASS=0         # Advanced DNS enumeration (slower)
USE_DNSX=1          # DNS validation
USE_HTTPX=1         # Live service probing
```

### Input File Formats

The script supports multiple URL/domain formats in `live_urls.txt`:

```
# Domain names
example.com
example.org

# With www prefix
www.example.com
www.example.org

# Full URLs with protocols
http://example.com
https://example.com
ftp://example.com

# URLs with ports
http://example.com:8080
https://example.com:443

# URLs with paths
https://www.example.com/path/to/resource

# Mixed formats (all supported)
EXAMPLE.COM
https://www.EXAMPLE.ORG:8080/test
  example.net  
# Comments are automatically skipped
```

---

## Configuration

### Environment Variables

```bash
# Override input/output files
export INPUT_FILE="targets.txt"
export OUTPUT_FILE="results.txt"
./subdomain_enum.sh

# Set custom thread count
export THREADS=100
./subdomain_enum.sh

# Disable specific tools
export USE_AMASS=0
export USE_FINDOMAIN=0
./subdomain_enum.sh
```

### Performance Tuning

```bash
# For large-scale scanning (100+ domains)
THREADS=100
TIMEOUT=15
# Disabling slower tools can improve speed
USE_AMASS=0

# For resource-constrained environments
THREADS=10
USE_FINDOMAIN=0  # Findomain is less critical
```

### Tool-Specific Configuration

#### Subfinder Configuration
```bash
# Subfinder uses providers.yaml for API keys
# Configure: ~/.config/subfinder/providers.yaml
# Improves enumeration coverage with authenticated APIs
```

#### DNSX Configuration
```bash
# Custom DNS servers can be used
dnsx -l domains.txt -resolver resolvers.txt
```

#### HTTPX Configuration
```bash
# Threads, timeouts, and probing options
# Modify HTTPX_OPTS variable in script for custom flags
```

---

## Methodology

### Phase 1: Input Collection

**Objective:** Gather target URLs/domains from input file

**Process:**
- Read `live_urls.txt` line by line
- Support multiple URL format variations
- Filter comments (lines starting with #) and empty lines
- Count total targets for progress tracking

**Input Validation:**
```
✓ example.com
✓ https://www.example.org
✓ http://example.net:8080/path
✗ invalid..domain
✗ http://
```

### Phase 2: Domain Normalization

**Objective:** Extract consistent, canonical domain names

**Transformations Applied:**
1. Lowercase conversion: `EXAMPLE.COM` → `example.com`
2. Whitespace trimming: `  example.com  ` → `example.com`
3. Protocol removal: `https://example.com` → `example.com`
4. WWW prefix removal: `www.example.com` → `example.com`
5. Port removal: `example.com:8080` → `example.com`
6. Path removal: `example.com/path` → `example.com`
7. Format validation: RFC 1123 domain regex validation

**Example Transformation:**
```
Input:  https://www.EXAMPLE.com:8080/test
Output: example.com
```

### Phase 3: Passive Enumeration

**Objective:** Discover subdomains through public sources

**Tools Used:**

1. **Subfinder** (Certificate Transparency Logs)
   - Scans CT logs for issued certificates
   - Discovers subdomains mentioned in certificate SANs
   - High accuracy, public infrastructure

2. **Assetfinder** (Public Sources)
   - Aggregates results from multiple sources
   - Includes SSL certificates, DNS records, web archives
   - Complements CT-based discovery

3. **Findomain** (Multiple Sources)
   - Fast enumeration using public APIs
   - Includes Censys, Virustotal, VirusShare sources
   - Rapid baseline discovery

4. **Amass** (Optional - Advanced DNS Enumeration)
   - Graph-based subdomain discovery
   - DNS brute-force guessing
   - Slower but more comprehensive
   - Disabled by default for performance

**Results:** Raw subdomain list (may contain duplicates)

### Phase 4: DNS Verification

**Objective:** Validate subdomains with actual DNS resolution

**Process:**
- Resolve each subdomain using DNSX
- Collect A-record and CNAME responses
- Filter non-existent or non-resolving hosts
- Validate DNS records exist in authoritative nameservers

**Benefits:**
- Eliminates false positives
- Confirms active DNS entries
- Reduces noise in results

**Example:**
```
Input:  potential.subdomain.example.com
        fake.subdomain.example.com
Output: potential.subdomain.example.com  [Resolves to 1.2.3.4]
        # fake.subdomain.example.com - NXDOMAIN (filtered)
```

### Phase 5: Live Host Verification

**Objective:** Identify active HTTP/HTTPS services

**Process:**
- Probe each resolved subdomain on HTTP/HTTPS ports
- Detect service availability and response status
- Extract page titles and metadata
- Analyze web technology stack
- Filter non-responsive or unreachable hosts

**Detection Capabilities:**
- HTTP status codes (200, 301, 302, 403, 404, 500, etc.)
- Protocol support (HTTP vs HTTPS)
- Web server identification
- Content management systems
- JavaScript frameworks
- Web application firewalls

**Results:** Confirmed live, web-accessible subdomains

### Phase 6: Reporting

**Objective:** Generate structured, timestamped output

**Report Contents:**
- Enumeration metadata (date, time, input file)
- Per-domain scanning results
- Discovered live subdomains
- DNS resolution information
- HTTP response codes and titles
- Technology stack detection
- Summary statistics
- Scan completion metrics

**Output Format:** Structured text with visual separators

---

## Tool Integration

### Subfinder

**Purpose:** Passive subdomain discovery via certificate transparency

```bash
subfinder -d example.com -silent -all
```

**Advantages:**
- Discovers subdomains from issued SSL certificates
- No DNS queries required (truly passive)
- High success rate for public domains
- Fast execution

**Limitations:**
- Requires domains to have issued certificates
- Limited to publicly trusted CAs
- May miss newly created subdomains

### Assetfinder

**Purpose:** Aggregate public subdomain sources

```bash
assetfinder --subs-only example.com
```

**Advantages:**
- Multiple data source aggregation
- Finds subdomains missed by CT logs
- Includes web archive data
- Public, free sources

**Limitations:**
- Slower than single-source tools
- Data freshness varies by source
- May include historical subdomains

### Findomain

**Purpose:** Fast passive enumeration with multiple sources

```bash
findomain -t example.com -q
```

**Advantages:**
- Rapid execution
- Multiple integrated sources (Censys, etc.)
- Good coverage for large target sets
- Stable performance

**Limitations:**
- API-dependent (some sources require keys)
- Rate limiting on free APIs
- Less comprehensive than Amass

### DNSX

**Purpose:** DNS validation and resolution

```bash
dnsx -l subdomains.txt -silent -a -resp
```

**Features:**
- Batch DNS resolution
- Multiple query types (A, CNAME, MX, etc.)
- Response extraction
- Concurrent resolution with threading
- Resolver configuration support

**Configuration:**
```bash
dnsx -l domains.txt -a -aaaa -cname -mx -ns
```

### HTTPX

**Purpose:** HTTP/HTTPS service probing and detection

```bash
httpx -l subdomains.txt -silent -threads 50 -timeout 10 \
      -status-code -title -tech-detect -no-color
```

**Features:**
- Concurrent HTTP probing
- Status code detection
- Page title extraction
- Technology fingerprinting
- Custom headers support
- Proxy support
- JSON output generation

**Output Format:**
```
http://api.example.com [200] [API Gateway] [nginx 1.19.0]
https://admin.example.com [403] [Admin Panel]
http://staging.example.com [404] [Not Found]
```

### ANEW

**Purpose:** Deduplication and unique result generation

```bash
tool1_output | anew results.txt
tool2_output | anew results.txt
```

**Functionality:**
- Append-only unique entries
- Automatic duplicate detection
- File creation if missing
- Fast performance with large datasets

---

## Output Specification

### Report Structure

```
################################################################################
#                       SUBDOMAIN ENUMERATION REPORT
################################################################################
# Generated on: [TIMESTAMP]
# Input File: live_urls.txt
# Tools Used: subfinder, assetfinder, findomain, dnsx, httpx
################################################################################

################################################################################
TARGET DOMAIN: example.com
SOURCE INPUT: https://www.example.com
SCAN DATE: 2024-01-15 14:30:45
################################################################################

LIVE SUBDOMAINS:
----------------
  ➜ api.example.com
  ➜ admin.example.com
  ➜ mail.example.com
  ➜ www.example.com
  ➜ staging.example.com

[✓] Total Live Subdomains Found: 5

################################################################################

################################################################################
TARGET DOMAIN: example.org
SOURCE INPUT: example.org
SCAN DATE: 2024-01-15 14:31:22
################################################################################

LIVE SUBDOMAINS:
----------------
  ➜ web.example.org
  ➜ ftp.example.org

[✓] Total Live Subdomains Found: 2

################################################################################

################################################################################
SCAN SUMMARY
################################################################################
Total Targets Processed: 2
Successful Scans: 2
Failed/Invalid: 0
Completion Time: 2024-01-15 14:32:10
################################################################################
```

### Output Statistics

Each report includes:
- **Targets Processed:** Total input domains evaluated
- **Successful Scans:** Domains with valid format and processing
- **Failed/Invalid:** Malformed or rejected domains
- **Total Subdomains:** Aggregate count across all targets
- **Live Services:** Count of HTTP/HTTPS accessible hosts
- **Execution Duration:** Time from start to completion

---

## Examples

### Example 1: Single Domain Enumeration

```bash
# Create input
echo "example.com" > live_urls.txt

# Execute
./subdomain_enum.sh

# Output excerpt:
# [1/1] ✓ Valid domain: example.com (from: example.com)
#     └─ Collecting subdomains from multiple sources...
#        ├─ Running Subfinder...
#        ├─ Running Assetfinder...
#        ├─ Running Findomain...
#        ├─ Raw subdomains found: 47
#        ├─ Resolving DNS records...
#        │  Resolved: 23
#        └─ Probing live HTTP/HTTPS services...
#     └─ Live subdomains: 18
```

### Example 2: Multiple Domain Batch Processing

```bash
# Create multi-domain input
cat > targets.txt << EOF
https://www.example.com
example.org
http://test.example.net:8080/api
# Example.in (comment - will be skipped)
EXAMPLE.IO
EOF

# Set custom output
export OUTPUT_FILE="batch_results.txt"
./subdomain_enum.sh

# Results:
# [1/5] ✓ Valid domain: example.com
# [2/5] ✓ Valid domain: example.org
# [3/5] ✓ Valid domain: example.net
# [4/5] ✗ Invalid: example.in (format validation failed)
# [5/5] ✓ Valid domain: example.io
```

### Example 3: Custom Configuration for Large-Scale Enumeration

```bash
#!/bin/bash
# High-performance configuration for 100+ domains

export INPUT_FILE="large_target_list.txt"
export OUTPUT_FILE="large_scale_results.txt"
export THREADS=150              # Increase concurrency
export TIMEOUT=15               # Longer timeout for slow servers
export USE_AMASS=0              # Disable slow enumeration
export USE_FINDOMAIN=1          # Fast tool only
export USE_SUBFINDER=1
export USE_ASSETFINDER=1
export USE_DNSX=1
export USE_HTTPX=1

./subdomain_enum.sh
```

### Example 4: Filtering Results for Security Analysis

```bash
# Extract only HTTPS-accessible subdomains
grep "\[200\]" subdomain_results.txt | awk '{print $1}' > live_https.txt

# Find subdomains with status code 403 (potential WAF/admin panels)
grep "\[403\]" subdomain_results.txt > blocked_endpoints.txt

# Extract all subdomains for further analysis
grep "➜" subdomain_results.txt | sed 's/.*➜ //' > all_subdomains.txt

# Count statistics
total_subdomains=$(grep -c "➜" subdomain_results.txt)
total_live=$(grep -c "\[200\]" subdomain_results.txt)
echo "Total discovered: $total_subdomains, Live (200): $total_live"
```

### Example 5: Integration with Other Security Tools

```bash
# Extract subdomains and pass to vulnerability scanner
grep "➜" subdomain_results.txt | sed 's/.*➜ //' | \
    nmap -iL - -p 80,443 -v > nmap_scan.txt

# Pass results to web vulnerability scanner
grep "➜" subdomain_results.txt | sed 's/.*➜ //' | \
    zaproxy_cli -i - -r zaproxy_report.html

# Extract and test for common misconfigurations
grep "➜" subdomain_results.txt | sed 's/.*➜ //' | \
    while read domain; do
        curl -s "https://$domain/.git/config" | grep -q "repositoryformatversion"
        [ $? -eq 0 ] && echo "$domain: Exposed .git directory"
    done
```

---

## Troubleshooting

### Issue: "Missing tools" Error on First Run

**Symptom:**
```
[-] Missing tools: subfinder assetfinder findomain dnsx httpx anew
[!] Install via: go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
```

**Solution:**

```bash
# Install Go if not present
sudo apt-get install golang-go

# Install all required tools
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
go install -v github.com/tomnomnom/assetfinder@latest
go install -v github.com/Findomain/Findomain@latest
go install -v github.com/projectdiscovery/dnsx/cmd/dnsx@latest
go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
go install -v github.com/tomnomnom/anew@latest

# Verify installation
subfinder -h
assetfinder -h
findomain --help
dnsx -h
httpx -h
anew -h

# All commands should return help text without errors
```

### Issue: "live_urls.txt not found" Error

**Symptom:**
```
[-] Error: live_urls.txt not found!
```

**Solution:**

```bash
# Create the input file
touch live_urls.txt

# Add target domains
cat > live_urls.txt << EOF
example.com
example.org
example.net
EOF

# Verify file exists and has content
cat live_urls.txt

# Run script again
./subdomain_enum.sh
```

### Issue: No Subdomains Discovered (Empty Results)

**Symptom:**
```
Live subdomains: 0
[✗] No live subdomains discovered
```

**Causes & Solutions:**

1. **Invalid Domain Format**
   ```bash
   # Check domain format
   grep "^  ➜" subdomain_results.txt
   
   # Verify with manual tool
   subfinder -d example.com -silent -all | head -5
   ```

2. **DNS Resolution Failures**
   ```bash
   # Test DNS connectivity
   dnsx -l domains.txt -silent -a -resp | head -10
   
   # Check network connectivity
   nslookup example.com
   ping -c 1 8.8.8.8
   ```

3. **Network Connectivity Issues**
   ```bash
   # Verify outbound HTTPS access
   curl -I https://www.google.com
   
   # Check firewall rules
   sudo iptables -L -n | grep OUTPUT
   ```

4. **Tool Configuration Issues**
   ```bash
   # Test each tool individually
   subfinder -d example.com -silent -all
   assetfinder --subs-only example.com
   findomain -t example.com -q
   ```

### Issue: Script Hangs or Times Out

**Symptom:**
```
Script appears to freeze during HTTPX probing
[No output for extended period]
```

**Solutions:**

1. **Reduce Thread Count**
   ```bash
   export THREADS=10  # Default is 50
   ./subdomain_enum.sh
   ```

2. **Increase Timeout Value**
   ```bash
   export TIMEOUT=30  # Default is 10 seconds
   ./subdomain_enum.sh
   ```

3. **Disable Slow Enumeration Tools**
   ```bash
   export USE_AMASS=0
   ./subdomain_enum.sh
   ```

4. **Check Resource Availability**
   ```bash
   # Monitor system resources
   top -b -n 1 | head -20
   df -h
   free -h
   ```

### Issue: Permission Denied When Running Script

**Symptom:**
```
bash: ./subdomain_enum.sh: Permission denied
```

**Solution:**

```bash
# Make script executable
chmod +x subdomain_enum.sh

# Verify permissions
ls -l subdomain_enum.sh
# Should show: -rwxr-xr-x

# Run again
./subdomain_enum.sh
```

### Issue: Invalid Domain Errors on Valid Targets

**Symptom:**
```
[1/10] ✗ Invalid: example.com (invalid)
```

**Debug & Solution:**

```bash
# Test domain extraction function manually
domain="https://www.example.com:8080/path"
clean=$(echo "$domain" | tr '[:upper:]' '[:lower:]' | \
  sed -E 's/^(https?|ftp)[[:space:]]*:\/\/|^(https?|ftp)[[:space:]]+//g' | \
  sed -E 's/^www[0-9]*\.//' | sed -E 's#/.*$##' | sed -E 's#:.*$##')
echo "Extracted: $clean"

# Verify regex validation
[[ "$clean" =~ ^([a-z0-9]([a-z0-9-]{0,61}[a-z0-9])?\.)+[a-z]{2,}$ ]] && echo "Valid" || echo "Invalid"
```

### Issue: Output File Not Generated

**Symptom:**
```
subdomain_results.txt not created or is empty
```

**Debugging:**

```bash
# Check current directory
pwd

# List files
ls -la

# Verify output file parameter
echo $OUTPUT_FILE

# Check disk space
df -h

# Test file writing permissions
touch test_write.txt
cat >> test_write.txt << EOF
test content
EOF
rm test_write.txt

# Run with explicit output path
./subdomain_enum.sh
ls -l subdomain_results.txt
wc -l subdomain_results.txt
```

---

## Performance Considerations

### Execution Time Estimates

| Scenario | Domains | Est. Duration | Factors |
|----------|---------|---------------|---------|
| Single small domain | 1 | 2-5 min | Subdomain count, DNS TTL |
| Single large domain | 1 | 5-15 min | High subdomain count, network latency |
| Batch (10 domains) | 10 | 20-50 min | Enumeration tool speed, concurrent probing |
| Large batch (50+) | 50+ | 2-6 hours | Thread count, tool efficiency |

### Optimization Strategies

#### 1. Tool Selection

```bash
# Fastest configuration (sacrifice some coverage)
export USE_SUBFINDER=1
export USE_ASSETFINDER=0  # Slower
export USE_FINDOMAIN=1
export USE_AMASS=0        # Much slower
```

#### 2. Thread Tuning

```bash
# For high-performance systems (8+ CPU cores, 16GB+ RAM)
export THREADS=200
export TIMEOUT=20

# For resource-limited systems
export THREADS=5
export TIMEOUT=10
```

#### 3. Batch Processing Optimization

```bash
# Process large domain lists in parallel
# Split input file
split -l 20 large_domain_list.txt targets_batch_

# Process each batch in background
for batch in targets_batch_*; do
    INPUT_FILE="$batch" \
    OUTPUT_FILE="results_${batch}.txt" \
    ./subdomain_enum.sh &
done

# Wait for completion
wait

# Merge results
cat results_targets_batch_*.txt >> final_results.txt
```

#### 4. Caching Strategy

```bash
# Store results for known domains
# Implement lookup before enumeration
if grep -q "^$domain$" domain_cache.txt; then
    grep "$domain" cached_results.txt >> subdomain_results.txt
    continue
fi

# Add new results to cache
grep "TARGET DOMAIN: $domain" subdomain_results.txt >> domain_cache.txt
```

### Resource Requirements by Scale

| Scale | CPU | RAM | Disk | Network |
|-------|-----|-----|------|---------|
| 1-10 domains | 2 cores | 2GB | 100MB | 1Mbps |
| 11-50 domains | 4 cores | 4GB | 500MB | 5Mbps |
| 51-200 domains | 8 cores | 8GB | 2GB | 10Mbps |
| 200+ domains | 16+ cores | 16GB+ | 5GB+ | 50Mbps+ |

---

## Security & Legal

### Authorization & Compliance

⚠️ **CRITICAL LEGAL NOTICE**

This tool is designed **exclusively for authorized security testing** on systems you own or have explicit written permission to test. Unauthorized access to computer systems is **illegal** under:

- Computer Fraud and Abuse Act (CFAA) - United States
- Computer Misuse Act - United Kingdom
- Digital Millennium Copyright Act (DMCA)
- Similar laws in virtually all jurisdictions

### Authorized Use Cases

✓ **Authorized Penetration Testing**
- Engagements with signed contracts and scope definitions
- Authorized by system owners or their representatives
- Within defined target scope and timeframe

✓ **Bug Bounty Programs**
- Participation in official bug bounty platforms
- Adherence to program rules and scope
- Responsible disclosure practices

✓ **Security Research**
- Academic research with proper authorization
- Publication with responsible disclosure timeline
- Institutional review board approval when required

✓ **Internal Security Assessments**
- Authorized testing on organization-owned systems
- Proper documentation and approval chains
- Compliance with organizational security policies

### Prohibited Use Cases

✗ **Unauthorized Access**
- Testing systems without explicit permission
- Circumventing security controls
- Accessing confidential or private information

✗ **Malicious Activities**
- Disrupting services or systems
- Data theft or exfiltration
- Extortion or blackmail

✗ **Violation of Terms of Service**
- Testing systems protected by ToS restrictions
- Abuse of free services or trials
- Circumventing access controls

### Data Protection & Privacy

**Confidentiality:**
- Treat all discovered information as confidential
- Securely store enumeration results
- Restrict access to authorized personnel only
- Delete results after engagement completion

**Compliance:**
- Respect privacy regulations (GDPR, CCPA, etc.)
- Do not collect or store personal data
- Follow data protection laws in your jurisdiction
- Document and report findings appropriately

### Responsible Disclosure

When vulnerabilities are discovered:

1. **Establish Contact:** Identify authorized security contacts
2. **Report Timeline:** Provide reasonable time to remediate (typically 90 days)
3. **Information Sharing:** Disclose only to authorized parties
4. **No Public Disclosure:** Avoid public disclosure during remediation period
5. **Follow Guidance:** Adhere to organization's disclosure policy

### Disclaimer

This software is provided **"AS-IS"** without warranties or guarantees. The authors and contributors:

- Accept no liability for misuse or unauthorized access
- Do not endorse illegal activities
- Assume users understand applicable laws
- Expect responsible, authorized use only

**By using this tool, you acknowledge:**
- You understand the legal implications
- You have proper authorization
- You will use it only for authorized purposes
- You accept full legal responsibility for your actions

---

## Contributing

### Contribution Guidelines

Contributions are welcome! Areas for enhancement:

1. **Additional Enumeration Sources**
   - Integration of new passive enumeration tools
   - Support for alternative DNS providers
   - Extended certificate source support

2. **Output Formats**
   - JSON export functionality
   - CSV format support
   - Database integration

3. **Performance Improvements**
   - Parallel domain processing
   - Result caching mechanisms
   - Memory optimization

4. **Feature Requests**
   - Custom filtering options
   - Integration with vulnerability databases
   - Automated reporting templates

### Submission Process

```bash
# Fork repository
git clone https://github.com/YourUsername/LIVE-SUBDOMAINS-ENUMERATION.git

# Create feature branch
git checkout -b feature/your-enhancement

# Implement changes with clear commit messages
git commit -m "Add feature: description of changes"

# Push to your fork
git push origin feature/your-enhancement

# Submit pull request with detailed description
# - Problem statement
# - Solution overview
# - Testing performed
# - Backward compatibility notes
```

### Code Quality Standards

- Follow existing code style and conventions
- Include error handling for edge cases
- Test with multiple input formats
- Document new features and functions
- Ensure backward compatibility

---

## Author

**Syed M Danish**
- Student ID: 64999
- Course: Penetration Testing (PN Test A2)
- Academic Institution: [Course Provider]
- Contact: [Email/GitHub]

### Acknowledgments

This project integrates work from multiple open-source security research projects:

- **ProjectDiscovery** - Subfinder, DNSX, HTTPX
- **Tom Nomnom** - Assetfinder, ANEW
- **Findomain Project** - Findomain enumeration tool
- **OWASP** - Security testing methodologies
- **Shodan** - Infrastructure search capabilities

### References & Resources

- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [ProjectDiscovery Tools](https://projectdiscovery.io/)
- [Shodan Webinars](https://help.shodan.io/)
- [RFC 1123: Requirements for Internet Hosts](https://tools.ietf.org/html/rfc1123)

---

## License

This project is provided for **educational purposes** in an academic penetration testing context. Use is governed by:

- Academic institution policies
- Applicable computer fraud and abuse laws
- Cybersecurity ethics standards
- Responsible disclosure principles

**Educational Use Only** - Unauthorized access to computer systems is illegal.

---

## Quick Reference

### Common Commands

```bash
# Full execution with default config
./subdomain_enum.sh

# Custom thread count
export THREADS=100 && ./subdomain_enum.sh

# Skip slow enumeration tools
export USE_AMASS=0 && ./subdomain_enum.sh

# Custom input/output files
export INPUT_FILE="targets.txt" OUTPUT_FILE="findings.txt" && ./subdomain_enum.sh

# View results
cat subdomain_results.txt

# Extract live subdomains only
grep "➜" subdomain_results.txt | sed 's/.*➜ //'

# Count results
wc -l subdomain_results.txt
```

### File Locations

| File | Purpose | Default Location |
|------|---------|------------------|
| Input domains | Target specification | `./live_urls.txt` |
| Output report | Enumeration results | `./subdomain_results.txt` |
| Temporary files | Working directory | `/tmp/subdomain_enum_$$` |
| Script | Main executable | `./subdomain_enum.sh` |

### Troubleshooting Checklist

- [ ] Script has execute permissions (`chmod +x`)
- [ ] All dependencies installed and in PATH
- [ ] Input file exists and contains valid domains
- [ ] Sufficient disk space available
- [ ] Network connectivity verified
- [ ] No permission errors on output directory
- [ ] Resource availability adequate for scale

---

**Version:** 3.0  
**Last Updated:** 2024-01-15  
**Status:** Production Ready  
**Educational Purpose:** Penetration Testing Course Assignment

---

