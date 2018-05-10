---
title: Rozšíření Azure vlastních skriptů pro Windows | Microsoft Docs
description: Automatizovat úkoly konfigurace virtuálního počítače s Windows pomocí rozšíření vlastních skriptů
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: danis
ms.openlocfilehash: 34c16b686a50994862bef14cefec1a4799a343c4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="custom-script-extension-for-windows"></a>Rozšíření vlastních skriptů pro Windows

Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z Azure Storage nebo z GitHubu, případně je za běhu rozšíření najdete na portálu Azure Portal. Rozšíření vlastních skriptů integruje šablony Azure Resource Manageru a je možné ho spustit také pomocí rozhraní příkazového řádku Azure, PowerShellu, portálu Azure Portal nebo REST API pro virtuální počítač Azure.

Tento dokument podrobně popisuje postup používání rozšíření vlastních skriptů pomocí modulu Azure PowerShell, šablon Azure Resource Manageru a podrobnosti o řešení potíží s kroky v systémech Windows.

## <a name="prerequisites"></a>Požadavky

> [!NOTE]  
> Vzhledem k tomu, že bude čekat na sobě spustit aktualizaci-AzureRmVM s stejného virtuálního počítače jako jeho parametr nepoužívejte rozšíření vlastních skriptů.  
>   
> 

### <a name="operating-system"></a>Operační systém

