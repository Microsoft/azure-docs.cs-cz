---
title: Vytvářet a spravovat virtuální počítač s Windows v Azure pomocí Python | Microsoft Docs
description: Naučte se používat jazyk Python vytvářet a spravovat virtuální počítač s Windows v Azure.
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
ms.openlocfilehash: dbe8f1603433f381c3c28cb47d2dbda543b462e0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Vytvářet a spravovat virtuální počítače Windows v Azure pomocí Python

[Virtuální počítač Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) vyžaduje několik podpůrných prostředků Azure. Tento článek popisuje vytváření, správu a odstraňování prostředky virtuálních počítačů pomocí Pythonu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření projektu ve Visual Studiu
> * Instalovat balíčky
> * Vytvořit přihlašovací údaje
> * Vytvoření prostředků
> * Provádění úloh správy
> * Odstranění prostředků
> * Spuštění aplikace

Proveďte tyto kroky trvá asi 20 minut.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. Pokud jste to ještě neudělali, nainstalujte [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Vyberte **vývoj Python** na stránce úlohy a pak klikněte na tlačítko **nainstalovat**. V souhrnu, můžete uvidíte, že **Python 3 64-bit (3.6.0)** je automaticky vybrána pro vás. Pokud jste již nainstalovali Visual Studio, můžete přidat Python zatížení pomocí Spouštěče sady Visual Studio.
2. Po instalaci a spuštění sady Visual Studio, klikněte na tlačítko **soubor** > **nový** > **projektu**.
3. Klikněte na tlačítko **šablony** > **Python** > **aplikace Python**, zadejte *myPythonProject* pro název projekt, vyberte umístění projektu a pak klikněte na tlačítko **OK**.

## <a name="install-packages"></a>Instalovat balíčky

1. V Průzkumníku řešení klikněte v části *myPythonProject*, klikněte pravým tlačítkem na **prostředí Python**a potom vyberte **přidat virtuální prostředí**.
2. Na obrazovce Přidat virtuální prostředí, přijměte výchozí název *env*, ujistěte se, že *3.6 Python (64 bitů)* pro základní překladač je vybrána a potom klikněte na **vytvořit** .
3. Klikněte pravým tlačítkem myši *env* prostředí, které jste vytvořili, klikněte na tlačítko **instalovat balíček Python**, zadejte *azure* do pole hledání a potom stiskněte klávesu Enter.

Měli byste vidět v oknech výstupu azure balíčky byly úspěšně nainstalovány. 

## <a name="create-credentials"></a>Vytvořit přihlašovací údaje

Než začnete tento krok, ujistěte se, že máte [objektu služby Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Také byste měli zaznamenat ID aplikace, ověřovací klíč a ID klienta, který budete potřebovat později.

1. Otevřete *myPythonProject.py* soubor, který byl vytvořen a poté přidejte tento kód k povolení spuštění vaší aplikace:

    ```python
    if __name__ == "__main__":
    ```

2. Pokud chcete importovat kód, který je potřeba, přidejte do horní části souboru .py tyto příkazy:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Dále v souboru .py přidejte proměnné po importu příkazy k určení běžné hodnoty, které se používá v kódu:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Nahraďte **id předplatného** s ID vašeho předplatného.

4. Pokud chcete vytvořit přihlašovací údaje služby Active Directory, které je třeba, aby žádosti, přidejte tuto funkci po proměnné v souboru .py:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Nahraďte **id aplikace**, **ověřovací klíč**, a **id klienta** hodnotami, které jste dříve shromážděna při vytváření služby Azure Active Directory objekt zabezpečení.

5. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Vytvoření prostředků
 
### <a name="initialize-management-clients"></a>Inicializace klientů pro správu

Klienti pro správu jsou potřebné k vytváření a správě prostředků v Azure pomocí sady SDK pro Python. Chcete-li vytvořit klientů pro správu, přidejte tento kód v části **Pokud** údajů na adrese pak konec souboru .py:

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

Musí být všechny prostředky obsažené v [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md).

1. Pokud chcete vytvořit skupinu prostředků, přidejte tuto funkci po proměnné v souboru .py:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Skupiny dostupnosti](tutorial-availability-sets.md) umožňují snadnější zachování virtuálních počítačů, které používá vaše aplikace.

1. Pokud chcete vytvořit skupinu dostupnosti, přidejte tuto funkci po proměnné v souboru .py:
   
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

2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

A [veřejnou IP adresu](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) je potřeba ke komunikaci s virtuálním počítačem.

1. Pokud chcete vytvořit veřejnou IP adresu pro virtuální počítač, přidejte tuto funkci po proměnné v souboru .py:

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

2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Virtuální počítač musí být v podsíti [virtuální síť](../../virtual-network/virtual-networks-overview.md).

1. Pokud chcete vytvořit virtuální síť, přidejte tuto funkci po proměnné v souboru .py:

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

2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Chcete-li přidat podsíť virtuální sítě, přidejte tuto funkci po proměnné v souboru .py:
    
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
        
4. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Virtuální počítač vyžaduje síťové rozhraní pro komunikaci ve virtuální síti.

1. Pokud chcete vytvořit síťové rozhraní, přidejte tuto funkci po proměnné v souboru .py:

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

2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Teď, když jste vytvořili doprovodné materiály, můžete vytvořit virtuální počítač.

1. Pokud chcete vytvořit virtuální počítač, přidejte tuto funkci po proměnné v souboru .py:
   
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
    > V tomto kurzu vytvoří virtuální počítač s verzí operačního systému Windows Server. Další informace o výběru ostatní Image, najdete v tématu [vyhledání a výběr imagí virtuálních počítačů Azure pomocí prostředí Windows PowerShell a rozhraní příkazového řádku Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Provádění úloh správy

Během životního cyklu virtuálního počítače možná budete potřebovat provádět úlohy správy, jako jsou spuštění, zastavení nebo odstranění virtuálního počítače. Kromě toho můžete vytvořit kód pro automatizaci úloh opakovaných nebo komplexní.

### <a name="get-information-about-the-vm"></a>Získat informace o virtuálním počítači

1. Chcete-li získat informace o virtuálním počítači, přidejte tuto funkci po proměnné v souboru .py:

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
2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Můžete zastavit virtuální počítač a ponechat jeho nastavení, ale nadále účtovat poplatek za ho nebo můžete zastavit virtuální počítač a jeho navrácení. Při zrušení jeho přidělení virtuálního počítače jsou končí deallocated a fakturace pro ni také všechny prostředky, které jsou s ním spojená.

1. Zastavte virtuální počítač bez rušení přidělení ho, přidejte tuto funkci po proměnné v souboru .py:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Pokud chcete zrušit přidělení virtuálního počítače, změna volání power_off tento kód:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Spusťte virtuální počítač

1. Pokud chcete spustit virtuální počítač, přidejte tuto funkci po proměnné v souboru .py:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Změnit velikost virtuálního počítače

Mnoho aspektů nasazení měli zvážit při rozhodování o velikosti pro virtuální počítač. Další informace najdete v tématu [velikosti virtuálních počítačů](sizes.md).

1. Chcete-li změnit velikost virtuálního počítače, přidejte tuto funkci po proměnné v souboru .py:

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

2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Přidat datový disk k virtuálnímu počítači

Virtuální počítače může mít jeden nebo více [datových disků](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , jsou uloženy jako soubory VHD.

1. Chcete-li přidat datový disk k virtuálnímu počítači, přidejte tuto funkci po proměnné v souboru .py: 

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

2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Odstranění prostředků

Vzhledem k tomu, že se vám účtovat prostředky využívané v Azure, vždycky je dobrým zvykem odstranit prostředky, které už nejsou potřeba. Pokud chcete odstranit virtuální počítače a všechny podpůrné prostředky, je vše, co musíte udělat, odstraňte skupinu prostředků.

1. Pokud chcete odstranit skupinu prostředků a všechny prostředky, přidejte tuto funkci po proměnné v souboru .py:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Chcete-li zavolat funkci, kterou jste dříve přidali, přidejte tento kód v části **Pokud** příkaz na konci souboru .py:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Uložit *myPythonProject.py*.

## <a name="run-the-application"></a>Spuštění aplikace

1. Chcete-li spustit aplikaci konzoly, klikněte na tlačítko **spustit** v sadě Visual Studio.

2. Stiskněte klávesu **Enter** po je vrácen stav každého prostředku. Informace o stavu, byste měli vidět **úspěšné** Stav zřizování. Po vytvoření virtuálního počítače, máte možnost odstranit všechny prostředky, které vytvoříte. Před stisknutím klávesy **Enter** zahájíte odstranění prostředků, může trvat několik minut na ověření jejich vytvoření na portálu Azure. Pokud máte na portálu Azure otevřete, můžete chtít aktualizovat v okně zobrazíte nové prostředky.  

    Dokončit má trvat přibližně pět minut, než tato Konzolová aplikace spustit úplně od začátku. Může trvat několik minut, po dokončení aplikace před všechny prostředky a skupině prostředků se odstraní.


## <a name="next-steps"></a>Další postup

- Pokud byly nějaké problémy s nasazením, je dalším krokem projít si téma [Řešení potíží s nasazením skupin prostředků pomocí webu Azure Portal](../../resource-manager-troubleshoot-deployments-portal.md).
- Další informace o [knihovna Python Azure](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

