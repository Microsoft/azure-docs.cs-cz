---
title: Prostředí PowerShell v rámci rychlého startu Azure Cloud Shell (Preview) | Dokumentace Microsoftu
description: Rychlý start pro PowerShell ve službě Cloud Shell
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
ms.date: 07/27/2018
ms.author: damaerte
ms.openlocfilehash: cb4b7f8851c6c891ca43f6c215ba812a0c784d28
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43108134"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell-preview"></a>Rychlý start pro PowerShell ve službě Azure Cloud Shell (Preview)

Tento dokument podrobně popisuje, jak pomocí prostředí PowerShell ve službě Cloud Shell v [webu Azure portal](https://aka.ms/PSCloudPreview).

> [!NOTE]
> A [Bash ve službě Azure Cloud Shell](quickstart.md) rychlý start je také k dispozici.

## <a name="start-cloud-shell"></a>Spustit Cloud Shell

1. Klikněte na **Cloud Shell** tlačítko v horním navigačním panelu na webu Azure portal

  ![](media/quickstart-powershell/shell-icon.png)

2. Z rozevíracího seznamu vyberte prostředí PowerShell a nebudete do Azure jednotky `(Azure:)`

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Spusťte příkazy Powershellu

Spuštění regulárního příkazy prostředí PowerShell ve službě Cloud Shell, například:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzureRmVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Procházení prostředků Azure

 1. Výpis všech předplatných z `Azure` jednotky

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd` k předplatnému upřednostňované

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Zobrazení všech vašich prostředků Azure v rámci aktuálního předplatného

    Typ `dir` seznam více zobrazení vašich prostředků Azure.

    ```azurepowershell-interactive
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

Typ `dir` pod `AllResources` adresář pro zobrazení vašich prostředků Azure.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Prozkoumejte skupiny prostředků

 Můžete přejít `ResourceGroups` adresář a v konkrétní skupině prostředků můžete najít virtuální počítače.

```azureowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> Můžete si všimnout, která při druhém volání po zadání `dir`, Cloud Shell je možné zobrazit položky mnohem rychlejší.
> Je to proto, že podřízené položky jsou uložené v mezipaměti v paměti pro lepší výkon.
Však můžete vždy použít `dir -Force` zobrazíte čerstvá data.

### <a name="navigate-storage-resources"></a>Procházení prostředků úložiště

Vstupem do `StorageAccounts` adresáře, můžete snadno procházet všechny prostředky úložiště

```azureowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

S připojovacím řetězcem slouží následující příkaz pro připojení sdílené složky Azure Files.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Podrobnosti najdete v tématu [připojení sdílené složky služby soubory Azure a přístup do sdílené složky ve Windows][azmount].

Můžete také přejít adresáře ve sdílené službě soubory Azure následujícím způsobem:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Pracovat s virtuálními počítači

Můžete najít všechny virtuální počítače v rámci aktuálního předplatného prostřednictvím `VirtualMachines` adresáře.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Vyvolání skriptu prostředí PowerShell ve vzdálených virtuálních počítačích

 > [!WARNING]
 > Najdete [řešení potíží s Vzdálená správa virtuálních počítačů Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Za předpokladu, že máte virtuální počítač MyVM1, použijeme `Invoke-AzureRmVMCommand` k vyvolání blok skriptu prostředí PowerShell na vzdáleném počítači.

  ```azurepowershell-interactive
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```

  Můžete také nejprve přejděte do adresáře, virtuálních počítačů a spustit `Invoke-AzureRmVMCommand` následujícím způsobem.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock {Get-ComputerInfo}

  # You will see output similar to the following:

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Přihlaste se interaktivně na vzdáleném virtuálním počítači

Můžete použít `Enter-AzureRmVM` přihlásit interaktivně přihlaste k virtuálnímu počítači v Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Můžete také dostat `VirtualMachines` adresáře první a spuštění `Enter-AzureRmVM` následujícím způsobem

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>Zjišťování webové aplikace

Vstupem do `WebApps` adresáře, můžete snadno procházet prostředky webové aplikace

```azurepowershell-interactive
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

K ověření serverů a virtuálních počítačů pomocí protokolu SSH, generování páru veřejného a privátního klíčů ve službě Cloud Shell a publikovat veřejný klíč, který `authorized_keys` na vzdáleném počítači, jako například `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Můžete vytvořit privátní veřejného klíče SSH pomocí `ssh-keygen` a publikujete je `$env:USERPROFILE\.ssh` ve službě Cloud Shell.

### <a name="using-ssh"></a>Pomocí protokolu SSH

Postupujte podle pokynů [tady](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) vytvořit nové konfigurace virtuálního počítače pomocí rutin AzureRM.
Před voláním do `New-AzureRmVM` aktivovat nasazení, přidejte veřejný klíč SSH do konfigurace virtuálního počítače.
Nově vytvořený virtuální počítač bude obsahovat veřejný klíč `~\.ssh\authorized_keys` umístění, a tím umožnit bez přihlašovacích údajů relaci SSH k virtuálnímu počítači.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzureRmVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Seznam dostupných příkazů

V části `Azure` jednotku, zadejte `Get-AzureRmCommand` získat kontextové příkazy Azure.

Alternativně můžete kdykoli použít `Get-Command *azurerm* -Module AzureRM.*` a zjistěte, k dispozici příkazy Azure.

## <a name="install-custom-modules"></a>Instalace vlastních modulů

Můžete spustit `Install-Module` pro instalaci modulů [Galerie prostředí PowerShell][gallery].

## <a name="get-help"></a>Get-Help

Typ `Get-Help` a získat informace o prostředí PowerShell ve službě Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

Ke konkrétnímu příkazu, můžete stále provádět `Get-Help` za nímž následuje rutiny.

```azurepowershell-interactive
Get-Help Get-AzureRmVM
```

## <a name="use-azure-files-to-store-your-data"></a>Používat soubory Azure k ukládání dat

Můžete vytvořit skript, Dejme tomu, že `helloworld.ps1`a uložte ho do vašeho `clouddrive` používat napříč relacemi prostředí.

```azurepowershell-interactive
cd $HOME\clouddrive
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Při použití prostředí PowerShell ve službě Cloud Shell, při příštím `helloworld.ps1` soubor bude existovat v rámci `$HOME\clouddrive` adresář, který se připojí vaše sdílenou složku služby soubory Azure.

## <a name="use-custom-profile"></a>Pomocí vlastního profilu

Prostředí PowerShell můžete přizpůsobit tak, že vytvoříte prostředí PowerShell profilů - `profile.ps1` (nebo `Microsoft.PowerShell_profile.ps1`).
Uložte ho v části `$profile.CurrentUserAllHosts` (nebo `$profile.CurrentUserAllHosts`), takže může být načteno v každé prostředí PowerShell v relaci služby Cloud Shell.

Postup vytvoření profilu, přečtěte si [o profily][profile].

## <a name="use-git"></a>Pomocí Gitu

Naklonujte úložiště Git ve službě Cloud Shell, je potřeba vytvořit [osobní přístupový token] [ githubtoken] a použít ho jako uživatelské jméno. Jakmile máte token, klonování úložiště následujícím způsobem:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Opusťte prostředí

Typ `exit` k ukončení relace.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
