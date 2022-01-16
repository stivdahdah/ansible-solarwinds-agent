# Ansible Role To Install SolarWinds Agent
No Galaxy Modules are requirements.

This playbook will help you to install SolarWinds Agent on RHEL/CentOS and Debian/Ubuntu.

First, you should generate the agent installation command from SolarWinds. This command should be placed in the YAML file: 
Please follow the below steps (You need to copy the command from Step-8):
https://documentation.solarwinds.com/en/success_center/orionplatform/content/core-deploy-a-linux-agent-manually.htm

Note: RHEL/CentOS command is different from Debian/Ubuntu. I would suggest that you have one playbook for each distro. 

In 

---
- name: Installing solawrWinds Agent
  hosts: all
  become: true
  tasks:
    - name: Using the genrated command line to deploy SolarWinds agent
      shell: PLACE SOLARWINDS GENERATE COMMAND LINE HERE
      register: output

    - debug:
        var: output
