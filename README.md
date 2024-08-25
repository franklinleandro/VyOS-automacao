# Implantação Automatizada do VMware Cloud Foundation em Home Lab

Import-Module ./VyOS.psm1

New-VyOSInstallation -VMName VyOS -ManagementPassword VMware1!



New-VyOSConfiguration -VMName VyOS -ConfigFile vyos.template -ManagementAddress 192.168.68.160/24 -ManagementGateway 192.168.68.1 -ManagementDNSDomain vmbeer.local -ManagementDNSServer 192.168.68.145 -ManagementJumpHostIP 192.168.68.127 -ManagementPassword VMware1!




## Observação

Este repositório foi criado a partir da cópia do [repositório](https://github.com/lamw/vcf-automated-lab-deployment) do William Lam, então todos os créditos pertencem a ele.
Eu realizei ajustes de acordo com as minhas necessidades, bem como ajustei e traduzi o conteúdo do README para facilitar o entendimento daqueles que possuem dificuldades no idioma Inglês.

## Descrição

Este script torna muito fácil para qualquer pessoa implantar um VMware Cloud Foundation (VCF) "básico" em um ambiente de laboratório nested para fins de aprendizado e educação. Todos os componentes VMware necessários (VMs do ESXi e Cloud Builder) são automaticamente implantados e configurados para permitir que o VCF seja implantado e configurado usando o VMware Cloud Builder. Para mais informações, você pode consultar a documentação oficial do [VMware Cloud Foundation](https://docs.vmware.com/en/VMware-Cloud-Foundation/index.html).

Abaixo está um diagrama do que é implantado como parte da solução, e você só precisa ter um ambiente vSphere existente rodando, gerenciado pelo vCenter Server e com recursos suficientes (CPU, Memória e Armazenamento) para implantar este laboratório "nested". Para habilitação do VCF (operação pós-implantação), consulte a seção [Execução de Exemplo](#sample-execution) abaixo.
