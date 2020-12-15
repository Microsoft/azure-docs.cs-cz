---
title: Obslužná rutina rozšíření pro konfiguraci požadovaného stavu Azure
description: Nahrání a použití konfigurace PowerShellu DSC na virtuálním počítači Azure s použitím rozšíření DSC
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 5254d83c18ddc9f2a5518ed4f711d4cd73ab6de7
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510936"
---
# <a name="powershell-dsc-extension"></a>Rozšíření PowerShell DSC

## <a name="overview"></a>Přehled

Rozšíření PowerShell DSC pro Windows je publikované a podporované Microsoftem. Rozšíření nahrává a na virtuálním počítači Azure používá konfiguraci PowerShellu DSC. Rozšíření DSC volá do PowerShell DSC, aby přijalo přijatou konfiguraci DSC na virtuálním počítači. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače DSC pro Windows.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření DSC podporuje následující operační systémy:

Windows Server 2019, Windows Server 2016, Windows server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření DSC pro Windows vyžaduje, aby cílový virtuální počítač mohl komunikovat s Azure a umístění konfiguračního balíčku (soubor. zip), pokud je uložený v umístění mimo Azure. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro část nastavení rozšíření DSC v šabloně Azure Resource Manager. 

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

| Name | Hodnota/příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| vydavatel | Microsoft. PowerShell. DSC | řetězec |
| typ | DSC | řetězec |
| typeHandlerVersion | 2,77 | int |

### <a name="settings-property-values"></a>Hodnoty vlastností nastavení

| Name | Typ dat | Popis
| ---- | ---- | ---- |
| Settings. wmfVersion | řetězec | Určuje verzi rozhraní Windows Management Framework, která má být nainstalována na VIRTUÁLNÍm počítači. Když nastavíte tuto vlastnost na nejnovější, nainstaluje se aktualizovaná verze WMF. Pro tuto vlastnost jsou dostupné jenom aktuální hodnoty "4,0", "5,0" a "nejnovější". Tyto možné hodnoty se vztahují na aktualizace. Výchozí hodnota je ' nejnovější '. |
| settings.configuration. URL | řetězec | Určuje umístění adresy URL, ze kterého se má stáhnout soubor zip konfigurace DSC. Pokud zadaná adresa URL vyžaduje pro přístup token SAS, bude nutné nastavit vlastnost protectedSettings.configurationUrlSasToken na hodnotu vašeho tokenu SAS. Tato vlastnost je povinná, pokud jsou definovány settings.configuration. Script a/nebo settings.configuration. Function.
| settings.configuration. Script | řetězec | Určuje název souboru skriptu, který obsahuje definici konfigurace DSC. Tento skript musí být v kořenové složce souboru ZIP staženého z adresy URL určené vlastností Configuration. URL. Tato vlastnost je povinná, pokud jsou definovány settings.configuration. URL nebo settings.configuration. Script.
| settings.configuration. Function | řetězec | Určuje název konfigurace DSC. Konfigurace s názvem musí být obsažena ve skriptu definovaném Configuration. Script. Tato vlastnost je povinná, pokud jsou definovány settings.configuration. URL nebo settings.configuration. Function.
| settings.configurationArguments | Kolekce | Definuje všechny parametry, které byste chtěli předat konfiguraci DSC. Tato vlastnost nebude zašifrována.
| settings.configurationData. URL | řetězec | Určuje adresu URL, ze které se má stáhnout soubor konfiguračních dat (. pds1), který se použije jako vstup pro konfiguraci DSC. Pokud zadaná adresa URL vyžaduje pro přístup token SAS, bude nutné nastavit vlastnost protectedSettings.configurationDataUrlSasToken na hodnotu vašeho tokenu SAS.
| Settings. Privacy. dataenable | řetězec | Povolí nebo zakáže shromažďování telemetrie. Jedinou možnou hodnotou této vlastnosti jsou Enable, Disable, nebo $null. Když tuto vlastnost necháte prázdnou, nebo hodnota null, povolí se telemetrie.
| Settings. advancedOptions. forcePullAndApply | Logická hodnota | Toto nastavení je navrženo pro zlepšení možností práce s rozšířením pro registraci uzlů pomocí Azure Automation DSC.  Pokud je hodnota `$true` , bude rozšíření čekat na první spuštění konfigurace, která byla ze služby načtena, a teprve potom vrátí úspěch nebo selhání.  Pokud je hodnota nastavena na $false, stav vrácený rozšířením bude odkazovat pouze na to, zda byl uzel registrován s konfigurací stavu Azure Automation úspěšně a že konfigurace uzlu nebude spuštěna během registrace.
| Settings. advancedOptions. downloadMappings | Kolekce | Definuje alternativní umístění pro stahování závislostí, jako jsou WMF a .NET.

### <a name="protected-settings-property-values"></a>Hodnoty vlastností chráněných nastavení

| Name | Typ dat | Popis
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | řetězec | Definuje všechny parametry, které byste chtěli předat konfiguraci DSC. Tato vlastnost bude zašifrována. |
| protectedSettings.configurationUrlSasToken | řetězec | Určuje token SAS pro přístup k adrese URL definované pomocí Configuration. URL. Tato vlastnost bude zašifrována. |
| protectedSettings.configurationDataUrlSasToken | řetězec | Určuje token SAS pro přístup k adrese URL definované pomocí configurationData. URL. Tato vlastnost bude zašifrována. |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager.
Šablony jsou ideální při nasazení jednoho nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení.
Ukázková Správce prostředků Šablona obsahující rozšíření DSC pro Windows najdete v [galerii Azure Rychlé zprovoznění](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí rozhraní příkazového řádku Azure CLI. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Balíček rozšíření se stáhne a nasadí do tohoto umístění na virtuálním počítači Azure.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Stavový soubor rozšíření obsahuje kód dílčího stavu a úspěchu/chyby stavu spolu s podrobnou chybou a popisem pro každé spuštění rozšíření.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Výstupní protokoly rozšíření jsou protokolovány do následujícího adresáře:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Kódy chyb a jejich významy

| Kód chyby | Význam | Možná akce |
| :---: | --- | --- |
| 1000 | Obecná chyba | Zpráva o této chybě je poskytována specifickou výjimkou v protokolech rozšíření. |
| 52 | Chyba instalace rozšíření | Zpráva o této chybě je poskytnuta specifickou výjimkou. |
| 1002 | Chyba instalace WMF | Při instalaci WMF došlo k chybě. |
| 1004 | Neplatný balíček zip | Neplatný PSČ; Při dekomprimaci souboru ZIP došlo k chybě. |
| 1100 | Chyba argumentu | Označuje problém ve vstupu, který poskytl uživatel. Zpráva o chybě je poskytnuta specifickou výjimkou.|


### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
