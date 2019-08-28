---
title: Spuštění virtuálního počítače se zablokuje při přípravě Windows. Nevypínejte počítač v Azure | Microsoft Docs
description: Zaveďte kroky pro řešení potíží, při kterých se spuštění virtuálního počítače zablokuje při přípravě na Windows. Nevypínejte počítač.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 2f3c18ea1887ea5b05bb89f85371139ac83dfe49
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080162"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Spuštění virtuálního počítače se zablokuje při přípravě Windows. Nevypínejte počítač v Azure

Tento článek vám pomůže vyřešit problém, pokud je váš virtuální počítač zablokovaný v části Příprava na Windows. Během spouštění nevypínejte fázi počítač.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Příznaky

Když použijete **diagnostiku spouštění** k získání snímku obrazovky virtuálního počítače, operační systém se úplně nespustí. Virtuální počítač zobrazí zprávu "Příprava Windows na přípravu. Nevypínejte počítač.

![Příklad zprávy pro Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Příklad zprávy](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Příčina

K tomuto problému obvykle dochází, když server provádí konečné restartování po změně konfigurace. Změna konfigurace může být inicializována aktualizacemi systému Windows nebo změnami rolí/funkcí serveru. Pro web Windows Update, pokud je velikost aktualizací velká, operační systém potřebuje více času na změnu konfigurace změn.

## <a name="back-up-the-os-disk"></a>Zálohování disku s operačním systémem

Než se pokusíte problém vyřešit, zálohujte disk s operačním systémem.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>U virtuálních počítačů s šifrovaným diskem je potřeba nejdřív odemknout disky.

Pomocí těchto kroků zjistíte, jestli je virtuální počítač zašifrovaný.

1. Na Azure Portal otevřete virtuální počítač a přejděte na disky.

2. Podívejte se na sloupec **šifrování** a zjistěte, jestli je povolené šifrování.

Pokud je disk s operačním systémem zašifrovaný, odemkněte zašifrovaný disk. K odemknutí disku použijte následující postup.

1. Vytvořte virtuální počítač pro obnovení, který se nachází ve stejné skupině prostředků, účtu úložiště a umístění jako ovlivněný virtuální počítač.

2. V Azure Portal odstraňte ovlivněný virtuální počítač a zachovejte disk.

3. Spusťte PowerShell jako správce.

4. Pokud chcete získat název tajného kódu, spusťte následující rutinu.

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Po použití tajného názvu spusťte v PowerShellu následující příkazy.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Převeďte hodnotu zakódovanou na base64 na bajty a zapište výstup do souboru. 

    > [!Note]
    > Pokud použijete možnost odemčení USB, název souboru klíče bek se musí shodovat s původním identifikátorem GUID klíče bek. Než budete postupovat podle těchto kroků, vytvořte složku na jednotce C s názvem "klíče bek".
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Po vytvoření souboru klíče bek na počítači zkopírujte soubor do virtuálního počítače pro obnovení, ke kterému máte připojený disk s operačním systémem. Spusťte následující příkazy pomocí umístění souboru klíče bek.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Volitelné**: V některých scénářích může být nutné dešifrovat disk pomocí tohoto příkazu.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Předchozí příkaz předpokládá, že disk, který se má zašifrovat, je na písmeno F.

8. Pokud potřebujete shromažďovat protokoly, přečtěte si **písmeno jednotky pro cestu: \ Windows\System32\winevt\Logs**.

9. Odpojte jednotku od počítače pro obnovení.

### <a name="create-a-snapshot"></a>Vytvoření snímku

Chcete-li vytvořit snímek, postupujte podle kroků v části vytvoření [snímku disku](../windows/snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Shromažďování výpisu paměti operačního systému

Postup uvedený v části [Výpis stavu operačního](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) systému se používá ke shromáždění výpisu operačního systému v případě, že je virtuální počítač zablokovaný v konfiguraci.

## <a name="contact-microsoft-support"></a>Kontaktovat podporu Microsoftu

Po shromáždění souboru s výpisem paměti se obraťte na [podporu Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby se provedla analýza hlavní příčiny.


## <a name="rebuild-the-vm-by-using-powershell"></a>Opětovné sestavení virtuálního počítače pomocí PowerShellu

Po shromáždění souboru s výpisem paměti proveďte následující postup a znovu sestavte virtuální počítač.

**Pro nespravované disky**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**Pro Managed disks**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

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

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
