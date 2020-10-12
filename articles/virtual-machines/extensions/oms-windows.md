---
title: Rozšíření Log Analytics pro virtuální počítače pro Windows
description: Nasaďte agenta Log Analytics na virtuální počítač s Windows pomocí rozšíření virtuálního počítače.
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
ms.date: 06/26/2020
ms.author: akjosh
ms.openlocfilehash: 4730f05adc2625ac576f5963f68d8e3ca8ede355
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331501"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Rozšíření Log Analytics pro virtuální počítače pro Windows

Protokoly Azure Monitor poskytují možnosti monitorování v cloudových i místních prostředcích. Rozšíření virtuálního počítače Log Analytics agenta pro Windows je publikované a podporované Microsoftem. Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření Log Analytics virtuálních počítačů pro Windows.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Podrobnosti o podporovaných operačních systémech Windows najdete v článku [přehled Azure Monitorch agentů](../../azure-monitor/platform/agents-overview.md#supported-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Verze agenta a rozšíření virtuálního počítače
Následující tabulka poskytuje mapování verze rozšíření virtuálního počítače s Windows Log Analytics a sady Log Analytics agenta pro každou verzi. 

| Log Analytics verze sady Windows Agent | Log Analytics verze rozšíření virtuálního počítače s Windows | Datum vydání | Zpráva k vydání verze |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18040 | 1.0.18040.2 | Srpen 2020   | <ul><li>Vyřeší problém v Arc Azure</li></ul> |
| 10.20.18038 | 1.0.18038 | Duben 2020   | <ul><li>Umožňuje připojení prostřednictvím privátního propojení pomocí Azure Monitor oborů privátních odkazů.</li><li>Přidá omezování příjmu, aby nedocházelo k náhlému a náhodnému výpadku v ingestování do pracovního prostoru.</li><li>Přidá podporu pro další Azure Government cloudy a oblasti.</li><li>Vyřeší chybu, ve které došlo k chybě HealthService.exe</li></ul> |
| 10.20.18029 | 1.0.18029 | Březen 2020   | <ul><li>Přidání podpory podepisování kódu SHA-2</li><li>Vylepšuje instalaci a správu rozšíření virtuálních počítačů.</li><li>Vyřeší chybu v integraci služby Azure ARC pro servery</li><li>Přidá integrovaný nástroj pro řešení potíží pro zákaznickou podporu.</li><li>Přidá podporu pro další Azure Government oblasti.</li> |
| 10.20.18018 | 1.0.18018 | Říjen 2019 | <ul><li> Drobné opravy chyb a ustálená vylepšení </li></ul> |
| 10.20.18011 | 1.0.18011 | Červenec 2019 | <ul><li> Drobné opravy chyb a ustálená vylepšení </li><li> Zvýšení MaxExpressionDepth na 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Červen 2019 | <ul><li> Drobné opravy chyb a ustálená vylepšení </li><li> Přidání možnosti zakázat výchozí pověření při vytváření připojení k proxy (podpora pro WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Březen 2019 | <ul><li>Menší stabilizace oprav </li></ul> |
| 10.19.10006 | neuvedeno | DEC 2018 | <ul><li> Menší stabilizace oprav </li></ul> | 
| 8.0.11136 | neuvedeno | Září 2018 |  <ul><li> Přidání podpory pro zjištění změny ID prostředku při přesunu virtuálního počítače </li><li> Přidání podpory pro ID prostředku pro vytváření sestav při použití jiné než rozšíření Instalace </li></ul>| 
| 8.0.11103 | neuvedeno |  Duben 2018 | |
| 8.0.11081 | 1.0.11081 | Listopadu 2017 | | 
| 8.0.11072 | 1.0.11072 | Září 2017 | |
| 8.0.11049 | 1.0.11049 | Únor 2017 | |


### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automaticky zřídí agenta Log Analytics a připojí ho k výchozímu Log Analytics pracovnímu prostoru předplatného Azure. Pokud používáte Azure Security Center, neprovádějte kroky v tomto dokumentu. Tím se přepíše nakonfigurovaný pracovní prostor a přerušení připojení k Azure Security Center.

### <a name="internet-connectivity"></a>Připojení k internetu
Rozšíření agenta Log Analytics pro systém Windows vyžaduje, aby byl cílový virtuální počítač připojen k Internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření agenta Log Analytics. Přípona vyžaduje ID pracovního prostoru a klíč pracovního prostoru z cílového pracovního prostoru Log Analytics. Ty najdete v nastavení pracovního prostoru v Azure Portal. Vzhledem k tomu, že klíč pracovního prostoru by měl být považován za citlivá data, měl by být uložen v konfiguraci chráněného nastavení. Data nastavení chráněná rozšířením virtuálního počítače Azure jsou šifrovaná a v cílovém virtuálním počítači se dešifrují jenom. Všimněte si, že **ID pracovního prostoru** a **workspaceKey** rozlišují velká a malá písmena.

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

| Name | Hodnota/příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| vydavatel | Microsoft. EnterpriseCloud. Monitoring |
| typ | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1,0 |
| ID pracovního prostoru (např.) * | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (např.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |

\* ID pracovního prostoru se nazývá consumerId v rozhraní Log Analytics API.

> [!NOTE]
> Další vlastnosti najdete v tématu [připojení počítačů s Windows k Azure a Azure monitor](../../azure-monitor/platform/agent-windows.md).

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Schéma JSON popsané v předchozí části lze použít v šabloně Azure Resource Manager ke spuštění rozšíření agenta Log Analytics během nasazování šablony Azure Resource Manager. Ukázková šablona, která obsahuje rozšíření virtuálního počítače agenta Log Analytics, se dá najít v [galerii Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Šablona nepodporuje zadání více než jednoho ID pracovního prostoru a klíč pracovního prostoru, pokud chcete nakonfigurovat agenta tak, aby nahlásil do více pracovních prostorů. Chcete-li nakonfigurovat agenta tak, aby nahlásil do více pracovních prostorů, přečtěte si téma [Přidání nebo odebrání pracovního prostoru](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

JSON pro rozšíření virtuálního počítače se dá vnořit do prostředku virtuálního počítače nebo umístit na kořenovou nebo nejvyšší úroveň šablony Správce prostředků JSON. Umístění formátu JSON má vliv na hodnotu názvu a typu prostředku. Další informace najdete v tématu [Nastavení názvu a typu pro podřízené prostředky](../../azure-resource-manager/templates/child-resource-name-type.md). 

Následující příklad předpokládá, že rozšíření Log Analytics je vnořeno do prostředku virtuálního počítače. Při vnořování prostředku rozšíření je kód JSON umístěný v `"resources": []` objektu virtuálního počítače.


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

Při umístění JSON rozšíření v kořenovém adresáři šablony obsahuje název prostředku odkaz na nadřazený virtuální počítač a typ odráží vnořenou konfiguraci. 

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

`Set-AzVMExtension`Příkaz lze použít k nasazení rozšíření Log Analytics agenta virtuálního počítače do existujícího virtuálního počítače. Před spuštěním příkazu musí být veřejné a privátní konfigurace uložené v zatřiďovací tabulce PowerShellu. 

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

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
