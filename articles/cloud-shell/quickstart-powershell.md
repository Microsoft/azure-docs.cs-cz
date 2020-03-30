---
title: Azure Cloud Shell Úvodní – PowerShell
description: Přečtěte si, jak používat PowerShell ve vašem prohlížeči pomocí Azure Cloud Shellu.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 72261989b7cee9d2251eb18b36431ec807b0e874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273004"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Úvodní příručka pro PowerShell v Azure Cloud Shellu

Tento dokument podrobně popisuje, jak používat PowerShell v Cloud Shellu na [webu Azure Portal](https://portal.azure.com/).

> [!NOTE]
> [Bash v Azure Cloud Shell](quickstart.md) Úvodní k dispozici je také k dispozici.

## <a name="start-cloud-shell"></a>Spuštění Cloud Shellu

1. Klikněte na tlačítko **Cloud Shell** z horního navigačního panelu portálu Azure.

   ![](media/quickstart-powershell/shell-icon.png)

2. Vyberte prostředí PowerShellu z rozevíracího prostředí a budete na disku Azure`(Azure:)`

   ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Spuštění příkazů prostředí PowerShell

Spouštět běžné příkazy Prostředí PowerShell v prostředí Cloud, například:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Navigace o prostředcích Azure

 1. Seznam všech předplatných z `Azure` disku

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd`k vašemu preferovanému předplatnému

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Zobrazení všech prostředků Azure v rámci aktuálního předplatného

    Zadejte `dir` do seznamu více zobrazení prostředků Azure.

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

### <a name="allresources-view"></a>Zobrazení AllResources

`dir` Zadejte `AllResources` do adresáře a zobrazte prostředky Azure.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Prozkoumat skupiny prostředků

 Můžete přejít `ResourceGroups` do adresáře a uvnitř skupiny prostředků můžete najít virtuální počítače.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> Můžete si všimnout, že `dir`podruhé při psaní , Cloud Shell je schopen zobrazit položky mnohem rychleji.
> Důvodem je, že podřízené položky jsou uloženy do mezipaměti v paměti pro lepší uživatelské prostředí.
Vždy však můžete `dir -Force` použít k získání nových dat.

### <a name="navigate-storage-resources"></a>Navigace ve zdrojích úložiště

Vstupem do `StorageAccounts` adresáře můžete snadno procházet všechny prostředky úložiště

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Pomocí připojovacího řetězce můžete použít následující příkaz k připojení sdílené složky Azure Files.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Podrobnosti najdete v [tématu připojení sdílené složky Azure Files a přístup ke sdílené složce ve Windows][azmount].

Můžete také procházet adresáře ve sdílené složce Soubory Azure takto:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Interakce s virtuálními počítači

Všechny virtuální počítače najdete v aktuálním předplatném prostřednictvím `VirtualMachines` adresáře.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Vyvolání skriptu Prostředí PowerShell mezi vzdálenými virtuálními aplikacemi

 > [!WARNING]
 > Informace o [řešení potíží se vzdálenou správou virtuálních počítačů Azure najdete v potížích](troubleshooting.md#troubleshooting-remote-management-of-azure-vms)s řešením potíží.

  Za předpokladu, že máte virtuální počítač MyVM1, použijeme `Invoke-AzVMCommand` k vyvolání bloku skriptu prostředí PowerShell ve vzdáleném počítači.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  Můžete také přejít do adresáře `Invoke-AzVMCommand` VirtualMachines první a spustit následujícím způsobem.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interaktivní přihlášení ke vzdálenému virtuálnímu počítači

Můžete použít `Enter-AzVM` interaktivní přihlášení k virtuálnímu počítači spuštěného v Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

Můžete také přejít `VirtualMachines` do adresáře a spustit `Enter-AzVM` následujícím způsobem

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>Seznamte se s webovými aplikacemi

Vstupem do `WebApps` adresáře můžete snadno procházet prostředky webových aplikací

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

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

Chcete-li se ověřit na serverech nebo virtuálních počítačích pomocí SSH, vygenerujte dvojici veřejných a soukromých klíčů v prostředí Cloud Shell a publikujte veřejný klíč ve `authorized_keys` vzdáleném počítači, například `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Můžete vytvořit SSH privátní veřejné klíče pomocí `ssh-keygen` a publikovat je do `$env:USERPROFILE\.ssh` cloudového prostředí.

### <a name="using-ssh"></a>Použití SSH

Postupujte podle pokynů [zde](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) k vytvoření nové konfigurace virtuálního počítače pomocí rutin Azure PowerShell.
Před voláním do `New-AzVM` zahájit nasazení, přidejte SSH veřejný klíč do konfigurace virtuálního počítače.
Nově vytvořený virtuální virtuální ms bude `~\.ssh\authorized_keys` obsahovat veřejný klíč v umístění, a tím povolí relaci SSH bez pověření pro virtuální hod.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Seznam dostupných příkazů

V `Azure` části `Get-AzCommand` jednotky zadejte, chcete-li získat kontextové příkazy Azure.

Případně můžete vždy zjistit `Get-Command *az* -Module Az.*` dostupné příkazy Azure.

## <a name="install-custom-modules"></a>Instalace vlastních modulů

Instalaci modulů můžete spustit `Install-Module` v Galerii prostředí [PowerShell][gallery].

## <a name="get-help"></a>Získat nápovědu

Pokud `Get-Help` chcete získat informace o PowerShellu v Azure Cloud Shellu, zadejte.

```azurepowershell-interactive
Get-Help
```

U určitého příkazu můžete `Get-Help` stále následovat rutina.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Ukládání dat pomocí souborů Azure

Můžete vytvořit skript, `helloworld.ps1`řekněme , a `clouddrive` uložit jej do vašeho použít přes relace prostředí.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Příště při použití PowerShellu v `helloworld.ps1` Cloud Shellu `$HOME\clouddrive` bude soubor existovat pod adresářem, který připojí vaši sdílenou složku Azure Files.

## <a name="use-custom-profile"></a>Použití vlastního profilu

Prostředí PowerShellu můžete přizpůsobit vytvořením profilů prostředí `profile.ps1` PowerShell – (nebo). `Microsoft.PowerShell_profile.ps1`
Uložte `$profile.CurrentUserAllHosts` ji `$profile.CurrentUserAllHosts`pod (nebo ), aby ji bylo možné načíst v každém prostředí PowerShell v relaci Cloud Shell.

Jak vytvořit profil, naleznete [v nazuje o profilech][profile].

## <a name="use-git"></a>Použití Gitu

Chcete-li klonovat úložiště Git v prostředí Cloud Shell, musíte vytvořit [osobní přístupový token][githubtoken] a použít jej jako uživatelské jméno. Jakmile budete mít svůj token, klonovat úložiště takto:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Opusťte prostředí

Chcete-li relaci ukončit, zadejte. `exit`

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
