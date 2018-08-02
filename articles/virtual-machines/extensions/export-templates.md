---
title: Export skupiny prostředků Azure, které obsahují rozšíření virtuálních počítačů | Dokumentace Microsoftu
description: Exportujte šablon Resource Manageru, které zahrnují rozšíření virtuálních počítačů.
services: virtual-machines-windows
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: roiyz
ms.openlocfilehash: 76305dd736adede954460e034fcd726ebfefdc91
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412089"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Export skupiny prostředků, které obsahují rozšíření virtuálních počítačů

Skupiny prostředků Azure je možné exportovat do nové šablony Resource Manageru, který lze potom znovu nasadit. Proces exportu interpretuje existujících prostředků a vytvoří šablonu Resource Manageru, které při nasazení má za následek podobné skupinu prostředků. Při použití možnosti export skupiny prostředků pro skupinu prostředků obsahující rozšíření virtuálních počítačů, několik položek musí brát jako je například rozšíření kompatibility a nastavení chráněné.

Tento dokument podrobně popisuje, jak funguje proces export skupiny prostředků týkající se rozšíření virtuálního počítače, včetně seznamu nepodporuje rozšíření a podrobnosti o zpracování zabezpečená data.

## <a name="supported-virtual-machine-extensions"></a>Rozšíření podporovaných virtuálních počítačů

Jsou k dispozici mnoho rozšíření virtuálního počítače. Ne všechna rozšíření, je možné exportovat do šablony Resource Manageru pomocí funkce "Automatizační skript". Pokud rozšíření virtuálního počítače není podporovaná, je potřeba umístit ručně zpět do vyexportované šablony.

Následující rozšíření je možné exportovat skript funkce automatizace.

| Linka ||||
|---|---|---|---|
| Acronis zálohování | Agent Windows služby Datadog | Opravy operačního systému pro Linux | Linux snímku virtuálního počítače
| Zálohování Acronis Linux | Rozšíření docker | Puppet agenta |
| Informace o BG | Rozšíření DSC | 24 x 7 webu Apm Insight |
| Linux agenta BMC CTM | Dynatrace Linux | Server lokality 24 x 7 Linux |
| Agent Windows BMC CTM | Dynatrace Windows | Site 24x7 Windows Server |
| Chef klienta | HPE zabezpečení aplikace Defenderu | Trend Micro DSA |
| Vlastní skript | IaaS Antimalware | Trend Micro DSA Linux |
| Rozšíření vlastních skriptů | Diagnostika IaaS | Přístup k virtuálním počítačům pro Linux |
| Vlastní skript pro Linux | Klient Linux Chef | Přístup k virtuálním počítačům pro Linux |
| Služby Datadog linuxového agenta | Linux Diagnostic | VM Snapshot |

## <a name="export-the-resource-group"></a>Export skupiny prostředků

Pokud chcete exportovat skupiny prostředků do opakovaně použitelné šablony, proveďte následující kroky:

1. Přihlášení k webu Azure Portal
2. V nabídce centra klikněte na skupiny prostředků
3. Vyberte cílovou skupinu prostředků ze seznamu
4. V okně skupiny prostředků klikněte na tlačítko automatizační skript

![Export šablony](./media/export-templates/template-export.png)

Skript automatizace Azure Resource Manageru vytvoří šablonu Resource Manageru, soubor parametrů a několika ukázky skriptů nasazení například prostředí PowerShell a rozhraní příkazového řádku Azure. V tomto okamžiku vyexportované šablony lze stáhnout pomocí tlačítka stáhnout, přidat jako novou šablonu do knihovny šablon nebo znovu nasadit pomocí tlačítka nasadit.

## <a name="configure-protected-settings"></a>Chráněné nastavení

Mnoho rozšíření virtuálního počítače Azure zahrnují chráněná nastavení pro konfiguraci, která šifruje citlivá data, jako jsou přihlašovací údaje a konfiguračních řetězců. Chráněné nastavení neexportují pomocí skriptu pro automatizaci. Pokud nastavení nezbytné, chráněné muset být vloženy do exportovaný bez vizuálního vzhledu.

### <a name="step-1---remove-template-parameter"></a>Krok 1 – odebrat parametr šablony

Při exportu skupinu prostředků, se vytvoří jednou šablonou parametr zadejte hodnotu na chráněná nastavení pro exportované. Tento parametr je možné odebrat. Odeberte parametr, prohlédněte si seznam parametrů a odstraňovat parametr, který vypadá podobně jako v tomto příkladu JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Krok 2: získání chráněné vlastnosti nastavení

Protože každý chráněný nastavení obsahuje sadu požadovaných vlastností, seznam těchto vlastností, které je třeba shromáždit. Každý parametr chráněná nastavení pro konfiguraci najdete v [schématu Azure Resource Manageru na Githubu](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Toto schéma obsahuje jenom sady parametrů pro rozšířeními uvedenými v oddílu Přehled tohoto dokumentu. 

Z v rámci tohoto schématu úložiště vyhledávání pro požadované rozšíření, v tomto příkladu `IaaSDiagnostics`. Po rozšíření `protectedSettings` byl objekt nachází, poznamenejte si hodnoty jednotlivých parametrů. V příkladu `IaasDiagnostic` rozšíření, vyžadují se parametry `storageAccountName`, `storageAccountKey`, a `storageAccountEndPoint`.

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

### <a name="step-3---re-create-the-protected-configuration"></a>Krok 3: znovu vytvořte chráněnou konfiguraci

Hledat v exportované šabloně `protectedSettings` a nahraďte objekt exportované chráněné nastavení nový, který obsahuje parametry požadované rozšíření a hodnotu pro každý z nich.

V příkladu `IaasDiagnostic` novou konfiguraci chráněných nastavení rozšíření, by vypadalo jako v následujícím příkladu:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Konečné rozšíření prostředků vypadá podobně jako v následujícím příkladu JSON:

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

Pokud pomocí parametrů šablona zadávat hodnoty vlastností, tyto potřeba vytvořit. Při vytváření parametrů šablony pro nastavení hodnot chráněné, je nutné použít `SecureString` parametr typu tak, aby citlivé hodnoty jsou zabezpečené. Další informace o používání parametrů naleznete v tématu [šablon pro vytváření Azure Resource Manageru](../../resource-group-authoring-templates.md).

V příkladu `IaasDiagnostic` rozšíření, tyto parametry by se vytvořily v sekci parametrů šablony Resource Manageru.

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

V tomto okamžiku šablony můžete nasadit pomocí libovolné metody nasazení šablony.
