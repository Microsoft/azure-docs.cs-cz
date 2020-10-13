---
title: Akcelerátor zápisu Azure
description: Dokumentace k povolení a použití Akcelerátor zápisu
author: raiye
manager: markkie
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 2/20/2019
ms.author: raiye
ms.subservice: disks
ms.openlocfilehash: fd0f489bd6109a5dcd6625eb26286e0d40c50c63
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962322"
---
# <a name="enable-write-accelerator"></a>Povolit Akcelerátor zápisu

Akcelerátor zápisu je schopnost disku pro procesory řady M-Series Virtual Machines (VM) v Premium Storage se výhradně Managed Disks Azure. Jako název uvádíme účel funkce ke zvýšení latence zápisu zápisů do služby Azure Premium Storage v/v. Akcelerátor zápisu je ideálně vhodným způsobem, kdy se aktualizace souboru protokolu vyžadují k uchování na disku vysoce výkonném způsobem pro moderní databáze.

Akcelerátor zápisu je všeobecně dostupná pro virtuální počítače řady M-Series ve veřejném cloudu.

## <a name="planning-for-using-write-accelerator"></a>Plánování použití Akcelerátor zápisu

Akcelerátor zápisu by se měly používat pro svazky, které obsahují protokol transakcí nebo znovu protokoly DBMS. Nedoporučuje se používat Akcelerátor zápisu pro datové svazky systému DBMS, protože funkce byla optimalizována pro použití s protokolem disků.