Rozšíření vlastních skriptů Linux se spustí na rozšíření rozšíření podporován operační systém na, další informace najdete v tématu to [článku](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Umístění skriptu

Rozšíření můžete použijte svoje přihlašovací údaje úložiště objektů Blob v Azure pro přístup k úložišti objektů Blob v Azure. Případně umístění skriptu může být žádné where, tak dlouho, dokud virtuální počítač může směrovat do tohoto koncového bodu, jako je například Githubu, interní souborového serveru atd.


### <a name="internet-connectivity"></a>Připojení k Internetu
Pokud budete muset stáhnout skript externě například GitHub nebo úložiště Azure, pak další brány firewall nebo síťových zabezpečení skupiny musí být otevřené porty. Například pokud váš skript nachází ve službě Azure Storage, můžete povolit přístup pomocí značek služby Azure skupina NSG pro [úložiště](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Pokud váš skript se na místním serveru, pak může potřebujete ještě další brány firewall nebo síťových zabezpečení muset otevřít porty skupiny.

### <a name="tips-and-tricks"></a>Tipy a triky
* Nejvyšší míra selhání pro toto rozšíření je z důvodu chyby syntaxe ve skriptu, testů, které skript se spustí bez chyby, a také vložit do další protokolování do skript tak, aby bylo snazší najít, kde se nezdařilo.
* Napsat skripty, které jsou idempotent, takže pokud získat spustit znovu, více než jednou omylem, nezpůsobí změny systému.
* Zajistěte, aby že skripty nevyžadují vstup uživatele při spuštění.
* Je 90 minut povolené pro spuštění skriptu, nic delší způsobí selhání zřídit rozšíření.
* Nevkládejte restartování uvnitř skriptu, to může způsobit problémy s další rozšíření, které se nainstalují, a po restartování, rozšíření nebude pokračovat po restartování. 
* Pokud máte skript, který způsobí restart, instalace aplikací a spouštět skripty atd. Měli byste naplánovat pomocí naplánované úlohy systému Windows, nebo pomocí nástrojů, jako je DSC, Chef, Puppet rozšíření nebo restartování.
* Rozšíření se spustí jenom skript jednou, pokud chcete spustit skript na každém spuštění, budete muset použít rozšíření k vytvoření naplánované úlohy systému Windows.
* Pokud chcete naplánovat, kdy bude skript spuštěn, by měl použít rozšíření k vytvoření naplánované úlohy systému Windows. 
* Když je skript spuštěn, zobrazí se pouze 'přechodu, stav rozšíření z portálu Azure nebo rozhraní příkazového řádku. Pokud chcete častější aktualizace stavu spouštění skriptu, musíte vytvořit vlastní řešení.
* Rozšíření vlastních skriptů nenabízí nativní podporu proxy servery, ale můžete použít nástroj přenos souboru, který podporuje proxy servery v rámci vašeho skriptu, například *Curl* 
* Mějte na jiné výchozí umístění adresáře, které skripty nebo příkazy, můžou záviset, mají logiku pro toto zpracování.


## <a name="extension-schema"></a>Schéma rozšíření

Konfigurace rozšíření vlastních skriptů určuje takové věci, jako umístění skriptu a příkaz ke spuštění. Můžete tuto konfiguraci uložit konfigurační soubory, zadejte na příkazovém řádku nebo zadejte jej v šablonu Azure Resource Manager. 

V konfiguraci chráněného, které se budou šifrovat a dešifrovat jenom ve virtuálním počítači můžete ukládat citlivá data. Chráněné konfigurace je užitečné při provádění příkazu zahrnuje tajné klíče, jako například heslo.

Tyto položky by měl být považován za citlivá data a zadaný v konfiguraci chráněných nastavení rozšíření. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaná a dešifrovat jenom na cílový virtuální počítač.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
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
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```
**Poznámka:** -jenom jedna verze nástroje rozšíření lze nainstalovat na virtuální počítač v bodě v čase, určení vlastních skriptů dvakrát ve stejné šablony Resource Manageru pro stejného virtuálního počítače se nezdaří. 

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota nebo příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| Vydavatele | Microsoft.Compute | řetězec |
| type | CustomScriptExtension | řetězec |
| typeHandlerVersion | 1.9 | celá čísla |
| fileUris (např.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | pole |
| commandToExecute (např.) | prostředí PowerShell - ExecutionPolicy Unrestricted - souboru konfigurace app.ps1 Hudba | řetězec |
| storageAccountName (např.) | examplestorageacct | řetězec |
| storageAccountKey (např.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | řetězec |

>[!NOTE]
>Názvy těchto vlastností jsou malá a velká písmena. Abyste zabránili problémům nasazení, použijte názvy, jak je vidět tady.

#### <a name="property-value-details"></a>Podrobnosti o hodnotě vlastnosti
 * `commandToExecute`: (**požadované**, řetězec) k provedení skriptu bodu položku. Toto pole použijte místo toho, pokud váš příkaz obsahuje tajné klíče, jako jsou hesla, nebo vaše fileUris jsou citlivé.
* `fileUris`: (volitelné, pole řetězců) adresy URL pro soubory ke stažení.
* `storageAccountName`: (volitelné, string) název účtu úložiště. Pokud chcete zadat pověření pro úložiště, všechny `fileUris` musí být adresy URL pro objekty BLOB Azure.
* `storageAccountKey`: (volitelné, string) přístupový klíč účtu úložiště

Následující hodnoty lze nastavit v nastavení veřejného nebo chráněné, rozšíření odmítnou jakákoli konfigurace, kde se níže uvedené hodnoty nastavují v nastavení veřejných a chráněné.
* `commandToExecute`

Pomocí nastavení veřejných může být užitečné pro ladění, ale důrazně doporučujeme použít chráněné nastavení.

Nastavení veřejných jsou odesílány ve formátu prostého textu k virtuálnímu počítači, kde bude skript proveden.  Chráněné nastavení jsou šifrované pomocí klíče zná pouze Azure a virtuální počítač. Nastavení se ukládají do virtuálního počítače, protože byly odeslány, tj. Pokud byly šifrované nastavení jsou uloženy šifrované ve virtuálním počítači. Certifikát používaný k dešifrování šifrovaných hodnot je uložená ve virtuálním počítači a použité k dešifrování nastavení (v případě potřeby) za běhu.

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure se dá nasadit pomocí šablon Azure Resource Manager. Schéma JSON, které jsou popsané v předchozí části lze použít v šablonu Azure Resource Manager ke spuštění rozšíření vlastních skriptů při nasazení šablony Azure Resource Manager. Ukázka šablony, která obsahuje rozšíření vlastních skriptů je zde uveden, [Githubu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Nasazení prostředí PowerShell

`Set-AzureRmVMCustomScriptExtension` Příkaz lze použít k přidání rozšíření vlastních skriptů do existujícího virtuálního počítače. Další informace najdete v tématu [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>Další příklady

### <a name="using-multiple-script"></a>Pomocí několika skriptu
V tomto příkladu máte tři skripty, které se používají k vytvoření vašeho serveru, volání 'commandToExecute', které mají první skript a potom na tom, jak se nazývají jiné možnosti, například může mít hlavní skript, který řídí provádění správné došlo k chybě zpracování, protokolování a správy stavu.

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzureRmVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Spouštění skriptů z místní sdílené složky
V tomto příkladu můžete chtít použít místní server protokolu SMB pro vaše umístění skriptu Všimněte si, není třeba předat další nastavení, v jiném, s výjimkou *commandToExecute*.

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzureRmVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Spuštění vlastního skriptu více než jednou pomocí rozhraní příkazového řádku
Pokud chcete spustit rozšíření vlastních skriptů více než jednou, můžete provést jenom to za těchto podmínek:
1. Parametr "Název" rozšíření je stejný jako předchozí nasazení rozšíření.
2. Jinak konfigurace bude příkaz nebyl proveden znovu musí aktualizovat, například můžete přidat v dynamických vlastností v příkazu, jako je například časového razítka. 

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření mohou být načteny z portálu Azure a pomocí modulu Azure PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Soubory, které jsou v adresáři následující na cílový virtuální počítač je protokolovány výstupu spuštění rozšíření.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Zadané soubory staženy do následujícího adresáře na cílový virtuální počítač.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
kde `<n>` je desítkové celé číslo, které může změnit mezi jednotlivými spuštěními rozšíření.  `1.*` Hodnota odpovídá skutečným, aktuální `typeHandlerVersion` hodnotu rozšíření.  Skutečný adresář může být například `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Při provádění `commandToExecute` příkaz rozšíření nastaví tento adresář (například `...\Downloads\2`) jako aktuální pracovní adresář. To umožňuje použití relativní cesty vyhledejte soubory stáhli prostřednictvím `fileURIs` vlastnost. Najdete v následující tabulce příklady.

Vzhledem k tomu, že cesta pro stažení absolutní se mohou lišit v čase, je lepší zvolit relativní skriptu nebo cesty k souboru v `commandToExecute` řetězce, kdykoli je to možné. Příklad:
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informace o cestě po první segment identifikátoru URI se zachovává kvůli soubory stažené prostřednictvím `fileUris` seznam vlastností.  Jak je znázorněno v následující tabulce, stažené soubory jsou mapovány na stažení podadresáře tak, aby odrážela strukturu `fileUris` hodnoty.  

#### <a name="examples-of-downloaded-files"></a>Příklady stažené soubory

| Identifikátor URI v fileUris | Relativní umístění stažené | Absolutní stáhli umístění * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Jako výš, cesty absolutní adresářů změnit během životního cyklu virtuálního počítače, ale není v rámci jednoho spuštění rozšíření CustomScript.

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte Get podpory. Informace o používání Azure podporovat, najdete v tématu [podporu Microsoft Azure – nejčastější dotazy](https://azure.microsoft.com/support/faq/).
