---
title: Kurz – Správa webového provozu s využitím Azure Application Gateway pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Ansible vytvořit a nakonfigurovat službu Azure Application Gateway pro správu webového provozu.
keywords: ansible, azure, devops, bash, playbooku, aplikační bránu, nástroj pro vyrovnávání zatížení, webového provozu
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3fd16381aba87b711e799835c9f069e9c53a02ce
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765935"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Kurz: Správa webového provozu s využitím Azure Application Gateway pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Application Gateway](/azure/application-gateway/overview) je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací. Na základě zdrojové IP adresy a portu, nástroje pro vyrovnávání zatížení směrování provozu do cílové IP adresy a portu. Služba Application Gateway poskytuje jemnější stupeň řízení, kde je možné směrovat provoz podle zadané adresy URL. Například můžete definovat, že pokud `images` je cesta adresy URL, provoz se směruje na konkrétní sadu serverů (označované jako fond) nakonfigurovaný pro bitové kopie.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Nastavit síť
> * Vytvoření dvou instancí kontejnerů Azure s imagemi HTTPD
> * Vytvoření aplikační brány, která funguje s instancemi kontejnerů Azure ve fondu serverů

## <a name="prerequisites"></a>Požadavky

* [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
* [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Playbook kód v této části se vytvoří skupina prostředků Azure. Skupina prostředků je logický kontejner, ve které jsou nakonfigurované prostředky.  

Uložte následující ukázkový playbook jako `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Před spuštěním playbooku, viz následující poznámky:

- Název skupiny prostředků je `myResourceGroup`. Tato hodnota se používá v rámci tohoto kurzu.
- Skupina prostředků se vytvořila v `eastus` umístění.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Playbook kódu v této části se vytvoří virtuální síť umožňuje komunikovat s ostatními prostředky application gateway.

Uložte následující ukázkový playbook jako `vnet_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Před spuštěním playbooku, viz následující poznámky:

* `vars` Oddíl obsahuje hodnoty, které se používají k vytvoření síťových prostředků. 
* Bude potřeba změnit tyto hodnoty pro konkrétní prostředí.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Vytvoření serverů

Playbook kód v této části vytvoří dvě instance kontejneru Azure s imagí HTTPD má být použit jako webových serverů pro službu application gateway.  

Uložte následující ukázkový playbook jako `aci_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Playbook kódu v této části se vytvoří aplikační brána s názvem `myAppGateway`.  

Uložte následující ukázkový playbook jako `appgw_create.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Před spuštěním playbooku, viz následující poznámky:

* `appGatewayIP` je definován v `gateway_ip_configurations` bloku. Pro konfiguraci IP adresy brány se vyžaduje odkaz na podsíť.
* `appGatewayBackendPool` je definován v `backend_address_pools` bloku. Aplikační brána musí mít alespoň jeden back-endový fond adres.
* `appGatewayBackendHttpSettings` je definován v `backend_http_settings_collection` bloku. Určuje, že port 80 a protokol HTTP se používají ke komunikaci.
* `appGatewayHttpListener` je definován v `backend_http_settings_collection` bloku. Jedná se o výchozí naslouchací proces přidružený k fondu appGatewayBackendPool.
* `appGatewayFrontendIP` je definován v `frontend_ip_configurations` bloku. Přiřadí adresu myAGPublicIPAddress k naslouchacímu procesu appGatewayHttpListener.
* `rule1` je definován v `request_routing_rules` bloku. Jedná se o výchozí pravidlo směrování přidružené k naslouchacímu procesu appGatewayHttpListener.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook appgw_create.yml
```

Vytvoření aplikační brány může trvat několik minut.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. V [vytvořte skupinu prostředků](#create-a-resource-group) části, zadejte umístění. Poznamenejte si jeho hodnotu.

1. V [vytvoření síťových prostředků](#create-network-resources) části, zadejte doménu. Poznamenejte si jeho hodnotu.

1. Pro testovací adresa URL umístění a domény nahrazením následujícímu vzoru: `http://<domain>.<location>.cloudapp.azure.com`.

1. Přejděte na adresu URL testu.

1. Pokud se zobrazí následující stránka, znamená to, že aplikační brána funguje očekávaným způsobem.

    ![Úspěšný test fungující aplikační brány](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující kód jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ansible v Azure](/azure/ansible/)