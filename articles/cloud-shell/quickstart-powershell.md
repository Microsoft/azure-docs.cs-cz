---
title: Prostředí PowerShell v cloudu Azure Quickstart prostředí (Preview) | Microsoft Docs
description: Rychlý start pro prostředí PowerShell v prostředí cloudu
services: Azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: damaerte
ms.openlocfilehash: a85d718d1c524a240f4b59b4db5004595fe1902f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608606"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell-preview"></a>Rychlý start pro prostředí PowerShell v prostředí cloudu Azure (Preview)

Tento dokument podrobně popisuje, jak pomocí prostředí PowerShell v prostředí cloudu v [portál Azure](https://aka.ms/PSCloudPreview).

> [!NOTE]
> A [Bash v prostředí cloudu Azure](quickstart.md) rychlý start je také k dispozici.

## <a name="start-cloud-shell"></a>Spusťte prostředí cloudu

1. Klikněte na **cloudové prostředí** tlačítko horním navigačním panelu portálu Azure

  ![](media/quickstart-powershell/shell-icon.png)

2. Z rozevíracího seznamu vyberte prostředí PowerShell a zobrazí se v Azure jednotce `(Azure:)`

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Spouštění příkazů prostředí PowerShell

Regulární příkazy prostředí PowerShell spusťte v prostředí cloudu, jako například:

```PowerShell
PS Azure:\> Get-Date
Monday, September 25, 2017 08:55:09 AM

PS Azure:\> Get-AzureRmVM -Status

ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Přejděte prostředků Azure

 1. Seznam odběrů

    ``` PowerShell
    PS Azure:\> dir
    ```

 2. `cd` k předplatnému upřednostňované

    ``` PowerShell
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Zobrazit všechny prostředky Azure v rámci aktuálního předplatného
 
    Typ `dir` seznam více zobrazení vašich prostředků Azure.
 
    ``` PowerShell
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
     ```

### <a name="allresources-view"></a>AllResources zobrazení 
Typ `dir` pod `AllResources` directory zobrazíte vašich prostředků Azure.
    
    PS Azure:\MySubscriptionName> dir AllResources

### <a name="explore-resource-groups"></a>Prozkoumejte skupiny prostředků

 Můžete přejít na `ResourceGroups` adresář a do určité skupiny zdrojů můžete najít virtuálního počítače.

``` PowerShell
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271

```
> [!NOTE]
> Můžete si všimnout, podruhé při zadávání `dir`, prostředí cloudu se bude moct zobrazit seznam položek mnohem rychlejší.
> Je to proto, že podřízené položky jsou uložené v mezipaměti v paměti pro lepší uživatelské prostředí.
Však můžete vždy použít `dir -Force` získat čerstvá data.

### <a name="navigate-storage-resources"></a>Přejděte prostředky úložiště
    
V rámci `StorageAccounts` adresář můžete snadno přejít vaše prostředky úložiště
    
``` PowerShell 
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files


Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>


```

Připojovacím řetězcem můžete použít následující příkaz připojit sdílenou složku Azure Files.
        
``` PowerShell
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>


```

Podrobnosti najdete v tématu [připojení Azure Files složky a přístupu ke sdílené složce v systému Windows][azmount].

Můžete také přejít adresáře sdílenou složku Azure Files následujícím způsobem:

            
``` PowerShell
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1

    
```

### <a name="interact-with-virtual-machines"></a>Komunikovat s virtuálními počítači

Můžete najít všechny virtuální počítače v aktuálním předplatném prostřednictvím `VirtualMachines` adresáře.
    
``` PowerShell
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running


```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Vyvolání skriptu prostředí PowerShell přes vzdálené virtuální počítače

 > [!WARNING]
 > Naleznete [řešení potíží s vzdálenou správu virtuálních počítačích Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Za předpokladu, že máte virtuální počítač, MyVM1, použijeme `Invoke-AzureRmVMCommand` k vyvolání blok skriptu prostředí PowerShell ve vzdáleném počítači.

  ``` Powershell
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```
  Můžete také nejprve přejděte do adresáře virtuálních počítačů a spustit `Invoke-AzureRmVMCommand` následujícím způsobem.

  ``` PowerShell
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock {Get-ComputerInfo}
  ```
  Zobrazí výstup podobný následujícímu:

  ``` PowerShell
  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interaktivní přihlášení na vzdálený počítač

Můžete použít `Enter-AzureRmVM` pro interaktivní přihlášení na virtuální počítač běží v Azure.

  ``` PowerShell
  Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Můžete také přejít na `VirtualMachines` directory první a spusťte `Enter-AzureRmVM` následujícím způsobem

  ``` PowerShell
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>Zjistit WebApps

V rámci `WebApps` adresář můžete snadno přejít prostředkům webových aplikací

``` PowerShell
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US



# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzureRmWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

```

## <a name="ssh"></a>SSH

[Win32 – OpenSSH](https://github.com/PowerShell/Win32-OpenSSH) je k dispozici v prostředí cloudu prostředí PowerShell.
K ověření na serverech nebo virtuálních počítačů pomocí protokolu SSH, vygenerovat pár klíčů veřejný soukromý v prostředí cloudu a publikovat veřejný klíč k `authorized_keys` ve vzdáleném počítači, jako například `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Můžete vytvořit privátní veřejné klíče SSH pomocí `ssh-keygen` a publikovat je do `$env:USERPROFILE\.ssh` v prostředí cloudu.

### <a name="using-a-custom-profile-to-persist-git-and-ssh-settings"></a>Pomocí vlastního profilu pro stávající nastavení GIT a SSH

Vzhledem k tomu, že relace nezůstanou zachována při odhlášení uložit vaše `$env:USERPROFILE\.ssh` do adresáře `clouddrive` nebo vytvořte symlink při získá spuštění prostředí cloudu.
Přidejte následující fragment kódu ve vaší profile.ps1 vytvořit symlink k `clouddrive`.

``` PowerShell
# Check if the .ssh directory exists
if( -not (Test-Path $home\clouddrive\.ssh)){
    mkdir $home\clouddrive\.ssh
}

# .ssh path relative to this script
$script:sshFolderPath = Join-Path $PSScriptRoot .ssh

# Create a symlink to .ssh in user's $home
if(Test-Path $script:sshFolderPath){
   if(-not (Test-Path (Join-Path $HOME .ssh ))){
        New-Item -ItemType SymbolicLink -Path $HOME -Name .ssh -Value $script:sshFolderPath
   }
}

```

### <a name="using-ssh"></a>Pomocí protokolu SSH

Postupujte podle pokynů [sem](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) k vytvoření nové konfigurace virtuálního počítače pomocí rutin AzureRM.
Před volání do `New-AzureRmVM` na zahajovací nasazení, přidejte veřejný klíč SSH ke konfiguraci virtuálních počítačů.
Nově vytvořený virtuální počítač bude obsahovat veřejný klíč ve `~\.ssh\authorized_keys` umístění, a umožňuje bez přihlašovacích údajů relace SSH do virtuálního počítače.

``` PowerShell

# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzureRmVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com

```


## <a name="list-available-commands"></a>Seznam dostupné příkazy

V části `Azure` disk, zadejte `Get-AzureRmCommand` Chcete-li získat konkrétní Azure příkazy.

Alternativně můžete vždy použít `Get-Command *azurerm* -Module AzureRM.*` a zjistěte, dostupné příkazy pro Azure.

## <a name="install-custom-modules"></a>Instalace vlastních modulů

Můžete spustit `Install-Module` instalace modulů z [Galerie prostředí PowerShell][gallery].

## <a name="get-help"></a>Get-Help

Typ `Get-Help` se získat informace o prostředí PowerShell v prostředí cloudu Azure.

``` PowerShell
PS Azure:\> Get-Help
```

Pro konkrétní příkaz, je stále možné `Get-Help` následuje rutiny.

``` PowerShell
PS Azure:\> Get-Help Get-AzureRmVM
```

## <a name="use-azure-files-to-store-your-data"></a>Použití Azure souborů k ukládání dat

Můžete vytvořit skript, například `helloworld.ps1`a uložte ho do vaší `clouddrive` používat napříč relacemi prostředí.

``` PowerShell
cd C:\users\ContainerAdministrator\clouddrive
PS C:\users\ContainerAdministrator\clouddrive> vim .\helloworld.ps1
# Add the content, such as 'Hello World!'
PS C:\users\ContainerAdministrator\clouddrive> .\helloworld.ps1
Hello World!
```

Při použití prostředí PowerShell v prostředí cloudu, při příštím `helloworld.ps1` soubor bude existovat v rámci `clouddrive` adresář, který připojí vaše soubory Azure sdílenou složku.

## <a name="use-custom-profile"></a>Použít vlastní profil

Prostředí PowerShell můžete přizpůsobit tak, že vytvoříte prostředí PowerShell profily - `profile.ps1` nebo `Microsoft.PowerShell_profile.ps1`. Uložte ji pod `clouddrive` tak, aby mohou být načteny v každé relaci prostředí PowerShell při spuštění prostředí cloudu.

Postup vytvoření profilu, použijte [o profily][profile].

## <a name="use-git"></a>Pomocí Git

Chcete-li klonovat úložiště Git v prostředí cloudu, je potřeba vytvořit [osobní přístupový token] [ githubtoken] a používejte ho jako uživatelské jméno. Jakmile je váš token, klonování úložiště následujícím způsobem:

 ``` PowerShell
  git clone https://<your-access-token>@github.com/username/repo.git

```
Vzhledem k tomu, že relací v prostředí cloudu se nezachovají, pokud se odhlásíte nebo vypršení časového limitu relace, nebude existovat konfiguračním souboru Git při dalším přihlášení. Pokud chcete, aby vaše konfigurace Git zachovat, je nutné uložit vaše .gitconfig k vaší `clouddrive` a zkopírujte jej, nebo vytvořte symlink při získá spuštění prostředí cloudu. Následující fragment kódu v profile.ps1, použít k vytvoření symlink k `clouddrive`.

 ``` PowerShell
 
# .gitconfig path relative to this script
$script:gitconfigPath = Join-Path $PSScriptRoot .gitconfig

# Create a symlink to .gitconfig in user's $home
if(Test-Path $script:gitconfigPath){

    if(-not (Test-Path (Join-Path $home .gitconfig ))){
         New-Item -ItemType SymbolicLink -Path $home -Name .gitconfig -Value $script:gitconfigPath
    }
}

```
## <a name="exit-the-shell"></a>Opusťte prostředí

Typ `exit` ukončit relaci.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
