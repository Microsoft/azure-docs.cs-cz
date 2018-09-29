---
title: Rozšíření vlastních skriptů Azure pro Windows | Dokumentace Microsoftu
description: Automatizace úkolů konfigurace virtuálních počítačů s Windows pomocí rozšíření vlastních skriptů
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.openlocfilehash: 1201e7ec232a5bd45351072949dc0b9c19af434f
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452169"
---
# <a name="custom-script-extension-for-windows"></a>Rozšíření vlastních skriptů pro Windows

Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z Azure Storage nebo z GitHubu, případně je za běhu rozšíření najdete na portálu Azure Portal. Rozšíření vlastních skriptů integruje šablony Azure Resource Manageru a je možné ho spustit také pomocí rozhraní příkazového řádku Azure, PowerShellu, portálu Azure Portal nebo REST API pro virtuální počítač Azure.

Tento dokument podrobně popisuje, jak použít rozšíření vlastních skriptů pomocí modulu Azure PowerShell, šablon Azure Resource Manageru a podrobnosti o řešení potíží v systémech Windows.

## <a name="prerequisites"></a>Požadavky

> [!NOTE]  
> Nepoužívejte ke spuštění Update-AzureRmVM se stejný virtuální počítač jako svůj parametr, protože se bude čekat na sobě rozšíření vlastních skriptů.  
>   
> 

### <a name="operating-system"></a>Operační systém

