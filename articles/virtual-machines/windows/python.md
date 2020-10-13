---
title: Vytvoření a Správa virtuálního počítače s Windows v Azure pomocí Pythonu
description: Naučte se pomocí Pythonu vytvářet a spravovat virtuální počítače s Windows v Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 06/22/2017
ms.author: cynthn
ms.custom: devx-track-python
ms.openlocfilehash: 402a61b4b5335c19d75cd1182243783453a0e7fa
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974851"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Vytváření a správa virtuálních počítačů s Windows v Azure pomocí Pythonu

[Virtuální počítač Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) potřebuje několik pomocných prostředků Azure. Tento článek popisuje vytváření, správu a odstraňování prostředků virtuálních počítačů pomocí Pythonu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření projektu ve Visual Studiu
> * Nainstalovat balíčky
> * Vytvořit pověření
> * Vytvoření prostředků
> * Provádění úloh správy
> * Odstranění prostředků
> * Spuštění aplikace

Provedení těchto kroků trvá přibližně 20 minut.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. Pokud jste to ještě neudělali, nainstalujte [Visual Studio](/visualstudio/install/install-visual-studio). Na stránce úlohy vyberte **vývoj v Pythonu** a pak klikněte na **nainstalovat**. V souhrnu vidíte, že je pro vás automaticky vybraný **Python 3 64-bit (3.6.0)** . Pokud jste již nainstalovali aplikaci Visual Studio, můžete přidat úlohu Pythonu pomocí spouštěče sady Visual Studio.
2. Po instalaci a spuštění sady Visual Studio klikněte na **soubor**  >  **Nový**  >  **projekt**.
3. Klikněte na **šablony**  >  **Python**  >  **Python Application**, jako název projektu zadejte *myPythonProject* , vyberte umístění projektu a pak klikněte na **OK**.

## <a name="install-packages"></a>Nainstalovat balíčky

1. V Průzkumník řešení v části *myPythonProject*klikněte pravým tlačítkem na **prostředí Python**a pak vyberte **Přidat virtuální prostředí**.
2. Na obrazovce Přidat virtuální prostředí přijměte výchozí název *ENV*, ujistěte se, že je pro základní překladač vybraný *Python 3,6 (64)* , a pak klikněte na **vytvořit**.
3. Klikněte pravým tlačítkem na prostředí *ENV* , které jste vytvořili, klikněte na **instalovat balíček Pythonu**, do vyhledávacího pole zadejte *Azure* a pak stiskněte ENTER.

Měli byste vidět ve výstupu Windows, že se úspěšně nainstalovaly balíčky Azure. 

## <a name="create-credentials"></a>Vytvořit pověření

Než zahájíte tento krok, ujistěte se, že máte [instanční objekt služby Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Měli byste také zaznamenat ID aplikace, ověřovací klíč a ID tenanta, které budete potřebovat v pozdějším kroku.

1. Otevřete soubor *myPythonProject.py* , který byl vytvořen, a pak přidejte tento kód, aby bylo možné aplikaci spustit:

    ```python
    if __name__ == "__main__":
    ```

2. Chcete-li importovat potřebný kód, přidejte tyto příkazy do horní části souboru. py:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. V dalším kroku v souboru. py přidejte proměnné za příkazy import za účelem určení běžných hodnot použitých v kódu:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Nahraďte **ID předplatného** identifikátorem vašeho předplatného.

4. Pokud chcete vytvořit přihlašovací údaje služby Active Directory, které potřebujete k vytvoření žádostí, přidejte tuto funkci za proměnné v souboru. py:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Nahraďte **ID aplikace**, **ověřovací klíč**a **ID tenanta** hodnotami, které jste předtím shromáždili při vytvoření Azure Active Directory instančního objektu.

5. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Vytvoření prostředků
 
### <a name="initialize-management-clients"></a>Inicializace klientů pro správu

Klienti pro správu jsou potřeba k vytváření a správě prostředků pomocí sady Python SDK v Azure. Chcete-li vytvořit klienty pro správu, přidejte tento kód pod příkazem **if** na konci souboru. py:

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

### <a name="create-the-vm-and-supporting-resources"></a>Vytvoření virtuálního počítače a podpůrných prostředků

Všechny prostředky musí být obsaženy ve [skupině prostředků](../../azure-resource-manager/management/overview.md).

1. Chcete-li vytvořit skupinu prostředků, přidejte tuto funkci za proměnné v souboru. py:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Skupiny dostupnosti](tutorial-availability-sets.md) usnadňují údržbu virtuálních počítačů používaných vaší aplikací.

1. Chcete-li vytvořit skupinu dostupnosti, přidejte tuto funkci za proměnné v souboru. py:
   
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

2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

[Veřejná IP adresa](../../virtual-network/public-ip-addresses.md) je potřeba ke komunikaci s virtuálním počítačem.

