---
title: Rozšíření vlastních skriptů Azure pro Windows
description: Automatizace úloh konfigurace virtuálních počítačů s Windows pomocí rozšíření vlastních skriptů
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/31/2020
ms.author: robreed
ms.openlocfilehash: e50c0b0fcb883b43650a5d99cea5aa39bae1cd94
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89426261"
---
# <a name="custom-script-extension-for-windows"></a>Rozšíření vlastních skriptů pro virtuální počítače

Rozšíření vlastních skriptů stáhne a spustí skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z Azure Storage nebo z GitHubu, případně je za běhu rozšíření najdete na portálu Azure Portal. Rozšíření vlastních skriptů se integruje s Azure Resource Manager šablonami a dá se spustit pomocí Azure CLI, PowerShellu, Azure Portal nebo virtuálního počítače Azure REST API.

Tento dokument popisuje, jak používat rozšíření vlastních skriptů pomocí modulu Azure PowerShell, Azure Resource Manager šablony a podrobně popisuje postup řešení potíží v systémech Windows.

## <a name="prerequisites"></a>Požadavky

> [!NOTE]  
> Nepoužívejte rozšíření vlastních skriptů ke spuštění rutiny Update-AzVM se stejným virtuálním počítačem jako jeho parametr, protože se bude čekat sám na sebe.  

### <a name="operating-system"></a>Operační systém

Rozšíření vlastních skriptů pro Windows se spustí v podporovaném rozšíření OSs s rozšířením.
### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>Umístění skriptu

Pro přístup k úložišti objektů BLOB v Azure můžete nakonfigurovat rozšíření pro použití přihlašovacích údajů služby Azure Blob Storage. Umístění skriptu může být kdekoli, pokud virtuální počítač může směrovat na tento koncový bod, jako je GitHub nebo interní souborový server.

### <a name="internet-connectivity"></a>Připojení k Internetu

