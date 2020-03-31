---
title: Rozšíření Log Analytics pro virtuální počítače pro Linux
description: Nasaďte agenta Log Analytics na virtuálním počítači s Linuxem pomocí rozšíření virtuálního počítače.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250553"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Rozšíření Log Analytics pro virtuální počítače pro Linux

## <a name="overview"></a>Přehled

Protokoly monitorování Azure poskytuje možnosti monitorování, výstrah a nápravu výstrah napříč cloudovými a místními prostředky. Rozšíření virtuálního počítače Log Analytics pro Linux je publikováno a podporováno společností Microsoft. Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače Log Analytics pro Linux.

>[!NOTE]
>V rámci probíhajícího přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se agent OMS pro Windows nebo Linux označuje jako agent log analytics pro Windows a agent log analytics pro Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Podrobnosti o podporovaných distribucích Linuxu najdete v článku [přehled agenta Analýzy protokolů.](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Verze rozšíření agenta a virtuálního virtuálního aplikace
Následující tabulka obsahuje mapování verze rozšíření virtuálního aplikace Analýzy protokolů a sady agentů Log Analytics pro každou verzi. Obsahuje se odkaz na poznámky k verzi pro verzi sady agenta Log Analytics. Poznámky k verzi obsahují podrobnosti o opravách chyb a nové funkce, které jsou k dispozici pro danou verzi agenta.  

| Verze rozšíření virtuálního počítače Pro Linux log Analytics | Verze balíčku agenta analýzy protokolů | 
|--------------------------------|--------------------------|
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
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

Azure Security Center automaticky zřídí agenta Analýzy protokolů a připojí ho k výchozímu pracovnímu prostoru Log Analytics vytvořenému asc ve vašem předplatném Azure. Pokud používáte Azure Security Center, neprocházet kroky v tomto dokumentu. Tím přepíšena nakonfigurovaný pracovní prostor a přeruší připojení s Azure Security Center.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření Agent Log Analytics pro Linux vyžaduje, aby byl cílový virtuální počítač připojený k internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření agenta analýzy protokolů. Rozšíření vyžaduje ID pracovního prostoru a klíč pracovního prostoru z cílového pracovního prostoru Analýzy protokolů. tyto hodnoty [najdete v pracovním prostoru Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) na webu Azure Portal. Vzhledem k tomu, že klíč pracovního prostoru by měl být považován za citlivá data, měl by být uložen v konfiguraci chráněného nastavení. Data nastavení chráněné rozšířením virtuálního počítače Azure jsou šifrovaná a dešifrovaná jenom na cílovém virtuálním počítači. Všimněte si, že **workspaceId** a **workspaceKey** rozlišují malá a velká písmena.

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
>Výše uvedené schéma předpokládá, že bude umístěn na kořenové úrovni šablony. Pokud jej vložíte do prostředku virtuálního `type` počítače `name` v šabloně, vlastnosti a by měly být změněny, jak je popsáno [dále dolů](#template-deployment).

### <a name="property-values"></a>Hodnoty vlastností

| Name (Název) | Hodnota / Příklad |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| vydavatel | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| id pracovního prostoru (např. | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (např. | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Šablony jsou ideální při nasazování jednoho nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení, jako je například připojení do protokolů monitorování Azure. Ukázkovou šablonu Správce prostředků, která obsahuje rozšíření virtuálního počítače agenta Analýzy protokolů, najdete v [galerii Azure Quickstart Gallery](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Konfigurace JSON pro rozšíření virtuálního počítače může být vnořena uvnitř prostředku virtuálního počítače nebo umístěna na kořenové nebo nejvyšší úrovni šablony JSON správce prostředků. Umístění konfigurace JSON ovlivňuje hodnotu názvu a typu prostředku. Další informace naleznete v tématu [Nastavení názvu a typu podřízených prostředků](../../azure-resource-manager/templates/child-resource-name-type.md). 

Následující příklad předpokládá, že rozšíření virtuálního počítače je vnořené uvnitř prostředku virtuálního počítače. Při vnoření prostředku rozšíření JSON je `"resources": []` umístěn v objektu virtuálního počítače.

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

Při umístění rozšíření JSON v kořenovém adresáři šablony obsahuje název prostředku odkaz na nadřazený virtuální počítač a typ odráží vnořenou konfiguraci.  

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

## <a name="azure-cli-deployment"></a>Nasazení azure cli

Azure CLI slouží k nasazení rozšíření agenta Log Analytics do existujícího virtuálního počítače. Nahraďte níže uvedenou hodnotu *myWorkspaceKey* klíčem pracovního prostoru a hodnotou *myWorkspaceId* ID pracovního prostoru. Tyto hodnoty najdete v pracovním prostoru Log Analytics na webu Azure Portal v části *Upřesnit nastavení*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z portálu Azure a pomocí azure cli. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí příkazu Příkaz příkazu Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstup spuštění rozšíření je zaznamenán do následujícího souboru:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Chybové kódy a jejich význam

| Kód chyby | Význam | Možná akce |
| :---: | --- | --- |
| 9 | Povolit předčasně volané | [Aktualizujte agenta Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) na nejnovější dostupnou verzi. |
| 10 | Virtuální virtuální připojení je už připojené k pracovnímu prostoru Log Analytics. | Chcete-li připojit virtuální počítač k pracovnímu prostoru určenému ve schématu rozšíření, nastavte stopOnMultipleConnections na false ve veřejném nastavení nebo tuto vlastnost odeberte. Tento virtuální virtuální účet se účtuje jednou za každý pracovní prostor, ke které je připojený. |
| 11 | Pro rozšíření byla poskytnuta neplatná konfigurace. | Postupujte podle předchozích příkladů nastavit všechny hodnoty vlastností potřebné pro nasazení. |
| 17 | Selhání instalace balíčku Log Analytics | 
| 19 | Selhání instalace balíčku OMI | 
| 20 | Selhání instalace balíčku SCX |
| 51 | Toto rozšíření není podporováno v operačním systému virtuálního počítači. | |
| 55 | Nelze se připojit ke službě Azure Monitor nebo je uzamčen chybějící požadovaný balíček nebo správce balíčků dpkg.| Zkontrolujte, zda má systém přístup k Internetu nebo zda byl k dispozici platný proxy server HTTP. Dále zkontrolujte správnost ID pracovního prostoru a ověřte, curl a tar nástroje jsou nainstalovány. |

Další informace o řešení potíží naleznete v [průvodci odstraňováním potíží s protokolem Log Analytics-Agent-for-Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
