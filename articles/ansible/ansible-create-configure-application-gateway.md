---
title: Správa webového provozu s využitím Azure Application Gateway pomocí Ansible
description: Zjistěte, jak pomocí Ansible vytvořit a nakonfigurovat službu Azure Application Gateway pro správu webového provozu.
ms.service: ansible
keywords: ansible, azure, devops, bash, playbooku, aplikační bránu, nástroj pro vyrovnávání zatížení, webového provozu
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: af7f22ae5c289a01e6876d8ce586cb32383c8d3b
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53253346"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible"></a>Správa webového provozu s využitím Azure Application Gateway pomocí Ansible

[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací.

Ansible pomáhá automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. V tomto článku se dozvíte, jak pomocí Ansible vytvořit aplikační bránu. Zjistíte také, jak pomocí této brány spravovat provoz do dvou webových serverů spuštěných v instancích kontejnerů Azure.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvoření dvou instancí kontejnerů Azure s imagemi HTTPD
> * Vytvoření aplikační brány, která funguje s instancemi kontejnerů Azure ve fondu serverů

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ke spuštění následujících ukázkových playbooků v tomto kurzu se vyžaduje Ansible 2.7. 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.  

Následující příklad vytvoří skupinu prostředků **myResourceGroup** v umístění **eastus**.

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

Uložte tento playbook jako *rg.yml*. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Nejprve vytvořte virtuální síť, aby aplikační brána mohla komunikovat s ostatními prostředky.

Následující příklad vytvoří virtuální síť **myVNet**, podsíť **myAGSubnet** a veřejnou IP adresu **myAGPublicIPAddress** s doménou **mydomain**.

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

Uložte tento playbook jako *vnet_create.yml*. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Vytvoření serverů

Následující příklad ukazuje, jak vytvořit dvě instance kontejnerů Azure s imagemi HTTPD, které se použijí jako webové servery pro aplikační bránu.  

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

Uložte tento playbook jako *aci_create.yml*. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Následující příklad vytvoří aplikační bránu **myAppGateway** s konfiguracemi back-endu, front-endu a HTTP.  

* **appGatewayIP** se definuje v bloku **gateway_ip_configurations**. Pro konfiguraci IP adresy brány se vyžaduje odkaz na podsíť.
* **appGatewayBackendPool** se definuje v bloku **backend_address_pools**. Aplikační brána musí mít alespoň jeden back-endový fond adres.
* **appGatewayBackendHttpSettings** se definuje v bloku **backend_http_settings_collection**. Určuje, že se ke komunikaci používá port 80 a protokol HTTP.
* **appGatewayHttpListener** se definuje v bloku **backend_http_settings_collection**. Jedná se o výchozí naslouchací proces přidružený k fondu appGatewayBackendPool.
* **appGatewayFrontendIP** se definuje v bloku **frontend_ip_configurations**. Přiřadí adresu myAGPublicIPAddress k naslouchacímu procesu appGatewayHttpListener.
* **rule1** se definuje v bloku **request_routing_rules**. Jedná se o výchozí pravidlo směrování přidružené k naslouchacímu procesu appGatewayHttpListener.

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

Uložte tento playbook jako *appgw_create.yml*. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook appgw_create.yml
```

Vytvoření aplikační brány může trvat několik minut.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

V ukázkovém playbooku pro síťové prostředky jste vytvořili doménu **mydomain** v oblasti **eastus**. V prohlížeči přejděte na adresu `http://mydomain.eastus.cloudapp.azure.com`. Pokud se zobrazí následující stránka, znamená to, že aplikační brána funguje očekávaným způsobem.

![Úspěšný test fungující aplikační brány](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tyto prostředky nepotřebujete, můžete je odstranit spuštěním následujícího kódu. Ten odstraní skupinu prostředků **myResourceGroup**.

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

Uložte tento playbook jako *rg_delete.yml*. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ansible v Azure](https://docs.microsoft.com/azure/ansible/)
