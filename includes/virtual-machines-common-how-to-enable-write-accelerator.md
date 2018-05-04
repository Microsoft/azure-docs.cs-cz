---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 04/30/2018
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 54faa5a50b3fe965bc7f95fc0da0fdda9388412f
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="write-accelerator"></a>Zápis akcelerátoru
Zapsat že akcelerátoru výhradně je funkce disku pro M-Series virtuální počítače (VM) na Storage úrovně Premium s Azure spravované disky. Jako název stavy, je účelem funkce vstupně-výstupních operací latenci zápisů Azure Premium Storage. Zapsat že akcelerátoru je ideální kterých jsou k zachování disk způsobem vysoce původce pro moderní databáze vyžaduje aktualizace souborů protokolu.

Zapsat že akcelerátoru je obecně dostupné pro virtuální počítače M-series ve veřejném cloudu.

## <a name="planning-for-using-write-accelerator"></a>Plánování pro použití zápisu akcelerátoru
Zapsat že akcelerátoru se mají použít pro svazky, které obsahují transakčního protokolu nebo vrátit protokoly databázového systému. Není doporučeno použít zápisu akcelerátoru pro svazcích dat databázového systému funkci optimalizovaná má být použit proti protokolu disky.

Zápis akcelerátoru pouze funguje ve spojení s [discích spravovaných pomocí Azure](https://azure.microsoft.com/services/managed-disks/). 


> [!IMPORTANT]
> Pokud chcete povolit nebo zakázat zápis akcelerátoru pro existující svazek, který vychází z více disků Storage úrovně Premium a rozdělená pomocí Správce Windows disk nebo svazek, prostory úložiště ve Windows, Windows škálovatelný souborový server (SOFS), Linux LVM nebo MDADM, všechny disky vytváření svazek musí být zapnutá nebo vypnutá pro zápis akcelerátoru v samostatné kroky. **Před povolením nebo zakázáním zápisu akcelerátoru v takové konfiguraci, vypněte virtuální počítač Azure**. 


> [!IMPORTANT]
> Chcete-li povolit zápis akcelerátorů do existujícího Azure disku, který není součástí sestavení svazku mimo více disků se Windows správci disk nebo svazek, prostory úložiště systému Windows, souborový server Windows Škálováním na více systémů (SOFS), Linux LVM nebo MDADM, úlohy, přístup k Azure disk je potřeba vypnout. Databázové aplikace pomocí Azure disku musí být vypnuté.

> [!IMPORTANT]
> Povolení zápisu akcelerátoru pro disk s operačním systémem virtuálního počítače se restartuje virtuální počítač. 

Povolení zápisu akcelerátoru pro disky operačního systému by neměl být potřebné pro SAP související s konfigurací virtuálních počítačů

### <a name="restrictions-when-using-write-accelerator"></a>Omezení při použití zápisu akcelerátoru
Pokud používáte zápis akcelerátoru pro Azure disk nebo VHD, platí tato omezení:

- Premium disku ukládání do mezipaměti musí být nastavena na hodnotu 'None' nebo "Jen pro čtení". Všechny ostatní režimy ukládání do mezipaměti nejsou podporovány.
- Ještě není podporované snímku na disku akcelerátoru zápis povolen. Toto omezení blokuje služby zálohování Azure schopnost provádět snímky konzistentní aplikací všechny disky virtuálního počítače.
- Menší velikost vstupně-výstupních operací trvá Zrychlený cestu. V zatížení načten situacích, kde je dat získávání hromadné nebo kde vyrovnávacích pamětí protokolu transakcí z různých databázového systému jsou vyplněny ve větší míře než získávání trvalé do úložiště, pravděpodobné, vstupně-výstupních operací zapsána do disk nejde Zrychlený cestu.

Existují omezení Azure Premium Storage VHD na virtuální počítač, který může podporovat zápis akcelerátoru. Aktuální omezení jsou:

| VIRTUÁLNÍ POČÍTAČ SKU | Počet disků zápisu akcelerátoru | Zápis disku akcelerátoru IOPS na virtuálních počítačů |
| --- | --- | --- |
| M128ms | 16 | 8000 |
| M128s | 16 | 8000 |
| M64ms | 8 | 4000 |
| M64s | 8 | 4000 | 

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Povolení zápisu akcelerátoru na určitém disku
Další několik části se popisují, jak lze povolit zápis akcelerátoru na Azure Premium Storage virtuálních pevných discích.


### <a name="prerequisites"></a>Požadavky
Následující požadavky platí pro používání akcelerátoru zápisu v tuto chvíli:

- Musí být disky, které chcete použít Azure zápisu akcelerátoru proti [discích spravovaných pomocí Azure](https://azure.microsoft.com/services/managed-disks/) na Storage úrovně Premium.
- Je třeba použít M-series virtuálních počítačů

### <a name="enabling-through-power-shell"></a>Povolení prostřednictvím Power Shell
Modul prostředí PowerShell Azure z verze 5.5.0 zahrnují změny do příslušných rutin chcete povolit nebo zakázat zápis akcelerátoru pro konkrétní disky Azure Premium Storage.
Aby bylo možné povolit nebo nasazení podporuje zápis akcelerátoru disky, tu následující příkazy prostředí PowerShell změnit a rozšířit tak, aby přijímal parametr pro zápis akcelerátoru.

Nový parametr přepínač "WriteAccelerator" byly přidány do následující rutiny: 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Nesmí mít parametr nastaví vlastnost na hodnotu false a provede nasazení disky, které nemají žádnou podporu pomocí zápisu akcelerátoru.

Nový parametr přepínač "OsDiskWriteAccelerator" byla přidána do následující rutiny: 

- Set-AzureRmVmssStorageProfile

Nesmí mít paramenter nastaví vlastnost na hodnotu false a získávat disky, které využívají není zápisu akcelerátoru.

Nový volitelný parametr typu Boolean (použití hodnot Null), "OsDiskWriteAccelerator" byly přidány do následující rutiny: 

- Update-AzureRmVM
- Update-AzureRmVmss

Zadejte buď $true nebo $false k řízení podpory Azure zápisu akcelerátoru s disky.

Příklady příkazů může vypadat podobně jako:

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

Dva základní scénáře možnost skripty, jak je znázorněno v následující části.

#### <a name="adding--new-disk-supported-by-write-accelerator"></a>Přidání nového disku nepodporuje zápis akcelerátoru
Tento skript můžete přidat nový disk k virtuálnímu počítači. Disk, který vytvořili pomocí tohoto skriptu bude používat zápis akcelerátoru.

```

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
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
Je nutné přizpůsobit názvy virtuálních počítačů, disku, skupinu prostředků, velikost disku a LunID disku pro konkrétní nasazení.


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>Povolení Azure zápisu akcelerátoru na existující Azure disk
Pokud potřebujete povolit zápis akcelerátoru na stávající disk, můžete tento skript k provedení úlohy:

```

#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

Je nutné přizpůsobit názvy virtuálních počítačů, disk a skupinu prostředků. Skript výše přidá zápisu akcelerátoru do existujícího disku, kde je hodnota $newstatus nastaven na '$true'. Pomocí hodnoty '$false' vypne akcelerátoru zapisovat na daný disk.

> [!Note]
> Provádění skriptu výše odpojit disk zadaný, povolí zápis akcelerátoru proti disku a pak připojte disk

### <a name="enabling-through-rest-apis"></a>Povolení prostřednictvím rozhraní API Rest
Abyste mohli nasadit prostřednictvím Rest API služby Azure, je potřeba nainstalovat Azure armclient

#### <a name="install-armclient"></a>Nainstalujte armclient

Pokud chcete spustit armclient, musíte ji nainstalovat prostřednictvím Chocolatey. Můžete ho nainstalovat prostřednictvím cmd.exe nebo prostředí powershell. Zvýšenými oprávněními použijte tyto příkazy ("Spustit jako správce").

Pomocí cmd.exe spusťte následující příkaz:

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

Pomocí prostředí PowerShell, budete muset použít:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Nyní můžete nainstalovat armclient s příkaz níže v cmd.exe nebo Power Shell

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>Získávání aktuální konfiguraci virtuálního počítače
Chcete-li změnit atributy vaše konfigurace disků, musíte nejprve získat aktuální konfiguraci v souboru JSON. Aktuální konfigurace můžete získat spuštěním následujícího příkazu:

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
Podmínky v rámci <> <>nahraďte vaše data, včetně názvu souboru, který by měl mít souboru JSON.

Výstup by měl vypadat jako:

```
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

Dalším krokem je aktualizace souboru JSON a povolit zápis akcelerátoru na disku s názvem 'log1'. Tento krok lze provést tak, že přidáte tento atribut do souboru JSON po položky mezipaměti disku. 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Aktualizujte stávající nasazení pomocí tohoto příkazu:

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

Výstup by měl vypadat jako jeden níže. Uvidíte, že je zápis akcelerátoru povolené pro jeden disk.

```
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
          **"writeAcceleratorEnabled": true,**
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

Z bodu změny na jednotky by mělo podporovat zápis akcelerátoru.

 
