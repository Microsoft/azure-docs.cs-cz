---
title: Log Analytics rozšíření virtuálního počítače pro Linux
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
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: 9ddac229fc38a91a8b97b24dc2807080b2295758
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468775"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics rozšíření virtuálního počítače pro Linux

## <a name="overview"></a>Přehled

Protokoly Azure Monitor poskytují možnosti monitorování, upozorňování a nápravy výstrah napříč cloudem a místními prostředky. Rozšíření virtuálního počítače Log Analytics pro Linux se zveřejňuje a podporuje Microsoft. Rozšíření nainstaluje agenta Log Analytics ve službě Azure virtual machines a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro virtuální počítač rozšíření Log Analytics pro Linux.

>[!NOTE]
>Jako součást probíhající přechod z Microsoft Operations Management Suite (OMS) do Azure monitoru Agent OMS pro Windows nebo Linux bude odkazovat jako agenta Log Analytics pro Windows a Log Analytics agenta pro Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Předpoklady

### <a name="operating-system"></a>Operační systém

Podrobnosti o podporovaných distribucích systému Linux najdete v článku [přehled log Analyticsho agenta](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Verze agenta a rozšíření virtuálního počítače
Následující tabulka obsahuje mapování verzi rozšíření virtuálního počítače Log Analytics a sady agenta Log Analytics pro každou vydanou verzí. Odkaz na poznámky k verzi sady agenta Log Analytics je součástí. Poznámky k verzi obsahují informace o opravy a nové funkce, které jsou k dispozici pro daného agenta verze.  

| Verze rozšíření log Analytics virtuálního počítače s Linuxem | Verze sady prostředků log Analytics agenta | 
|--------------------------------|--------------------------|
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0 – 9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1 – 256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0 – 256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0 – 42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3 – 174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2 – 125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1 – 123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1 – 45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automaticky zřídí agenta Log Analytics a připojí ji k pracovnímu prostoru Log Analytics výchozí vytvořené ASC ve vašem předplatném Azure. Pokud používáte Azure Security Center, nespouštějte kroky v tomto dokumentu. Tím se přepíše nakonfigurovaný pracovní prostor a přestane fungovat připojení pomocí služby Azure Security Center.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření agenta Log Analytics pro Linux vyžaduje, aby cílový virtuální počítač je připojený k Internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schéma pro rozšíření agenta Log Analytics. Přípona vyžaduje ID pracovního prostoru a klíč pracovního prostoru z cílového pracovního prostoru Log Analytics; Tyto hodnoty najdete [v pracovním prostoru Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) v Azure Portal. Protože klíč pracovního prostoru by měl být považován za citlivá data, by měl být uložený v chráněném nastavení konfigurace. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaný a dešifrovat jenom na cílovém virtuálním počítači. Všimněte si, že **ID pracovního prostoru** a **workspaceKey** rozlišují velká a malá písmena.

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
>Výše uvedené schéma se předpokládá, že budou umístěny na kořenové úrovni šablony. Pokud jste ho umístili do prostředku virtuálního počítače v šabloně, je třeba změnit vlastnosti `type` a `name`, jak je popsáno [dále](#template-deployment).

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

JSON konfigurace pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo objektu umístěn na kořenový server WSUS nebo nejvyšší úrovni šablony JSON Resource Manageru. Umístění konfigurace JSON má vliv na hodnotu názvu prostředku a typů. Další informace najdete v tématu [Nastavení názvu a typu pro podřízené prostředky](../../azure-resource-manager/templates/child-resource-name-type.md). 

V následujícím příkladu se předpokládá, že rozšíření virtuálního počítače je vnořená do prostředku virtuálního počítače. Při vnořování prostředku rozšíření se JSON umístí do objektu `"resources": []` virtuálního počítače.

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
| 9 | Povolit volá předčasně ukončen. | [Aktualizujte agenta Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) na nejnovější dostupnou verzi. |
| 10 | Virtuální počítač je už připojený k pracovnímu prostoru Log Analytics | Připojení virtuálního počítače do pracovního prostoru, určená ve schématu rozšíření, stopOnMultipleConnections nastavena na hodnotu false v nastavení veřejné nebo odeberte tuto vlastnost. Tento virtuální počítač se fakturují za každý pracovní prostor po připojení. |
| 11 | Neplatná konfigurace rozšíření k dispozici | Postupujte podle předchozích ukázkách nastavit všechny hodnoty vlastností nezbytné pro nasazení. |
| 17 | Selhání instalace balíčku analýzy protokolů | 
| 19 | Selhání instalace balíčku (OMI) | 
| 20 | Selhání instalace balíčku SCX |
| 51 | Toto rozšíření nepodporuje operační systém Virtuálního počítače | |
| 55 | Nelze se připojit ke službě Azure Monitor nebo chybí požadované balíčky nebo je správce balíčků bázi dpkg uzamčen.| Zkontrolujte, že systém má přístup k Internetu nebo že byl poskytnut správný proxy server HTTP. Kromě toho zkontrolujte správnost ID pracovního prostoru a ověřte, zda jsou nainstalované nástroje curl a cíl. |

Další informace o řešení potíží najdete v [Průvodci odstraňováním potíží s Log Analytics-agenty pro Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