Rozšíření vlastních skriptů pro Linux se spustí na rozšíření nepodporuje rozšíření OS, další informace najdete v tomto [článku](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Umístění skriptu

Můžete použít rozšíření použijte svoje přihlašovací údaje úložiště objektů Blob v Azure pro přístup k úložišti objektů Blob v Azure. Můžete také umístění skriptu může být libovolné where, tak dlouho, dokud virtuální počítač může směrovat do tohoto koncového bodu, jako je například GitHub, interní souborový server atd.


### <a name="internet-connectivity"></a>Připojení k Internetu
Pokud potřebujete stáhnout skript externě jako GitHub nebo služby Azure Storage, potom další brány firewall nebo sítě skupiny zabezpečení musí být otevřené porty. Například pokud váš skript nachází ve službě Azure Storage, můžete povolit přístup pomocí značky služeb Azure NSG pro [úložiště](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Pokud váš skript je na místním serveru, pak může i nadále potřebovat další brány firewall nebo sítě zabezpečení musejí být otevřeny porty skupiny.

### <a name="tips-and-tricks"></a>Tipy a triky
* Nejvyšší chybovost pro toto rozšíření je z důvodu chyby syntaxe v skriptu testu, které skript se spustí bez chyb, a také vložit další protokolování do skriptu, aby bylo snazší najít, kde se nezdařilo.
* Psát skripty, které jsou idempotentní, takže pokud získat spusťte znovu více než jednou omylem, nesmí způsobit změny systému.
* Zajistěte, aby že skripty nevyžadují, aby uživatelský vstup při spuštění.
* Není povolené pro spuštění skriptu 90 minut, cokoli delšího způsobí selhání zřizování rozšíření.
* Neumisťujte restartování uvnitř skriptu, to způsobí problémy s další rozšíření, které se nainstalují, a po restartování počítače, rozšíření nebude pokračovat po restartování. 
* Pokud máte skript, který způsobí restartování, instalace aplikací a spouštět skripty atd. Měli byste naplánovat pomocí naplánované úlohy Windows, nebo pomocí nástrojů, jako je DSC, Chef, Puppet rozšíření nebo restartování.
* Rozšíření se spustí jenom skript jednou, pokud chcete spustit skript na každém spuštění počítače, je nutné použít příponu k vytvoření naplánované úlohy Windows.
* Pokud chcete naplánovat, kdy bude skript spuštěn, používejte k vytvoření naplánované úlohy Windows rozšíření. 
* Když je spuštěný skript, zobrazí se pouze "přenos" stav rozšíření z webu Azure portal nebo rozhraní příkazového řádku. Pokud chcete častější aktualizace stavu spuštění skriptu, je potřeba vytvořit svoje vlastní řešení.
* Rozšíření vlastních skriptů nativně nepodporuje proxy servery, ale můžete použít nástroj pro přenos souborů, která podporuje proxy servery v rámci skriptu, jako například *Curl* 
* Mějte na paměti z jiné výchozí adresář umístění, které mohou spoléhat skripty nebo příkazy, mají logiky, která by to.


## <a name="extension-schema"></a>Schéma rozšíření

Konfiguraci rozšíření vlastních skriptů určuje věci, jako je umístění skriptu a příkazu ke spuštění. Uložit tuto konfiguraci v konfiguračních souborech, zadejte na příkazovém řádku nebo ho zadat v šabloně Azure Resource Manageru. 

Ukládat citlivá data v chráněném konfigurace, který je šifrovaný a dešifrovat jenom uvnitř virtuálního počítače. Chráněné konfigurace je užitečná při provádění příkazu zahrnuje tajné kódy jako jsou hesla.

Tyto položky by měly považovat za citlivá data a zadaný v konfiguraci chráněných nastavení rozšíření. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaný a dešifrovat jenom na cílovém virtuálním počítači.

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
**Poznámka:** – pouze jednu verzi rozšíření lze nainstalovat na virtuálním počítači v bodě v čase, zadání vlastní skript dvakrát ve stejné šablony Resource Manageru pro stejný virtuální počítač se nezdaří. 

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| vydavatele | Microsoft.Compute | řetězec |
| type | CustomScriptExtension | řetězec |
| typeHandlerVersion | 1.9 | int |
| fileUris (např.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | pole |
| commandToExecute (např.) | prostředí PowerShell - ExecutionPolicy Unrestricted - soubor konfigurace app.ps1 Hudba | řetězec |
| storageAccountName (např.) | examplestorageacct | řetězec |
| storageAccountKey (např.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | řetězec |

>[!NOTE]
>Tyto názvy vlastností rozlišují malá a velká písmena. Aby se zabránilo problémů s nasazením, použijte názvy, jak je znázorněno zde.

#### <a name="property-value-details"></a>Podrobnosti o hodnotě vlastnosti
 * `commandToExecute`: (**požadované**, string) skript vstupního bodu ke spuštění. Místo toho použijte toto pole, pokud váš příkaz obsahuje tajné kódy jako jsou hesla, nebo vaše fileUris citlivé.
* `fileUris`: (volitelné, pole řetězců) adresy URL pro soubory ke stažení.
* `storageAccountName`: (volitelné, string) název účtu úložiště. Pokud zadáte přihlašovací údaje úložiště všechny `fileUris` musí být adresy URL pro objekty BLOB Azure.
* `storageAccountKey`: (volitelné, string) přístupový klíč účtu úložiště

Následující hodnoty lze nastavit v nastavení veřejná nebo chráněná, rozšíření odmítnou jakékoli konfigurace, ve kterém níže uvedené hodnoty se nastavují v nastavení veřejné a chráněné.
* `commandToExecute`

Pomocí nastavení veřejné může být užitečné pro ladění, ale důrazně doporučujeme používat chráněné nastavení.

Nastavení veřejné odesílají ve formátu prostého textu do virtuálních počítačů, ve kterém se skript spustí.  Chráněné nastavení jsou šifrované pomocí klíče zná pouze Azure a virtuální počítač. Nastavení se ukládají do virtuálního počítače, protože byly odeslány, tedy pokud byly šifrované nastavení jsou uloženy zašifrované na virtuálním počítači. Certifikát používaný k dešifrování šifrovaných hodnot je uložená ve virtuálním počítači a použité k dešifrování nastavení (v případě potřeby) za běhu.

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Schéma JSON, které jsou podrobně popsané v předchozí části lze použít v šabloně Azure Resource Manageru pro spuštění pomocí rozšíření vlastních skriptů při nasazení šablony Azure Resource Manageru. Ukázková šablona, která zahrnuje rozšíření vlastních skriptů můžete najít zde, [Githubu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Nasazení pomocí Powershellu

`Set-AzureRmVMCustomScriptExtension` Příkaz je možné přidat do existujícího virtuálního počítače rozšíření vlastních skriptů. Další informace najdete v tématu [Set-AzureRmVMCustomScriptExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmcustomscriptextension).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>Další příklady

### <a name="using-multiple-script"></a>Použití více skriptu
V tomto příkladu máte tři skripty, které se používají k vytvoření serveru, volání "commandToExecute", první skript, budete mít možnosti, jak se nazývají ostatní, například můžete mít hlavní skript, který určuje provádění s chybou vpravo zpracování, protokolování a správu stavu.

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
V tomto příkladu můžete chtít použít místní server protokolu SMB pro umístění vašeho skriptu mějte na paměti, není nutné předat další nastavení, v jiném, s výjimkou *commandToExecute*.

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

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Jak spustit vlastní skript více než jednou pomocí rozhraní příkazového řádku
Pokud chcete spustit více než jednou rozšíření vlastních skriptů, lze provést pouze to za těchto podmínek:
1. Parametr "Name" rozšíření je stejný jako předchozí nasazení rozšíření.
2. Jinak konfigurace bude příkaz nebyl proveden znovu musí aktualizovat, například můžete přidat v dynamických vlastností v příkazu, jako je časové razítko. 

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí modulu Azure PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Rozšíření provádění výstup je zaznamenán soubory nalezené v následujícím adresáři na cílovém virtuálním počítači.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Zadané soubory se stáhnou do následující složky v cílovém virtuálním počítači.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
kde `<n>` je desítkové celé číslo, které mohou změnit mezi spuštěními rozšíření.  `1.*` Hodnota odpovídá aktuálnímu skutečnou `typeHandlerVersion` hodnotu rozšíření.  Například může být skutečného adresáře `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Při provádění `commandToExecute` příkazu rozšíření nastaví tento adresář (třeba `...\Downloads\2`) jako aktuální pracovní adresář. To umožňuje použít relativní cesty pro vyhledání souborů prostřednictvím `fileURIs` vlastnost. Najdete v následující tabulce příklady.

Cesta pro stažení absolutní může lišit v čase, je lepší zvolit cesty relativní skript v `commandToExecute` string, kdykoli je to možné. Příklad:
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informace o cestě po první segment identifikátoru URI je zachován z důvodu soubory stahované prostřednictvím `fileUris` seznam vlastností.  Jak je znázorněno v následující tabulce, stahované soubory jsou mapovány na stažení podadresáře tak, aby odrážely struktury `fileUris` hodnoty.  

#### <a name="examples-of-downloaded-files"></a>Příklady stažené soubory

| Identifikátor URI v fileUris | Relativní umístění | Stáhnout absolutní umístění * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Jako výše, absolutní adresář cesty změnit během životního cyklu virtuálního počítače, ale ne v rámci jednoho spuštění rozšíření customscript by.

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
