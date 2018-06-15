---
title: Azure zápisu akcelerátoru pro nasazení SAP | Microsoft Docs
description: Provozní příručka pro SAP HANA systémy, které jsou nasazeny na virtuálních počítačích Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 89a4216a3893892eedd6c216c7e0e5d51cf64749
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2018
ms.locfileid: "31809984"
---
# <a name="azure-write-accelerator-for-sap-deployments"></a>Azure zápisu akcelerátoru pro nasazení SAP
Azure akcelerátoru zápisu je funkce, které je získávání vrácena výhradně pro virtuální počítače M-Series. Zápis akcelerátoru Azure není k dispozici žádné další VM-Series v Azure, s výjimkou M-Series. Jako název stavy, je účelem funkce vstupně-výstupních operací latenci zápisů Azure Premium Storage. 

Funkce zápisu akcelerátoru Azure k dispozici pro nasazení řady M jako ve verzi public preview v:

- Západní USA 2
- Východní US2
- Západní Evropa
- Jihovýchodní Asie

## <a name="planning-for-using-azure-write-accelerator"></a>Plánování pro používání Azure zápisu akcelerátoru
Azure akcelerátoru zápisu by měl použít pro svazky, které obsahují transakčního protokolu nebo vrátit protokoly databázového systému. Nedoporučuje se používat Azure zápisu akcelerátoru pro svazcích dat databázového systému. Důvod tohoto omezení je, že akcelerátoru zápisu Azure vyžaduje Azure Premium úložiště VHD upevnění bez další čtení ukládání do mezipaměti, který je k dispozici pro úložiště Premium. Větší výhody s tímto typem ukládání do mezipaměti, můžete vidět u tradičních databází. Vzhledem k tomu, že zápis akcelerátoru se týká pouze aktivity zápisu a není urychlit čtení, je podporované návrhu pro SAP použijte zápis akcelerátoru proti transakčního protokolu nebo vrátit jednotky protokolu SAP podporované databází. 

