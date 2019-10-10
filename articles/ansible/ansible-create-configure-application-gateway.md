---
title: Kurz – Správa webového provozu pomocí Azure Application Gateway pomocí Ansible
description: Zjistěte, jak pomocí Ansible vytvořit a nakonfigurovat službu Azure Application Gateway pro správu webového provozu.
keywords: Ansible, Azure, DevOps, bash, PlayBook, Application Gateway, nástroj pro vyrovnávání zatížení, webový provoz
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1dd547fb59a41a90de18d595a392b64ef518023a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241888"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Kurz: Správa webového provozu pomocí Azure Application Gateway pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Application Gateway](/azure/application-gateway/overview) je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací. Na základě zdrojové IP adresy a portu budou tradiční nástroje pro vyrovnávání zatížení směrovat provoz na cílovou IP adresu a port. Application Gateway poskytuje přesnější úroveň řízení, kde je možné směrovat provoz na základě adresy URL. Například můžete definovat, že pokud je `images` cesta adresy URL, provoz se směruje na konkrétní sadu serverů (označované jako fond) nakonfigurovaný pro obrázky.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Nastavit síť
> * Vytvoření dvou instancí kontejnerů Azure s imagemi HTTPD
> * Vytvoření aplikační brány, která funguje s instancemi kontejnerů Azure ve fondu serverů

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Kód PlayBook v této části vytvoří skupinu prostředků Azure. Skupina prostředků je logický kontejner, ve kterém jsou nakonfigurované prostředky Azure.  

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

Před spuštěním PlayBook se podívejte na následující poznámky:

- Název skupiny prostředků je `myResourceGroup`. Tato hodnota se používá v celém kurzu.
- Skupina prostředků se vytvoří v umístění `eastus`.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

PlayBook kód v této části vytvoří virtuální síť, která povolí službě Application Gateway komunikovat s jinými prostředky.

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

Před spuštěním PlayBook se podívejte na následující poznámky:

* Část `vars` obsahuje hodnoty, které se používají k vytvoření síťových prostředků. 
* Tyto hodnoty budete muset změnit pro konkrétní prostředí.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Vytvoření serverů

Kód PlayBook v této části vytvoří dvě instance kontejnerů Azure s imagemi HTTPD, které se použijí jako webové servery pro službu Application Gateway.  

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Kód PlayBook v této části vytvoří Aplikační bránu s názvem `myAppGateway`.  

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

Před spuštěním PlayBook se podívejte na následující poznámky:

* v bloku `gateway_ip_configurations` je definována hodnota `appGatewayIP`. Pro konfiguraci IP adresy brány se vyžaduje odkaz na podsíť.
* v bloku `backend_address_pools` je definována hodnota `appGatewayBackendPool`. Aplikační brána musí mít alespoň jeden back-endový fond adres.
* v bloku `backend_http_settings_collection` je definována hodnota `appGatewayBackendHttpSettings`. Určuje, že pro komunikaci se používá port 80 a protokol HTTP.
* v bloku `backend_http_settings_collection` je definována hodnota `appGatewayHttpListener`. Jedná se o výchozí naslouchací proces přidružený k fondu appGatewayBackendPool.
* v bloku `frontend_ip_configurations` je definována hodnota `appGatewayFrontendIP`. Přiřadí adresu myAGPublicIPAddress k naslouchacímu procesu appGatewayHttpListener.
* v bloku `request_routing_rules` je definována hodnota `rule1`. Jedná se o výchozí pravidlo směrování přidružené k naslouchacímu procesu appGatewayHttpListener.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook appgw_create.yml
```

Vytvoření aplikační brány může trvat několik minut.

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

1. V části [Vytvoření skupiny prostředků](#create-a-resource-group) zadejte umístění. Poznamenejte si jeho hodnotu.

1. V části [vytvoření síťových prostředků](#create-network-resources) zadáte doménu. Poznamenejte si jeho hodnotu.

1. Pro testovací adresu URL nahraďte následujícím vzorem umístění a doména: `http://<domain>.<location>.cloudapp.azure.com`.

1. Přejděte na adresu URL testu.

1. Pokud se zobrazí následující stránka, znamená to, že aplikační brána funguje očekávaným způsobem.

    ![Úspěšný test fungující aplikační brány](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Následující kód uložte jako `cleanup.yml`:

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ansible v Azure](/azure/ansible/)