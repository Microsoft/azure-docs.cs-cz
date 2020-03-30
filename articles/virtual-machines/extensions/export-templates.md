---
title: Export skupin prostředků Azure, které obsahují rozšíření virtuálních aplikací
description: Exportujte šablony Správce prostředků, které obsahují rozšíření virtuálních strojů.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 79991dad96742109817d579b951082d1a30e3951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253907"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Export skupin prostředků obsahujících rozšíření virtuálních aplikací

Skupiny prostředků Azure lze exportovat do nové šablony Správce prostředků, kterou pak můžete znovu nasadit. Proces exportu interpretuje existující prostředky a vytvoří šablonu Správce prostředků, která při nasazení zavede výsledky v podobné skupině prostředků. Při použití možnosti exportu skupiny prostředků proti skupině prostředků obsahující rozšíření virtuálního počítače je třeba považovat za několik položek, jako je kompatibilita rozšíření a chráněná nastavení.

Tento dokument podrobně popisuje, jak funguje proces exportu skupiny prostředků týkající se rozšíření virtuálních strojů, včetně seznamu podporovaných rozšíření a podrobností o zpracování zabezpečených dat.

## <a name="supported-virtual-machine-extensions"></a>Podporovaná rozšíření virtuálních strojů

K dispozici je mnoho rozšíření virtuálního počítače. Ne všechna rozšíření lze exportovat do šablony Správce prostředků pomocí funkce "Automatizační skript". Pokud rozšíření virtuálního počítače není podporováno, musí být ručně umístěno zpět do exportované šablony.

Následující rozšíření lze exportovat pomocí funkce automatizačního skriptu.

| Linka ||||
|---|---|---|---|
| Zálohování Acronis | Datadog Windows Agent | Opravy operačního systému pro Linux | Snímek virtuálního počítače Linux
| Acronis Zálohování Linux | Rozšíření Dockeru | Loutkář |
| Bg Info | Rozšíření DSC | Stránky 24x7 Apm Insight |
| BMC CTM Agent Linux | Dynatrace Linux | Stránky 24x7 Linux Server |
| Systém Windows agenta CTM BMC | Dynatrace Windows | Web 24x7 Windows Server |
| Klient šéfkuchaře | HpE bezpečnostní aplikace Defender | Trend Micro DSA |
| Vlastní skript | IaaS Antimalware | Trend Micro DSA Linux |
| Rozšíření vlastních skriptů | Diagnostika IaaS | Přístup k virtuálním min( VM pro Linux) |
| Vlastní skript pro Linux | Klient linuxového šéfkuchaře | Přístup k virtuálním min( VM pro Linux) |
| Agent Linuxu Datadog | Diagnostika Linuxu | VM Snapshot |

## <a name="export-the-resource-group"></a>Export skupiny prostředků

Chcete-li exportovat skupinu prostředků do opakovaně použitelné šablony, proveďte následující kroky:

1. Přihlášení k webu Azure Portal
2. V nabídce Rozbočovač klikněte na Skupiny prostředků.
3. Vyberte cílovou skupinu prostředků ze seznamu.
4. V okně Skupina prostředků klikněte na Automatizační skript.

![Export šablony](./media/export-templates/template-export.png)

Skript automatizace Azure Resource Manager vytváří šablonu Správce prostředků, soubor parametrů a několik ukázkových skriptů nasazení, jako je PowerShell a Azure CLI. V tomto okamžiku lze exportovanou šablonu stáhnout pomocí tlačítka stáhnout, přidat jako novou šablonu do knihovny šablon nebo znovu nasadit pomocí tlačítka pro nasazení.

## <a name="configure-protected-settings"></a>Konfigurace chráněných nastavení

Mnoho rozšíření virtuálních počítačů Azure zahrnuje konfiguraci chráněného nastavení, která šifruje citlivá data, jako jsou přihlašovací údaje a konfigurační řetězce. Chráněná nastavení se neexportují pomocí automatizačního skriptu. V případě potřeby je třeba do exportované šablony znovu vložit chráněná nastavení.

### <a name="step-1---remove-template-parameter"></a>Krok 1 – Odebrat parametr šablony

Při exportu skupiny prostředků je vytvořen jeden parametr šablony, který poskytuje hodnotu exportovaným chráněným nastavením. Tento parametr lze odebrat. Chcete-li odebrat parametr, projděte seznam parametrů a odstraňte parametr, který vypadá podobně jako tento příklad JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Krok 2 – Získání vlastností chráněného nastavení

Vzhledem k tomu, že každé chráněné nastavení má sadu požadovaných vlastností, je třeba shromáždit seznam těchto vlastností. Každý parametr konfigurace chráněného nastavení najdete ve [schématu Azure Resource Manager na GitHubu](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Toto schéma zahrnuje pouze sady parametrů pro rozšíření uvedená v části přehled tohoto dokumentu. 

Z úložiště schématu vyhledejte požadované rozšíření, v tomto `IaaSDiagnostics`příkladu . Jakmile je `protectedSettings` objekt rozšíření lokalizován, poznamenejte si každý parametr. `IaasDiagnostic` V příkladu rozšíření `storageAccountName`jsou parametry require `storageAccountKey`, `storageAccountEndPoint`, a .

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Krok 3 – Opětovné vytvoření chráněné konfigurace

V exportované šabloně `protectedSettings` vyhledejte a nahraďte exportovaný chráněný objekt nastavení novým objektem, který obsahuje požadované parametry rozšíření a hodnotu pro každý z nich.

V příkladu `IaasDiagnostic` rozšíření by nová konfigurace chráněného nastavení vypadala jako následující příklad:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Konečný prostředek rozšíření vypadá podobně jako v následujícím příkladu JSON:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Pokud pomocí parametrů šablony poskytují hodnoty vlastností, musí být vytvořeny. Při vytváření parametrů šablony pro chráněné hodnoty nastavení `SecureString` nezapomeňte použít typ parametru tak, aby byly zabezpečeny citlivé hodnoty. Další informace o použití parametrů najdete v [tématu Vytváření šablon Azure Resource Manageru](../../resource-group-authoring-templates.md).

V příkladu `IaasDiagnostic` rozšíření by byly vytvořeny následující parametry v části parametry šablony Správce prostředků.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

V tomto okamžiku lze šablonu nasadit pomocí libovolné metody nasazení šablony.
