# Ansible Role To Install SolarWinds Agent
No Galaxy modules are required.

This playbook will help you to install SolarWinds Agent on RHEL/CentOS and Debian/Ubuntu.

First, you should generate the agent installation command from SolarWinds. This command should be placed in the YAML file: 
Please follow the below steps (You need to copy the command from Step-8):
https://documentation.solarwinds.com/en/success_center/orionplatform/content/core-deploy-a-linux-agent-manually.htm

Requirments:
- The network connection between your agent and SolarWinds should be Allowed since this playbook will fetch the installtion from the SolarWinds server directly.

Note: RHEL/CentOS generated command is different from Debian/Ubuntu. I would suggest creating a playbook for each distro. 


Editing the Playbook - solarwinds_agent_Installation.yml

Once the command line is generated copy the command line and place it in Lice-7 after "shell: "

In my case the gerated shell was:
```
([ "$AUTO_SHELL" = "" ] && AUTO_SHELL=`bash -c 'echo bash' 2>/dev/null || ksh -c 'echo ksh' 2>/dev/null || ( >&2 echo "Unable to find supported shell (bash or ksh). Please, define full path to your shell in environment variable AUTO_SHELL and retry installation" && exit 2 )`; "${AUTO_SHELL}" -c "AUTO_SHELL='${AUTO_SHELL}'; export AUTO_SHELL; PERL_LWP_SSL_VERIFY_HOSTNAME=0; export PERL_LWP_SSL_VERIFY_HOSTNAME; download_with_perl() { perl -MLWP::Simple -e '\$c=get \$ARGV[0]; exit 1 unless length \$c; print \$c' \"\$1\"; }; download_with_wget() { wget -O - --no-check-certificate --tries=1 --read-timeout=30 \"\$1\"; }; download_with_curl() { curl --insecure --fail --retry 1 \"\$1\"; }; "'if [ "${DT}" != "" ]; then >&2 echo "Using download tool \"${DT}\" from environment"; elif wget --version 2>/dev/null >/dev/null; then DT=wget; elif curl file:///dev/null 2>/dev/null >/dev/null; then DT=curl; elif perl -MLWP::Simple -e "" 2>/dev/null; then DT=perl; else >&2 echo "Cannot find download tool - please install wget, curl or perl with module LWP::Simple or use other installation method"; exit 18; fi; export DT; [ "${DEBUG}" != "" ] && >&2 printf "AUTO_SHELL=\"%s\"\nDT=\"%s\"\n" "${AUTO_SHELL}" "${DT}"; x() { P=$1; shift; >&2 printf "\nDownloading installation data from Orion Poller..."; while [ "$#" -gt "0" ]; do u=$1; shift; printf "URL=\"%s\"\nexport URL\n" "$u"; if download_with_$DT $u$P 2>/dev/null; then >&2 echo; return; fi; >&2 printf "."; done; >&2 echo; >&2 echo "Unable to connect to Orion Poller to download agent package. Please use an alternate deployment method, such as Add Node wizard."; >&2 echo "See http://www.solarwinds.com/documentation/helpLoader.aspx?lang=en&topic=OrionAgentDeployAgentTop"; exit 3; }; X=`x /Orion/AgentManagement/DownloadLinuxOnlineInstallScript.ashx?requestId=dea13dbf-02dd-44f3-b730-8e7ba084f875 https://steven https://192.168.1.21 https://steven.steven.local https://steven:443`||exit $?; "${AUTO_SHELL}" -c "[ \"${DEBUG}\" != \"\" ] && set -x; ${X}"; ')
```
 
My playbook "solarwinds_agent_Installation.yml" will be:
```
---
- name: Installing solawrWinds Agent on all the managed nodes
  hosts: all
  become: true
  tasks:
    - name: Using the 1 line command to deploy solarwind agents
      shell: ([ "$AUTO_SHELL" = "" ] && AUTO_SHELL=`bash -c 'echo bash' 2>/dev/null || ksh -c 'echo ksh' 2>/dev/null || ( >&2 echo "Unable to find supported shell (bash or ksh). Please, define full path to your shell in environment variable AUTO_SHELL and retry installation" && exit 2 )`; "${AUTO_SHELL}" -c "AUTO_SHELL='${AUTO_SHELL}'; export AUTO_SHELL; PERL_LWP_SSL_VERIFY_HOSTNAME=0; export PERL_LWP_SSL_VERIFY_HOSTNAME; download_with_perl() { perl -MLWP::Simple -e '\$c=get \$ARGV[0]; exit 1 unless length \$c; print \$c' \"\$1\"; }; download_with_wget() { wget -O - --no-check-certificate --tries=1 --read-timeout=30 \"\$1\"; }; download_with_curl() { curl --insecure --fail --retry 1 \"\$1\"; }; "'if [ "${DT}" != "" ]; then >&2 echo "Using download tool \"${DT}\" from environment"; elif wget --version 2>/dev/null >/dev/null; then DT=wget; elif curl file:///dev/null 2>/dev/null >/dev/null; then DT=curl; elif perl -MLWP::Simple -e "" 2>/dev/null; then DT=perl; else >&2 echo "Cannot find download tool - please install wget, curl or perl with module LWP::Simple or use other installation method"; exit 18; fi; export DT; [ "${DEBUG}" != "" ] && >&2 printf "AUTO_SHELL=\"%s\"\nDT=\"%s\"\n" "${AUTO_SHELL}" "${DT}"; x() { P=$1; shift; >&2 printf "\nDownloading installation data from Orion Poller..."; while [ "$#" -gt "0" ]; do u=$1; shift; printf "URL=\"%s\"\nexport URL\n" "$u"; if download_with_$DT $u$P 2>/dev/null; then >&2 echo; return; fi; >&2 printf "."; done; >&2 echo; >&2 echo "Unable to connect to Orion Poller to download agent package. Please use an alternate deployment method, such as Add Node wizard."; >&2 echo "See http://www.solarwinds.com/documentation/helpLoader.aspx?lang=en&topic=OrionAgentDeployAgentTop"; exit 3; }; X=`x /Orion/AgentManagement/DownloadLinuxOnlineInstallScript.ashx?requestId=dea13dbf-02dd-44f3-b730-8e7ba084f875 https://steven https://192.168.1.21 https://steven.steven.local https://steven:443`||exit $?; "${AUTO_SHELL}" -c "[ \"${DEBUG}\" != \"\" ] && set -x; ${X}"; ')
      register: output

    - debug:
        var: output
```
