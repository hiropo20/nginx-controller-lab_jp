# Getting Started

==========================

Please use the "**NGINX Controller 3.x Demo / Lab**" Blueprint for this lab exercise

The infrastructure is all ready for you.
Controller, nginx plus, and agents are all installed.  Controller is pre-configured.

The JumpHost contains the Postman collection for the demo, which also outlines steps through a workflow and the settings in the JSON body that you can use through either the GUI or API.

=== *Note* ===
*All work for this lab will be performed exclusively from the Windows
jumphost. No installation or interaction with your local system is
required.*

## Credentials

===========================

- Windows Jumphost:
  - .\administrator / BZ8D8MCVR

- NGINX Controller:
  - admin@acmefinancial.net / Admin123!@#  ( aka David )
  - retail-dev@acmefinancial.net / Admin123!@#   ( aka Samantha )
  - lending-admin@acmefinancial.net / Admin123!@#  ( aka Olivia )

- Trading Application:
  - admin / iloveblue

## Lab Topology

===========================

Network Topology:

- three networks
  - management
  - production
  - development

Infrastructure Machines:

- NGINX data plane infrastructure
  - 4 production
  - 2 development
  - 1 developer portal
- Windows jumphost
- Controller
- Database
- load generator (WIP)
- Ansible Tower (WIP)

- Application hosts:
  - appcontainer-1:
    - 'reflector'
        HTTP 8000 - 8999
        HTTPS 4000 - 4999
        HTTP always unhealthy 5800 - 5899
        HTTPS always unhealthy 5400 - 5499
        HTTP random health 6100 - 6299
        HTTPS random health 6200 - 6299
    - 'Arcadia Financial'
        mainapp 9801
        backend 9803
        app2 9804
        app3 9805
    - 'recruiting'
        careers west 3004
  - appcontainer-2:
    - 'reflector'
        HTTP 8000 - 8999
        HTTPS 4000 - 4999
        HTTP always unhealthy 5800 - 5899
        HTTPS always unhealthy 5400 - 5499
        HTTP random health 6100 - 6299
        HTTPS random health 6200 - 6299
    - 'recruiting'
        careers east 3004
  - opencart-1 / opencart-2
    - HTTP 80
    - HTTPS 443
    - HTTPS 8443
    - Note: images are broken if transport changes https -> http, but not if transport stays the same https -> https