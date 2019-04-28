---
title: Kurz – konfigurace sítě ve službě Azure Kubernetes Service (AKS) pomocí Ansible kubenet | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible ke konfiguraci kubenet sítí v clusteru Azure Kubernetes Service (AKS)
keywords: ansible, azure, devops, bash, cloud shell, playbooku, aks, kontejner, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 96c280f43aec32fe3d6631108dfd7e0a3f789224
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767289"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Kurz: Konfigurace kubenet sítě ve službě Azure Kubernetes Service (AKS) pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Používání AKS můžete nasadit cluster pomocí následující modely sítě:

- [Sítě Kubenet](/azure/aks/configure-kubenet) – jsou síťovým prostředkům obvykle vytvoření a konfiguraci při nasazení clusteru AKS.
- [Sítě Azure Container síťové rozhraní (CNI)](/azure/aks/configure-azure-cni) – AKS cluster je připojený k existující prostředky virtuální sítě a konfigurace.

Další informace o možnostech sítě pro vaše aplikace ve službě AKS najdete v tématu [sítě koncepty pro aplikace ve službě AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření clusteru AKS
> * Konfigurace sítí Azure kubenet

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

Playbook kód v této části se vytvoří následující prostředky Azure:

- Virtuální síť
- Podsítě v rámci virtuální sítě

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

Playbook kód v této části se vytvoří cluster AKS ve virtuální síti. 

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

Tady jsou některé důležité poznámky vzít v úvahu při práci s playbook ukázka:

- Použití `azure_rm_aks_version` modulu najít podporovanou verzi.
- `vnet_subnet_id` Je podsíť vytvořená v předchozím oddílu.
- `network_profile` Definuje vlastnosti pro modul plug-in kubenet sítě.
- `service_cidr` Slouží k přiřazení interních služeb v clusteru AKS na IP adresu. Tento rozsah IP adres by měl být adresní prostor, který se nepoužívá jinde ve vaší síti. 
- `dns_service_ip` Adresa by měla být ". 10" adresu vašeho rozsahu IP adres služby.
- `pod_cidr` By měl být velký adresní prostor, který se používá jinde ve vašem síťovém prostředí. Rozsah adres musí být dostatečně velký, aby mohla pojmout množství uzly, které plánujete škálovat až. Tento rozsah adres nelze změnit, až se cluster nasazuje.
- Rozsah IP adres pod slouží k přiřazení/24 je adresní prostor k jednotlivým uzlům v clusteru. V následujícím příkladu `pod_cidr` z 192.168.0.0/16 přiřadí první uzel 192.168.0.0/24, druhý uzel 192.168.1.0/24 a třetí 192.168.2.0/24 uzlu.
- Škálování clusteru nebo upgrady Azure se stále jste rozsah IP adres pod přiřadit každého nového uzlu.
- Načte playbook `ssh_key` z `~/.ssh/id_rsa.pub`. Když ji upravíte, použijte jednořádkovém formátu – začíná na "ssh-rsa" (bez uvozovek).
- `client_id` a `client_secret` načtení hodnot z `~/.azure/credentials`, což je výchozí soubor přihlašovacích údajů. Můžete nastavit tyto hodnoty do vaší služby instančního objektu nebo načíst tyto hodnoty z proměnné prostředí:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Přidružit síťové prostředky

Při vytváření clusteru AKS, se vytvoří tabulky skupiny a postupu zabezpečení sítě. Tyto prostředky jsou spravovaná službou AKS a aktualizován při vytváření a zpřístupňují služby. Přidružte síťové zabezpečení skupiny a směrovací tabulky podsítě vaší virtuální sítě následujícím způsobem. 

Uložte následující playbook jako `associate.yml`.

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

Tady jsou některé důležité poznámky vzít v úvahu při práci s playbook ukázka:

- `node_resource_group` Je název skupiny prostředků, ve kterém se vytvářejí uzly AKS.
- `vnet_subnet_id` Je podsíť vytvořená v předchozím oddílu.


## <a name="run-the-sample-playbook"></a>Spustit playbook vzorku

Tato část obsahuje seznam playbook úplnou ukázku, která volá úkolů a vytvoření v tomto článku. 

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

V `vars` části, proveďte následující změny:

- Pro `resource_group` změnu klíče, `aksansibletest` hodnota, která má název skupiny prostředků.
- Pro `name` změnu klíče, `aksansibletest` hodnota, která má název vašeho AKS.
- Pro `Location` změnu klíče, `eastus` hodnotu pro vaše umístění skupiny prostředků.

Spuštění pomocí kompletní playbook `ansible-playbook` příkaz:

```bash
ansible-playbook aks-kubenet.yml
```

Spuštění playbooku zobrazí výsledky podobné následující výstup:

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

Uložte následující kód jako `cleanup.yml`:

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

V `vars` části, nahraďte `{{ resource_group_name }}` zástupný symbol s názvem vaší skupiny prostředků.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz – konfigurace sítí v Azure kontejner síťové rozhraní (CNI) ve službě AKS pomocí Ansible](./ansible-aks-configure-cni-networking.md)