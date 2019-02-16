---
title: Vytvoření a Správa virtuálního počítače s Windows v Azure pomocí Pythonu | Dokumentace Microsoftu
description: Naučte se používat Python k vytváření a správě virtuálního počítače s Windows v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: cynthn
ms.openlocfilehash: 748bc08e003d398e96ef55493e4f3b0bf6b7da28
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326972"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Vytvoření a správa virtuálních počítačů s Windows v Azure pomocí Pythonu

[Virtuálního počítače Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) potřebuje několik Podpůrné prostředky Azure. Tento článek popisuje vytváření, Správa a odstranění prostředků virtuálního počítače pomocí Pythonu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření projektu ve Visual Studiu
> * Instalace balíčků
> * Vytvořte přihlašovací údaje
> * Vytvoření prostředků
> * Provádění úloh správy
> * Odstranění prostředků
> * Spuštění aplikace

Proveďte tyto kroky trvá přibližně 20 minut.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. Pokud jste tak dosud neučinili, nainstalujte [sady Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Vyberte **vývoj v jazyce Python** na stránku úlohy a pak klikněte na tlačítko **nainstalovat**. Stručně řečeno, vidíte, že **Python 3, 64 bitů (3.6.0)** se vybere automaticky za vás. Pokud jste již nainstalovali Visual Studio, můžete přidat Python úlohy pomocí Spouštěče sady Visual Studio.
2. Po instalaci a spuštění sady Visual Studio, klikněte na tlačítko **souboru** > **nový** > **projektu**.
3. Klikněte na tlačítko **šablony** > **Python** > **aplikace v Pythonu**, zadejte *myPythonProject* pro název projekt, vyberte umístění projektu a pak klikněte na tlačítko **OK**.

## <a name="install-packages"></a>Instalace balíčků

1. V Průzkumníku řešení klikněte v části *myPythonProject*, klikněte pravým tlačítkem na **prostředí Pythonu**a pak vyberte **přidat virtuální prostředí**.
2. V dialogovém okně Přidat virtuální prostředí, přijměte výchozí název *env*, ujistěte se, že *Python 3.6 (64-bit)* je vybrán pro základní interpret a potom klikněte na **vytvořit** .
3. Klikněte pravým tlačítkem myši *env* prostředí, které jste vytvořili, klikněte na tlačítko **instalovat balíček Pythonu**, zadejte *azure* do vyhledávacího pole a potom stiskněte klávesu Enter.

Měli byste vidět v oknech výstupu balíčky azure byly úspěšně nainstalovány. 

## <a name="create-credentials"></a>Vytvořte přihlašovací údaje

Předtím, než se pustíte do tohoto kroku, ujistěte se, že máte [instanční objekt služby Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Také byste měli zaznamenávat ID aplikace, ověřovací klíč a ID tenanta, který budete potřebovat v pozdějším kroku.

1. Otevřít *myPythonProject.py* soubor, který byl vytvořen a následně přidejte následující kód k aktivaci aplikace ke spuštění:

    ```python
    if __name__ == "__main__":
    ```

2. Import kód, který je nutný, přidejte k hornímu okraji soubor .py tyto příkazy:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Dále v soubor .py, přidejte proměnných za příkazy pro import zadání běžných hodnot používá v kódu:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Nahraďte **id předplatného** identifikátorem předplatného.

4. Vytvoření přihlašovacích údajů Active Directory, které potřebujete k podání žádostí o, přidejte tuto funkci po proměnné soubor .py:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Nahraďte **id aplikace**, **ověřovací klíč**, a **id tenanta** hodnotami, které jste shromáždili dříve při vytváření služby Azure Active Directory objekt zabezpečení.

5. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Vytvoření prostředků
 
### <a name="initialize-management-clients"></a>Inicializovat klientů pro správu

Klientů pro správu jsou potřebné k vytváření a správě prostředků v Azure pomocí sady Python SDK. Vytvoření klientů pro správu, přidejte tento kód v rámci **Pokud** příkaz pak konci soubor .py:

```python
resource_group_client = ResourceManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>Vytvoření virtuálního počítače a podpůrné prostředky

Všechny prostředky musí být součástí [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md).

1. Pokud chcete vytvořit skupinu prostředků, přidejte tuto funkci po proměnné soubor .py:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Skupiny dostupnosti](tutorial-availability-sets.md) usnadňují údržbu virtuálních počítačů, které používá vaše aplikace.

1. Pokud chcete vytvořit skupinu dostupnosti, přidejte tuto funkci po proměnné soubor .py:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

A [veřejnou IP adresu](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) je potřeba ke komunikaci s virtuálním počítačem.

1. Chcete-li vytvořit veřejnou IP adresu pro virtuální počítač, přidejte tuto funkci po proměnné soubor .py:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Virtuální počítač musí být v podsíti [virtuální síť](../../virtual-network/virtual-networks-overview.md).

1. Pokud chcete vytvořit virtuální síť, přidejte tuto funkci po proměnné soubor .py:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Přidání podsítě do virtuální sítě, přidejte tuto funkci po proměnné soubor .py:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Virtuální počítač potřebuje síťové rozhraní ve virtuální síti komunikovat.

1. Chcete-li vytvořit síťové rozhraní, přidejte tuto funkci po proměnné soubor .py:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Teď, když jste vytvořili všechny podpůrné prostředky, můžete vytvořit virtuální počítač.

1. K vytvoření virtuálního počítače, přidejte tuto funkci po proměnné soubor .py:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > V tomto kurzu se vytvoří virtuální počítač s verzí operačního systému Windows Server. Další informace o výběru další Image najdete v tématu [vyhledání a výběr imagí virtuálních počítačů Azure pomocí prostředí Windows PowerShell a Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Provádění úloh správy

Během životního cyklu virtuálního počítače možná budete potřebovat provádět úlohy správy, jako jsou spuštění, zastavení nebo odstranění virtuálního počítače. Kromě toho můžete vytvořit kód pro automatizaci opakovaných nebo složitých úloh.

### <a name="get-information-about-the-vm"></a>Získání informací o virtuálním počítači

1. Pokud chcete získat informace o virtuálním počítači, přidejte tuto funkci po proměnné soubor .py:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Zastavit virtuální počítač a ponechat jeho nastavení ale dál bude účtovat, nebo můžete zastavit virtuální počítač a uvolnit ji. Při zrušení přidělení virtuálního počítače jsou všechny prostředky s ním spojená také končí uvolnění a účtování pro něj.

1. Pokud chcete zastavit virtuální počítač bez rušení přidělení ho, přidejte tuto funkci po proměnné soubor .py:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Pokud chcete zrušit přidělení virtuálního počítače, změna power_off volání tento kód:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Spuštění virtuálního počítače

1. Ke spuštění virtuálního počítače, přidejte tuto funkci po proměnné soubor .py:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Změna velikosti virtuálního počítače

Mnoho aspektů nasazení má brát při rozhodování o velikosti pro virtuální počítač. Další informace najdete v tématu [velikosti virtuálních počítačů](sizes.md).

1. Chcete-li změnit velikost virtuálního počítače, přidejte tuto funkci po proměnné soubor .py:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Přidání datového disku k virtuálnímu počítači

Virtuální počítače můžou mít jednu nebo více [datové disky](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , která jsou uložena jako virtuální pevné disky.

1. Chcete-li přidat datový disk k virtuálnímu počítači, přidejte tuto funkci po proměnné soubor .py: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Odstranění prostředků

Vzhledem k tomu, že se vám účtovat prostředky používané v Azure, je vždy vhodné odstranit prostředky, které už nejsou potřeba. Pokud chcete odstranit virtuální počítače a všechny podpůrné prostředky, je vše, co musíte udělat, odstraňte skupinu prostředků.

1. Pokud chcete odstranit skupinu prostředků a všechny prostředky, přidejte tuto funkci po proměnné soubor .py:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Pro volání funkce, která jste přidali dříve, přidejte tento kód v rámci **Pokud** příkaz na konci soubor .py:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Uložit *myPythonProject.py*.

## <a name="run-the-application"></a>Spuštění aplikace

1. Chcete-li spustit konzolovou aplikaci, klikněte na tlačítko **Start** v sadě Visual Studio.

2. Stisknutím klávesy **Enter** po vrácení stav jednotlivých prostředků. Informace o stavu, byste měli vidět **Succeeded** Stav zřizování. Po vytvoření virtuálního počítače máte příležitost k odstranění všech prostředků, které vytvoříte. Než stisknete klávesu **Enter** spuštění odstranění prostředků, může trvat několik minut na ověření jejich vytvoření na webu Azure Portal. Pokud máte na webu Azure portal otevřete, možná budete muset aktualizovat okno a zobrazte si nové prostředky.  

    To by měla trvat asi pět minut, než tuto konzolovou aplikaci pro spuštění úplně od začátku. Může trvat několik minut poté, co je aplikace dokončena před všechny prostředky a skupiny prostředků se odstraní.


## <a name="next-steps"></a>Další postup

- Pokud byly nějaké problémy s nasazením, je dalším krokem projít si téma [Řešení potíží s nasazením skupin prostředků pomocí webu Azure Portal](../../resource-manager-troubleshoot-deployments-portal.md).
- Další informace o [knihovna Python pro Azure](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

