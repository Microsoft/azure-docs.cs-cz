---
title: Obslužná rutina rozšíření konfigurace požadovaného stavu Azure
description: Nahrání a použití konfigurace DSC prostředí PowerShell u virtuálního počítače Azure pomocí rozšíření DSC
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 592c731d1851ac36cf9b57864750df0603b6c3fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253959"
---
# <a name="powershell-dsc-extension"></a>Rozšíření aplikace PowerShell DSC

## <a name="overview"></a>Přehled

Rozšíření PowerShell DSC pro Windows je publikováno a podporováno společností Microsoft. Rozšíření nahraje a použije konfiguraci DSC prostředí PowerShell na virtuálním počítači Azure. Rozšíření DSC volá do prostředí PowerShell DSC k provedení přijaté konfigurace DSC na virtuálním počítači. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače DSC pro Windows.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření DSC podporuje následující operační

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření DSC pro Windows vyžaduje, aby cílový virtuální počítač je schopen komunikovat s Azure a umístění konfiguračního balíčku (.zip soubor), pokud je uložen v umístění mimo Azure. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro nastavení část rozšíření DSC v šabloně Azure Resource Manager. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Name (Název) | Hodnota / Příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| vydavatel | Microsoft.Powershell.DSC | řetězec |
| type | DSC | řetězec |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Nastavení hodnot vlastností

| Name (Název) | Typ dat | Popis
| ---- | ---- | ---- |
| verze settings.wmfVersion | řetězec | Určuje verzi rozhraní Windows Management Framework, která by měla být nainstalována na vašem virtuálním počítači. Nastavení této vlastnosti na 'nejnovější' nainstaluje nejaktuálnější verzi WMF. Pouze aktuální možné hodnoty pro tuto vlastnost jsou '4.0', '5.0' a 'latest'. Tyto možné hodnoty podléhají aktualizacím. Výchozí hodnota je nejnovější. |
| adresu settings.configuration.url | řetězec | Určuje umístění adresy URL, ze kterého chcete stáhnout soubor zip konfigurace DSC. Pokud zadaná adresa URL vyžaduje pro přístup token SAS, bude nutné nastavit vlastnost protectedSettings.configurationUrlSasToken na hodnotu tokenu SAS. Tato vlastnost je vyžadována, pokud jsou definovány funkce settings.configuration.script a/nebo settings.configuration.function.
| settings.configuration.script | řetězec | Určuje název souboru skriptu, který obsahuje definici konfigurace DSC. Tento skript musí být v kořenové složce souboru ZIP staženého z adresy URL určené vlastností configuration.url. Tato vlastnost je vyžadována, pokud jsou definovány parametry settings.configuration.url a/or settings.configuration.script.
| settings.configuration.function | řetězec | Určuje název konfigurace DSC. Pojmenovaná konfigurace musí být obsažena ve skriptu definovaném souborem configuration.script. Tato vlastnost je vyžadována, pokud jsou definovány parametry settings.configuration.url a/nebo settings.configuration.function.
| settings.configurationArguments | Kolekce | Definuje všechny parametry, které chcete předat konfiguraci DSC. Tato vlastnost nebude zašifrována.
| settings.configurationData.url | řetězec | Určuje adresu URL, ze které chcete stáhnout konfigurační data (.pds1), která má být používána jako vstup pro konfiguraci DSC. Pokud zadaná adresa URL vyžaduje pro přístup token SAS, bude nutné nastavit vlastnost protectedSettings.configurationDataUrlSasToken na hodnotu tokenu SAS.
| settings.privacy.dataEnabled | řetězec | Povolí nebo zakáže kolekci telemetrie. Jediné možné hodnoty pro tuto vlastnost jsou "Povolit", Zakázat, "nebo $null. Ponechání této vlastnosti prázdné nebo nulové povolí telemetrická data.
| settings.advancedOptions.forcePullAndApply | Logická hodnota | Toto nastavení je navrženo tak, aby vylepšilo možnosti práce s rozšířením pro registraci uzlů pomocí Azure Automation DSC.  Pokud je `$true`hodnota , rozšíření bude čekat na první spuštění konfigurace vytáhl ze služby před vrácením úspěch/neúspěch.  Pokud je hodnota nastavena na $false, stav vrácený rozšířením bude odkazovat pouze na to, zda byl uzel úspěšně zaregistrován pomocí konfigurace stavu azure automation a konfigurace uzlu nebude spuštěna během registrace.
| settings.advancedOptions.downloadMappings | Kolekce | Definuje alternativní umístění pro stahování závislostí, jako jsou WMF a .NET.

### <a name="protected-settings-property-values"></a>Hodnoty vlastností chráněného nastavení

| Name (Název) | Typ dat | Popis
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | řetězec | Definuje všechny parametry, které chcete předat konfiguraci DSC. Tato vlastnost bude zašifrována. |
| protectedSettings.configurationUrlSasToken | řetězec | Určuje token SAS pro přístup k adrese URL definované adresou configuration.url. Tato vlastnost bude zašifrována. |
| protectedSettings.configurationDataUrlSasToken | řetězec | Určuje token SAS pro přístup k adrese URL definované konfiguračním data.url. Tato vlastnost bude zašifrována. |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru.
Šablony jsou ideální při nasazování jednoho nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení.
Ukázkovou šablonu Správce prostředků, která obsahuje rozšíření DSC pro Windows, najdete v [galerii rychlých startů Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z portálu Azure a pomocí azure cli. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí příkazu Příkaz příkazu Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Balíček rozšíření se stáhne a nasadí do tohoto umístění na virtuálním počítači Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Stavový soubor rozšíření obsahuje kódy úspěchu/chyby dílčího stavu a stavu spolu s podrobnou chybou a popisem pro každý běh rozšíření.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Výstupní protokoly rozšíření jsou protokolovány do následujícího adresáře:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Chybové kódy a jejich význam

| Kód chyby | Význam | Možná akce |
| :---: | --- | --- |
| 1000 | Obecná chyba | Zpráva pro tuto chybu je poskytována konkrétní výjimku v protokolech rozšíření |
| 52 | Chyba instalace rozšíření | Zpráva pro tuto chybu je poskytována konkrétní výjimku |
| 1002 | Chyba instalace wmf | Při instalaci wmf došlo k chybě. |
| 1004 | Neplatný balíček ZIP | Neplatný zip ; Při rozbalení zipu došlo k chybě. |
| 1100 | Chyba argumentu | Označuje problém ve vstupu poskytnutém uživatelem. Zpráva o chybě je poskytována konkrétní výjimkou|


### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
