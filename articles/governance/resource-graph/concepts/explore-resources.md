---
title: Procházení prostředků Azure
description: Naučte se používat jazyk dotazů grafu prostředků k prozkoumání vašich prostředků a zjištění způsobu jejich připojení.
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 01f9c0c574d8a8d6d4b5f06fc1398313649cfb8d
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882904"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Zkoumání prostředků Azure pomocí služby Resource Graph

Azure Resource Graph nabízí možnost prozkoumat a zjišťovat prostředky Azure rychle a ve velkém měřítku. Je skvělý způsob, jak se naučit vaše prostředí a také informace o vlastnostech, které existují na vašich prostředcích Azure, pro rychlé odpovědi.

## <a name="explore-virtual-machines"></a>Prozkoumat virtuální počítače

Běžným prostředkem v Azure je virtuální počítač. Jako typ prostředku mají virtuální počítače mnoho vlastností, které se dají dotazovat. Každá vlastnost nabízí možnost filtrovat nebo vyhledat přesně prostředek, který hledáte.

### <a name="virtual-machine-discovery"></a>Zjišťování virtuálních počítačů

Pojďme začít s jednoduchým dotazem, který získá jeden virtuální počítač z našeho prostředí a Prohlédněte si vrácené vlastnosti.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> `Search-AzGraph`Rutina Azure PowerShell ve výchozím nastavení vrátí **PSCustomObject** . Aby výstup vypadal stejně jako výsledek vrácený rozhraním Azure CLI, `ConvertTo-Json` použije se rutina. Výchozí hodnota **hloubky** je _2_. Nastavení na hodnotu _100_ by mělo být převedeno na všechny vrácené úrovně.

Výsledky JSON jsou strukturované podobně jako v následujícím příkladu:

```json
[
  {
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

Vlastnosti oznamují dodatečné informace o samotném prostředku virtuálního počítače. Mezi tyto vlastnosti patří: operační systém, disky, značky a skupina prostředků a předplatné, které je členem.

### <a name="virtual-machines-by-location"></a>Virtuální počítače podle umístění

Podíváme se na to, co se na prostředku virtuálních počítačů dozvědělo, a pomocí vlastnosti **Location** spočítat všechny virtuální počítače podle umístění. Chcete-li aktualizovat dotaz, odeberte tento limit a Shrňte počet hodnot umístění.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

Výsledky JSON jsou strukturované podobně jako v následujícím příkladu:

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

Teď můžeme zjistit, kolik virtuálních počítačů máme v každé oblasti Azure.

### <a name="virtual-machines-by-sku"></a>Virtuální počítače podle SKU

Až se vrátíte k původním vlastnostem virtuálního počítače, zkusíme najít všechny virtuální počítače, které mají velikost SKU **Standard_B2s**. Při prohlížení vráceného kódu JSON uvidíme, že je uložený v **Properties. položka hardwareprofile. VMSize**. Aktualizujeme dotaz tak, aby vyhledal všechny virtuální počítače, které odpovídají této velikosti, a vrátí jenom název virtuálního počítače a oblasti.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Virtuální počítače připojené k diskům spravovaným na úrovni Premium

Pokud chcete získat podrobné informace o discích spravovaných na úrovni Premium, které jsou připojené k těmto **Standard_B2s** virtuálních počítačů, rozšíříme dotaz tak, aby vracel ID prostředku těchto spravovaných disků.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Výsledkem je seznam ID disků.

### <a name="managed-disk-discovery"></a>Zjišťování spravovaných disků

S prvním záznamem z předchozího dotazu prozkoumáme vlastnosti, které existují na spravovaném disku, který byl připojen k prvnímu virtuálnímu počítači. Aktualizovaný dotaz používá ID disku a mění typ.

Příklad výstupu z předchozího dotazu například:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Než začnete s dotazem, zjistili jsme, že tento **typ** by teď měl být **Microsoft. COMPUTE/disks**? Pokud se podíváte na celé ID, uvidíte **/providers/Microsoft.COMPUTE/disks/** jako součást řetězce.
Tento fragment řetězce vám poskytne pokyn pro hledání typu. Alternativním způsobem je odebrat limit podle typu a místo toho Hledat v poli ID. Vzhledem k tomu, že ID je jedinečné, vrátí se pouze jeden záznam a vlastnost **Type** v něm poskytne podrobnosti.

> [!NOTE]
> Aby tento příklad fungoval, je nutné nahradit pole ID výsledkem z vašeho vlastního prostředí.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Výsledky JSON jsou strukturované podobně jako v následujícím příkladu:

```json
[
  {
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

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Prozkoumejte virtuální počítače a vyhledejte veřejné IP adresy.

Tato sada dotazů nejprve najde a uloží všechna síťová rozhraní (NIC) připojená k virtuálním počítačům. Pak dotazy použijí seznam síťových adaptérů k vyhledání každého prostředku IP adresy, který je veřejná IP adresa a ukládá tyto hodnoty. Nakonec dotazy poskytují seznam veřejných IP adres.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Pomocí souboru (Azure CLI) nebo proměnné (Azure PowerShell) v dalším dotazu získáte podrobnosti o prostředcích síťového rozhraní, kde je k síťovému adaptéru připojená veřejná IP adresa.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Nakonec použijte seznam prostředků veřejných IP adres uložených v souboru (Azure CLI) nebo proměnnou (Azure PowerShell), abyste získali skutečnou veřejnou IP adresu ze souvisejícího objektu a zobrazili.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Pokud chcete zjistit, jak provést tyto kroky v jednom dotazu s `join` operátorem, podívejte se na [Seznam virtuálních počítačů s jejich síťovým rozhraním a ukázkou veřejné IP adresy](../samples/advanced.md#join-vmpip) .

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [dotazovacím jazyce](query-language.md).
- Podívejte se na jazyk používaný v [počátečních dotazech](../samples/starter.md).
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md).