Akcelerátor zápisu funguje jenom ve spojení se službou [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Povolením Akcelerátor zápisu disku s operačním systémem virtuálního počítače se virtuální počítač restartuje.
>
> Pokud chcete povolit Akcelerátor zápisu na stávající disk Azure, který není součástí svazku sestaveného z více disků s využitím disků nebo správců svazků Windows, prostorů úložiště Windows, souborového serveru se škálováním na více systémů (SOFS), Linux LVM nebo MDADM, je potřeba vypnout úlohy, které přistupují k disku Azure. Databázové aplikace používající disk Azure musí být vypnuté.
>
> Pokud chcete povolit nebo zakázat Akcelerátor zápisu pro existující svazek, který je založený na více discích Azure Premium Storage a prokládaných pomocí disků Windows nebo správců svazků, prostory úložiště Windows, souborový server s Windows se škálováním na více systémů (SOFS), Linux LVM nebo MDADM, musí být všechny disky vytvářející svazek povolený nebo zakázaný pro Akcelerátor zápisu v samostatných krocích. **Než povolíte nebo zakážete akcelerátor zápisu v takové konfiguraci, vypněte virtuální počítač Azure**.

Povolení Akcelerátor zápisu pro disky s operačním systémem by nemělo být nutné pro konfigurace virtuálních počítačů souvisejících s SAP.

### <a name="restrictions-when-using-write-accelerator"></a>Omezení při použití Akcelerátorů zápisu

Při použití Akcelerátor zápisu pro disk/VHD Azure platí tato omezení:

- Mezipaměť disku úrovně Premium musí být nastavená na hodnotu None nebo jen pro čtení. Všechny ostatní režimy ukládání do mezipaměti nejsou podporovány.
- Snímek se v současnosti nepodporuje u disků s povoleným Akcelerátor zápisu. Během zálohování služba Azure Backup automaticky vyloučí disky s podporou Akcelerátor zápisu připojené k virtuálnímu počítači.
- Urychlené cesty jsou pouze menší velikosti vstupně-výstupních operací (<= 512 KiB). V situacích, kdy jsou data načítána hromadně, nebo kde jsou vyrovnávací paměti pro transakční protokol různých systémů DBMS vyplněny větším stupněm před tím, než se uloží do úložiště, je pravděpodobné, že vstupně-výstupní operace zapsané na disk nebere urychlenou cestu.

Existují limity pro virtuální počítače Azure Premium Storage, které může Akcelerátor zápisu podporovat. Aktuální limity jsou:

| Skladová položka virtuálního počítače | Počet Akcelerátor zápisu disků | Akcelerátor zápisu vstupně-výstupních operací na virtuální počítač |
| --- | --- | --- |
| M416ms_v2 M416s_v2| 16 | 20000 |
| M208ms_v2 M208s_v2| 8 | 10000 |
| M128ms, M128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

Omezení IOPS jsou vázaná na virtuální počítač a *ne* na disk. Všechny Akcelerátor zápisu disky sdílí stejný limit počtu IOPS na virtuální počítač.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Povolení Akcelerátoru zápisu na konkrétním disku

V následujících částech se dozvíte, jak můžete Akcelerátor zápisu povolit na virtuálních pevných discích Azure Premium Storage.

### <a name="prerequisites"></a>Předpoklady

Následující požadavky se vztahují na použití Akcelerátor zápisu v tomto okamžiku:

- Disky, které chcete použít pro Azure Akcelerátor zápisu, musí být na Premium Storage [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) .
- Musíte používat virtuální počítač M-Series.

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Povolení Akcelerátoru zápisu Azure s využitím Azure PowerShellu

Modul Azure Power Shell z verze 5.5.0 obsahuje změny odpovídajících rutin pro povolení nebo zakázání Akcelerátor zápisu pro konkrétní disky Azure Premium Storage.
Aby bylo možné povolit nebo nasadit disky podporované nástrojem Akcelerátor zápisu, došlo ke změně následujících příkazů prostředí Power Shell a k rozšíření přijmout parametr pro Akcelerátor zápisu.

Nový parametr přepínače **-WriteAccelerator** byl přidán do následujících rutin:

- [Set-AzVMOsDisk](/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Pokud parametr neuvedete, nastaví vlastnost na hodnotu false a nasadí disky bez podpory Akcelerátor zápisu.

Nový parametr přepínače **– OsDiskWriteAccelerator** bylo přidáno do následujících rutin:

- [Set-AzVmssStorageProfile](/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Pokud parametr nezadáte, nastaví ve výchozím nastavení vlastnost na hodnotu false a vrátí disky, které nevyužívají Akcelerátor zápisu.

Do následujících **rutin se přidal** nový volitelný logický parametr (bez hodnoty null):

- [Update-AzVM](/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update – AzVmss](/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Zadejte buď $true, nebo $false k řízení podpory služby Azure Akcelerátor zápisu na discích.

Příklady příkazů mohou vypadat takto:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Pomocí dvou hlavních scénářů se dá skriptovat, jak je znázorněno v následujících oddílech.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Přidání nového disku podporovaného Akcelerátor zápisu pomocí prostředí PowerShell

Tento skript můžete použít k přidání nového disku do virtuálního počítače. Disk vytvořený pomocí tohoto skriptu používá Akcelerátor zápisu.

Nahraďte `myVM` , `myWAVMs` , `log001` , velikost disku a LunID disku hodnotami vhodnými pro konkrétní nasazení.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Povolení Akcelerátor zápisu na stávajícím disku Azure pomocí PowerShellu

Tento skript můžete použít k povolení Akcelerátor zápisu na stávajícím disku. `myVM`Hodnoty, `myWAVMs` a nahraďte `test-log001` odpovídajícími hodnotami pro konkrétní nasazení. Skript přidá Akcelerátor zápisu na existující disk, na kterém je hodnota pro **$newstatus** nastavená na $true. Použití hodnoty ' $false ' zakáže Akcelerátor zápisu na daném disku.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Při spuštění výše uvedeného skriptu dojde k odpojení zadaného disku, povolení Akcelerátor zápisu na disku a opětovném připojení disku

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Povolení Akcelerátoru zápisu s využitím webu Azure Portal

Akcelerátor zápisu můžete povolit přes portál, kde zadáte nastavení ukládání do mezipaměti disku:

![Akcelerátor zápisu Azure Portal](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Povolení Akcelerátoru zápisu s využitím rozhraní příkazového řádku Azure

K povolení Akcelerátor zápisu můžete použít rozhraní příkazového [řádku Azure](/cli/azure/?view=azure-cli-latest) .

Pokud chcete povolit Akcelerátor zápisu na stávajícím disku, použijte příkaz [AZ VM Update](/cli/azure/vm?view=azure-cli-latest#az-vm-update), můžete použít následující příklady, pokud nahradíte hodnoty disk, VMName a Source vlastními hodnotami: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Pokud chcete připojit disk s povoleným Akcelerátor zápisu, použijte příkaz [AZ VM disk Attach](/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), pokud nahradíte vlastní hodnoty, můžete použít následující příklad: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Pokud chcete Akcelerátor zápisu zakázat, použijte příkaz [AZ VM Update](/cli/azure/vm?view=azure-cli-latest#az-vm-update)a nastavte vlastnosti na false: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Povolení Akcelerátor zápisu pomocí rozhraní REST API

K nasazení prostřednictvím rozhraní Azure REST API je potřeba nainstalovat Azure armclient.

### <a name="install-armclient"></a>Nainstalovat armclient

Chcete-li spustit armclient, je nutné jej nainstalovat prostřednictvím čokolády. Můžete ji nainstalovat pomocí cmd.exe nebo PowerShellu. Použijte zvýšená práva pro tyto příkazy ("spustit jako správce").

Pomocí cmd.exe spusťte následující příkaz: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Pomocí Power shellu spusťte následující příkaz: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Nyní můžete nainstalovat armclient pomocí následujícího příkazu v buď cmd.exe nebo PowerShellu. `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Získává se aktuální konfigurace virtuálních počítačů.

Chcete-li změnit atributy konfigurace disku, je třeba nejprve získat aktuální konfiguraci v souboru JSON. Aktuální konfiguraci můžete získat spuštěním následujícího příkazu: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Nahraďte výrazy v rámci "<<   >>" daty, včetně názvu souboru, který by měl mít soubor JSON.

Výstup by mohl vypadat takto:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Dále aktualizujte soubor JSON a povolte Akcelerátor zápisu na disku s názvem ' log1 '. To lze provést přidáním tohoto atributu do souboru JSON po zadání mezipaměti disku.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Pak aktualizujte existující nasazení pomocí tohoto příkazu: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

Výstup by měl vypadat jako na následujícím obrázku. Můžete vidět, že Akcelerátor zápisu povolené pro jeden disk.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Až tuto změnu provedete, měla by být jednotka podporovaná Akcelerátor zápisu.