---
title: Rozšíření virtuálního počítače Azure Monitor pro Windows | Dokumentace Microsoftu
description: Nasaďte agenta Log Analytics na virtuálním počítači Windows pomocí rozšíření virtuálního počítače.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/29/2019
ms.author: roiyz
ms.openlocfilehash: fb931d5ce72b21cb17abbcd11095dbc8d611f0c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064425"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>Rozšíření virtuálního počítače Azure Monitor pro Windows

Protokoly Azure monitoru poskytuje funkce sledování v rámci cloudových a místních prostředků. Rozšíření virtuálního počítače agenta Log Analytics pro Windows je publikována a podporované společností Microsoft. Rozšíření nainstaluje agenta Log Analytics ve službě Azure virtual machines a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače Azure Monitor pro Windows.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření agenta Log Analytics pro Windows podporuje následující verze operačního systému Windows:

- Windows Server 2019
- Windows Server 2008 R2, 2012, 2012 R2, 2016, verze 1709 a 1803

### <a name="agent-and-vm-extension-version"></a>Verze agenta a rozšíření virtuálního počítače
Následující tabulka obsahuje mapování verzi rozšíření virtuálního počítače Windows Azure monitoru a sady agenta Log Analytics pro každou vydanou verzí. 

| Verze sady prostředků agenta log Analytics Windows | Verze rozšíření Azure VM Windows monitorování | Datum vydání | Poznámky k verzi |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18001 | 1.0.18001 | 2019 dne | <ul><li> Stabilizace vylepšení a opravy menších chyb </li><li> Přidání možnosti zakázat výchozí přihlašovací údaje při navazování připojení proxy server (podpora pro WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | 2019\. března | <ul><li>Stabilizace menší opravy </li></ul> |
| 10.19.10006 | neuvedeno | DEC 2018 | <ul><li> Stabilizace menší opravy </li></ul> | 
| 8.0.11136 | neuvedeno | Září 2018 |  <ul><li> Přidání podpory pro zjišťování Změna ID prostředku. přesun virtuálních počítačů </li><li> Přidání podpory pro vytváření sestav nainstalovat ID při použití rozšíření prostředků </li></ul>| 
| 8.0.11103 | neuvedeno |  Duben 2018 | |
| 8.0.11081 | 1.0.11081 | Listopad 2017 | | 
| 8.0.11072 | 1.0.11072 | Září 2017 | |
| 8.0.11049 | 1.0.11049 | Únor 2017 | |

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automaticky zřídí agenta Log Analytics a připojí ji k pracovnímu prostoru Log Analytics výchozího předplatného Azure. Pokud používáte Azure Security Center, nespouštějte kroky v tomto dokumentu. Tím přepíšete nakonfigurovaný pracovní prostor a zrušit propojení s Azure Security Center.

### <a name="internet-connectivity"></a>Připojení k internetu
Rozšíření agenta Log Analytics pro Windows vyžaduje, aby cílový virtuální počítač je připojený k Internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schéma pro rozšíření agenta Log Analytics. Rozšíření vyžaduje ID pracovního prostoru a klíč pracovního prostoru z cílového pracovního prostoru Log Analytics. Ty najdete v nastavení pracovního prostoru na webu Azure Portal. Protože klíč pracovního prostoru by měl být považován za citlivá data, by měl být uložený v chráněném nastavení konfigurace. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaný a dešifrovat jenom na cílovém virtuálním počítači. Všimněte si, že **ID pracovního prostoru** a **workspaceKey** jsou malá a velká písmena.

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

\* ID pracovního prostoru je volána consumerId v rozhraní API služby Log Analytics.

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Schéma JSON, které jsou podrobně popsané v předchozí části lze použít v šabloně Azure Resource Manageru pro spuštění rozšíření agenta Log Analytics při nasazení šablony Azure Resource Manageru. Ukázková šablona, která zahrnuje rozšíření virtuálního počítače agenta Log Analytics můžete najít na [Galerie Azure rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Šablony nepodporuje zadání více než jeden pracovní prostor s ID a klíč pracovního prostoru, pokud chcete provést konfiguraci agenta na generování sestav k několika pracovním prostorům. Konfigurace agenta na generování sestav k několika pracovním prostorům, naleznete v tématu [přidání nebo odebrání pracovního prostoru](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

JSON pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo objektu umístěn na kořenový server WSUS nebo nejvyšší úrovni šablony JSON Resource Manageru. Umístění ve formátu JSON má vliv na hodnotu názvu prostředku a typů. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

V následujícím příkladu se předpokládá, že rozšíření Azure Monitor je vnořená do prostředku virtuálního počítače. Při vnoření rozšíření prostředků, ve formátu JSON je umístěn v `"resources": []` objekt virtuálního počítače.


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

## <a name="powershell-deployment"></a>Nasazení pomocí Powershellu

`Set-AzVMExtension` Příkaz lze použít k nasazení do existujícího virtuálního počítače rozšíření virtuálního počítače agenta Log Analytics. Před spuštěním příkazu, veřejné a privátní konfigurace muset být uložena v tabulce hash prostředí PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
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

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí modulu Azure PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí modulu Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Rozšíření provádění výstup je zaznamenán soubory nalezené v následujícím adresáři:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
