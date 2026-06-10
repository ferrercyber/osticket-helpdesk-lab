# osTicket HelpDesk Deployment Lab

Self-hosted osTicket HelpDesk system deployed on Ubuntu Server 26.04 in a local virtualized environment. Demonstrates full LAMP stack deployment, ticketing system administration, SLA configuration, knowledge base authoring, and end-to-end Tier 1 ticket lifecycle management.

**Platform:** osTicket v1.18.1 on Ubuntu Server 26.04 LTS (VirtualBox)
**Stack:** Apache 2, MySQL, PHP 8.5
**Company simulated:** FerrerCyber Corp — the same `ferrercyber.local` environment from my [AWS Active Directory lab](https://github.com/ferrercyber/aws-active-directory-lab)

## What I Built

I deployed and configured a complete HelpDesk ticketing system from scratch — starting with a bare Ubuntu Server VM, building the LAMP stack, installing and hardening osTicket, and then configuring it into a realistic multi-tier support environment. The result is a working ticketing system with departments, SLA policies, help topics, agents, canned responses, a knowledge base, and a fully documented Tier 1 ticket resolution workflow.

This lab pairs with my AWS Active Directory lab. That project is the infrastructure being supported; this one is the support tooling that sits on top of it.


---

## Step 1: Deployed Ubuntu Server in VirtualBox

I installed Ubuntu Server 26.04 LTS in a VirtualBox VM configured with a bridged network adapter so the server is reachable from the host machine. After install, I confirmed the network configuration and noted the server's IP address for SSH and web access.

<img width="1416" height="945" alt="IP A Output" src="https://github.com/user-attachments/assets/a5221974-78ff-410f-ae6e-e1ce6ceda8e1" />

---

## Step 2: Updated the System

Before installing any services, I brought the system fully up to date with `sudo apt update && sudo apt upgrade`.

<img width="1586" height="989" alt="APT Upgrade" src="https://github.com/user-attachments/assets/5e06415d-438f-4461-8867-0ea4c9742fa0" />

---

## Step 3: Installed and Verified Apache

I installed Apache 2 and confirmed it was serving correctly by loading the default Apache page from the host browser. Seeing the "It works!" page confirms the web server is running and reachable over the network.

<img width="1896" height="707" alt="Apache2 Default Page" src="https://github.com/user-attachments/assets/e318622f-6ac1-4dac-a02f-7de3aa3add5a" />

---

## Step 4: Installed MySQL and PHP, Secured the Database

I installed MySQL and ran `mysql_secure_installation` to harden it — removing anonymous users, disabling remote root login, and dropping the test database. I then installed PHP 8.5 and the required extensions.

<img width="1431" height="988" alt="ApacheSQLPHP Outputs" src="https://github.com/user-attachments/assets/2903d40f-bb3f-41d8-ab0f-0e83f39d4875" />

---

## Step 5: Created the osTicket Database

I created a dedicated MySQL database (`osticket`) and a dedicated database user with privileges scoped only to that database — following the principle of least privilege rather than letting the application use the MySQL root account.

```sql
CREATE DATABASE osticket;
CREATE USER 'osticket_user'@'localhost' IDENTIFIED BY '<password>';
GRANT ALL PRIVILEGES ON osticket.* TO 'osticket_user'@'localhost';
FLUSH PRIVILEGES;
```

---

## Step 6: Configured Apache and Launched the osTicket Installer

After downloading osTicket, setting file permissions, and creating an Apache virtual host, I confirmed the installer's prerequisite check passed. All required components were green.

<img width="979" height="846" alt="osTicket Welcome" src="https://github.com/user-attachments/assets/09b2b4dc-9b13-47c9-9719-b36e0a4c1c34" />

---

## Step 7: Completed the Installation

The web installer connected to the database, created the osTicket schema, and completed successfully.

<img width="941" height="683" alt="osTicket Install" src="https://github.com/user-attachments/assets/549fb9d8-22a5-4199-b30e-10900acf1688" />

After install, I performed the post-install hardening steps: locking down the config file permissions (`chmod 0644 ost-config.php`) and deleting the `/setup` directory so the installer could not be re-run by anyone who found the URL.

---

## Step 8: Configured Departments

I logged into the Staff Control Panel and set up a three-tier support structure: Tier 1 Support (front-line), Tier 2 Support (escalations), and Network & Infrastructure (network/server-level issues).

<img width="1059" height="519" alt="Staff Control Panel" src="https://github.com/user-attachments/assets/77795026-6bb0-413e-b958-0b6246fa6f14" />

---

## Step 9: Configured Help Topics with Priority and Routing

I created 11 help topics that map to the categories a real Tier 1 tech sees in a typical week. Each topic is mapped to the correct department and assigned a priority — password resets and lockouts are High, printer issues are Low, network issues route to the Network & Infrastructure team.

<img width="1066" height="728" alt="Help Topics" src="https://github.com/user-attachments/assets/ae41526a-7121-4210-8c5c-ca7b522fb345" />

---

## Step 10: Created Agents with Role-Based Department Assignment

I created a multi-agent environment: the admin account plus three agents assigned to the appropriate departments. Agent accounts have no administrator privileges — only the admin account does, following least-privilege access.

<img width="1023" height="502" alt="4 Agents" src="https://github.com/user-attachments/assets/b3aed77d-473b-497b-8fcc-50935cfa9989" />

---

## Step 11: Created Canned Responses

I built three canned responses for the most common Tier 1 tickets — password reset confirmation, account unlock confirmation, and identity verification request. Each uses osTicket template variables for personalization, with manual placeholders for security-sensitive data like temporary passwords.

<img width="1001" height="431" alt="Canned Responses" src="https://github.com/user-attachments/assets/84d6abc6-363e-44eb-b5fb-d6a2bbdd8c26" />

---

## Step 12: Built the Knowledge Base — Public Articles

I authored five public self-service articles covering the most common end-user questions. These are visible on the customer-facing portal so users can resolve simple issues themselves before ever opening a ticket.

<img width="1009" height="598" alt="Common Articles" src="https://github.com/user-attachments/assets/aab04093-3d7c-444f-b27e-2a59f6a8da6a" />

---

## Step 13: Built the Knowledge Base — Internal Runbooks

I also authored two internal Tier 1 runbooks (private, agent-only) documenting the exact step-by-step procedure for password resets and account unlocks — including the identity-verification requirement before any account change.

<img width="1020" height="489" alt="Admin Articles" src="https://github.com/user-attachments/assets/a4368063-edce-4421-95ab-56827020c74b" />

---

## Step 14: Tested the End-User Ticket Submission

I submitted a ticket through the customer-facing portal as a fictional end user (Maria Lopez) to validate the full user experience, confirming the support center accepts and acknowledges new tickets.

<img width="879" height="546" alt="Ticket Created" src="https://github.com/user-attachments/assets/d253ba3d-1999-49cc-8269-13a08cb24a1f" />

---

## Step 15: Worked the Ticket — Full Tier 1 Lifecycle

Finally, I worked the ticket through the complete Tier 1 lifecycle from the agent side:

1. **Verified user identity** via phone callback (logged as an internal note, per the runbook)
2. **Reset the password** and replied using the canned response with a temporary password
3. **Logged confirmation** that the user successfully logged in
4. **Documented the resolution** referencing the internal Tier 1 runbook
5. **Closed the ticket** with status Resolved

End-to-end resolution time: 10 minutes.

<img width="977" height="1207" alt="Maria Lopez Ticket Resolved" src="https://github.com/user-attachments/assets/24401485-c1ff-4e28-bb11-36cb2668ce7c" />

---

## Security Notes

- MySQL application user scoped with least-privilege grants, separate from the root account
- `mysql_secure_installation` run to remove anonymous users, disable remote root login, and drop the test database
- osTicket config file permissions locked down and `/setup` directory removed after install
- Agent accounts created without administrator privileges
- All screenshots reviewed before publishing; the only network detail shown is a private (RFC 1918) LAN address, which is not internet-routable

