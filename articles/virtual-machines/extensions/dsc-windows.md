---
title: Azure požadovaného stavu konfigurace rozšíření obslužné rutiny | Microsoft Docs
description: Nahrání a použití konfigurace DSC prostředí PowerShell ve virtuálním počítači Azure pomocí rozšíření DSC
services: virtual-machines-windows
documentationcenter: ''
author: eshaparmar
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: esparmar
ms.openlocfilehash: b34314951980f7dbe2269119883dec52a90a0587
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="powershell-dsc-extension"></a>Rozšíření DSC prostředí PowerShell

## <a name="overview"></a>Přehled

Rozšíření DSC prostředí PowerShell pro systém Windows je publikována a společnost Microsoft podporuje. Rozšíření odešle a použije konfiguraci DSC prostředí PowerShell ve virtuálním počítači Azure. Rozšíření DSC zavolá do prostředí PowerShell DSC na uplatní obdržená konfigurace DSC do virtuálního počítače. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače DSC pro systém Windows.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření DSC podporuje následující operační systém

Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, klient Windows 7 nebo 8.1

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření DSC pro Windows vyžaduje, aby cílový virtuální počítač je připojený k Internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

Následujícím kódu JSON znázorňuje schéma pro část nastavení rozšíření DSC v šablonu Azure Resource Manager. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.73",
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
            "forcePullAndApply": false
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

| Název | Hodnota nebo příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| Vydavatele | Microsoft.Powershell.DSC | řetězec |
| type | DSC | řetězec |
| typeHandlerVersion | 2,73 | celá čísla |

### <a name="settings-property-values"></a>Nastavení hodnot vlastností

| Název | Typ dat | Popis
| ---- | ---- | ---- |
| settings.wmfVersion | řetězec | Určuje verzi systému Windows Management Framework, který by měly být nainstalovány na vašem virtuálním počítači. Nastavení této vlastnosti 'nejnovější' nainstaluje nejaktuálnější verzi WMF. Pouze aktuální hodnoty této vlastnosti jsou 4.0, '5.0' a 'nejnovější'. Tyto hodnoty jsou předmětem aktualizace. Výchozí hodnota je 'nejnovější'. |
| Settings.Configuration.URL | řetězec | Určuje adresu URL umístění, ze kterého ke stažení souboru zip konfigurace DSC. Pokud zadaná adresa URL vyžaduje SAS token pro přístup, musíte nastavit vlastnost protectedSettings.configurationUrlSasToken na hodnotu tokenu SAS. Tato vlastnost je vyžadována, pokud jsou definovány settings.configuration.script nebo settings.configuration.function.
| Settings.Configuration.Script | řetězec | Určuje název souboru skriptu, který obsahuje definici konfigurace DSC. Tento skript musí být v kořenové složce stáhnout z adresy URL určeného vlastností configuration.url souboru zip. Tato vlastnost je vyžadována, pokud jsou definovány settings.configuration.url nebo settings.configuration.script.
| Settings.Configuration.Function | řetězec | Určuje název konfigurace DSC. Konfigurace s názvem musí být obsažené ve skriptu definované configuration.script. Tato vlastnost je vyžadována, pokud jsou definovány settings.configuration.url nebo settings.configuration.function.
| settings.configurationArguments | Kolekce | Definuje žádné parametry, které chcete předat do vaší konfigurace DSC. Tato vlastnost se šifrovat nebude.
| settings.configurationData.url | řetězec | Určuje adresu URL, ze kterého mají být staženy konfiguračního souboru dat (.pds1) chcete použít jako vstup pro konfiguraci DSC. Pokud zadaná adresa URL vyžaduje SAS token pro přístup, musíte nastavit vlastnost protectedSettings.configurationDataUrlSasToken na hodnotu tokenu SAS.
| settings.privacy.dataEnabled | řetězec | Povolí nebo zakáže telemetrie kolekce. Pouze pro tuto vlastnost hodnoty jsou povolit, "Zakázat", ", nebo $null. Opouštění tato vlastnost prázdná nebo mít hodnotu null zapne telemetrii
| settings.advancedOptions.forcePullAndApply | BOOL | Umožňuje rozšíření DSC k aktualizaci a uplatní konfigurace DSC vyžádání obsahu při aktualizaci režimu.
| settings.advancedOptions.downloadMappings | Kolekce | Určuje alternativní umístění pro stažení závislosti, jako je například WMF a rozhraní .NET

