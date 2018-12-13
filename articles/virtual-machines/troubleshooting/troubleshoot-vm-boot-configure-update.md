---
title: Po spuštění virtuálního počítače se zasekla na "Začínáme připravené pro Windows. Nevypínejte počítač"v Azure | Dokumentace Microsoftu
description: Zavést kroky k odstranění tohoto problému, ve kterém se zasekne při spuštění virtuálního počítače na "Začínáme připravené pro Windows. Nevypínejte počítač.
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
ms.openlocfilehash: eb27b4e6c60f23a55a58cd2aae3cff927ffeaf03
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316093"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Po spuštění virtuálního počítače se zasekla na "Začínáme připravené pro Windows. Nevypínejte počítač"v Azure

Tento článek pomůže vyřešit problém, když virtuální počítač (VM) se zasekla na "Začínáme Windows připraveno. Nevypínejte počítač"fáze, při spuštění.

## <a name="symptoms"></a>Příznaky

Při použití **Diagnostika spouštění** získat snímek obrazovky virtuálního počítače, operační systém nespustí plně. Virtuální počítač zobrazí zprávu "Začínáme připravené pro Windows. Nevypínejte počítač.

![Ukázková zpráva pro Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Ukázková zpráva](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Příčina

Tomuto problému obvykle dochází, když server provádí konečné restartování po změny konfigurace. Změna konfigurace může být inicializován, aktualizacemi Windows nebo změny v rámci role nebo funkce serveru. Pro Windows Update Pokud byl velký, velikost aktualizace operačního systému vyžaduje více času k překonfigurování změny.

## <a name="back-up-the-os-disk"></a>Zálohování disku s operačním systémem

Než se pokusíte k vyřešení tohoto problému, proveďte zálohu disku s operačním systémem.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>Pro virtuální počítače pomocí šifrovaného disku musíte odemknout disky nejprve

Postupujte podle těchto kroků k určení, zda je virtuální počítač šifrovaný virtuální počítač.

1. Na portálu Azure portal otevřete váš virtuální počítač a potom vyhledejte disky.

2. Podívejte se na **šifrování** sloupec, který chcete zjistit, zda je povoleno šifrování.

Pokud je disk s operačním systémem zašifrovaný, odemkněte šifrovaného disku. K odemknutí disku, postupujte podle těchto kroků.

1. Vytvoření virtuálního počítače pro obnovení, který je umístěný ve stejné skupině prostředků, účet úložiště a umístění jako ovlivněných virtuálních počítačů.

2. Na webu Azure Portal odstraňte ovlivněných virtuálních počítačů a zachovat na disku.

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

5. Jakmile budete mít název tajného kódu, spusťte následující příkazy v prostředí PowerShell.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Převeďte hodnotu s kódováním Base64 na bajty a zapisovat výstup do souboru. 

    > [!Note]
    > Pokud používáte zařízení USB možnost odemknout, název souboru klíče BEK musí odpovídat identifikátoru GUID původního klíče BEK. Vytvoření složky na jednotce C s názvem "Klíče BEK" před provedením těchto kroků.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Po vytvoření souboru klíče BEK ve vašem počítači, zkopírujte soubor do máte uzamčené disk s operačním systémem připojené k virtuální počítač pro obnovení. Spuštěním následujících příkazů pomocí klíče BEK umístění souboru.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Volitelné**: V některých případech může být nutný k dešifrování disku pomocí tohoto příkazu.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Předchozí příkaz předpokládá, že je na disk a šifrování na písmeno F.

8. Pokud potřebujete shromažďovat protokoly, přejděte k cestě **písmeno jednotky: \Windows\System32\winevt\Logs**.

9. Odpojení disku před počítač pro obnovení.

### <a name="create-a-snapshot"></a>Vytvoření snímku

K vytvoření snímku, postupujte podle kroků v [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Shromažďovat výpis stavu paměti operačního systému

Postupujte podle kroků v [os shromažďování výpisu stavu systému](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) část shromažďování výpisu stavu systému operačním systémem, je-li virtuální počítač se zasekla na konfiguraci.

## <a name="contact-microsoft-support"></a>Kontaktovat podporu Microsoftu

Jakmile shromáždíte soubor s výpisem paměti, obraťte se na [podpory Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) k analýze hlavní příčinu.


## <a name="rebuild-the-vm-by-using-powershell"></a>Opětovné vytvoření virtuálního počítače pomocí Powershellu

Jakmile shromáždíte souboru výpisu paměti, použijte následující postup znovu vytvořit virtuální počítač.

**Pro nespravované disky**

```PowerShell
# To log in to Azure Resource Manager
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
# To log in to Azure Resource Manager
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

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzureRmVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzureRmVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
