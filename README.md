# Implantação Automatizada do VMware Cloud Foundation em Home Lab

Instalação e Configuração do VyOS

Download OVA e fazer o deploy no Cluster de VMware

https://vyos.net/get/nightly-builds/

https://github.com/vyos/vyos-nightly-build/releases/download/1.5-rolling-202411070006/vyos-1.5-rolling-202411070006-generic-amd64.iso

Criar um PG isolado (VSS/VDS) e configurar para passar todas as VLAN nela. Basta colocar a VLAN ID 4095
Nesse mesmo PG, precisa habilitar os recursos de Security abaixo:
Promiscuous mode - Accept
MAC address changes - Accept
Forged transmits - Accept

Conectar a primeira vNIC(public) para o PG da sua rede local (LAN), em meu caso será: pg-mgmt
Adicione a segunda vNIC(internal) e conecte ela para o PG isolado que voce criou no passo anterior, em meu caso é: pg-vyos
Agora voce precisa configurar as Advanced Settings abaixo nessa VM:
cdrom.showIsoLockWarning = FALSE
msg.autoanswer = TRUE (required for Automation to work as CD-ROM is locked during VyOS installation)
Ligue a VM

Abra o repositorio https://github.com/franklinleandro/VyOS-automacao e baixar os arquivos abaixo:
VyOS.psm1 - Modulo com a função de interagir com o vyOS
vyos.template - Configurações que serão aplicadas no vyOS

Executar os comandos:
Connect-VIServer vmb-vce01.vmbeer.local -User 'administrator@vsphere.local' -Pass '#BAfrjhtt@25'
cd C:\Temp\OVA\VyOS-automacao-main
Import-Module ./VyOS.psm1
New-VyOSInstallation -VMName vmb-vyos01 -ManagementPassword '#BAfrjhtt@25'
New-VyOSConfiguration -VMName vmb-vyos01 -ConfigFile vyos.template -ManagementAddress 192.168.68.236/24 -ManagementGateway 192.168.68.1 -ManagementDNSDomain vmbeer.local -ManagementDNSServer 192.168.68.239 -ManagementPassword '#BAfrjhtt@25'



Para checar as interfaces criadas, basta digitar:
sh interfaces

Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down
Interface    IP Address         MAC                VRF        MTU  S/L    Description
-----------  -----------------  -----------------  -------  -----  -----  -----------------------
eth0         192.168.68.236/24  00:50:56:9e:ef:a8  default   1500  u/u    pg-mgmt internet
eth1         -                  00:50:56:9e:12:89  default   9000  u/u
eth1.1631    172.16.31.1/24     00:50:56:9e:12:89  default   9000  u/u    pg-vyos-mgmt-1631
eth1.1632    172.16.32.1/24     00:50:56:9e:12:89  default   9000  u/u    pg-vyos-vmotion-1632
eth1.1633    172.16.33.1/24     00:50:56:9e:12:89  default   9000  u/u    pg-vyos-vsan-1633
eth1.1634    172.16.34.1/24     00:50:56:9e:12:89  default   9000  u/u    pg-vyos-overlay-1634
eth1.2711    172.27.11.1/24     00:50:56:9e:12:89  default   9000  u/u    pg-vyos-route-up01-2711
eth1.2712    172.27.12.1/24     00:50:56:9e:12:89  default   9000  u/u    pg-vyos-route-up02-2712
eth1.2713    172.27.13.1/24     00:50:56:9e:12:89  default   9000  u/u    pg-vyos-edge-2713
lo           127.0.0.1/8        00:00:00:00:00:00  default  65536  u/u
             ::1/128

Do VyOS, checar o ping para a ponte:
ping 192.168.68.238

Da ponte, pingar a interface eth1 do VyOS:
ping 192.168.68.236

Add rota na ponte
route -p add 192.168.68.0 mask 255.255.255.0 192.168.8.240

Checar o BGP
configure
show protocols bgp





## Observação

Este repositório foi criado a partir da cópia do [repositório](https://github.com/lamw/vcf-automated-lab-deployment) do William Lam, então todos os créditos pertencem a ele.
Eu realizei ajustes de acordo com as minhas necessidades, bem como ajustei e traduzi o conteúdo do README para facilitar o entendimento daqueles que possuem dificuldades no idioma Inglês.

## Descrição

Este script torna muito fácil para qualquer pessoa implantar um VMware Cloud Foundation (VCF) "básico" em um ambiente de laboratório nested para fins de aprendizado e educação. Todos os componentes VMware necessários (VMs do ESXi e Cloud Builder) são automaticamente implantados e configurados para permitir que o VCF seja implantado e configurado usando o VMware Cloud Builder. Para mais informações, você pode consultar a documentação oficial do [VMware Cloud Foundation](https://docs.vmware.com/en/VMware-Cloud-Foundation/index.html).

Abaixo está um diagrama do que é implantado como parte da solução, e você só precisa ter um ambiente vSphere existente rodando, gerenciado pelo vCenter Server e com recursos suficientes (CPU, Memória e Armazenamento) para implantar este laboratório "nested". Para habilitação do VCF (operação pós-implantação), consulte a seção [Execução de Exemplo](#sample-execution) abaixo.
