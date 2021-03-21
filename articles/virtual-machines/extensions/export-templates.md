---
title: Exportování skupin prostředků Azure, které obsahují rozšíření virtuálních počítačů
description: Exportujte Správce prostředků šablony, které obsahují rozšíření virtuálních počítačů.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 12/05/2016
ms.openlocfilehash: df1ae43b2c6a74448a6782a43fb86f8f4939b13a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560000"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportování skupin prostředků, které obsahují rozšíření virtuálních počítačů

Skupiny prostředků Azure je možné exportovat do nové šablony Správce prostředků, kterou je možné znovu nasadit. Proces exportu interpretuje existující prostředky a vytvoří šablonu Správce prostředků, která při nasazení výsledků do podobné skupiny prostředků. Při použití možnosti exportu skupiny prostředků u skupiny prostředků, která obsahuje rozšíření virtuálních počítačů, je potřeba zvážit několik položek, jako je například kompatibilita rozšíření a chráněné nastavení.

Tento dokument podrobně popisuje, jak proces exportu skupiny prostředků funguje v souvislosti s rozšířeními virtuálních počítačů, včetně seznamu podporovaných rozšíření, a podrobnostmi o manipulaci s zabezpečenými daty.

## <a name="supported-virtual-machine-extensions"></a>Podporovaná rozšíření virtuálních počítačů

K dispozici je řada rozšíření virtuálních počítačů. Do šablony Správce prostředků se nedají exportovat všechna rozšíření pomocí funkce skriptu Automation. Pokud rozšíření virtuálního počítače není podporováno, je nutné jej ručně umístit zpět do exportované šablony.

Následující rozšíření lze exportovat pomocí funkce skriptu automatizace.

> Acronis Backup, Acronis Backup Linux, BG info, BMC CTM agent Linux, BMC CTM agenti, Windows, vlastní skript, rozšíření vlastních skriptů, vlastní skript pro Linux, služby Datadog Linux agent, služby Datadog Windows Agent, Docker Extension, rozšíření DSC, dynaTrace Linux, dynaTrace Windows, HPE zabezpečení aplikace Defender, IaaS antimalware, IaaS Diagnostics, Linux , Neizolovaný Server Linux serveru, web s nepřetržitými systémy Windows Server, Trend Micro DSA, Trend Micro DSA, Linux, přístup k virtuálnímu počítači pro Linux, přístup k VIRTUÁLNÍm počítačům pro Linux, snímek virtuálního počítače

## <a name="export-the-resource-group"></a>Export skupiny prostředků

Pokud chcete exportovat skupinu prostředků do opakovaně použitelné šablony, proveďte následující kroky:

1. Přihlášení k webu Azure Portal
2. V nabídce centra klikněte na skupiny prostředků.
3. Vyberte cílovou skupinu prostředků ze seznamu.
4. V okně Skupina prostředků klikněte na skript Automation.

![Export šablony](./media/export-templates/template-export.png)

Skript Azure Resource Manager Automation vytvoří šablonu Správce prostředků, soubor parametrů a několik ukázkových skriptů nasazení, jako je PowerShell a Azure CLI. V tuto chvíli je možné vyexportovaná šablona stáhnout pomocí tlačítka pro stažení, přidat jako novou šablonu do knihovny šablon nebo ji znovu nasadit pomocí tlačítka nasadit.

## <a name="configure-protected-settings"></a>Konfigurovat chráněná nastavení

Mnoho rozšíření virtuálních počítačů Azure zahrnuje konfiguraci chráněného nastavení, která šifruje citlivá data, jako jsou přihlašovací údaje a konfigurační řetězce. Chráněná nastavení nejsou exportována pomocí skriptu Automation. V případě potřeby musí být do exportované šablony znovu vložena chráněná nastavení.

### <a name="step-1---remove-template-parameter"></a>Krok 1 – odebrání parametru šablony

Při exportu skupiny prostředků je vytvořen jeden parametr šablony, který poskytuje hodnotu pro exportované chráněné nastavení. Tento parametr lze odebrat. Pokud chcete parametr odebrat, Prohlédněte si seznam parametrů a odstraňte parametr, který vypadá podobně jako tento příklad JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Krok 2 – získání vlastností chráněných nastavení

Vzhledem k tomu, že každé chráněné nastavení má sadu požadovaných vlastností, je třeba shromáždit seznam těchto vlastností. Každý parametr konfigurace chráněného nastavení najdete ve [schématu Azure Resource Manager na GitHubu](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Toto schéma obsahuje jenom sady parametrů pro rozšíření uvedená v tomto dokumentu v části Přehled. 

V rámci úložiště schématu vyhledejte v tomto příkladu požadovanou příponu `IaaSDiagnostics` . Po `protectedSettings` umístění objektu rozšíření si poznamenejte každý parametr. V příkladu `IaasDiagnostic` rozšíření jsou vyžadovány parametry `storageAccountName` , `storageAccountKey` a `storageAccountEndPoint` .

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

### <a name="step-3---re-create-the-protected-configuration"></a>Krok 3 – opětovné vytvoření chráněné konfigurace

V exportované šabloně vyhledejte `protectedSettings` a nahraďte exportovaný objekt Protected nastavení novým, který obsahuje požadované parametry rozšíření a hodnotu pro každý z nich.

V příkladu `IaasDiagnostic` rozšíření by nová konfigurace chráněného nastavení vypadala jako v následujícím příkladu:

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

Pokud používáte parametry šablony k poskytnutí hodnot vlastností, je nutné je vytvořit. Při vytváření parametrů šablony pro hodnoty chráněného nastavení nezapomeňte použít `SecureString` typ parametru, aby byly citlivé hodnoty zabezpečené. Další informace o použití parametrů najdete v tématu [vytváření Azure Resource Manager šablon](../../azure-resource-manager/templates/template-syntax.md).

V příkladu `IaasDiagnostic` rozšíření se vytvoří následující parametry v oddílu Parameters šablony Správce prostředků.

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

V tuto chvíli je možné šablonu nasadit pomocí jakékoli metody nasazení šablony.
