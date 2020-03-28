---
title: Kurz – konfigurace sítí kubenet ve službě Azure Kubernetes Service (AKS) pomocí ansible
description: Zjistěte, jak pomocí ansible nakonfigurovat sítě kubenet v clusteru Služby Azure Kubernetes Service (AKS)
keywords: ansible, azurové, devops, bash, cloudshell, playbook, aks, kontejner, aks, kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bfb19371ad651439c087cebd03023d48852ee2df
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156892"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Kurz: Konfigurace sítí kubenet ve službě Azure Kubernetes Service (AKS) pomocí ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Pomocí služby AKS můžete nasadit cluster pomocí následujících síťových modelů:

- [Kubenet networking](/azure/aks/configure-kubenet) – síťové prostředky jsou obvykle vytvářeny a konfigurovány při nasazení clusteru AKS.
- [Azure Container Networking Interface (CNI) networking](/azure/aks/configure-azure-cni) – cluster AKS je připojený k existujícím prostředkům a konfiguracím virtuální sítě.

Další informace o vytváření sítí do aplikací v AKS naleznete v [tématu Síťové koncepty pro aplikace v AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření clusteru AKS
> * Konfigurace sítě Azure kubenet

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

Kód playbooku v této části vytvoří následující prostředky Azure:

- Virtuální síť
- Podsíť v této virtuální síti.

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

Kód playbooku v této části vytvoří cluster AKS v rámci virtuální sítě. 

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

Zde jsou některé klíčové poznámky, které je třeba zvážit při práci s ukázkovým playbookem:

- Pomocí `azure_rm_aks_version` modulu vyhledejte podporovanou verzi.
- Jedná `vnet_subnet_id` se o podsíť vytvořenou v předchozí části.
- Definuje `network_profile` vlastnosti modulu plug-in sítě kubenet.
- Slouží `service_cidr` k přiřazení interních služeb v clusteru AKS k adrese IP. Tento rozsah IP adres by měl být adresní prostor, který se nepoužívá jinde v síti. 
- Adresa `dns_service_ip` by měla být ".10" adresa rozsahu IP adres služby.
- Měl `pod_cidr` by se jedná o velký adresní prostor, který se nepoužívá jinde v síťovém prostředí. Rozsah adres musí být dostatečně velký, aby se přizpůsobil počtu uzlů, které očekáváte, že navštěvujete kapacitu. Tento rozsah adres nelze změnit po nasazení clusteru.
- Rozsah adres pod IP se používá k přiřazení adresního prostoru /24 každému uzlu v clusteru. V následujícím příkladu `pod_cidr` 192.168.0.0/16 přiřadí první uzel 192.168.0.0/24, druhý uzel 192.168.1.0/24 a třetí uzel 192.168.2.0/24.
- Jak se cluster škáluje nebo upgraduje, Azure nadále přiřazuje rozsah IP adres podu každému novému uzlu.
- Playbook `ssh_key` načte `~/.ssh/id_rsa.pub`z . Pokud jej upravíte, použijte jednořádkový formát - počínaje "ssh-rsa" (bez uvozovek).
- Hodnoty `client_id` `client_secret` a jsou `~/.azure/credentials`načteny z , což je výchozí soubor pověření. Tyto hodnoty můžete nastavit na instanční objekt nebo načíst tyto hodnoty z proměnných prostředí:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Přidružení síťových prostředků

Při vytváření clusteru AKS se vytvoří skupina zabezpečení sítě a směrovací tabulka. Tyto prostředky jsou spravovány AKS a aktualizovány při vytváření a vystavit služby. Přidružte skupinu zabezpečení sítě a směrovací tabulku k podsíti virtuální sítě následujícím způsobem. 

Následující playbook uložte jako `associate.yml`.

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

Zde jsou některé klíčové poznámky, které je třeba zvážit při práci s ukázkovým playbookem:

- Jedná `node_resource_group` se o název skupiny prostředků, ve kterém jsou vytvořeny uzly AKS.
- Jedná `vnet_subnet_id` se o podsíť vytvořenou v předchozí části.


## <a name="run-the-sample-playbook"></a>Spuštění ukázkového playbooku

V této části je uveden úplný ukázkový playbook, který volá úkoly, které se vytvářejí v tomto článku. 

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

V `vars` části proveďte následující změny:

- Pro `resource_group` klíč změňte `aksansibletest` hodnotu na název skupiny prostředků.
- Pro `name` klíč změňte `aksansibletest` hodnotu na název AKS.
- U `Location` klíče změňte `eastus` hodnotu na umístění skupiny prostředků.

Spusťte kompletní playbook pomocí příkazu: `ansible-playbook`

```bash
ansible-playbook aks-kubenet.yml
```

Spuštění playbooku zobrazuje výsledky podobné následujícímu výstupu:

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

Pokud již není potřeba, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující `cleanup.yml`kód jako :

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

V `vars` části nahraďte `{{ resource_group_name }}` zástupný symbol názvem skupiny prostředků.

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz – konfigurace sítě Azure Container Networking Interface (CNI) v AKS pomocí Ansible](./ansible-aks-configure-cni-networking.md)