---
title: Řešení problémů s virtuálním počítačem Azure pomocí vzdálených nástrojů | Dokumenty společnosti Microsoft
description: ''
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
ms.openlocfilehash: b86b1a2d8a49554cc3df99e0a32a2c0ccaacb560
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920004"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Řešení problémů s virtuálním počítačem Azure pomocí vzdálených nástrojů

Při řešení problémů na virtuálním počítači (VM) Azure se můžete připojit k virtuálnímu počítači pomocí vzdálených nástrojů, které jsou popsány v tomto článku namísto použití protokolu RDP (RdP).

## <a name="serial-console"></a>Konzola sériového portu

Ke spouštění příkazů na vzdáleném virtuálním počítači Azure použijte [sériovou konzolu pro virtuální počítače Azure.](serial-console-windows.md)

## <a name="remote-cmd"></a>Vzdálený CMD

Stáhnout [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Připojte se k virtuálnímu virtuálnímu provozu spuštěním následujícího příkazu:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* Příkaz musí být spuštěn v počítači, který je ve stejné virtuální síti.
>* Dip nebo HostName lze \<nahradit> počítače.
>* Parametr -s zajišťuje, že je příkaz vyvolán pomocí systémového účtu (oprávnění správce).
>* PsExec používá tcp porty 135 a 445. V důsledku toho musí být oba porty otevřeny na bráně firewall.

## <a name="run-command"></a>Spusťte příkaz .

Další informace o použití funkce příkazu spustit ke spouštění skriptů na virtuálním počítači najdete v tématu [Spuštění skriptů prostředí PowerShell v virtuálním počítači systému Windows pomocí příkazu spustit](../windows/run-command.md).

## <a name="custom-script-extension"></a>Rozšíření vlastních skriptů

Pomocí funkce Rozšíření vlastního skriptu můžete spustit vlastní skript na cílovém virtuálním počítači. Chcete-li tuto funkci použít, musí být splněny následující podmínky:

* Virtuální připojení má připojení.
* Azure Virtual Machine Agent je nainstalovaný a funguje podle očekávání na virtuálním počítači.
* Rozšíření nebyl dříve nainstalován na virtuálním počítači.
 
  Rozšíření vloží skript pouze při prvním použití. Pokud tuto funkci použijete později, rozšíření rozpozná, že již bylo použito, a nenahraje nový skript.

Nahrajte skript do účtu úložiště a vygenerujte vlastní kontejner. Potom spusťte následující skript v Azure PowerShell u počítače, který má připojení k virtuálnímu počítači.

### <a name="for-classic-deployment-model-vms"></a>Pro klasické virtuální počítače nasazení

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

### <a name="for-azure-resource-manager-vms"></a>Pro virtuální počítače Azure Resource Manager

 

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
>Tcp Port 5986 (HTTPS) musí být otevřený, abyste mohli použít tuto možnost.
>
>Pro virtuální počítače Azure Resource Manager je nutné otevřít port 5986 ve skupině zabezpečení sítě (NSG). Další informace naleznete v tématu Skupiny zabezpečení. 
>
>Pro virtuální zařízení RDFE musíte mít koncový bod, který má soukromý port (5986) a veřejný port. Potom budete muset také otevřít, že veřejný port na nsg.

### <a name="set-up-the-client-computer"></a>Nastavení klientského počítače

Chcete-li použít Prostředí PowerShell pro vzdálené připojení k virtuálnímu počítači, musíte nejprve nastavit klientský počítač, aby bylo připojení povoleno. Chcete-li to provést, přidejte virtuální ho do seznamu důvěryhodných hostitelů prostředí PowerShell spuštěním následujícího příkazu, podle potřeby.

Přidání jednoho virtuálního virtuálního zařízení do seznamu důvěryhodných hostitelů:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Přidání více virtuálních počítačů do seznamu důvěryhodných hostitelů:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Přidání všech počítačů do seznamu důvěryhodných hostitelů:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Povolení vzdáleného ps na virtuálním počítači

Pro virtuální počítače vytvořené pomocí klasického modelu nasazení spusťte pomocí rozšíření Vlastní skript k spuštění následujícího skriptu:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Pro virtuální počítače Azure Resource Manager, použijte spustit příkazy z portálu ke spuštění enableRemotePS skript:

![Spusťte příkaz .](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Spusťte následující příkaz založený na umístění klientského počítače:

* Mimo virtuální síť nebo nasazení

  * Pro virtuální počítače vytvořené pomocí modelu klasického nasazení spusťte následující příkaz:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Pro virtuální počítač Azure Resource Manager nejprve přidejte název DNS na veřejnou IP adresu. Podrobné kroky najdete [v tématu Vytvoření plně kvalifikovaného názvu domény na portálu Azure pro virtuální počítač s Windows](../windows/portal-create-fqdn.md). Pak spusťte následující příkaz:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Uvnitř virtuální sítě nebo nasazení spusťte následující příkaz:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Nastavení příznaku SkipCaCheck obchází požadavek na import certifikátu do virtuálního soudu při spuštění relace.

Rutina Invoke-Command můžete také použít ke vzdálenému spuštění skriptu na virtuálním počítači.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Vzdálený registr

>[!NOTE]
>Aby bylo možné tuto možnost použít, musí být otevřený port TCP 135 nebo 445.
>
>Pro virtuální počítače Azure Resource Manager, musíte otevřít port 5986 na nsg. Další informace naleznete v tématu Skupiny zabezpečení. 
>
>Pro virtuální připojení RDFE musíte mít koncový bod, který má soukromý port 5986 a veřejný port. Musíte také otevřít veřejný přístav na nsg.

1. Z jiného virtuálního počítače ve stejné virtuální síti otevřete editor registru (regedit.exe).

2. Vyberte **položku Registr** > připojení souborů **.**

   ![Editor registru](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Locate the target VM by **host name** or **dynamic IP** (preferable) by entering it in the **Enter the object name to select** box.

   ![Zadejte pole s názvem objektu, který chcete vybrat.](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Zadejte přihlašovací údaje pro cílový virtuální virtuální soud.

5. Proveďte nezbytné změny registru.

## <a name="remote-services-console"></a>Konzola vzdálených služeb

>[!NOTE]
>Aby bylo možné tuto možnost použít, musí být otevřeny porty TCP 135 nebo 445.
>
>Pro virtuální počítače Azure Resource Manager, musíte otevřít port 5986 na nsg. Další informace naleznete v tématu Skupiny zabezpečení. 
>
>Pro virtuální připojení RDFE musíte mít koncový bod, který má soukromý port 5986 a veřejný port. Musíte také otevřít veřejný přístav na nsg.

1. Z jiného virtuálního počítače ve stejné virtuální síti otevřete instanci **Services.msc**.

2. Klepněte pravým tlačítkem myši na **položku Služby (Místní).**

3. Vyberte **Připojit k jinému počítači**.

   ![Vzdálená služba](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Zadejte dynamickou IP adresu cílového virtuálního počítačů.

   ![Vstupní dynamická IP adresa](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Proveďte nezbytné změny služeb.

## <a name="next-steps"></a>Další kroky

- Další informace o rutině Enter-PSSession naleznete [v tématu Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- Další informace o vlastní aplikaci Script Extension pro Windows pomocí klasického modelu nasazení naleznete v [tématu Custom Script Extension for Windows](../extensions/custom-script-classic.md).
- PsExec je součástí [sady PSTools Suite](https://download.sysinternals.com/files/PSTools.zip).
- Další informace o sadě PSTools Suite naleznete v tématu [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).


