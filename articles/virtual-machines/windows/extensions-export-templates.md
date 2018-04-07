---
title: Export skupiny prostředků Azure, které obsahují rozšíření virtuálního počítače | Microsoft Docs
description: Exportujte šablony Resource Manager, které zahrnují rozšíření virtuálního počítače.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
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
ms.author: danis
ms.openlocfilehash: 2dc05fd21df3d408ed57badeec2b5c06e25bf50c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Export skupiny prostředků, které obsahují rozšíření virtuálního počítače

Skupiny prostředků Azure je možné exportovat do nové šablony Resource Manager, který lze potom znovu nasadit. Proces exportu interpretuje existující prostředky a vytvoří šablonu Resource Manager, která při nasazení výsledkem podobné skupiny prostředků. Při použití možnosti export skupiny prostředků pro skupinu prostředků obsahující rozšíření virtuálního počítače, je potřeba zvážit například rozšíření kompatibility několik položek a chráněné nastavení.

Tento dokument podrobnosti, jak funguje skupina prostředků procesu exportu týkající se rozšíření virtuálního počítače, včetně seznamu podporované rozšíření a informace o zpracování zabezpečená data.

## <a name="supported-virtual-machine-extensions"></a>Rozšíření podporované virtuálního počítače

Jsou k dispozici mnoho rozšíření virtuálního počítače. Ne všechny rozšíření je možné exportovat do šablony Resource Manageru pomocí funkce "Skriptu pro automatizaci". Pokud rozšíření virtuálního počítače není podporován, je nutné ručně umístí zpět do vyexportované šablony.

Následující rozšíření je možné exportovat s funkcí skript automatizace.

| Linka ||||
|---|---|---|---|
| Acronis Backup | Agent webu Datadog Windows | Opravy chyb pro Linux operačního systému | Linux snímek virtuálního počítače
| Acronis Backup Linux | Rozšíření docker | Puppet agenta |
| Informace o BG | Rozšíření DSC | Přehled Apm lokalita 24 x 7 |
| Linux Agent BMC CTM | Dynatrace Linux | Site 24x7 Linux Server |
| BMC CTM Agent Windows | Dynatrace Windows | Site 24x7 Windows Server |
| Chef klienta | HPE zabezpečení aplikace Defender | Trend malých DSA |
| Vlastní skript | Antimalwarové IaaS | Trend malých DSA Linux |
| Rozšíření vlastních skriptů | Diagnostika IaaS | Virtuální počítač přístup pro Linux |
| Vlastní skript pro Linux | Linux Chef klienta | Virtuální počítač přístup pro Linux |
| Datadog agenta systému Linux | Linux diagnostiky | Snímku virtuálního počítače |

## <a name="export-the-resource-group"></a>Export skupiny prostředků

Pokud chcete exportovat skupiny prostředků do opakovatelně použitelných šablony, proveďte následující kroky:

1. Přihlášení k webu Azure Portal
2. V nabídce centra klikněte na skupiny prostředků
3. Vyberte cílová skupina prostředků ze seznamu
4. V okně skupiny prostředků klikněte na příkaz skriptu pro automatizaci

![Export šablony](./media/extensions-export-templates/template-export.png)

Skript automatizaci Azure Resource Manager vytvoří šablony Resource Manageru, soubor parametrů a několik ukázkové skripty nasazení například prostředí PowerShell a rozhraní příkazového řádku Azure. V tomto okamžiku vyexportované šablony lze stáhnout pomocí tlačítko Stáhnout, přidány jako novou šablonu do šablony knihovny, nebo znovu nasadit pomocí tlačítka nasadit.

## <a name="configure-protected-settings"></a>Konfigurace nastavení chráněných

Mnoho rozšíření virtuálního počítače Azure zahrnují konfiguraci chráněných nastavení, který šifruje citlivá data, jako je například přihlašovací údaje a konfigurace řetězce. Chráněné nastavení se neexportují pomocí skriptu pro automatizaci. Pokud nastavení potřebné, chráněné je třeba znovu vložit do exportovaný podle šablony.

### <a name="step-1---remove-template-parameter"></a>Krok 1 – odebrání parametru šablony

Při exportu skupinu prostředků, parametr jednu šablonu se vytvoří pro poskytnutí hodnoty exportovaných nastaveních chráněný. Tento parametr lze odebrat. Odeberte parametr, projděte seznam parametrů a odstraňovat parametr, který vypadá podobně jako v tomto příkladu JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Krok 2 – Get chráněné vlastnosti nastavení

Protože každý chráněný nastavení obsahuje sadu požadované vlastnosti, seznam těchto vlastností, které je třeba shromáždit. Každý parametr konfiguraci chráněných nastavení najdete v [Azure Resource Manager schématu na Githubu](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Toto schéma zahrnuje jenom sady parametrů pro rozšíření uvedených v části Přehled tohoto dokumentu. 

Z v úložišti schémat vyhledejte požadovanou příponu, v tomto příkladu `IaaSDiagnostics`. Po rozšíření `protectedSettings` byl objekt nachází, poznamenejte si jednotlivé parametry. V příkladu `IaasDiagnostic` rozšíření, vyžadovat parametry jsou `storageAccountName`, `storageAccountKey`, a `storageAccountEndPoint`.

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

### <a name="step-3---re-create-the-protected-configuration"></a>Krok 3 – znovu vytvořit chráněné konfigurace

V šabloně exportovaný, vyhledejte `protectedSettings` a nahraďte objekt exportovaný chráněné nastavení novou, který obsahuje parametry požadované rozšíření a hodnota pro každé z nich.

V příkladu `IaasDiagnostic` rozšíření, nové chráněné nastavení konfigurace vypadat jako v následujícím příkladu:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Vypadá podobně jako v následujícím příkladu JSON posledním rozšíření prostředků:

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

Pokud používáte parametry šablony k poskytování hodnot vlastností, tyto musí vytvořit. Při vytváření parametry šablony chráněné nastavení hodnot, nezapomeňte použít `SecureString` parametr zadejte tak, aby citlivé hodnoty jsou zabezpečené. Další informace o použití parametrů najdete v tématu [šablon pro tvorbu Azure Resource Manageru](../../resource-group-authoring-templates.md).

V příkladu `IaasDiagnostic` rozšíření, by se vytvořily následující parametry v sekci parametrů šablony Resource Manageru.

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

V tomto okamžiku šabloně se dá nasadit pomocí libovolné metody nasazení šablony.