### <a name="protected-settings-property-values"></a>Chráněné hodnoty nastavení vlastností

| Název | Typ dat | Popis
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | řetězec | Definuje žádné parametry, které chcete předat do vaší konfigurace DSC. Tato vlastnost bude šifrovat. |
| protectedSettings.configurationUrlSasToken | řetězec | Určuje token SAS, který má přístup k adrese URL definované configuration.url. Tato vlastnost bude šifrovat. |
| protectedSettings.configurationDataUrlSasToken | řetězec | Určuje token SAS, který má přístup k adrese URL definované configurationData.url. Tato vlastnost bude šifrovat. |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure se dá nasadit pomocí šablon Azure Resource Manager. Šablony jsou ideální, pokud nasazujete jednu nebo více virtuálních počítačů, které vyžadují konfiguraci nasazení post. Ukázka šablonu Resource Manager, která obsahuje rozšíření virtuálního počítače agenta OMS naleznete na [Azure rychlý Start Galerie](https://github.com/Azure/azure-quickstart-templates/tree/052db5feeba11f85d57f170d8202123511f72044/dsc-extension-iis-server-windows-vm). 

Konfigurace JSON pro rozšíření virtuálního počítače můžete vnořit prostředek virtuálního počítače nebo umístěn na kořenový server WSUS nebo nejvyšší úrovně šablony JSON Resource Manager. Umístění konfigurace JSON ovlivňuje hodnota název prostředku a typem. 

Při vnoření rozšíření prostředků, JSON je umístěn v `"resources": []` objektu virtuálního počítače. Při vkládání rozšíření JSON v kořenovém adresáři šablony, názvu prostředku obsahuje odkaz na nadřazený virtuální počítač a typ odráží vnořené konfigurace.  


## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Rozhraní příkazového řádku Azure můžete použít k nasazení rozšíření virtuálního počítače agenta OMS na existující virtuální počítač. Nahraďte klíč OMS a OMS ID s těmi, která z pracovního prostoru OMS. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.73 --protected-settings '{}' \
  --settings '{}'
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Z portálu Azure a pomocí rozhraní příkazového řádku Azure je možné načíst data o stavu nasazení rozšíření. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí rozhraní příkazového řádku Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

V balíčku rozšíření je stáhnout a nasadit do tohoto umístění pro virtuální počítač Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Rozšíření stavový soubor obsahuje dílčí stav a stavové kódy úspěch nebo Chyba spolu s podrobné chyby a popis pro každé rozšíření spustit.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Rozšíření výstupní protokoly jsou zaznamenány na následující adresář:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Kódy chyb a jejich významů

| Kód chyby | Význam | Možné akce |
| :---: | --- | --- |
| 1000 | Obecná chyba | Zpráva pro tuto chybu poskytuje konkrétní výjimka v protokolech rozšíření |
| 52 | Chyba instalace rozšíření | Zpráva pro tuto chybu poskytuje konkrétní výjimky |
| 1002 | Chyba instalaci WMF | Při instalaci WMF došlo k chybě. |
| 1004 | Neplatný Zip balíčku | Neplatný zip; Chyba rozbalování souboru zip |
| 1100 | Chyba argumentu | Označuje potíže s vstup uživatelem. Zprávy pro chyby poskytuje konkrétní výjimky|


### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte Get podpory. Informace o používání Azure podporovat, najdete v tématu [podporu Microsoft Azure – nejčastější dotazy](https://azure.microsoft.com/support/faq/).