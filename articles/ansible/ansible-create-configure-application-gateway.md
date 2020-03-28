---
title: Kurz – Správa webového provozu pomocí brány aplikace Azure pomocí ansible
description: Zjistěte, jak pomocí Ansible vytvořit a nakonfigurovat službu Azure Application Gateway pro správu webového provozu.
keywords: ansible, azure, devops, bash, playbook, aplikační brána, vyrovnávání zatížení, webový provoz
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 07f75e39b8c6f592ecd4c48697527493b1109bb9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156607"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Kurz: Správa webového provozu pomocí Brány aplikací Azure pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Application Gateway](/azure/application-gateway/overview) je nástroj pro vyrovnávání zatížení webového provozu, který umožňuje spravovat provoz do webových aplikací. Na základě zdrojové adresy IP a portu směrují tradiční vykladače zatížení provoz na cílovou adresu IP a port. Aplikační brána poskytuje jemnější úroveň kontroly, kde může být provoz směrován na základě adresy URL. Můžete například definovat, `images` že pokud je cesta adresy URL, provoz je směrován na určitou sadu serverů (označované jako fond) nakonfigurované pro obrázky.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Nastavit síť
> * Vytvoření dvou instancí kontejnerů Azure s imagemi HTTPD
> * Vytvoření aplikační brány, která funguje s instancemi kontejnerů Azure ve fondu serverů

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Kód playbooku v této části vytvoří skupinu prostředků Azure. Skupina prostředků je logický kontejner, ve kterém jsou nakonfigurované prostředky Azure.  

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

Před spuštěním playbooku se podívejte na následující poznámky:

- Název skupiny `myResourceGroup`prostředků je . Tato hodnota se používá v celém kurzu.
- Skupina prostředků je vytvořena `eastus` v umístění.

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Playbook kód v této části vytvoří virtuální síť povolit aplikační brány komunikovat s jinými prostředky.

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

Před spuštěním playbooku se podívejte na následující poznámky:

* Oddíl `vars` obsahuje hodnoty, které se používají k vytvoření síťových prostředků. 
* Budete muset změnit tyto hodnoty pro konkrétní prostředí.

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Vytvoření serverů

Kód playbooku v této části vytvoří dvě instance kontejneru Azure s ibiHTTPD, které se použijí jako webové servery pro aplikační bránu.  

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Kód playbooku v této části `myAppGateway`vytvoří aplikační bránu s názvem .  

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

Před spuštěním playbooku se podívejte na následující poznámky:

* `appGatewayIP`je definována `gateway_ip_configurations` v bloku. Pro konfiguraci IP adresy brány se vyžaduje odkaz na podsíť.
* `appGatewayBackendPool`je definována `backend_address_pools` v bloku. Aplikační brána musí mít alespoň jeden back-endový fond adres.
* `appGatewayBackendHttpSettings`je definována `backend_http_settings_collection` v bloku. Určuje, že port 80 a protokol HTTP se používají pro komunikaci.
* `appGatewayHttpListener`je definována `backend_http_settings_collection` v bloku. Jedná se o výchozí naslouchací proces přidružený k fondu appGatewayBackendPool.
* `appGatewayFrontendIP`je definována `frontend_ip_configurations` v bloku. Přiřadí adresu myAGPublicIPAddress k naslouchacímu procesu appGatewayHttpListener.
* `rule1`je definována `request_routing_rules` v bloku. Jedná se o výchozí pravidlo směrování přidružené k naslouchacímu procesu appGatewayHttpListener.

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook appgw_create.yml
```

Vytvoření aplikační brány může trvat několik minut.

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

1. V části [Vytvořit skupinu prostředků](#create-a-resource-group) určíte umístění. Všimněte si jeho hodnoty.

1. V části [Vytvořit síťové prostředky](#create-network-resources) určíte doménu. Všimněte si jeho hodnoty.

1. Pro testovací adresu URL nahrazením následujícího vzoru `http://<domain>.<location>.cloudapp.azure.com`umístěním a doménou: .

1. Přejděte na testovací adresu URL.

1. Pokud se zobrazí následující stránka, znamená to, že aplikační brána funguje očekávaným způsobem.

    ![Úspěšný test fungující aplikační brány](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující `cleanup.yml`kód jako :

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ansible v Azure](/azure/ansible/)