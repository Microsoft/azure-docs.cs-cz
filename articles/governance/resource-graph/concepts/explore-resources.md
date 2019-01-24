---
title: Prozkoumat prostředky Azure.
description: Zjistěte, jak pomocí dotazovacího jazyka prostředků grafu můžete zkoumat vaše prostředky a zjistit, jak jsou připojené.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 5dd891c7c715ea4e8d27e15ca30c57aae9e80ec2
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853744"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Zkoumání prostředků Azure pomocí služby Resource Graph

Azure Graph prostředků poskytuje schopnost zkoumat a zjišťovat prostředky Azure, rychle a ve velkém měřítku. Navržené pro rychlou odezvu, je skvělý způsob, jak získat informace o vašem prostředí a také o vlastnosti, které tvoří vašich prostředků Azure.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="explore-virtual-machines"></a>Prozkoumejte virtual machines

Běžné prostředků v Azure je virtuální počítač. Jako typ prostředku virtuální počítače mají mnoho vlastností, které je možné zadávat dotazy. Každou vlastnost nabízí možnost pro filtrování nebo vyhledání přesně prostředek, který hledáte.

### <a name="virtual-machine-discovery"></a>Zjišťování virtuálních počítačů

Začněme s použitím jednoduchého dotazu k získání jednoho virtuálního počítače z našeho prostředí a podívejte se na vlastnosti vrátila.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

Výsledky JSON jsou strukturovaná podobně jako v následujícím příkladu:

```json
[
  {
    "aliases": {
      "Microsoft.Compute/imageId": null,
      "Microsoft.Compute/imageOffer": "WindowsServer",
      "Microsoft.Compute/imagePublisher": "MicrosoftWindowsServer",
      "Microsoft.Compute/imageSku": "2016-Datacenter",
      "Microsoft.Compute/imageVersion": "latest",
      "Microsoft.Compute/licenseType": null,
      "Microsoft.Compute/virtualMachines/availabilitySet.id": null,
      "Microsoft.Compute/virtualMachines/diagnosticsProfile.bootDiagnostics": null,
      "Microsoft.Compute/virtualMachines/diagnosticsProfile.bootDiagnostics.enabled": null,
      "Microsoft.Compute/virtualMachines/diagnosticsProfile.bootDiagnostics.storageUri": null,
      "Microsoft.Compute/virtualMachines/imageOffer": "WindowsServer",
      "Microsoft.Compute/virtualMachines/imagePublisher": "MicrosoftWindowsServer",
      "Microsoft.Compute/virtualMachines/imageSku": "2016-Datacenter",
      "Microsoft.Compute/virtualMachines/imageVersion": "latest",
      "Microsoft.Compute/virtualMachines/networkInterfaces[*].id": [
        "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535"
      ],
      "Microsoft.Compute/virtualMachines/osDisk.Uri": null,
      "Microsoft.Compute/virtualMachines/osProfile.adminPassword": null,
      "Microsoft.Compute/virtualMachines/osProfile.adminUsername": "localAdmin",
      "Microsoft.Compute/virtualMachines/osProfile.linuxConfiguration": null,
      "Microsoft.Compute/virtualMachines/osProfile.linuxConfiguration.disablePasswordAuthentication": null,
      "Microsoft.Compute/virtualMachines/osProfile.windowsConfiguration": {
        "enableAutomaticUpdates": true,
        "provisionVMAgent": true
      },
      "Microsoft.Compute/virtualMachines/osProfile.windowsConfiguration.enableAutomaticUpdates": true,
      "Microsoft.Compute/virtualMachines/osProfile.windowsConfiguration.provisionVMAgent": true,
      "Microsoft.Compute/virtualMachines/sku.name": "Standard_B2s",
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].caching": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].createOption": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].diskSizeGB": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].image.uri": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].lun": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].managedDisk.id": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].managedDisk.storageAccountType": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].name": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].vhd.uri": [],
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.caching": "ReadWrite",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.createOption": "FromImage",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.encryptionSettings": null,
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.encryptionSettings.enabled": null,
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType": "Premium_LRS",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.osType": "Windows",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.vhd": null,
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.vhd.uri": null
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

První sada vlastností v rámci **aliasy** nabízí několik hodnot souvisejících vlastností. Pokud chcete získat další informace o aliasech a zjistěte, jaké aliasy jsou k dispozici, najdete v článku [struktura definic Azure Policy - aliasy](../../policy/concepts/definition-structure.md#aliases). Aliasy se primárně používají zásadami Azure ke správě dodržování předpisů pro organizaci pravidla a zásady správného řízení.

Ostatní vlastnosti Řekněte nám, další informace o prostředku virtuálního počítače, včetně SKU, operační systém, disky, značky, a skupinu prostředků a předplatného je členem.

### <a name="virtual-machines-by-location"></a>Virtuální počítače podle umístění

Pořízení, co jsme se dozvěděli o prostředků virtuálních počítačů, použijeme **umístění** vlastnosti count všech virtuálních počítačů podle umístění. Pokud chcete aktualizovat dotaz, vytvoříme odeberete limit a shrnout počet hodnot umístění.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

Výsledky JSON jsou strukturovaná podobně jako v následujícím příkladu:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Teď vidíme kolik virtuálních počítačů v každé oblasti Azure.

### <a name="virtual-machines-by-sku"></a>Skladová jednotka potřebuje virtuální počítače

Po návratu do původní vlastnosti virtuálního počítače, zkuste najít všechny virtuální počítače, které mají velikost SKU **Standard_B2s**. Vrátí vyhledávání v kódu JSON, vidíme, že je uložený v **properties.hardwareprofile.vmsize**. Aktualizujeme dotaz, který najde všechny virtuální počítače, které odpovídají této velikosti a vrátit pouze název virtuálního počítače a oblast.

```Query
where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s
| project name, resourceGroup"
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Virtuální počítače připojené na premium managed disks

