---
title: Kurz – konfigurace sítě kubenet ve službě Azure Kubernetes Service (AKS) pomocí Ansible
description: Naučte se používat Ansible ke konfiguraci sítě kubenet v clusteru Azure Kubernetes Service (AKS).
keywords: Ansible, Azure, DevOps, bash, cloudshellu, PlayBook, AKS, Container, AKS, Kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 949a55fd8c004bc656d02816231c4ebb6dd8f92b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242168"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Kurz: konfigurace sítě kubenet ve službě Azure Kubernetes Service (AKS) pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Pomocí AKS můžete nasadit cluster pomocí následujících síťových modelů:

- [Kubenet](/azure/aks/configure-kubenet) Networks – síťové prostředky se většinou vytvářejí a konfigurují při nasazení clusteru AKS.
- [Síťové rozhraní CNI (Azure Container Networking Interface)](/azure/aks/configure-azure-cni) – cluster AKS je připojený ke stávajícím prostředkům a konfiguracím virtuální sítě.

Další informace o sítích pro aplikace v AKS najdete v tématu [Koncepty sítě pro aplikace v AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření clusteru AKS
> * Konfigurace sítě Azure kubenet

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

Kód PlayBook v této části vytvoří následující prostředky Azure:

- Virtuální síť
- Podsíť v rámci virtuální sítě

Uložte následující ukázkový playbook jako `vnet.yml`:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Vytvoření clusteru AKS ve virtuální síti

Kód PlayBook v této části vytvoří cluster AKS v rámci virtuální sítě. 

Uložte následující ukázkový playbook jako `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

Tady jsou některé klíčové poznámky, které je potřeba vzít v úvahu při práci s ukázkovým PlayBook:

- K nalezení podporované verze použijte modul `azure_rm_aks_version`.
- @No__t-0 je podsíť vytvořená v předchozí části.
- @No__t-0 definuje vlastnosti pro modul plug-in kubenet sítě.
- @No__t-0 slouží k přiřazení interních služeb v clusteru AKS k IP adrese. Tento rozsah IP adres by měl být adresní prostor, který se v síti nepoužívá jinde. 
- Adresa `dns_service_ip` by měla být adresa ". 10" rozsahu IP adres služby.
- @No__t-0 by měl být velký adresní prostor, který se ve vašem síťovém prostředí nepoužívá jinde. Rozsah adres musí být dostatečně velký, aby odpovídal počtu uzlů, na které očekáváte horizontální navýšení kapacity. Po nasazení clusteru už tento rozsah adres nemůžete změnit.
- Rozsah IP adres pod se používá k přiřazení adresního prostoru/24 k jednotlivým uzlům v clusteru. V následujícím příkladu `pod_cidr` 192.168.0.0/16 přiřadí první uzel 192.168.0.0/24, druhý uzel 192.168.1.0/24 a třetí uzel 192.168.2.0/24.
- Při škálování a upgradech clusteru Azure stále přiřazuje každému novému uzlu rozsah IP adres pod.
- PlayBook načítá `ssh_key` z `~/.ssh/id_rsa.pub`. Pokud ho upravíte, použijte jednořádkový formát začínající na "SSH-RSA" (bez uvozovek).
- Hodnoty `client_id` a `client_secret` jsou načteny z `~/.azure/credentials`, což je výchozí soubor přihlašovacích údajů. Tyto hodnoty můžete nastavit na instanční objekt nebo načíst tyto hodnoty z proměnných prostředí:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Přidružení síťových prostředků

Při vytváření clusteru AKS se vytvoří skupina zabezpečení sítě a směrovací tabulka. Tyto prostředky jsou spravované nástrojem AKS a při vytváření a vystavování služeb se aktualizují. Následujícím způsobem přidružte skupinu zabezpečení sítě a směrovací tabulku k podsíti virtuální sítě. 

Následující PlayBook uložte jako `associate.yml`.

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

Tady jsou některé klíčové poznámky, které je potřeba vzít v úvahu při práci s ukázkovým PlayBook:

- @No__t-0 je název skupiny prostředků, ve které jsou vytvořeny uzly AKS.
- @No__t-0 je podsíť vytvořená v předchozí části.


## <a name="run-the-sample-playbook"></a>Spuštění ukázkové PlayBook

V této části je uveden úplný vzorový PlayBook, který volá úkoly vytvořené v tomto článku. 

Uložte následující ukázkový playbook jako `aks-kubenet.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

V části `vars` proveďte následující změny:

- U klíče `resource_group` změňte hodnotu `aksansibletest` na název skupiny prostředků.
- U klíče `name` změňte hodnotu `aksansibletest` na název AKS.
- U klíče `Location` změňte hodnotu `eastus` na umístění skupiny prostředků.

Spusťte úplnou PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook aks-kubenet.yml
```

Spuštění PlayBook zobrazí výsledky podobné následujícímu výstupu:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Následující kód uložte jako `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

V části `vars` nahraďte zástupný text `{{ resource_group_name }}` názvem vaší skupiny prostředků.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz – konfigurace sítě CNI (Azure Container Networking Interface) v AKS pomocí Ansible](./ansible-aks-configure-cni-networking.md)