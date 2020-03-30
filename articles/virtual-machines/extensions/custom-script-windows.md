---
title: Rozšíření vlastního skriptu Azure pro Windows
description: Automatizace úloh konfigurace virtuálních počítačů s Windows pomocí rozšíření Vlastní skript
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 698fab470cdc8b8d04fa4319fd71c31b58d1c5a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066875"
---
# <a name="custom-script-extension-for-windows"></a>Rozšíření vlastních skriptů pro virtuální počítače

Rozšíření vlastní skript stáhne a spustí skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z Azure Storage nebo z GitHubu, případně je za běhu rozšíření najdete na portálu Azure Portal. Rozšíření vlastní skript se integruje se šablonami Azure Resource Manager a dá se spouštět pomocí rozhraní API Azure, PowerShellu, portálu Azure nebo rozhraní API AZURE Virtual Machine REST.

Tento dokument podrobně popisuje, jak používat rozšíření Vlastní skript pomocí modulu Azure PowerShell, šablony Azure Resource Manager a podrobnosti o řešení potíží kroky v systémech Windows.

## <a name="prerequisites"></a>Požadavky

> [!NOTE]  
> Nepoužívejte vlastní skript rozšíření ke spuštění Update-AzVM se stejným virtuálním počítačem jako jeho parametr, protože bude čekat na sebe.  

### <a name="operating-system"></a>Operační systém