Pokud jsme chtěli přimět podrobnosti premium managed disks, které jsou připojené k těmto **Standard_B2s** virtuální počítače, můžeme rozšířit dotazu a poskytli nám ID prostředku tyto spravované disky.

```Query
where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Dalším způsobem, jak získat skladovou Položku by byly pomocí **aliasy** vlastnost **Microsoft.Compute/virtualMachines/sku.name**.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
  Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Výsledkem je seznam ID disků.

### <a name="managed-disk-discovery"></a>Zjišťování spravovaného disku

První záznam z předchozího dotazu, se podíváme vlastnosti, které existují na spravovaný disk, který se připojuje k první virtuální počítač. Aktualizované dotaz používá ID disku a změní typ.

Příklad výstupu z předchozího dotazu, například:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
````

```Query
where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Před spuštěním dotazu, jak jsme věděli **typ** by měla nyní být **Microsoft.Compute/disks**?
Když se podíváte na úplné ID, zobrazí se vám **/providers/Microsoft.Compute/disks/** jako součást řetězce. Tento fragment řetězce poskytuje nápovědu, jaký typ pro hledání. Je to alternativní metoda může být odeberete limit podle typu a místo toho pouze hledat podle ID pole. Je jedinečný Identifikátor, by byla vrácena jenom jednomu záznamu a **typ** vlastnost na něm poskytuje tuto podrobností.

> [!NOTE]
> Pro tento příklad fungoval je potřeba nahradit pole ID výsledku ze svého vlastního prostředí.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Výsledky JSON jsou strukturovaná podobně jako v následujícím příkladu:

```json
[
  {
    "aliases": {
      "Microsoft.Compute/disks/sku.name": "Premium_LRS",
      "Microsoft.Compute/imageId": null,
      "Microsoft.Compute/imageOffer": null,
      "Microsoft.Compute/imagePublisher": null,
      "Microsoft.Compute/imageSku": null,
      "Microsoft.Compute/imageVersion": null
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Prozkoumejte virtual machines k vyhledání veřejné IP adresy

Tato sada rozhraní příkazového řádku Azure dotazů, nejprve vyhledá a ukládá všechna síťová rozhraní (NIC) prostředkům připojeným k virtuálním počítačům. Poté používá k nalezení každý prostředek IP adresy, která je veřejná IP adresa seznam síťových adaptérů a tyto hodnoty. Navíc poskytuje seznam veřejných IP adres.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nic' variable
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

Použití `nics.txt` soubor v dalším dotazu se získat podrobnosti zdroje související síťové rozhraní ve kterých je veřejné IP adresy připojit k síťové kartě

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

Poslední, použijte seznam veřejných IP adres prostředků uložený v `ips.txt` od jejich získat skutečný veřejnou IP adresu a zobrazit.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

## <a name="next-steps"></a>Další postup

- Další informace o [dotazovacím jazyku](query-language.md)
- Zobrazit jazyk v aplikaci [Starter dotazy](../samples/starter.md)
- Viz advanced používá v [upřesňujících dotazů](../samples/advanced.md)