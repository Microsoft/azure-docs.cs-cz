---
title: Řešení potíží s virtuálními počítači Azure pomocí nástrojů Remote Tools | Microsoft Docs
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
ms.openlocfilehash: 70d777fe2e939c1871bc318eed439214fd3e3f60
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155732"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Řešení potíží s virtuálními počítači Azure pomocí nástrojů Remote Tools

Při řešení problémů na virtuálním počítači Azure se můžete připojit k virtuálnímu počítači pomocí nástrojů Remote Tools popsaných v tomto článku místo použití protokol RDP (Remote Desktop Protocol) (RDP).

## <a name="serial-console"></a>Sériová konzola

Pomocí [konzoly sériového portu Virtual Machine](serial-console-windows.md) spusťte příkazy na VZDÁLENÉm virtuálním počítači Azure.

## <a name="remote-cmd"></a>Vzdálený CMD

Stáhněte si [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Připojte se k virtuálnímu počítači spuštěním následujícího příkazu:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* Příkaz musí být spuštěný na počítači, který je ve stejné virtuální síti.
>* K nahrazení \<> počítače se dá použít DIP nebo název hostitele.
>* Parametr-s zajistí, že se příkaz vyvolá pomocí systémového účtu (oprávnění správce).
>* PsExec používá porty TCP 135 a 445. Proto musí být oba porty otevřeny v bráně firewall.

## <a name="run-commands"></a>Spustit příkazy

Další informace o tom, jak používat funkci spustit příkazy ke spouštění skriptů na virtuálním počítači, najdete [v tématu Spuštění skriptů PowerShellu na virtuálním počítači s Windows pomocí příkazu Run](../windows/run-command.md) .

## <a name="customer-script-extension"></a>Rozšíření zákaznického skriptu

Pomocí funkce rozšíření vlastních skriptů můžete na cílovém virtuálním počítači spustit vlastní skript. Chcete-li použít tuto funkci, musí být splněny následující podmínky:

* Virtuální počítač má připojení.

* Agent Azure je nainstalovaný a na virtuálním počítači funguje podle očekávání.

* Rozšíření nebylo na virtuálním počítači dříve nainstalováno.
 
  Rozšíření bude skript vkládat pouze při prvním použití. Použijete-li tuto funkci později, rozšíření rozpozná, že již bylo použito, a nebude nahrávat nový skript.

Váš skript musíte nahrát do účtu úložiště a vygenerovat jeho vlastní kontejner. Pak na počítači, který má připojení k VIRTUÁLNÍmu počítači, spusťte následující skript Azure PowerShell.

### <a name="for-v1-vms"></a>Pro virtuální počítače v1

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>Pro virtuální počítače v2

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Vzdálené prostředí PowerShell

>[!Note]
>Aby bylo možné použít tuto možnost, musí být spuštěný port TCP 5986 (HTTPS).
>
>V případě virtuálních počítačů ARM musíte otevřít port 5986 ve skupině zabezpečení sítě (NSG). Další informace najdete v tématu skupiny zabezpečení. 
>
>U virtuálních počítačů RDFE musíte mít koncový bod, který má privátní port (5986) a veřejný port. Pak musíte tento veřejný port na NSG otevřít také.

### <a name="set-up-the-client-computer"></a>Nastavení klientského počítače

Pokud chcete použít PowerShell pro vzdálené připojení k VIRTUÁLNÍmu počítači, musíte nejdřív nastavit klientský počítač, aby připojení bylo povolené. Pokud to chcete provést, přidejte virtuální počítač do seznamu důvěryhodných hostitelů PowerShellu tak, že podle potřeby spustíte následující příkaz.

Přidání jednoho virtuálního počítače do seznamu důvěryhodných hostitelů:

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

U klasických virtuálních počítačů spusťte pomocí rozšíření vlastních skriptů tento skript:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Pro virtuální počítače ARM použijte příkazy Run z portálu ke spuštění skriptu EnableRemotePS:

![Spuštění příkazu](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

V závislosti na umístění klientského počítače spusťte následující příkaz:

* Mimo virtuální síť nebo nasazení

  * Pro klasický virtuální počítač spusťte následující příkaz:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Pro virtuální počítač ARM nejdřív přidejte název DNS do veřejné IP adresy. Podrobný postup najdete v tématu [Vytvoření plně kvalifikovaného názvu domény v Azure Portal pro virtuální počítač s Windows](../windows/portal-create-fqdn.md). Pak spusťte následující příkaz:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* V rámci virtuální sítě nebo nasazení spusťte následující příkaz:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>Nastavení příznaku SkipCaCheck obchází požadavek na Import certifikátu do virtuálního počítače při spuštění relace.

Můžete také použít rutinu Invoke-Command ke vzdálenému spuštění skriptu na virtuálním počítači:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Vzdálený registr

>[!Note]
>Aby bylo možné použít tuto možnost, musí být spuštěný port TCP 135 nebo 445.
>
>V případě virtuálních počítačů ARM musíte na NSG otevřít port 5986. Další informace najdete v tématu skupiny zabezpečení. 
>
>U virtuálních počítačů RDFE musíte mít koncový bod s privátním portem 5986 a veřejným portem. Musíte otevřít i tento veřejně přístupný port na NSG.

1. Z jiného virtuálního počítače ve stejné virtuální síti otevřete Editor registru (Regedit. exe).

2. Vyberte **soubor** >**připojit síťový registr**.

   ![Možnost vzdálené](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Vyhledejte cílový virtuální počítač podle **názvu hostitele** nebo **dynamického protokolu IP** (vhodnější) tak, že ho zadáte do pole zadejte název objektu k výběru.

   ![Možnost vzdálené](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Zadejte přihlašovací údaje pro cílový virtuální počítač.

5. Proveďte potřebné změny registru.

## <a name="remote-services-console"></a>Konzola vzdálených služeb

>[!Note]
>Aby bylo možné použít tuto možnost, musí být otevřeno porty TCP 135 nebo 445.
>
>V případě virtuálních počítačů ARM musíte na NSG otevřít port 5986. Další informace najdete v tématu skupiny zabezpečení. 
>
>U virtuálních počítačů RDFE musíte mít koncový bod s privátním portem 5986 a veřejným portem. Pak musíte otevřít tento veřejně přístupný port na NSG.

1. Z jiného virtuálního počítače ve stejné virtuální síti otevřete instanci **služby Services. msc**.

2. Klikněte pravým tlačítkem na **služby (místní)** .

3. Vyberte **připojit k jinému počítači**.

   ![Vzdálená služba](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Zadejte dynamickou IP adresu cílového virtuálního počítače.

   ![Vstupní DIP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Proveďte potřebné změny služeb.

## <a name="next-steps"></a>Další kroky

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Rozšíření vlastních skriptů pro Windows s použitím modelu nasazení Classic](../extensions/custom-script-classic.md)

PsExec je součástí [sady nástroj PsTools](https://download.sysinternals.com/files/PSTools.zip).

Další informace o sadě nástroj PsTools naleznete v tématu [Nástroj PsTools Suite](https://docs.microsoft.com/sysinternals/downloads/pstools).


