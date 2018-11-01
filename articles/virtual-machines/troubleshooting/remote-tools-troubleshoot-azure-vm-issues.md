---
title: Pomocí nástrojů pro vzdálenou potíží virtuálního počítače Azure | Dokumentace Microsoftu
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: d9bb13b24a16cc38f738d9a654789d4410225c09
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633784"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Použití nástrojů pro vzdálenou potíží virtuálního počítače Azure

Při odstraňování potíží v Azure virtuální počítač (VM) můžete připojit k virtuálnímu počítači pomocí nástrojů remote tools, které jsou popsané v tomto článku namísto použití protokolu RDP (Remote Desktop).

## <a name="serial-console"></a>Konzola sériového portu

Použití [konzoly sériového portu virtuálního počítače](serial-console-windows.md) ke spouštění příkazů na vzdáleném virtuálním počítači Azure.

## <a name="remote-cmd"></a>Vzdálené CMD

Stáhněte si [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Připojte se k virtuálnímu počítači spuštěním následujícího příkazu:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* Příkaz musí spustit v počítači, který je ve stejné virtuální síti.
>* VYHRAZENÉ IP adresy nebo názvu hostitele je možné nahradit <computer>.
>* Parametr -s zajišťuje, že příkaz je vyvolán pomocí systémový účet (oprávnění správce).
>* PsExec používá porty TCP 135 a 445. Proto se dvěma porty musí být ve firewallu otevřené.

## <a name="run-commands"></a>Spouštěcí příkazy

Zobrazit [spustit Powershellové skripty ve virtuálním počítači Windows pomocí příkazu Spustit](../windows/run-command.md) Další informace o tom, jak používat funkci spustit příkazy ke spouštění skriptů na virtuálním počítači.

## <a name="customer-script-extension"></a>Rozšíření vlastních skriptů

Funkce rozšíření vlastních skriptů můžete spustit vlastní skript na cílovém virtuálním počítači. Chcete-li tuto funkci používat, musí být splněny následující podmínky:

* Virtuální počítač může připojit.

* Azure Agent je nainstalovaný a funguje podle očekávání na virtuálním počítači.

* Rozšíření nebyla dřív nainstalovaná na virtuálním počítači.
 
  Rozšíření se skript vložit jenom při prvním spuštění, který se používá. Pokud tuto funkci použít později, rozšíření rozpozná, že již byl použit a neodešle nový skript.

Budete muset Nahrát skript do účtu úložiště a generovat vlastní kontejner. Potom spusťte následující skript v prostředí Azure PowerShell v počítači, který má připojení k virtuálnímu počítači.

### <a name="for-v1-vms"></a>Pro virtuální počítače V1

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

### <a name="for-v2-vms"></a>U virtuálních počítačů V2

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
Import-Module AzureRM
Login-AzureRmAccount #Ensure Login with account associated with subscription ID
Get-AzureRmSubscription -SubscriptionId $subscriptionID | Select-AzureRmSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzureRmVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Vzdáleného prostředí PowerShell

>[!Note]
>TCP Port 5986 (HTTPS) musí být otevřený, aby mohli používat tuto možnost.
>
>Pro virtuální počítače ARM musíte otevřít port 5986 na skupiny zabezpečení sítě (NSG). Další informace najdete v tématu skupiny zabezpečení. 
>
>Pro virtuální počítače RDFE, musí mít koncový bod, který má privátní port (5986) a veřejný port. Potom budete muset také otevřít tento veřejný přístup – port na skupiny zabezpečení sítě.

### <a name="set-up-the-client-computer"></a>Nastavení klientského počítače

Vzdálené připojení k virtuálnímu počítači pomocí Powershellu, musíte nejprve nastavit klientského počítače a povolit připojení. K tomuto účelu se virtuální počítač přidal do seznamu důvěryhodných hostitelů PowerShell spuštěním následujícího příkazu, podle potřeby.

Chcete-li přidat jeden virtuální počítač do seznamu důvěryhodných hostitelů:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Chcete-li přidat více virtuálních počítačů do seznamu důvěryhodných hostitelů:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Všechny počítače přidat do seznamu důvěryhodných hostitelů:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Povolit RemotePS na virtuálním počítači

Pro klasické virtuální počítače spusťte následující skript pomocí rozšíření vlastních skriptů:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Pro virtuální počítače ARM použijte příkazy spustit z portálu ke spuštění skriptu EnableRemotePS:

![Spuštění příkazu](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Spusťte následující příkaz, v závislosti na klientovi umístění počítače:

* Mimo virtuální síť nebo nasazení

    * Klasický virtuální počítač spusťte následující příkaz:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

    * Pro virtuální počítač ARM nejprve přidáte název DNS na veřejnou IP adresu. Podrobné pokyny najdete v článku [vytvořit použitím plně kvalifikovaného názvu domény pro virtuální počítač s Windows na webu Azure Portal](../windows/portal-create-fqdn.md). Pak spusťte následující příkaz:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Uvnitř virtuální sítě nebo nasazení spusťte následující příkaz:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>Nastavení příznaku SkipCaCheck obchází požadavek na Import certifikátu do virtuálního počítače při spuštění relace.

Můžete také použít rutinu Invoke-Command ke vzdálenému spouštění skriptu ve virtuálním počítači:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Vzdálený registr

>[!Note]
>Port TCP 135 nebo 445 musí být otevřený, chcete-li použít tuto možnost.
>
>U virtuálních počítačů ARM budete muset otevřít port 5986 na skupiny zabezpečení sítě. Další informace najdete v tématu skupiny zabezpečení. 
>
>Pro virtuální počítače RDFE musí mít koncový bod, který má privátní port 5986 a veřejný port. Je třeba také otevřít tento port veřejně přístupných na skupiny zabezpečení sítě.

1. Z jiného virtuálního počítače ve stejné virtuální síti otevřete editor registru (regedit.exe).

2. Vyberte **souboru** >**připojit síť registru**.

   ![Vzdálené možnosti](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Najít cílový virtuální počítač pomocí **hostname** nebo **zjišťování dynamických IP** (vhodnější) tak, že zadáte do pole "Název objektu k výběru zadejte".

   ![Vzdálené možnosti](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Zadejte pověření pro cílový virtuální počítač.

5. Proveďte potřebné změny.

## <a name="remote-services-console"></a>Vzdálené konzoly

>[!Note]
>Porty TCP 135 nebo 445 musí být otevřený, chcete-li použít tuto možnost.
>
>U virtuálních počítačů ARM budete muset otevřít port 5986 na skupiny zabezpečení sítě. Další informace najdete v tématu skupiny zabezpečení. 
>
>Pro virtuální počítače RDFE musí mít koncový bod, který má privátní port 5986 a veřejný port. Potom budete muset také otevřít tento port veřejně přístupných na skupiny zabezpečení sítě.

1. Z jiného virtuálního počítače ve stejné virtuální síti, spusťte instanci **Services.msc**.

2. Klikněte pravým tlačítkem na **služby (místní počítač)**.

3. Vyberte **připojit k jinému počítači**.

   ![Vzdálené služby](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Zadejte IP adresu dynamické cílového virtuálního počítače.

   ![Vstup vyhrazené IP adresy](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Proveďte potřebné změny do služby.

## <a name="next-steps"></a>Další kroky

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Vlastní skript rozšíření pro Windows pomocí modelu nasazení classic](../extensions/custom-script-classic.md)

PsExec je součástí [PSTools Suite](https://download.sysinternals.com/files/PSTools.zip).

Další informace o sadě PSTools najdete v tématu [PSTools Suite](https://docs.microsoft.com/sysinternals/downloads/pstools).


