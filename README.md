# tls-version-scanner-auto-loop-1h8kr

## Overview

This n8n workflow periodically retrieves a list of IP addresses from a Google Sheet, identifies those that haven't been scanned for TLS versions, performs a TLS scan using nmap and curl, parses the results, and then updates the Google Sheet with the detected TLS information. It's designed to run continuously, ensuring the TLS status of listed IPs is regularly checked and maintained.

## Features

- Scheduled execution for continuous scanning
- Reads target IPs from a Google Sheet
- Filters out already-scanned IPs to optimize resource usage
- Performs TLS version detection using 'nmap' and 'curl' commands
- Parses and extracts TLS version and cipher information from scan outputs
- Updates the Google Sheet with the scan results
- Gracefully handles scenarios where no IPs require scanning

## Services Used

- Google Sheets
- Local system shell (for 'nmap' and 'curl')

## Trigger

The workflow is triggered by a schedule every 1 minute.

## Prerequisites

- An n8n instance capable of executing shell commands (e.g., Docker container with 'nmap' and 'curl' installed, or bare metal install)
- Network access from the n8n host to target IPs on port 443
- A Google Sheet with at least two columns: 'IPs' and 'TLS Versions'

## Credentials

- Google Sheets OAuth2 API account (named 'Google Sheets account')

## Configuration

1. Import the workflow JSON into your n8n instance.
2. Configure the 'Google Sheets account' credential with appropriate OAuth2 access to your Google Drive/Sheets.
3. Ensure the Google Sheets nodes ('Get IPs from Sheet', 'Update TLS in Sheet') are configured with the correct 'Document ID' and 'Sheet Name' (currently '1xR9eC9-0QtWOFQOuA9dXRVXBxK1w_EEBfOEk63XXl2o' and 'Sheet3' with gid '297856582').
4. Verify that the n8n execution environment has 'nmap' and 'curl' installed and accessible from the command line (e.g., `apt-get install nmap curl` on Debian/Ubuntu).
5. Ensure the Google Sheet column headers match those expected by the workflow: 'IPs' and 'TLS Versions'.

## Usage

1. Activate the workflow in n8n.
2. Populate the 'IPs' column in your designated Google Sheet with the IP addresses you wish to scan.
3. The workflow will automatically run every minute, fetching unscanned IPs, performing the TLS check, and updating the 'TLS Versions' column.
4. Monitor the workflow execution logs in n8n for any errors or 'All Done' messages.
5. Review the 'TLS Versions' column in your Google Sheet to see the scan results.

## Troubleshooting

- 'Scan failed' or 'Host unreachable': Check network connectivity from n8n host to target IP and ensure port 443 is not blocked by firewalls.
- 'Port 443 closed/filtered': The target host either has port 443 closed or a firewall is blocking access; verify target server configuration.
- 'Port open but no TLS detected': HTTPS might not be configured correctly on the target, or curl failed to negotiate TLS (check target web server).
- Google Sheets errors: Verify credential validity, sheet ID, sheet name, and column headers ('IPs', 'TLS Versions').
- nmap/curl command errors: Confirm 'nmap' and 'curl' are installed on the n8n host and the n8n user has execution permissions.
- 'Filter Unscanned IPs' always returning 'All Done': Ensure 'TLS Versions' column in the sheet is empty or contains 'empty' for IPs needing a scan.

## Security Notes

- The 'Execute Command' node runs shell commands; ensure the n8n environment is secured against command injection and that only trusted commands are executed.
- Scanning IP addresses without explicit permission can be illegal or violate terms of service; ensure you have authorization for all target IPs.
- Google Sheets credentials should be properly scoped to only necessary permissions and secured.
- Be aware that 'nmap' and 'curl' can generate network traffic that might be detected by IDS/IPS systems.
