---
title: Rozšíření Log Analytics pro virtuální počítače pro Windows
description: Nasaďte agenta Log Analytics na virtuálním počítači s Windows pomocí rozšíření virtuálního počítače.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/30/2020
ms.author: akjosh
ms.openlocfilehash: 85977819d30ddc8745eb9231242eb1990222676c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530984"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Rozšíření Log Analytics pro virtuální počítače pro Windows

Protokoly monitorování Azure poskytují možnosti monitorování napříč cloudovými a místními prostředky. Rozšíření virtuálního počítače agenta Log Analytics pro Windows je publikováno a podporováno společností Microsoft. Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače Log Analytics pro Windows.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Podrobnosti o podporovaných operačních systémech Windows naleznete v článku [přehled agenta Analýzy protokolů.](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Verze rozšíření agenta a virtuálního virtuálního aplikace
Následující tabulka obsahuje mapování verze rozšíření virtuálního aplikace Windows Log Analytics a balíčku agenta Log Analytics pro každou verzi. 

| Verze sady windows agent protokolu Analytics windows | Verze rozšíření virtuálního aplikace pro Windows analýzy protokolu | Datum vydání | Poznámky k verzi |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18029 | 1.0.18029 | Březen 2020   | <ul><li>Přidá podporu podepisování kódu SHA-2.</li><li>Zlepšuje instalaci a správu rozšíření virtuálního zařízení.</li><li>Řeší chybu v integraci Azure Arc pro servery</li><li>Přidá integrovaný nástroj pro řešení potíží pro zákaznickou podporu.</li><li>Přidává podporu pro další oblasti Azure Government.</li> |
| 10.20.18018 | 1.0.18018 | Říjen 2019 | <ul><li> Drobné opravy chyb a vylepšení stabilizace </li></ul> |
| 10.20.18011 | 1.0.18011 | Červenec 2019 | <ul><li> Drobné opravy chyb a vylepšení stabilizace </li><li> Zvýšena maximální hloubka výrazu na 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Červen 2019 | <ul><li> Drobné opravy chyb a vylepšení stabilizace </li><li> Přidána možnost zakázat výchozí pověření při připojení k proxy serveru (podpora WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Březen 2019 | <ul><li>Drobné stabilizační opravy </li></ul> |
| 10.19.10006 | neuvedeno | Prosinec 2018 | <ul><li> Drobné stabilizační opravy </li></ul> | 
| 8.0.11136 | neuvedeno | Září 2018 |  <ul><li> Přidána podpora pro zjišťování změny ID prostředků při přesunu virtuálního počítače </li><li> Přidána podpora pro hlášení ID prostředku při použití instalace bez rozšíření </li></ul>| 
| 8.0.11103 | neuvedeno |  Duben 2018 | |
| 8.0.11081 | 1.0.11081 | Listopad 2017 | | 
| 8.0.11072 | 1.0.11072 | Září 2017 | |
| 8.0.11049 | 1.0.11049 | Únor 2017 | |


### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automaticky zřídí agenta Analýzy protokolů a propojí ho s výchozím pracovním prostorem Log Analytics předplatného Azure. Pokud používáte Azure Security Center, neprocházet kroky v tomto dokumentu. Tím přepíšena nakonfigurovaný pracovní prostor a přerušit připojení s Azure Security Center.

### <a name="internet-connectivity"></a>Připojení k internetu
Rozšíření agenta Log Analytics pro Systém Windows vyžaduje, aby byl cílový virtuální počítač připojen k internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření agenta Analýzy protokolů. Rozšíření vyžaduje ID pracovního prostoru a klíč pracovního prostoru z cílového pracovního prostoru Analýzy protokolů. Ty najdete v nastavení pracovního prostoru na webu Azure Portal. Vzhledem k tomu, že klíč pracovního prostoru by měl být považován za citlivá data, měl by být uložen v konfiguraci chráněného nastavení. Data nastavení chráněné rozšířením virtuálního počítače Azure jsou šifrovaná a dešifrovaná jenom na cílovém virtuálním počítači. Všimněte si, že **workspaceId** a **workspaceKey** rozlišují malá a velká písmena.

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

| Name (Název) | Hodnota / Příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| vydavatel | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (např.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (např. | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\*Id pracovního prostoru se nazývá consumerId v rozhraní API analýzy protokolů.

> [!NOTE]
> Další vlastnosti najdete v tématu [Azure Connect Windows Computers to Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Schéma JSON popsané v předchozí části lze použít v šabloně Azure Resource Manager ke spuštění rozšíření agenta Log Analytics během nasazení šablony Azure Resource Manager. Ukázková šablona, která obsahuje rozšíření virtuálního počítače agenta Analýzy protokolů, najdete v [galerii Azure Quickstart Gallery](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Šablona nepodporuje určení více než jednoho ID pracovního prostoru a klíče pracovního prostoru, pokud chcete nakonfigurovat agenta tak, aby se hlásil do více pracovních prostorů. Pokud chcete agenta nakonfigurovat tak, aby se hlásil do více pracovních prostorů, přečtěte si informace [o přidání nebo odebrání pracovního prostoru](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

JSON pro rozšíření virtuálního počítače může být vnořené uvnitř prostředku virtuálního počítače nebo umístěny na kořenové nebo nejvyšší úrovni šablony Správce prostředků JSON. Umístění JSON ovlivňuje hodnotu názvu a typu prostředku. Další informace naleznete v tématu [Nastavení názvu a typu podřízených prostředků](../../azure-resource-manager/templates/child-resource-name-type.md). 

Následující příklad předpokládá, že rozšíření Log Analytics je vnořené uvnitř prostředku virtuálního počítače. Při vnoření prostředku rozšíření JSON je `"resources": []` umístěn v objektu virtuálního počítače.


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

Při umístění rozšíření JSON v kořenovém adresáři šablony obsahuje název prostředku odkaz na nadřazený virtuální počítač a typ odráží vnořenou konfiguraci. 

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

## <a name="powershell-deployment"></a>Nasazení PowerShellu

Příkaz `Set-AzVMExtension` lze použít k nasazení rozšíření virtuálního počítače agenta Log Analytics do existujícího virtuálního počítače. Před spuštěním příkazu je třeba uložit veřejné a soukromé konfigurace v tabulce hash prostředí PowerShell. 

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

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z webu Azure Portal a pomocí modulu Azure PowerShell. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí modulu Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Výstup spuštění rozšíření je zaznamenán do souborů nalezených v následujícím adresáři:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