1. Pro vytvoření veřejné IP adresy pro virtuální počítač přidejte tuto funkci za proměnné v souboru. py:

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

2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Virtuální počítač musí být v podsíti [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

1. Pokud chcete vytvořit virtuální síť, přidejte tuto funkci za proměnné v souboru. py:

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

2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Chcete-li přidat podsíť do virtuální sítě, přidejte tuto funkci za proměnné v souboru. py:
    
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
        
4. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Virtuální počítač potřebuje ke komunikaci s virtuální sítí síťové rozhraní.

1. Chcete-li vytvořit síťové rozhraní, přidejte tuto funkci za proměnné v souboru. py:

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

2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Teď, když jste vytvořili všechny podpůrné prostředky, můžete vytvořit virtuální počítač.

1. Chcete-li vytvořit virtuální počítač, přidejte tuto funkci za proměnné v souboru. py:
   
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
    > V tomto kurzu se vytvoří virtuální počítač s verzí operačního systému Windows Server. Další informace o výběru dalších imagí najdete v tématu [navigace a výběr imagí virtuálních počítačů Azure pomocí prostředí Windows PowerShell a rozhraní příkazového řádku Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Provádění úloh správy

Během životního cyklu virtuálního počítače možná budete potřebovat provádět úlohy správy, jako jsou spuštění, zastavení nebo odstranění virtuálního počítače. Kromě toho můžete chtít vytvořit kód pro automatizaci opakujících se nebo složitých úloh.

### <a name="get-information-about-the-vm"></a>Získat informace o virtuálním počítači

1. Chcete-li získat informace o virtuálním počítači, přidejte tuto funkci za proměnné v souboru. py:

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
2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Můžete zastavit virtuální počítač a zachovat všechna jeho nastavení, ale nadále se vám bude účtovat, nebo můžete virtuální počítač zastavit a zrušit jeho přidělení. Když je virtuální počítač uvolněný, oddělují se i všechny prostředky, které jsou k němu přidružené, a pro něj končí fakturace.

1. Pokud chcete virtuální počítač zastavit bez jeho přidělení, přidejte tuto funkci za proměnné v souboru. py:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Pokud chcete zrušit přidělení virtuálního počítače, změňte power_off volání tohoto kódu:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Spuštění virtuálního počítače

1. Pokud chcete virtuální počítač spustit, přidejte tuto funkci za proměnné v souboru. py:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Změna velikosti virtuálního počítače

Při rozhodování o velikosti vašeho virtuálního počítače je třeba vzít v úvahu mnoho aspektů nasazení. Další informace najdete v tématu [velikosti virtuálních počítačů](../sizes.md).

1. Chcete-li změnit velikost virtuálního počítače, přidejte tuto funkci za proměnné v souboru. py:

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

2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Přidání datového disku k virtuálnímu počítači

Virtuální počítače mohou mít jeden nebo více [datových disků](../managed-disks-overview.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json) , které jsou uloženy jako VHD.

1. Chcete-li přidat datový disk k virtuálnímu počítači, přidejte tuto funkci za proměnné v souboru. py: 

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

2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Odstranění prostředků

Vzhledem k tomu, že se vám účtují prostředky používané v Azure, je vždy dobrým zvykem odstranit prostředky, které už nepotřebujete. Pokud chcete odstranit virtuální počítače a všechny podpůrné prostředky, stačí odstranit skupinu prostředků.

1. Pokud chcete odstranit skupinu prostředků a všechny prostředky, přidejte tuto funkci za proměnné v souboru. py:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Chcete-li zavolat funkci, kterou jste přidali dříve, přidejte tento kód pod příkaz **if** na konci souboru. py:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Uložte *myPythonProject.py*.

## <a name="run-the-application"></a>Spuštění aplikace

1. Chcete-li spustit konzolovou aplikaci, klikněte na tlačítko **Start** v aplikaci Visual Studio.

2. Po vrácení stavu každého prostředku stiskněte klávesu **ENTER** . V informacích o stavu byste měli vidět stav zřizování **úspěšné** . Po vytvoření virtuálního počítače máte příležitost odstranit všechny prostředky, které vytvoříte. Než stisknete **ENTER** a začnete odstraňovat prostředky, může trvat několik minut, než se jejich vytváření v Azure Portal ověří. Pokud máte Azure Portal otevřené, možná budete muset aktualizovat okno, aby se zobrazily nové prostředky.  

    Spuštění této konzolové aplikace z začátku do konce by mělo trvat přibližně pět minut. Před odstraněním všech prostředků a skupiny prostředků může trvat několik minut, než se aplikace dokončí.


## <a name="next-steps"></a>Další kroky

- Pokud byly nějaké problémy s nasazením, je dalším krokem projít si téma [Řešení potíží s nasazením skupin prostředků pomocí webu Azure Portal](../../azure-resource-manager/templates/deployment-history.md).
- Další informace o [knihovně Pythonu v Azure](/python/api/overview/azure/?view=azure-python)