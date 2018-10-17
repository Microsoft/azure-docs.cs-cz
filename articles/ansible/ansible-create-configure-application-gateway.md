---
title: Správa webového provozu pomocí Azure Application Gateway za použití Ansible (funkce Preview)
description: Zjistěte, jak můžete použít Ansible k vytvoření a konfigurace Azure Application Gateway ke správě webového provozu
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, azure application gateway, nástroj pro vyrovnávání zatížení, webový provoz
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 02b98cb22d897fc9599f6e44ddc57ef4211b0893
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410815"
---
# <a name="manage-web-traffic-with-azure-application-gateway-using-ansible-preview"></a>Správa webového provozu pomocí Azure Application Gateway za použití Ansible (funkce Preview)
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací. 

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. Tento článek ukazuje, jak použít Ansible k vytvoření služby Azure Application Gateway a použít ho ke správě provozu dvou webových serverů spuštěných v instancích kontejneru Azure. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvořte dvě instance kontejneru Azure s imagí httpd
> * Vytvoření brány aplikace s instancemi kontejneru Azure v back-endovém fondu


## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ke spuštění následujících ukázkových playbooků v tomto kurzu se vyžaduje Ansible 2.7. Spuštěním příkazu `sudo pip install ansible[azure]==2.7.0rc2` můžete nainstalovat verzi Ansible 2.7 RC. Vydání Ansible 2.7 proběhne v říjnu 2018. Potom už tady nebudete muset zadávat verzi, protože výchozí verze bude 2.7. 

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

Uložte výše uvedený playbook jako *rg.yml*. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 
Aby mohla aplikační brána komunikovat s jinými prostředky, musíte vytvořit virtuální síť. 

Následující příklad vytvoří virtuální síť s názvem **myVNet**, podsíť s názvem **myAGSubnet** a veřejnou IP adresu s názvem **myAGPublicIPAddress** s doménou s názvem **mydomain**. 

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

Uložte výše uvedený playbook jako *vnet_create.yml*. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook vnet_create.yml
```

## <a name="create-backend-servers"></a>Vytvoření serverů back-end
V tomto příkladu vytvoříte dvě instance kontejneru Azure s imagí httpd, které se použijí jako servery back-end pro aplikační bránu.  

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

Uložte výše uvedený playbook jako *aci_create.yml*. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Teď vytvoříme bránu aplikace. Následující příklad vytvoří bránu aplikace s názvem **myAppGateway** s konfigurací back-end, front-end a http.  

> [!div class="checklist"]
> * **appGatewayIP** definovaná v bloku **gateway_ip_configurations** – pro konfiguraci IP brány je vyžadována reference na podsíť. 
> * **appGatewayBackendPool** definovaný v bloku **backend_address_pools** – aplikační brána musí mít aspoň jeden back-endový fond adres. 
> * **appGatewayBackendHttpSettings** definované v bloku **backend_http_settings_collection** – určuje, že se ke komunikaci používá port 80 a protokol HTTP. 
> * **appGatewayHttpListener** definovaný v bloku **backend_http_settings_collection** – výchozí naslouchací proces přidružený k fondu appGatewayBackendPool. 
> * **appGatewayFrontendIP** definovaná v bloku **frontend_ip_configurations** – adresu myAGPublicIPAddress přiřadí k appGatewayHttpListener. 
> * **rule1** definované v bloku **request_routing_rules** – výchozí pravidlo směrování přidružené k naslouchacímu procesu appGatewayHttpListener. 

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

Uložte výše uvedený playbook jako *appgw_create.yml*. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook appgw_create.yml
```

Vytvoření aplikační brány může trvat několik minut. 

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

Ve výše uvedeném playbooku pro síťové prostředky, doména s názvem **mydomain** byla vytvořena v **eastus**. Nyní může přejít do prohlížeče, typ `http://mydomain.eastus.cloudapp.azure.com`, a měla by se zobrazit následující stránka potvrzující, že Application Gateway funguje podle očekávání.

![Přístup k Application Gateway](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tyto prostředky nepotřebujete, můžete je odstranit spuštěním následujícího příkladu. Ten odstraní skupinu prostředků **myResourceGroup**. 

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

Uložte výše uvedený playbook jako *rg_delete.yml*. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Ansible v Azure](https://docs.microsoft.com/azure/ansible/)
