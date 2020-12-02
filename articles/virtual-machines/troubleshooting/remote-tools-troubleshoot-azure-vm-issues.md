---
title: Řešení potíží s virtuálními počítači Azure pomocí nástrojů Remote Tools | Microsoft Docs
description: Přečtěte si o PsExec, skriptech PowerShellu a dalších vzdálených nástrojích, které můžete použít k řešení potíží se vzdáleným virtuálním počítačem Azure bez použití RDP.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: c5974388c096c9bc8693c5fc2cf918989c6eadd3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488727"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Řešení potíží s virtuálními počítači Azure pomocí nástrojů Remote Tools

Při řešení problémů na virtuálním počítači Azure se můžete připojit k virtuálnímu počítači pomocí nástrojů Remote Tools popsaných v tomto článku místo použití protokol RDP (Remote Desktop Protocol) (RDP).

## <a name="serial-console"></a>Konzola sériového portu

Pomocí [sériové konzoly pro Azure Virtual Machines](serial-console-windows.md) spusťte příkazy na VZDÁLENÉm virtuálním počítači Azure.

## <a name="remote-cmd"></a>Vzdálený CMD

Stáhněte si [PsExec](/sysinternals/downloads/psexec). Připojte se k virtuálnímu počítači spuštěním následujícího příkazu:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* Příkaz musí být spuštěný na počítači, který je ve stejné virtuální síti.
>* K nahrazení lze použít DIP nebo název hostitele \<computer> .
>* Parametr-s zajistí, že se příkaz vyvolá pomocí systémového účtu (oprávnění správce).
>* PsExec používá porty TCP 135 a 445. V důsledku toho musí být oba porty otevřeny v bráně firewall.

## <a name="run-command"></a>Spustit příkaz

Další informace o tom, jak používat funkci Run Command ke spouštění skriptů na virtuálním počítači, najdete v tématu [spuštění skriptů PowerShellu na virtuálním počítači s Windows pomocí příkazu Spustit](../windows/run-command.md).

## <a name="custom-script-extension"></a>Rozšíření vlastních skriptů

Pomocí funkce rozšíření vlastních skriptů můžete na cílovém virtuálním počítači spustit vlastní skript. Chcete-li použít tuto funkci, musí být splněny následující podmínky:

* Virtuální počítač má připojení.
* Agent virtuálního počítače Azure je nainstalovaný a na VIRTUÁLNÍm počítači funguje podle očekávání.
* Rozšíření se dřív na virtuálním počítači nenainstalovalo.
 
  Rozšíření vloží skript pouze při prvním použití. Pokud tuto funkci použijete později, rozšíření rozpozná, že už je použité, a neodešle nový skript.

Nahrajte svůj skript do účtu úložiště a vygenerujte svůj vlastní kontejner. Pak na počítači, který má připojení k VIRTUÁLNÍmu počítači, spusťte následující skript Azure PowerShell.

### <a name="for-classic-deployment-model-vms"></a>Pro virtuální počítače s modelem nasazení Classic

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Pro Azure Resource Manager virtuální počítače

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Vzdálené prostředí PowerShell

>[!NOTE]
>Aby bylo možné použít tuto možnost, musí být spuštěný port TCP 5986 (HTTPS).
>
>U Azure Resource Manager virtuálních počítačů musíte otevřít port 5986 ve skupině zabezpečení sítě (NSG). Další informace najdete v tématu skupiny zabezpečení. 
>
>U virtuálních počítačů RDFE musíte mít koncový bod, který má privátní port (5986) a veřejný port. Pak je také nutné otevřít tento veřejně přístupný port na NSG.

### <a name="set-up-the-client-computer"></a>Nastavení klientského počítače

Pokud chcete použít PowerShell pro vzdálené připojení k VIRTUÁLNÍmu počítači, musíte nejdřív nastavit klientský počítač, aby připojení bylo povolené. Pokud to chcete provést, přidejte virtuální počítač do seznamu důvěryhodných hostitelů PowerShellu tak, že podle potřeby spustíte následující příkaz.

