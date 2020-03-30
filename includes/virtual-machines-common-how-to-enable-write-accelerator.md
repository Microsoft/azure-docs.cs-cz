---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 11/27/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 456d550659c04b2272c048fcd64fe73b1a11522a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74566237"
---
Akcelerátor zápisu je funkce disku pro virtuální počítače řady M (VM) v úložišti Premium s výhradně spravovanými disky Azure. Jak název uvádí, účelem funkce je zlepšit latenci vstupně-výstupních zápisů proti Azure Premium Storage. Akcelerátor zápisu je ideální tam, kde jsou aktualizace souboru protokolu nutné zachovat na disk ve vysoce výkonným způsobem pro moderní databáze.

Akcelerátor zápisu je obecně dostupný pro virtuální aplikace řady M ve veřejném cloudu.

## <a name="planning-for-using-write-accelerator"></a>Plánování použití akcelerátoru zápisu

Akcelerátor zápisu by měl být použit pro svazky, které obsahují protokol transakce nebo protokoly opakování systému DBMS. Nedoporučuje se používat akcelerátor zápisu pro datové svazky systému DBMS, protože funkce byla optimalizována pro použití na disky protokolu.

Akcelerátor zápisu funguje jenom ve spojení se [spravovanými disky Azure](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Povolení akcelerátoru zápisu pro disk operačního systému virtuálního počítače restartuje virtuální počítač.
>
> Povolení akcelerátoru zápisu na existující disk Azure, který není součástí sestavení svazku z více disků se správci disků nebo svazků, prostory úložiště Windows, souborový server Windows Scale-out (SOFS), Linux LVM nebo MDADM, úlohy přistupující k disku Azure musí být vypnuta. Databázové aplikace pomocí disku Azure musí být vypnut.
>
> Pokud chcete povolit nebo zakázat akcelerátor zápisu pro existující svazek, který je sestavený z více disků Úložiště Azure Premium a prokládaný pomocí správců disků nebo svazků Windows, prostory úložiště Windows, souborový server SOFS, Linux LVM nebo MDADM, všechny disky, které vytváří svazek, musí být povoleny nebo zakázány pro akcelerátor zápisu v samostatných krocích. **Před povolením nebo zakázáním akcelerátoru zápisu v takové konfiguraci vypněte virtuální počítač Azure**.

Povolení akcelerátoru zápisu pro disky operačního systému by nemělo být nutné pro konfigurace virtuálních počítačů souvisejících se systémem SAP.

### <a name="restrictions-when-using-write-accelerator"></a>Omezení při použití Akcelerátorů zápisu

Při použití akcelerátoru zápisu pro disk Azure/virtuálního pevného disku platí tato omezení:

- Ukládání disku Premium do mezipaměti musí být nastaveno na žádné nebo jen pro čtení. Všechny ostatní režimy ukládání do mezipaměti nejsou podporovány.
- Snímek není aktuálně podporován pro disky s povolenou akcelerátorem zápisu. Během zálohování služba Azure Backup automaticky vyloučí disky s povoleným akcelerátorem zápisu připojené k virtuálnímu počítači.
- Pouze menší velikosti V/O (<= 512 KiB) se ubírají zrychlenou cestou. V situacích zatížení, kde jsou data stále hromadně načtena nebo kde jsou vyrovnávací paměti transakčního protokolu různých DBMS vyplněny ve větší míře před získáním trvalé do úložiště, je pravděpodobné, že vstupně-va zapsané na disk nebere zrychlenou cestu.

Existují omezení virtuálních dispon ů úložiště Azure Premium na virtuální počítač, která můžou být podporovaná akcelerátorem zápisu. Aktuální limity jsou:

| Skladová položka virtuálního počítače | Počet disků akcelerátoru zápisu | VIOP na disk akcelerátoru zápisu na virtuální hod |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20000 |
| M208ms_v2, M208s_v2| 8 | 10000 |
| M128ms, M128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

Limity viops jsou na virtuální hod a *ne* na disk. Všechny disky akcelerátoru zápisu sdílejí stejný limit viops na virtuální hod.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Povolení Akcelerátoru zápisu na konkrétním disku

V několika dalších částech se popíše, jak lze povolit akcelerátor zápisu na virtuálních počítačích úložiště Azure Premium.

### <a name="prerequisites"></a>Požadavky

Následující požadavky platí pro použití akcelerátoru zápisu v tomto okamžiku:

- Disky, které chcete použít Azure Write Accelerator proti musí být [Azure spravované disky](https://azure.microsoft.com/services/managed-disks/) na Úložiště Premium.
- Musíte používat virtuální m-series virtuálního měn

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Povolení Akcelerátoru zápisu Azure s využitím Azure PowerShellu

Modul Azure Power Shell z verze 5.5.0 zahrnuje změny příslušných rutin, které umožňují nebo zakazují akcelerátor zápisu pro konkrétní disky úložiště Azure Premium.
Chcete-li povolit nebo nasadit disky podporované akcelerátorem zápisu, byly změněny následující příkazy prostředí Power Shell a rozšířeny tak, aby přijaly parametr akcelerátoru zápisu.

Nový parametr přepínače **-WriteAccelerator** byl přidán do následujících rutin:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Přidat-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Neposkytnutí parametru nastaví vlastnost na hodnotu false a nasadí disky, které nemají žádnou podporu akcelerátorem zápisu.

Nový parametr přepínače **-OsDiskWriteAccelerator** byl přidán do následujících rutin:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Není zadání parametru nastaví vlastnost false ve výchozím nastavení, vrácení disky, které nemají využití akcelerátoru zápisu.

Nový volitelný logický (nenulelný) parametr **-OsDiskWriteAccelerator** byl přidán do následujících rutin:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Aktualizace-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Zadejte buď $true nebo $false pro řízení podpory Azure Write Accelerator s disky.

Příklady příkazů mohou vypadat takto:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Dva hlavní scénáře lze skriptovat, jak je znázorněno v následujících částech.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Přidání nového disku podporovaného akcelerátorem zápisu pomocí prostředí PowerShell

Pomocí tohoto skriptu můžete přidat nový disk do virtuálního počítače. Disk vytvořený pomocí tohoto skriptu používá akcelerátor zápisu.

Nahraďte `myVM`, `myWAVMs`, `log001`, velikost disku a LunID disku hodnotami odpovídajícími pro konkrétní nasazení.

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

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Povolení akcelerátoru zápisu na existujícím disku Azure pomocí PowerShellu

Tento skript můžete použít k povolení akcelerátoru zápisu na existujícím disku. Nahraďte `myVM`, `myWAVMs`a `test-log001` hodnoty vhodné pro konkrétní nasazení. Skript přidá akcelerátor zápisu na existující disk, kde je hodnota **$newstatus** nastavena na "$true". Pomocí hodnoty '$false' zakáže akcelerátor zápisu na daném disku.

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
> Spuštění výše uvedeného skriptu odpojí zadaný disk, povolí akcelerátor zápisu proti disku a znovu připojí disk

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Povolení Akcelerátoru zápisu s využitím webu Azure Portal

Akcelerátor zápisu můžete povolit prostřednictvím portálu, kde zadáte nastavení ukládání disků do mezipaměti:

![Akcelerátor zápisu na portál Azure](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Povolení Akcelerátoru zápisu s využitím rozhraní příkazového řádku Azure

K povolení akcelerátoru zápisu můžete použít [azure cli.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

Chcete-li povolit akcelerátor zápisu na existujícím disku, použijte [aktualizaci az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), můžete použít následující příklady, pokud nahradíte název disku, název v a skupinu prostředků vlastními hodnotami:`az vm update -g group1 -n vm1 -write-accelerator 1=true`

Chcete-li připojit disk s povoleným akcelerátorem [zápisu, použijte připojení disku AZ VM](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), můžete použít následující příklad, pokud nahradíte vlastní hodnoty:`az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Chcete-li zakázat akcelerátor [zápisu, použijte aktualizaci az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update)a nastavení vlastností na hodnotu false:`az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Povolení akcelerátoru zápisu pomocí rest API

Chcete-li nasadit prostřednictvím rozhraní Azure Rest API, musíte nainstalovat klienta armclient Azure.

### <a name="install-armclient"></a>Instalace armclientu

Chcete-li spustit armclient, musíte jej nainstalovat přes Chocolatey. Můžete jej nainstalovat prostřednictvím cmd.exe nebo powershellu. Pro tyto příkazy používejte zvýšená práva ("Spustit jako správce").

Pomocí programu cmd.exe spusťte následující příkaz:`@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Pomocí prostředí Power Shell spusťte následující příkaz:`Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Nyní můžete nainstalovat armclient pomocí následujícího příkazu v cmd.exe nebo PowerShell`choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Získání aktuální konfigurace virtuálního počítače

Chcete-li změnit atributy konfigurace disku, musíte nejprve získat aktuální konfiguraci v souboru JSON. Aktuální konfiguraci můžete získat provedením následujícího příkazu:`armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Nahraďte termíny v rámci "<<   >>" svými daty, včetně názvu souboru JSON.

Výstup může vypadat takto:

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

Dále aktualizujte soubor JSON a povolte akcelerátor zápisu na disku s názvem "log1". Toho lze dosáhnout přidáním tohoto atributu do souboru JSON po vstupu do mezipaměti disku.

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

Potom aktualizujte stávající nasazení pomocí tohoto příkazu:`armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

Výstup by měl vypadat jako ten níže. Můžete vidět, že akcelerátor zápisu povolen pro jeden disk.

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

Po provedení této změny by měla být jednotka podporována akcelerátorem zápisu.