Azure akcelerátoru zapisovat pouze funguje ve spojení s [discích spravovaných pomocí Azure](https://azure.microsoft.com/services/managed-disks/). Proto je třeba naplánovat odpovídajícím způsobem. 

>[!NOTE]
> Funkce zápisu akcelerátoru Azure je stále ve verzi public preview, žádné scénář nasazení v produkčním prostředí se zatím nepodporuje na funkce.

Existují omezení Azure Premium Storage VHD na virtuální počítač, který může podporovat Azure zápisu akcelerátoru. Aktuální omezení jsou:


| VIRTUÁLNÍ POČÍTAČ SKU | Počet disků zápisu akcelerátoru | Zápis akcelerátoru IOPS na virtuálních počítačů |
| --- | --- | --- |
| M128ms | 16 | 8000 |
| M128s | 16 | 8000 |
| M64ms | 8 | 4000 |
| M64s | 8 | 4000 | 



> [!IMPORTANT]
> Pokud chcete povolit nebo zakázat Azure zápisu akcelerátoru pro existující svazek, který vychází z více disků Storage úrovně Premium a rozdělená pomocí Správce Windows disk nebo svazek, prostory úložiště ve Windows, Windows škálovatelný souborový server (SOFS), Linux LVM nebo MDADM, vytváření svazku všechny disky musí být zapnutá nebo vypnutá pro zápis akcelerátoru v samostatné kroky. **Před povolením nebo zakázáním zápisu akcelerátoru v takové konfiguraci, vypněte virtuální počítač Azure**. 


> [!IMPORTANT]
> Povolit Azure akcelerátoru k zápisu do existujícího Azure disku, který není součástí sestavení svazku mimo více disků se disk systému Windows nebo správci svazku, prostory úložiště ve Windows, souborový server (SOFS) Windows Škálováním na více systémů, Linux LVM nebo MDADM, přístup k zatížení Azure disk je možné vypnout. Databázové aplikace pomocí Azure disku musí být vypnuté.

> [!IMPORTANT]
> Povolení zápisu akcelerátoru pro virtuální počítač Azure disk operačního systému virtuálního počítače se restartuje virtuální počítač. 

Povolení Azure zápisu akcelerátoru pro operační disky by neměly být potřebné pro SAP související s konfigurací virtuálních počítačů

### <a name="restrictions-when-using-azure-write-accelerator"></a>Omezení při použití Azure zápisu akcelerátoru
Při používání Azure zápisu akcelerátoru pro Azure disk nebo VHD, platí tato omezení:

- Premium disku ukládání do mezipaměti musí být nastavena na hodnotu 'None' nebo "Jen pro čtení". Všechny ostatní režimy ukládání do mezipaměti nejsou podporovány.
- Ještě není podporované snímku na disku akcelerátoru zápis povolen. Toto omezení blokuje služby zálohování Azure schopnost provádět snímky konzistentní aplikací všechny disky virtuálního počítače.
- Menší velikost vstupně-výstupních operací trvá Zrychlený cestu. V zatížení načten situacích, kde je dat získávání hromadné nebo kde vyrovnávacích pamětí protokolu transakcí z různých databázového systému jsou vyplněny ve větší míře než získávání trvalé do úložiště, pravděpodobné, vstupně-výstupních operací zapsána do disk nejde Zrychlený cestu.


## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Povolení zápisu akcelerátoru na určitém disku
Další několik části se popisují, jak lze povolit zápis akcelerátoru Azure na Azure Premium Storage virtuálních pevných discích.

Povolení zápisu akcelerátoru prostřednictvím Rest API služby Azure a Power Shell v tuto chvíli, jsou jenom metody. Ostatní metody až podpory v Azure bude sledovat portálu v průběhu další trvá několik týdnů.

### <a name="prerequisites"></a>Požadavky
Následující požadavky platí pro používání Azure zápisu akcelerátoru v tuto chvíli:

- Vaše ID odběru, která byla použita k nasazení virtuálních počítačů a úložiště pro virtuální počítač musí být uvedené prázdné. Kontaktujte Microsoft CSA, GBB, nebo uvedené prázdné účet správce získat svoje ID předplatného. 
- Musí být disky, které chcete použít Azure zápisu akcelerátoru proti [discích spravovaných pomocí Azure](https://azure.microsoft.com/services/managed-disks/).

### <a name="enabling-through-power-shell"></a>Povolení prostřednictvím Power Shell
Modul prostředí PowerShell Azure z verze 5.5.0 zahrnují změny do příslušných rutin k povolení nebo zakázání Azure zápisu akcelerátoru pro konkrétní disky Azure Premium Storage.
Aby bylo možné povolit nebo nasazení podporuje zápis akcelerátoru disky, tu následující příkazy prostředí PowerShell změnit a rozšířit tak, aby přijímal parametr pro zápis akcelerátoru.

Nový parametr přepínač "WriteAccelerator" byly přidány do následující rutiny: 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Nesmí mít parametr nastaví vlastnost na hodnotu false a provede nasazení disky, které nemají žádnou podporu pomocí Azure zápisu akcelerátoru.

Nový parametr přepínač "OsDiskWriteAccelerator" byla přidána do následující rutiny: 

- Set-AzureRmVmssStorageProfile

Není poskytnutí sady vlastnost na hodnotu false a získávat disky, které využívají není akcelerátoru zápisu Azure.

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

#### <a name="adding--new-disk-supported-by-azure-write-accelerator"></a>Přidání nové disk podporovaný službou Azure zápisu akcelerátoru
Tento skript můžete přidat nový disk k virtuálnímu počítači. Disk, který vytvořili pomocí tohoto skriptu bude používat Azure zápisu akcelerátoru.

```

# Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
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
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "testsap-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

Je nutné přizpůsobit názvy virtuálních počítačů, disk a skupinu prostředků. Skript výše přidá zápisu akcelerátoru existující disk je, že hodnota $newstatus je nastaven na '$true'. Pomocí hodnoty '$false' vypne akcelerátoru zapisovat na daný disk.

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

 