Rozšíření vlastní skript pro Windows se spustí na rozšíření podporované rozšíření operačních systémů, další informace naleznete v tomto [azure extension podporované operační systémy](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Umístění skriptu

Rozšíření můžete nakonfigurovat tak, aby používalo vaše přihlašovací údaje úložiště objektů Blob Azure pro přístup k úložišti objektů Blob Azure. Umístění skriptu může být kdekoli, tak dlouho, dokud virtuální ho virtuálního serveru můžete směrovat do tohoto koncového bodu, jako je například GitHub nebo interní souborový server.

### <a name="internet-connectivity"></a>Připojení k Internetu

Pokud potřebujete stáhnout skript externě, například z GitHubu nebo Azure Storage, pak je potřeba otevřít další porty firewallu a skupiny zabezpečení sítě. Pokud je například váš skript umístěný ve službě Azure Storage, můžete povolit přístup pomocí značek služby Azure NSG pro [úložiště](../../virtual-network/security-overview.md#service-tags).

Pokud je skript na místním serveru, může být stále nutné otevřít další brány firewall a porty skupiny zabezpečení sítě.

### <a name="tips-and-tricks"></a>Tipy a triky

* Nejvyšší míra selhání pro toto rozšíření je z důvodu syntaktické chyby ve skriptu, testování skriptu běží bez chyby a také dát do dalšího přihlášení do skriptu, aby bylo snazší najít, kde se nezdařilo.
* Napište skripty, které jsou idempotentní. Tím je zajištěno, že pokud se spustí znovu náhodně, nezpůsobí změny systému.
* Zajistěte, aby skripty za běhu nevyžadovaly uživatelský vstup.
* Skript může běžet maximálně 90 minut. Pokud poběží déle, způsobí to selhání zřizování rozšíření.
* Nepoužívejte ve skriptu restartování, protože tato akce způsobí problémy s dalšími instalovanými rozšířeními. Instalace rozšíření po restartování nebude pokračovat.
* Pokud máte skript, který způsobí restartování, pak nainstalovat aplikace a spustit skripty, můžete naplánovat restartování pomocí naplánované úlohy systému Windows nebo použít nástroje, jako je Například DSC, Chef nebo Puppet rozšíření.
* Rozšíření spustí skript pouze jednou. Pokud chcete spustit skript při každém spuštění, musíte pomocí rozšíření vytvořit naplánovanou úlohu Windows.
* Pokud chcete naplánovat, kdy se skript spustí, měli byste pomocí rozšíření vytvořit naplánovanou úlohu Windows.
* Když je skript spuštěný, na webu Azure Portal nebo v rozhraní příkazového řádku se rozšíření zobrazí pouze v přechodném stavu. Pokud chcete častější aktualizace stavu spuštěného skriptu, budete si muset vytvořit vlastní řešení.
* Vlastní skript rozšíření nepodporuje nativně proxy servery, ale můžete použít nástroj pro přenos souborů, který podporuje proxy servery v rámci skriptu, jako je *Curl*
* Udržujte si přehled o jiných než výchozích umístěních adresářů, na kterých můžou vaše skripty nebo příkazy záviset, a zajistěte si logiku pro řešení takové situace.
* Vlastní rozšíření skriptu bude spuštěno pod účtem LocalSystem

## <a name="extension-schema"></a>Schéma rozšíření

Konfigurace rozšíření vlastního skriptu určuje věci, jako je umístění skriptu a příkaz, který má být spuštěn. Tuto konfiguraci můžete uložit do konfiguračních souborů, zadat ji na příkazovém řádku nebo ji zadat v šabloně Správce prostředků Azure.

Citlivá data můžete ukládat v chráněné konfiguraci, která je šifrovaná a dešifrovaná pouze uvnitř virtuálního počítače. Chráněná konfigurace je užitečná, pokud příkaz spuštění obsahuje tajné klíče, například heslo.

Tyto položky by měly být považovány za citlivá data a zadali v konfiguraci nastavení chráněné rozšíření. Data nastavení chráněné rozšířením virtuálního počítače Azure jsou šifrovaná a dešifrovaná jenom na cílovém virtuálním počítači.

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
> vlastnost managedIdentity **nesmí** být používána ve spojení s vlastnostmi storageAccountName nebo storageAccountKey

> [!NOTE]
> Jenom jedna verze rozšíření může být nainstalována na virtuální ms v určitém okamžiku, zadání vlastního skriptu dvakrát ve stejné šabloně Správce prostředků pro stejný virtuální počítač se nezdaří.

> [!NOTE]
> Můžeme použít toto schéma uvnitř prostředku VirtualMachine nebo jako samostatný prostředek. Název prostředku musí být v tomto formátu "virtualMachineName/extensionName", pokud toto rozšíření se používá jako samostatný prostředek v šabloně ARM. 

### <a name="property-values"></a>Hodnoty vlastností

| Name (Název) | Hodnota / Příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| vydavatel | Microsoft.Compute | řetězec |
| type | Rozšíření customscriptextension | řetězec |
| typeHandlerVersion | 1.10 | int |
| fileUris (např. | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | pole |
| časové razítko (např. | 123456789 | 32bitové celé číslo |
| commandToExecute (např. | powershell -ExecutionPolicy Neomezený -File configure-music-app.ps1 | řetězec |
| storageAccountName (např. | examplestorageacct | řetězec |
| storageAccountKey (např. | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | řetězec |
| managedIdentity (např. | { } nebo { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } nebo { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json objekt |

>[!NOTE]
>Tyto názvy vlastností rozlišují malá a velká písmena. Chcete-li se vyhnout problémům s nasazením, použijte názvy, jak je znázorněno zde.

#### <a name="property-value-details"></a>Podrobnosti o hodnotě nemovitosti

* `commandToExecute`: (**povinné**, řetězec) skript vstupního bodu ke spuštění. Toto pole použijte místo toho, pokud váš příkaz obsahuje tajné klíče, jako jsou hesla, nebo souborUris jsou citlivé.
* `fileUris`: (volitelné, pole řetězců) adresy URL pro soubory, které mají být staženy.
* `timestamp`(volitelné, 32bitové celé číslo) použijte toto pole pouze k aktivaci opětovného spuštění skriptu změnou hodnoty tohoto pole.  Jakákoli celá hodnota je přijatelná; musí se lišit pouze od předchozí hodnoty.
* `storageAccountName`: (nepovinné, řetězec) název účtu úložiště. Pokud zadáte přihlašovací údaje `fileUris` úložiště, všechny musí být adresy URL pro objekty Blob Azure.
* `storageAccountKey`: (nepovinné, řetězec) přístupový klíč účtu úložiště
* `managedIdentity`: (volitelně, json objekt) [spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pro stahování souborů
  * `clientId`: (nepovinné, řetězec) ID klienta spravované identity
  * `objectId`: (volitelné, řetězec) ID objektu spravované identity

Následující hodnoty lze nastavit ve veřejném nebo chráněném nastavení, rozšíření odmítne jakoukoli konfiguraci, kde jsou níže uvedené hodnoty nastaveny ve veřejném i chráněném nastavení.

* `commandToExecute`

Použití veřejné nastavení může být užitečné pro ladění, ale doporučujese používat chráněné nastavení.

Veřejná nastavení se odesílají ve prostém textu do virtuálního počítače, kde bude skript spuštěn.  Chráněná nastavení se šifrují pomocí klíče známého jenom pro Azure a virtuální počítač. Nastavení se uloží do virtuálního počítače při jejich odeslání, to znamená, že pokud byla nastavení zašifrována, jsou uložena zašifrovaná na virtuálním počítači. Certifikát použitý k dešifrování šifrovaných hodnot je uložen na virtuálním počítači a používá se k dešifrování nastavení (v případě potřeby) za běhu.

####  <a name="property-managedidentity"></a>Vlastnost: managedIdentity

CustomScript (verze 1.10 a dále) podporuje [spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pro stahování souborů z adres URL uvedených v nastavení "fileUris". Umožňuje CustomScript přístup k privátním objektům BLOB nebo kontejnerům azure storage, aniž by uživatel musel předávat tajné kódy, jako jsou tokeny SAS nebo klíče účtů úložiště.

Chcete-li použít tuto funkci, uživatel musí přidat [systémem přiřazenou](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) nebo [uživatelem přiřazenou](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) identitu do virtuálního počítače nebo v oblasti VMSS, kde se očekává spuštění CustomScriptu, a [udělit přístup ke spravované identitě kontejneru nebo objektu blob služby Azure Storage](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access).

Chcete-li použít systémem přiřazenou identitu na cílovém virtuálním počítači/vmss, nastavte pole "managedidentity" na prázdný objekt json. 

> Příklad:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Chcete-li použít identitu přiřazenou uživateli na cílovém virtuálním počítači/virtuálním počítači, nakonfigurujte pole "managedidentity" s ID klienta nebo ID objektu spravované identity.

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
> vlastnost managedIdentity **nesmí** být používána ve spojení s vlastnostmi storageAccountName nebo storageAccountKey

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Schéma JSON, které je podrobně popsáno v předchozí části, lze použít v šabloně Správce prostředků Azure ke spuštění rozšíření vlastního skriptu během nasazení. Následující ukázky ukazují, jak používat rozšíření Vlastní skript:

* [Kurz: Nasazování rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Nasazení dvouvrstvé aplikace ve Windows a Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Nasazení PowerShellu

Příkaz `Set-AzVMCustomScriptExtension` lze použít k přidání rozšíření vlastní skript do existujícího virtuálního počítače. Další informace naleznete [v tématu Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Další příklady

### <a name="using-multiple-scripts"></a>Použití více skriptů

V tomto příkladu máte tři skripty, které se používají k vytvoření serveru. **PříkazToExecute** volá první skript, pak máte možnosti, jak jsou volány ostatní. Můžete mít například hlavní skript, který řídí provádění, se správným zpracováním chyb, protokolováním a správou stavu. Skripty jsou staženy do místního počítače pro spuštění. Například `1_Add_Tools.ps1` v vám `2_Add_Features.ps1` by `.\2_Add_Features.ps1` volání přidáním do skriptu a opakujte `$settings`tento proces pro ostatní skripty, které definujete v .

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

### <a name="running-scripts-from-a-local-share"></a>Spouštění skriptů z místní sdílené složky

V tomto příkladu můžete chtít použít místní server SMB pro umístění skriptu. Tímto způsobem není nutné zadat žádné další nastavení, s výjimkou **příkazuToExecute**.

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

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Jak spustit vlastní skript více než jednou s CLI

Pokud chcete spustit rozšíření vlastního skriptu více než jednou, můžete provést tuto akci pouze za těchto podmínek:

* Parametr **Název** rozšíření je stejný jako předchozí nasazení rozšíření.
* Aktualizujte konfiguraci, jinak nebude příkaz znovu proveden. Do příkazu můžete přidat dynamickou vlastnost, například časové razítko.

Alternativně můžete nastavit [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) vlastnost **true**.

### <a name="using-invoke-webrequest"></a>Použití požadavku invoke-webrequest

Pokud ve skriptu používáte [invoke-webrequest,](/powershell/module/microsoft.powershell.utility/invoke-webrequest) musíte `-UseBasicParsing` zadat parametr, jinak se při kontrole podrobného stavu zobrazí následující chyba:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů

Informace o nasazení rozšíření vlastního skriptu ve škálovací sadě naleznete v [tématu Add-AzVmssExtension](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>Klasické virtuální počítače

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

K nasazení rozšíření vlastního skriptu na klasické virtuální počítače můžete použít portál Azure nebo rutiny Classic Azure PowerShell.

### <a name="azure-portal"></a>portál Azure

Přejděte na svůj klasický prostředek virtuálního počítače. V části **Nastavení** **vyberte Rozšíření** .

Klepněte na **+ Přidat** a v seznamu zdrojů zvolte **Vlastní rozšíření skriptu**.

Na stránce **Instalovat příponu** vyberte místní soubor prostředí PowerShell, vyplňte všechny argumenty a klepněte na tlačítko **Ok**.

### <a name="powershell"></a>PowerShell

Pomocí rutiny [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) lze přidat rozšíření vlastní skript do existujícího virtuálního počítače.

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

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z webu Azure Portal a pomocí modulu Azure PowerShell. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítače, spusťte následující příkaz:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Výstup rozšíření je zaznamenán k souborům nalezeným pod následující složkou v cílovém virtuálním počítači.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Zadané soubory se stáhnou do následující složky v cílovém virtuálním počítači.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

kde `<n>` je desítkové celé číslo, které se může změnit mezi spuštěnírozšíření.  Hodnota `1.*` odpovídá skutečné, `typeHandlerVersion` aktuální hodnotu rozšíření.  Skutečný adresář může být `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`například .  

Při provádění `commandToExecute` příkazu nastaví rozšíření tento adresář `...\Downloads\2`(například) jako aktuální pracovní adresář. Tento proces umožňuje použití relativní cesty k vyhledání `fileURIs` souborů stažených prostřednictvím vlastnosti. Příklady najdete v následující tabulce.

Vzhledem k tomu, že absolutní cesta ke stažení se může v průběhu `commandToExecute` času lišit, je lepší zvolit relativní cesty skriptu / souboru v řetězci, kdykoli je to možné. Například:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informace o cestě po uchojený první `fileUris` segment IDENTIFIKÁTORURI pro soubory stažené prostřednictvím seznamu vlastností.  Jak je znázorněno v následující tabulce, stažené soubory jsou mapovány `fileUris` do podadresářů ke stažení tak, aby odrážely strukturu hodnot.  

#### <a name="examples-of-downloaded-files"></a>Příklady stažených souborů

| Identifikátor URI v souboru Uris | Relativní stažené umístění | Absolutně stažená lokalita <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Absolutní cesty adresáře se mění po dobu životnosti virtuálního virtuálního serveru, ale ne v rámci jednoho spuštění rozšíření CustomScript.

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Můžete také podat incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
