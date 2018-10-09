---
title: Po spuštění virtuálního počítače se zasekla na "Windows Příprava. Nevypínejte počítač." v Azure | Dokumentace Microsoftu
description: Zavést kroky k odstranění tohoto problému, ve kterém virtuálním počítači se zasekne při spuštění na "Windows Příprava. Nevypínejte počítač".
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: e0ad7a8b083a72a9d3c630fa53601aa8fb6ad601
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48862770"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Po spuštění virtuálního počítače se zasekla na "Windows Příprava. Nevypínejte počítač." v Azure

Tento článek pomůže vyřešit problém, když se zasekne vašeho virtuálního počítače (VM) na "Začínáme Windows připraveno. Nevypínejte počítač." fáze během spouštění.

## <a name="symptoms"></a>Příznaky

Při použití **Diagnostika spouštění** získat snímek obrazovky virtuálního počítače, zjistíte, operační systém se nespustí plně. Kromě toho virtuální počítač zobrazení **"Příprava Windows. Nevypínejte počítač."** zpráva.

![Ukázková zpráva](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Ukázková zpráva](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Příčina

Tomuto problému obvykle dochází, když server provádí konečné restartování po změny konfigurace. Změna konfigurace nelze inicializovat pomocí aktualizací Windows nebo změny v rámci role nebo funkce serveru. Pro Windows Update Pokud byl velký, velikost aktualizace operačního systému potřebovat více času na překonfigurovat změny.

## <a name="back-up-the-os-disk"></a>Zálohování disku s operačním systémem

Než se pokusíte k vyřešení tohoto problému, proveďte zálohu disku s operačním systémem:

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>Pro virtuální počítače pomocí šifrovaného disku musíte odemknout disky nejprve

Ověřte, jestli je virtuální počítač šifrovaný virtuální počítač. Postupujte přitom takto:

1. Na portálu otevřete váš virtuální počítač a potom vyhledejte disky.

2. Zobrazí se vám sloupec volání "Šifrování", což vám dá vědět, jestli je povolené šifrování.

Pokud je Disk s operačním systémem zašifrovaný, odemkněte šifrovaného disku. Postupujte přitom takto:

1. Vytvoření virtuálního počítače pro obnovení, který je umístěný ve stejné skupině prostředků, účet úložiště a umístění jako ovlivněných virtuálních počítačů.

2. Na webu Azure portal odstraňte ovlivněných virtuálních počítačů a zachovat na disku.

3. Spusťte PowerShell jako správce.

4. Spuštěním následující rutiny můžete získat název tajného kódu.

    ```Powershell
    Login-AzureRmAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Jakmile budete mít název tajného kódu, spusťte následující příkazy v prostředí PowerShell:

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Převést hodnotou kódovanou jako Base64 na bajty a zapisovat výstup do souboru. 

    > [!Note]
    > Název souboru klíče BEK musí odpovídat původní možnost Odemknout klíče BEK GUID, pokud používáte zařízení USB. Kromě toho budete potřebovat k vytvoření složky na jednotce C s názvem "Klíče BEK" předtím, než takto bude fungovat.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Po vytvoření souboru klíče BEK ve vašem počítači, zkopírujte soubor do máte uzamčené disk s operačním systémem připojené k virtuální počítač pro obnovení. Spusťte následující příkaz pomocí klíče BEK umístění souboru:

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Volitelné** v některých scénářích může být nutné také dešifrování disků pomocí tohoto příkazu.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Toto je vzhledem k tomu, že je disk k šifrování na písmenem F:.

8. Pokud potřebujete shromažďovat protokoly, můžete přejít k cestě **písmeno jednotky: \Windows\System32\winevt\Logs**.

9. Odpojení disku před počítač pro obnovení.

### <a name="create-a-snapshot"></a>Vytvoření snímku

K vytvoření snímku, postupujte podle kroků v [pořízení snímku disku](..\windows\snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Shromažďovat výpis stavu paměti operačního systému

Postupujte podle kroků v [os shromažďování výpisu stavu systému](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) část shromažďování výpisu stavu systému operačním systémem, je-li virtuální počítač se zasekla na konfiguraci.

## <a name="contact-microsoft-support"></a>Kontaktovat podporu Microsoftu

Jakmile shromáždíte soubor s výpisem paměti, obraťte se na [podpory Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) analýzu původní příčinu.


## <a name="rebuild-the-vm-using-powershell"></a>Znovu sestavit virtuálního počítače pomocí Powershellu

Jakmile shromáždíte souboru výpisu paměti, v následujícím návodu k opětovnému sestavení virtuálního počítače.

**Pro nespravované disky**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID “SubscriptionID” | Select-AzureRmSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzureRmVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzureRmNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzureRmVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzureRmVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**Za spravované disky**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID "SubscriptionID" | Select-AzureRmSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure Portal if the format below doesn't match
$osDiskResouceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availabilty Set
#$avSet = Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResouceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResouceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additnal Data Disk
#Add-AzureRmVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzureRmVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
