---
title: Azure Monitor rozšíření virtuálního počítače pro Linux
description: Nasaďte agenta Log Analytics na virtuální počítač s Linuxem pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: akjosh
ms.openlocfilehash: a021c76eb973eba11e1dc1ee89f3d7f829a53f70
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073062"
---
# <a name="azure-monitor-virtual-machine-extension-for-linux"></a>Azure Monitor rozšíření virtuálního počítače pro Linux

## <a name="overview"></a>Přehled

Protokoly Azure Monitor poskytují možnosti monitorování, upozorňování a nápravy výstrah napříč cloudem a místními prostředky. Rozšíření virtuálního počítače agenta Log Analytics pro Linux je publikována a podporované společností Microsoft. Rozšíření nainstaluje agenta Log Analytics ve službě Azure virtual machines a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření Azure Monitor virtuálního počítače pro Linux.

>[!NOTE]
>Jako součást probíhající přechod z Microsoft Operations Management Suite (OMS) do Azure monitoru Agent OMS pro Windows nebo Linux bude odkazovat jako agenta Log Analytics pro Windows a Log Analytics agenta pro Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Podrobnosti o podporovaných distribucích systému Linux najdete v článku [přehled log Analyticsho agenta](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Verze agenta a rozšíření virtuálního počítače
Následující tabulka poskytuje mapování verze Azure Monitorho rozšíření virtuálního počítače a sady Log Analytics agenta pro každou verzi. Odkaz na poznámky k verzi sady agenta Log Analytics je součástí. Poznámky k verzi obsahují informace o opravy a nové funkce, které jsou k dispozici pro daného agenta verze.  

| Verze rozšíření virtuálního počítače s Azure Monitor Linux | Verze sady prostředků log Analytics agenta | 
|--------------------------------|--------------------------|
| 1.11.15 | [1.11.0 – 9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automaticky zřídí agenta Log Analytics a připojí ji k pracovnímu prostoru Log Analytics výchozí vytvořené ASC ve vašem předplatném Azure. Pokud používáte Azure Security Center, nespouštějte kroky v tomto dokumentu. Tím se přepíše nakonfigurovaný pracovní prostor a přestane fungovat připojení pomocí služby Azure Security Center.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření agenta Log Analytics pro Linux vyžaduje, aby cílový virtuální počítač je připojený k Internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schéma pro rozšíření agenta Log Analytics. Rozšíření vyžaduje ID pracovního prostoru a klíč pracovního prostoru z pracovního prostoru Log Analytics cíl; Tyto hodnoty mohou být [nalezen ve vašem pracovním prostoru Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) na webu Azure Portal. Protože klíč pracovního prostoru by měl být považován za citlivá data, by měl být uložený v chráněném nastavení konfigurace. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaný a dešifrovat jenom na cílovém virtuálním počítači. Všimněte si, že **ID pracovního prostoru** a **workspaceKey** jsou malá a velká písmena.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>Výše uvedené schéma se předpokládá, že budou umístěny na kořenové úrovni šablony. Pokud jste ji vložili uvnitř prostředku virtuálního počítače v šabloně `type` a `name` vlastnosti by měl změnit, jak je popsáno [níže](#template-deployment).
>

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (např.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (např.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Šablony jsou ideální při nasazení jednoho nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení, jako je připojování k Azure Monitor protokolů. Ukázková Správce prostředků Šablona obsahující rozšíření virtuálního počítače Log Analytics agenta najdete v [galerii rychlý Start Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

JSON konfigurace pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo objektu umístěn na kořenový server WSUS nebo nejvyšší úrovni šablony JSON Resource Manageru. Umístění konfigurace JSON má vliv na hodnotu názvu prostředku a typů. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/child-resource-name-type.md). 

V následujícím příkladu se předpokládá, že rozšíření virtuálního počítače je vnořená do prostředku virtuálního počítače. Při vnoření rozšíření prostředků, ve formátu JSON je umístěn v `"resources": []` objekt virtuálního počítače.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Při vkládání rozšíření JSON v kořenovém adresáři šablony, název prostředku obsahuje odkaz na nadřazený virtuální počítač a typ odráží vnořené konfigurace.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

Azure CLI slouží k nasazení do existujícího virtuálního počítače rozšíření virtuálního počítače agenta Log Analytics. Hodnotu *myworkspacekey svým* níže nahraďte klíčem pracovního prostoru a hodnotou *MYWORKSPACEID* s vaším ID pracovního prostoru. Tyto hodnoty najdete v pracovním prostoru Log Analytics v Azure Portal v části *Upřesnit nastavení*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí rozhraní příkazového řádku Azure. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Rozšíření provádění výstup je zaznamenán do následujícího souboru:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Kódy chyb a jejich význam

| Kód chyby | Význam | Je to možné akce |
| :---: | --- | --- |
| 9 | Povolit volá předčasně ukončen. | [Aktualizace agenta Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) nejnovější dostupnou verzi. |
| 10 | Virtuální počítač je už připojený k pracovnímu prostoru Log Analytics | Připojení virtuálního počítače do pracovního prostoru, určená ve schématu rozšíření, stopOnMultipleConnections nastavena na hodnotu false v nastavení veřejné nebo odeberte tuto vlastnost. Tento virtuální počítač se fakturují za každý pracovní prostor po připojení. |
| 11 | Neplatná konfigurace rozšíření k dispozici | Postupujte podle předchozích ukázkách nastavit všechny hodnoty vlastností nezbytné pro nasazení. |
| 17 | Selhání instalace balíčku analýzy protokolů | 
| 19 | Selhání instalace balíčku (OMI) | 
| 20 | Selhání instalace balíčku SCX |
| 51 | Toto rozšíření nepodporuje operační systém Virtuálního počítače | |
| 55 | Nelze se připojit ke službě Azure Monitor nebo chybí požadované balíčky nebo je správce balíčků bázi dpkg uzamčen.| Zkontrolujte, že systém má přístup k Internetu nebo že byl poskytnut správný proxy server HTTP. Kromě toho zkontrolujte správnost ID pracovního prostoru a ověřte, zda jsou nainstalované nástroje curl a cíl. |

Další informace o odstraňování potíží najdete na [Průvodce odstraňováním potíží se Log Analytics agenta pro Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
