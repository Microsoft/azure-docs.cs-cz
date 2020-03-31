---
title: Procházení prostředků Azure
description: Naučte se používat dotazovací jazyk Resource Graph k prozkoumání vašich prostředků a zjištění, jak jsou propojeny.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: 0c191915b8c558d80ffef554ef758a35157e035c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76156977"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Zkoumání prostředků Azure pomocí služby Resource Graph

Azure Resource Graph poskytuje možnost prozkoumat a objevit vaše prostředky Azure rychle a ve velkém měřítku. Navrženo pro rychlé reakce, je to skvělý způsob, jak se dozvědět o vašem prostředí a také o vlastnostech, které tvoří vaše prostředky Azure.

## <a name="explore-virtual-machines"></a>Prozkoumejte virtuální počítače

Běžným prostředkem v Azure je virtuální počítač. Jako typ prostředku virtuální počítače mají mnoho vlastností, které lze dotazovat. Každá vlastnost poskytuje možnost filtrování nebo hledání přesně zdroj, který hledáte.

### <a name="virtual-machine-discovery"></a>Zjišťování virtuálního počítače

Začněme s jednoduchýdotaz získat jeden virtuální počítače z našeho prostředí a podívejte se na vrácené vlastnosti.

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
> Rutina Prostředí `Search-AzGraph` Azure PowerShell vrátí ve výchozím nastavení **objekt PSCustomObject.** Chcete-li mít výstup vypadat stejně jako co `ConvertTo-Json` se vrací pomocí příkazového příkazu k příkazu Azure, rutina se používá. Výchozí hodnota pro **Hloubka** je _2_. Nastavení na _100_ by měl převést všechny vrácené úrovně.

Výsledky JSON jsou strukturovány podobně jako v následujícím příkladu:

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

Vlastnosti nám sdělují další informace o samotném prostředku virtuálního počítače, vše od skladové položky, operačního systému, disků, značek a skupiny prostředků a předplatného, jehož je členem.

### <a name="virtual-machines-by-location"></a>Virtuální počítače podle umístění

Vezmeme-li to, co jsme se dozvěděli o prostředku virtuálních počítačů, použijeme vlastnost **umístění** k počítání všech virtuálních počítačů podle umístění. Chcete-li dotaz aktualizovat, odebereme limit a shrneme počet hodnot umístění.

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

Výsledky JSON jsou strukturovány podobně jako v následujícím příkladu:

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

### <a name="virtual-machines-by-sku"></a>Virtuální počítače od SKU

Vraťme se zpět k původní vlastnosti virtuálního počítače, pokusme se najít všechny virtuální počítače, které mají velikost skladové položky **Standard_B2s**. Při pohledu na JSON vrátil, vidíme, že je uložen v **properties.hardwareprofile.vmsize**. Aktualizujeme dotaz najít všechny virtuální počítače, které odpovídají této velikosti a vrátit pouze název virtuálního počítače a oblasti.

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

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Virtuální počítače připojené k diskům s prémiovou správou

Pokud bychom chtěli získat podrobnosti o disky spravované prémií, které jsou připojeny k těmto **Standard_B2s** virtuálnípočítače, můžeme rozšířit dotaz, aby nám ID prostředku těchto spravovaných disků.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Dalším způsobem, jak získat skladovou položku, by bylo použití **vlastnosti aliasy** **Microsoft.Compute/virtualMachines/sku.name**. Podívejte se na [příklady zobrazit aliasy](../samples/starter.md#show-aliases) a [Zobrazit odlišné hodnoty aliasů.](../samples/starter.md#distinct-alias-values)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Výsledkem je seznam ID disku.

### <a name="managed-disk-discovery"></a>Zjišťování spravovaného disku

S prvním záznamem z předchozího dotazu prozkoumáme vlastnosti, které existují na spravovaném disku, který byl připojen k prvnímu virtuálnímu počítači. Aktualizovaný dotaz používá ID disku a změní typ.

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

Před spuštěním dotazu, jak jsme věděli, že **typ** by měl být nyní **Microsoft.Compute/disky**?
Pokud se podíváte na úplné ID, uvidíte **/providers/Microsoft.Compute/disks/** jako součást řetězce. Tento fragment řetězce poskytuje nápovědu k tomu, jaký typ hledat. Alternativní metodou by bylo odebrat limit podle typu a místo toho pouze hledat podle pole ID. Vzhledem k tomu, že ID je jedinečné, bude vrácen pouze jeden záznam a vlastnost **typu** na něm poskytuje tento detail.

> [!NOTE]
> Aby tento příklad fungoval, je nutné nahradit pole ID výsledkem z vlastního prostředí.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Výsledky JSON jsou strukturovány podobně jako v následujícím příkladu:

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

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Prozkoumejte virtuální počítače a vyjděte veřejné IP adresy

Tato sada dotazů nejprve vyhledá a uloží všechny prostředky síťových rozhraní (NIC) připojené k virtuálním počítačům. Pak dotazy pomocí seznamu nic najít každý prostředek IP adresy, která je veřejná IP adresa a uložit tyto hodnoty. Nakonec dotazy poskytují seznam veřejných IP adres.

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

Pomocí souboru (Azure CLI) nebo proměnné (Azure PowerShell) v dalším dotazu získat podrobnosti o souvisejících prostředků síťového rozhraní, kde je veřejná IP adresa připojená k síťové kartě.

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

Nakonec použijte seznam prostředků veřejné IP adresy uložené v souboru (Azure CLI) nebo proměnné (Azure PowerShell) získat skutečné veřejné IP adresy z souvisejícího objektu a zobrazení.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Postup provedení těchto kroků v jednom dotazu pomocí `join` operátora naleznete v seznamu [virtuálních počítačů s jejich síťovým rozhraním a ukázkou veřejné IP](../samples/advanced.md#join-vmpip) adresy.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [dotazovacím jazyce](query-language.md).
- Podívejte se na jazyk, který se používá v [dotazech Starter](../samples/starter.md).
- Zobrazení pokročilých použití v [rozšířených dotazech](../samples/advanced.md).
