---
title: Azure Monitor rozšíření virtuálního počítače pro Windows | Microsoft Docs
description: Nasaďte agenta Log Analytics na virtuální počítač s Windows pomocí rozšíření virtuálního počítače.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2019
ms.author: roiyz
ms.openlocfilehash: 700d8c6ea1527598591aa4300a977f80085e04b0
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990369"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>Azure Monitor rozšíření virtuálního počítače pro Windows

Protokoly Azure Monitor poskytují možnosti monitorování v cloudových i místních prostředcích. Rozšíření virtuálního počítače Log Analytics agenta pro Windows je publikované a podporované Microsoftem. Rozšíření nainstaluje agenta Log Analytics ve službě Azure virtual machines a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření Azure Monitor virtuálních počítačů pro Windows.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Podrobnosti o podporovaných operačních systémech Windows najdete v článku [Přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Verze agenta a rozšíření virtuálního počítače
Následující tabulka poskytuje mapování verze rozšíření virtuálního počítače s Windows Azure Monitor a sady Log Analytics agenta pro každou verzi. 

| Log Analytics verze sady Windows Agent | Azure Monitor verze rozšíření virtuálního počítače s Windows | Datum vydání | Poznámky k verzi |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18011 | 1.0.18011 | Červenec 2019 | <ul><li> Drobné opravy chyb a ustálená vylepšení </li><li> Zvýšení MaxExpressionDepth na 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Červeně 2019 | <ul><li> Drobné opravy chyb a ustálená vylepšení </li><li> Přidání možnosti zakázat výchozí pověření při vytváření připojení k proxy (podpora pro WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Březen 2019 | <ul><li>Menší stabilizace oprav </li></ul> |
| 10.19.10006 | neuvedeno | DEC 2018 | <ul><li> Menší stabilizace oprav </li></ul> | 
| 8.0.11136 | neuvedeno | Září 2018 |  <ul><li> Přidání podpory pro zjištění změny ID prostředku při přesunu virtuálního počítače </li><li> Přidání podpory pro ID prostředku pro vytváření sestav při použití jiné než rozšíření Instalace </li></ul>| 
| 8.0.11103 | neuvedeno |  Duben 2018 | |
| 8.0.11081 | 1.0.11081 | Listopadu 2017 | | 
| 8.0.11072 | 1.0.11072 | Září 2017 | |
| 8.0.11049 | 1.0.11049 | Únor 2017 | |

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automaticky zřídí agenta Log Analytics a připojí ho k výchozímu Log Analytics pracovnímu prostoru předplatného Azure. Pokud používáte Azure Security Center, nespouštějte kroky v tomto dokumentu. Tím se přepíše nakonfigurovaný pracovní prostor a přerušení připojení k Azure Security Center.

### <a name="internet-connectivity"></a>Připojení k internetu
Rozšíření agenta Log Analytics pro systém Windows vyžaduje, aby byl cílový virtuální počítač připojen k Internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření agenta Log Analytics. Přípona vyžaduje ID pracovního prostoru a klíč pracovního prostoru z cílového pracovního prostoru Log Analytics. Ty najdete v nastavení pracovního prostoru v Azure Portal. Protože klíč pracovního prostoru by měl být považován za citlivá data, by měl být uložený v chráněném nastavení konfigurace. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaný a dešifrovat jenom na cílovém virtuálním počítači. Všimněte si, že **ID pracovního prostoru** a **workspaceKey** jsou malá a velká písmena.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (e.g)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (např.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\*ID pracovního prostoru se nazývá consumerId v rozhraní Log Analytics API.

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Schéma JSON popsané v předchozí části lze použít v šabloně Azure Resource Manager ke spuštění rozšíření agenta Log Analytics během nasazování šablony Azure Resource Manager. Ukázkovou šablonu, která obsahuje rozšíření virtuálního počítače agenta Log Analytics, najdete v [galerii Azure Rychlé zprovoznění](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Šablona nepodporuje zadání více než jednoho ID pracovního prostoru a klíč pracovního prostoru, pokud chcete nakonfigurovat agenta tak, aby nahlásil do více pracovních prostorů. Chcete-li nakonfigurovat agenta tak, aby nahlásil do více pracovních prostorů, přečtěte si téma [Přidání nebo odebrání pracovního prostoru](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

JSON pro rozšíření virtuálního počítače se dá vnořit do prostředku virtuálního počítače nebo umístit na kořenovou nebo nejvyšší úroveň šablony Správce prostředků JSON. Umístění formátu JSON má vliv na hodnotu názvu a typu prostředku. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/child-resource-name-type.md). 

Následující příklad předpokládá, že rozšíření Azure Monitor je vnořeno do prostředku virtuálního počítače. Při vnoření rozšíření prostředků, ve formátu JSON je umístěn v `"resources": []` objekt virtuálního počítače.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Při vkládání rozšíření JSON v kořenovém adresáři šablony, název prostředku obsahuje odkaz na nadřazený virtuální počítač a typ odráží vnořené konfigurace. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Nasazení prostředí PowerShell

`Set-AzVMExtension` Příkaz lze použít k nasazení rozšíření Log Analytics agenta virtuálního počítače do existujícího virtuálního počítače. Před spuštěním příkazu musí být veřejné a privátní konfigurace uložené v zatřiďovací tabulce PowerShellu. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí modulu Azure PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí modulu Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Výstup spuštění rozšíření se protokoluje do souborů, které se nacházejí v následujícím adresáři:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