Pokud potřebujete stáhnout skript externě, například z GitHubu nebo Azure Storage, je nutné otevřít další porty brány firewall a skupiny zabezpečení sítě. Pokud se například váš skript nachází v Azure Storage, můžete povolení přístupu pomocí značek služeb Azure NSG pro [úložiště](../../virtual-network/security-overview.md#service-tags).

Pokud je váš skript na místním serveru, budete možná potřebovat otevřít i další porty brány firewall a skupiny zabezpečení sítě.

### <a name="tips-and-tricks"></a>Tipy a triky

* Nejvyšší míra selhání tohoto rozšíření je způsobená chybami syntaxe ve skriptu, testování spuštění skriptu bez chyb a také další přihlášení do skriptu, aby bylo snazší zjistit, kde se nezdařila.
* Pište skripty, které jsou idempotentní. Tím se zajistí, že pokud se znovu spustí, nezpůsobí se změny systému.
* Zajistěte, aby skripty za běhu nevyžadovaly uživatelský vstup.
* Skript může běžet maximálně 90 minut. Pokud poběží déle, způsobí to selhání zřizování rozšíření.
* Nepoužívejte ve skriptu restartování, protože tato akce způsobí problémy s dalšími instalovanými rozšířeními. Instalace rozšíření po restartování nebude pokračovat.
* Pokud máte skript, který způsobí restart, pak nainstalujte aplikace a spusťte skripty, můžete naplánovat restartování pomocí naplánované úlohy Windows nebo použít nástroje, jako je DSC, počítač nebo rozšíření Puppet.
* Nedoporučujeme spouštět skript, který způsobí zastavení nebo aktualizaci agenta virtuálního počítače. To může mít příponu ve stavu přechodu, což vede k vypršení časového limitu.
* Rozšíření spustí skript pouze jednou. Pokud chcete spustit skript při každém spuštění, musíte pomocí rozšíření vytvořit naplánovanou úlohu Windows.
* Pokud chcete naplánovat, kdy se skript spustí, měli byste pomocí rozšíření vytvořit naplánovanou úlohu Windows.
* Když je skript spuštěný, na webu Azure Portal nebo v rozhraní příkazového řádku se rozšíření zobrazí pouze v přechodném stavu. Pokud chcete častější aktualizace stavu spuštěného skriptu, budete si muset vytvořit vlastní řešení.
* Rozšíření vlastních skriptů nepodporují nativně proxy servery, ale můžete použít nástroj pro přenos souborů, který podporuje proxy servery ve vašem skriptu, jako je například *kudrlinkou* .
* Udržujte si přehled o jiných než výchozích umístěních adresářů, na kterých můžou vaše skripty nebo příkazy záviset, a zajistěte si logiku pro řešení takové situace.
* Rozšíření vlastních skriptů se spustí pod účtem LocalSystem.
* Pokud plánujete použít vlastnosti *storageAccountName* a *storageAccountKey* , musí být tyto vlastnosti společně umístěného v *protectedSettings*.

## <a name="extension-schema"></a>Schéma rozšíření

Konfigurace rozšíření vlastních skriptů určuje například umístění skriptu a příkaz, který má být spuštěn. Tuto konfiguraci můžete uložit do konfiguračních souborů, zadat ji na příkazovém řádku nebo ji zadat v šabloně Azure Resource Manager.

Citlivá data můžete ukládat do chráněné konfigurace, která je zašifrovaná a v rámci virtuálního počítače se šifruje jenom. Chráněná konfigurace je užitečná, když příkaz pro spuštění zahrnuje tajné klíče, jako je třeba heslo.

Tyto položky by měly být považovány za citlivá data a specifikována v konfiguraci nastavení chráněného rozšíření. Data nastavení chráněná rozšířením virtuálního počítače Azure jsou šifrovaná a v cílovém virtuálním počítači se dešifrují jenom.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> vlastnost managedIdentity se **nesmí** používat ve spojení s vlastnostmi StorageAccountName nebo storageAccountKey.

> [!NOTE]
> V daném časovém okamžiku může být na virtuálním počítači nainstalovaná jenom jedna verze rozšíření. zadání vlastního skriptu dvakrát ve stejné Správce prostředků šabloně pro stejný virtuální počítač se nezdaří.

> [!NOTE]
> Toto schéma můžeme použít uvnitř prostředku VirtualMachine nebo jako samostatný prostředek. Název prostředku musí být v tomto formátu "virtualMachineName/klapka", pokud se toto rozšíření používá jako samostatný prostředek v šabloně ARM.

### <a name="property-values"></a>Hodnoty vlastností

| Name | Hodnota/příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| vydavatel | Microsoft.Compute | řetězec |
| typ | CustomScriptExtension | řetězec |
| typeHandlerVersion | 1.10 | int |
| Identifikátory URI (např.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| časové razítko (např.) | 123456789 | 32-bitové celé číslo |
| commandToExecute (např.) | PowerShell – ExecutionPolicy bez omezení – soubor configure-music-app.ps1 | řetězec |
| storageAccountName (např.) | examplestorageacct | řetězec |
| storageAccountKey (např.) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | řetězec |
| managedIdentity (např.) | {} nebo {"clientId": "31b403aa-C364-4240-a7ff-d85fb6cd7232"} nebo {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | objekt JSON |

>[!NOTE]
>U těchto názvů vlastností se rozlišují velká a malá písmena. Aby nedocházelo k potížím s nasazením, použijte názvy, jak je znázorněno zde.

#### <a name="property-value-details"></a>Podrobnosti hodnoty vlastnosti

* `commandToExecute`: (**Required**; String) skript vstupního bodu, který se má provést. Místo toho použijte toto pole, pokud váš příkaz obsahuje tajné kódy, jako jsou hesla, nebo jsou vaše identifikátory URI typu "citlivé".
* `fileUris`: (volitelné, pole řetězců) adresy URL pro soubory, které mají být staženy.
* `timestamp` (volitelné, 32 celé číslo) Toto pole použijte pouze k aktivaci opětovného spuštění skriptu změnou hodnoty tohoto pole.  Je přijatelné libovolné celočíselné hodnoty; musí se lišit jenom od předchozí hodnoty.
* `storageAccountName`: (volitelné, řetězec) název účtu úložiště. Pokud zadáte přihlašovací údaje úložiště, `fileUris` musí být všechny adresy URL pro objekty blob Azure.
* `storageAccountKey`: (volitelné, String) přístupový klíč účtu úložiště
* `managedIdentity`: (volitelné, objekt JSON) [spravovaná identita](../../active-directory/managed-identities-azure-resources/overview.md) pro stahování souborů
  * `clientId`: (volitelné, String) ID klienta spravované identity
  * `objectId`: (volitelné, String) ID objektu spravované identity

V rámci veřejného nebo chráněného nastavení lze nastavit následující hodnoty, rozšíření bude odmítat všechny konfigurace, kde jsou níže uvedené hodnoty nastaveny v nastavení veřejné i chráněné.

* `commandToExecute`

Použití veřejného nastavení může být užitečné pro ladění, ale doporučuje se používat chráněná nastavení.

Veřejné nastavení se odesílá ve formě prostého textu do virtuálního počítače, ve kterém se skript spustí.  Chráněná nastavení se šifrují pomocí klíče, který je známý jenom pro Azure a virtuální počítač. Nastavení se uloží do virtuálního počítače, protože se poslalo, to znamená, že pokud byla nastavení zašifrovaná, ukládají se na virtuálním počítači jako šifrované. Certifikát použitý k dešifrování šifrovaných hodnot je uložený ve virtuálním počítači a slouží k dešifrování nastavení (v případě potřeby) za běhu.

####  <a name="property-managedidentity"></a>Vlastnost: managedIdentity
> [!NOTE]
> Tato vlastnost **musí** být určena pouze v chráněných nastaveních.

CustomScript (verze 1,10 a vyšší) podporuje [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) pro stahování souborů z adres URL, které jsou k dispozici v nastavení "identifikátory URI". Umožňuje CustomScript získat přístup k Azure Storage privátním objektům blob nebo kontejnerům bez toho, aby uživatel musel předávat tajné kódy, jako jsou tokeny SAS nebo klíče účtu úložiště.

Aby bylo možné tuto funkci používat, musí uživatel přidat identitu přiřazenou [systémem](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) nebo [uživatelem přiřazenou](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) k virtuálnímu počítači nebo VMSS, kde se očekává spuštění CustomScript, a [udělit spravované identitě přístup k kontejneru Azure Storage nebo objektu BLOB](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Pokud chcete použít identitu přiřazenou systémem na cílovém virtuálním počítači nebo VMSS, nastavte pole managedidentity na prázdný objekt JSON. 

> Příklad:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Pokud chcete v cílovém virtuálním počítači/VMSS použít identitu přiřazenou uživatelem, nakonfigurujte pole managedidentity s ID klienta nebo ID objektu spravované identity.

> Příklady:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> vlastnost managedIdentity se **nesmí** používat ve spojení s vlastnostmi StorageAccountName nebo storageAccountKey.

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Schéma JSON, které je podrobně popsáno v předchozí části, lze použít v šabloně Azure Resource Manager ke spuštění rozšíření vlastních skriptů během nasazování. Následující ukázky ukazují, jak používat rozšíření vlastních skriptů:

* [Kurz: Nasazování rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Nasazení dvou aplikačních vrstev ve Windows a Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Nasazení PowerShellu

Pomocí `Set-AzVMCustomScriptExtension` příkazu můžete přidat rozšíření vlastních skriptů do existujícího virtuálního počítače. Další informace najdete v tématu [set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Další příklady

### <a name="using-multiple-scripts"></a>Používání více skriptů

V tomto příkladu máte tři skripty, které se používají k sestavení serveru. **CommandToExecute** volá první skript, potom máte možnosti, jak jsou voláni ostatními. Můžete mít například hlavní skript, který řídí provádění, se správným zpracováním chyb, protokolováním a správou stavu. Skripty se stáhnou do místního počítače, aby je bylo možné spustit. Například `1_Add_Tools.ps1` byste volali `2_Add_Features.ps1` Přidání  `.\2_Add_Features.ps1` do skriptu a tento postup opakujte pro ostatní skripty, které definujete v `$settings` .

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Spouštění skriptů z místního sdílení

V tomto příkladu můžete chtít pro umístění skriptu použít místní server SMB. Tímto způsobem nemusíte zadávat žádná další nastavení, s výjimkou **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Postup spuštění vlastního skriptu více než jednou pomocí rozhraní příkazového řádku

Pokud chcete rozšíření vlastních skriptů spustit více než jednou, můžete tuto akci provést jenom za těchto podmínek:

* Parametr **názvu** rozšíření je stejný jako předchozí nasazení rozšíření.
* Aktualizace konfigurace v opačném případě příkaz nebude znovu spuštěn. Do příkazu můžete přidat do dynamické vlastnosti, jako je například časové razítko.

Případně můžete nastavit vlastnost [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) na **hodnotu true**.

### <a name="using-invoke-webrequest"></a>Použití metody Invoke-WebRequest

Pokud ve svém skriptu používáte [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) , musíte zadat parametr `-UseBasicParsing` nebo jinak se při kontrole podrobného stavu zobrazí následující chyba:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Postup nasazení rozšíření vlastních skriptů v sadě škálování najdete v tématu [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0) .

## <a name="classic-vms"></a>Klasické virtuální počítače

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Pokud chcete nasadit rozšíření vlastních skriptů na klasických virtuálních počítačích, můžete použít rutiny Azure Portal nebo klasických Azure PowerShell.

### <a name="azure-portal"></a>portál Azure

Přejděte na prostředek klasického virtuálního počítače. V části **Nastavení**vyberte **rozšíření** .

Klikněte na **+ Přidat** a v seznamu prostředků vyberte **rozšíření vlastních skriptů**.

Na stránce **instalovat rozšíření** vyberte místní soubor PowerShell a vyplňte všechny argumenty a klikněte na **OK**.

### <a name="powershell"></a>PowerShell

Pomocí rutiny [set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) můžete přidat rozšíření vlastních skriptů do existujícího virtuálního počítače.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Odstraňování potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí modulu Azure PowerShell. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Výstup rozšíření je protokolován do souborů nalezených v následující složce na cílovém virtuálním počítači.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Zadané soubory jsou staženy do následující složky v cílovém virtuálním počítači.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

kde `<n>` je desítkové celé číslo, které se může změnit mezi spouštěním rozšíření.  `1.*`Hodnota odpovídá skutečné aktuální `typeHandlerVersion` hodnotě rozšíření.  Například skutečný adresář může být `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` .  

Při provádění `commandToExecute` příkazu rozšíření nastaví tento adresář (například `...\Downloads\2` ) jako aktuální pracovní adresář. Tento proces umožňuje použití relativních cest k vyhledání souborů stažených prostřednictvím `fileURIs` Vlastnosti. Příklady najdete v následující tabulce.

Vzhledem k tomu, že absolutní cesta ke stažení se může v průběhu času lišit, je lepší vyjádřit výslovný souhlas s relativními cestami Script/File v `commandToExecute` řetězci, kdykoli je to možné. Například:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informace o cestě po prvním segmentu URI se uchovávají pro soubory stažené přes `fileUris` seznam vlastností.  Jak je znázorněno v následující tabulce, stažené soubory jsou namapovány na podadresáře ke stažení, aby odrážely strukturu `fileUris` hodnot.  

#### <a name="examples-of-downloaded-files"></a>Příklady stažených souborů

| Identifikátor URI v identifikátorech URI | Relativní umístění ke stažení | Absolutní stažený umístění <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> absolutní cesty k adresářům se mění během životnosti virtuálního počítače, ale ne v rámci jednoho spuštění rozšíření CustomScript.

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Můžete také zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