Postup přidání jednoho virtuálního počítače do seznamu důvěryhodných hostitelů:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Přidání více virtuálních počítačů do seznamu důvěryhodných hostitelů:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Chcete-li přidat všechny počítače do seznamu důvěryhodných hostitelů:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Povolení RemotePS na virtuálním počítači

Pro virtuální počítače vytvořené pomocí modelu nasazení Classic použijte rozšíření vlastních skriptů a spusťte následující skript:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

U Azure Resource Managerch virtuálních počítačů použijte příkazy spustit z portálu ke spuštění skriptu EnableRemotePS:

![Spustit příkaz](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Spusťte následující příkaz založený na umístění klientského počítače:

* Mimo virtuální síť nebo nasazení

  * U virtuálního počítače vytvořeného pomocí modelu nasazení Classic spusťte následující příkaz:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Pro Azure Resource Manager virtuální počítač přidejte do veřejné IP adresy název DNS. Podrobný postup najdete v tématu [Vytvoření plně kvalifikovaného názvu domény v Azure Portal pro virtuální počítač s Windows](../create-fqdn.md). Potom spusťte následující příkaz:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* V rámci virtuální sítě nebo nasazení spusťte následující příkaz:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Nastavení příznaku SkipCaCheck obchází požadavek na Import certifikátu do virtuálního počítače při spuštění relace.

Můžete také použít rutinu Invoke-Command ke vzdálenému spuštění skriptu na virtuálním počítači.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Vzdálený registr

>[!NOTE]
>Aby bylo možné použít tuto možnost, musí být spuštěný port TCP 135 nebo 445.
>
>V případě Azure Resource Manager virtuálních počítačů je nutné na NSG otevřít port 5986. Další informace najdete v tématu skupiny zabezpečení. 
>
>U virtuálních počítačů RDFE musíte mít koncový bod s privátním portem 5986 a veřejným portem. Také je nutné otevřít tento veřejně přístupný port na NSG.

1. Z jiného virtuálního počítače ve stejné virtuální síti otevřete Editor registru (regedit.exe).

2. Vyberte **soubor**  >  **připojit síťový registr**.

   ![Editor registru](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Vyhledejte cílový virtuální počítač podle **názvu hostitele** nebo **dynamického protokolu IP** (vhodnější) tak, že ho zadáte do pole **Zadejte název objektu k výběru** .

   ![Zadejte název objektu k výběru.](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Zadejte přihlašovací údaje pro cílový virtuální počítač.

5. Proveďte potřebné změny registru.

## <a name="remote-services-console"></a>Konzola vzdálených služeb

>[!NOTE]
>Aby bylo možné použít tuto možnost, musí být otevřeno porty TCP 135 nebo 445.
>
>V případě Azure Resource Manager virtuálních počítačů je nutné na NSG otevřít port 5986. Další informace najdete v tématu skupiny zabezpečení. 
>
>U virtuálních počítačů RDFE musíte mít koncový bod s privátním portem 5986 a veřejným portem. Také je nutné otevřít tento veřejně přístupný port na NSG.

1. Z jiného virtuálního počítače ve stejné virtuální síti otevřete instanci **služby Services. msc**.

2. Klikněte pravým tlačítkem na **služby (místní)**.

3. Vyberte **připojit k jinému počítači**.

   ![Vzdálená služba](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Zadejte dynamickou IP adresu cílového virtuálního počítače.

   ![Vstupní dynamická IP adresa](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Proveďte potřebné změny služeb.

## <a name="next-steps"></a>Další kroky

- Další informace o rutině Enter-PSSession naleznete v tématu [Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession?view=powershell-5.1).
- Další informace o rozšíření vlastních skriptů pro Windows s použitím modelu nasazení Classic najdete v tématu [rozšíření vlastních skriptů pro Windows](../extensions/custom-script-windows.md).
- PsExec je součástí [sady nástroj PsTools](https://download.sysinternals.com/files/PSTools.zip).
- Další informace o sadě nástroj PsTools naleznete v tématu [Nástroj PsTools](/sysinternals/downloads/pstools).