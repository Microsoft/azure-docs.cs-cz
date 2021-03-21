---
title: Rozšíření Log Analytics pro virtuální počítače pro Linux
description: Nasaďte agenta Log Analytics na virtuálním počítači se systémem Linux pomocí rozšíření virtuálního počítače.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/18/2020
ms.openlocfilehash: 3ac6937d83bd2d21fefc09878408a54aa0eb41f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559065"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Rozšíření Log Analytics pro virtuální počítače pro Linux

## <a name="overview"></a>Přehled

Protokoly Azure Monitor poskytují možnosti monitorování, upozorňování a nápravy výstrah napříč cloudem a místními prostředky. Rozšíření virtuálního počítače Log Analytics pro Linux se zveřejňuje a podporuje Microsoft. Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření Log Analytics virtuálního počítače pro Linux.

>[!NOTE]
>V rámci průběžného přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se agent OMS pro Windows nebo Linux bude označovat jako agent Log Analytics pro Windows a agenta Log Analytics pro Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Předpoklady

### <a name="operating-system"></a>Operační systém

Podrobnosti o podporovaných distribucích systému Linux najdete v článku [přehled Azure Monitorch agentů](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Verze agenta a rozšíření virtuálního počítače
Následující tabulka poskytuje mapování verze Log Analyticsho rozšíření virtuálního počítače a sady Log Analytics agenta pro každou verzi. Obsahuje odkaz na poznámky k verzi pro verzi sady Log Analytics agentů. Poznámky k verzi obsahují podrobnosti o opravách chyb a nové funkce, které jsou k dispozici pro danou verzi agenta.  

| Verze rozšíření virtuálního počítače s Log Analytics Linux | Verze sady prostředků agenta Log Analytics | 
|--------------------------------|--------------------------|
| 1.13.33 | [1.13.33](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.33-0) |
| 1.13.27 | [1.13.27](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.27-0) |
| 1.13.15 | [1.13.9-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.9-0) |
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

Azure Security Center automaticky zřídí agenta Log Analytics a připojí ho k výchozímu Log Analytics pracovnímu prostoru vytvořenému pomocí ASC ve vašem předplatném Azure. Pokud používáte Azure Security Center, neprovádějte kroky v tomto dokumentu. Tím dojde k přepsání nakonfigurovaného pracovního prostoru a přerušení připojení k Azure Security Center.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření agenta Log Analytics pro Linux vyžaduje, aby byl cílový virtuální počítač připojený k Internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření agenta Log Analytics. Přípona vyžaduje ID pracovního prostoru a klíč pracovního prostoru z cílového pracovního prostoru Log Analytics; Tyto hodnoty najdete [v pracovním prostoru Log Analytics](../../azure-monitor/vm/quick-collect-linux-computer.md#obtain-workspace-id-and-key) v Azure Portal. Vzhledem k tomu, že klíč pracovního prostoru by měl být považován za citlivá data, měl by být uložen v konfiguraci chráněného nastavení. Data nastavení chráněná rozšířením virtuálního počítače Azure jsou šifrovaná a v cílovém virtuálním počítači se dešifrují jenom. Všimněte si, že **ID pracovního prostoru** a **workspaceKey** rozlišují velká a malá písmena.

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
    "typeHandlerVersion": "1.13",
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
>Výše uvedené schéma předpokládá, že bude umístěn na kořenové úrovni šablony. Pokud jste ho umístili do prostředku virtuálního počítače v šabloně, je `type` `name` třeba změnit vlastnosti a, jak je popsáno [dále](#template-deployment).

### <a name="property-values"></a>Hodnoty vlastností

| Name | Hodnota/příklad |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| vydavatel | Microsoft. EnterpriseCloud. Monitoring |
| typ | OmsAgentForLinux |
| typeHandlerVersion | 1.13 |
| ID pracovního prostoru (např.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (např.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |


## <a name="template-deployment"></a>Nasazení šablon

>[!NOTE]
>Některé součásti rozšíření virtuálního počítače Log Analytics jsou také dodány v [rozšíření Diagnostika virtuálního počítače](./diagnostics-linux.md). Z důvodu této architektury mohou konflikty nastat, pokud jsou obě rozšíření vytvořena ve stejné šabloně ARM. Aby se tyto konflikty při instalaci nezobrazovaly, použijte [ `dependsOn` direktivu](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) , abyste zajistili, že se rozšíření nainstalují sekvenčně. Rozšíření lze instalovat v libovolném pořadí.

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Šablony jsou ideální při nasazení jednoho nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení, jako je připojování k Azure Monitor protokolů. Ukázková Správce prostředků Šablona obsahující rozšíření virtuálního počítače Log Analytics agenta najdete v [galerii rychlý Start Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Konfiguraci JSON pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo umístit na kořenovou nebo nejvyšší úroveň šablony Správce prostředků JSON. Umístění konfigurace JSON má vliv na hodnotu názvu a typu prostředku. Další informace najdete v tématu [Nastavení názvu a typu pro podřízené prostředky](../../azure-resource-manager/templates/child-resource-name-type.md). 

Následující příklad předpokládá, že rozšíření virtuálního počítače je vnořené uvnitř prostředku virtuálního počítače. Při vnořování prostředku rozšíření je kód JSON umístěný v `"resources": []` objektu virtuálního počítače.

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
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Při umístění JSON rozšíření v kořenovém adresáři šablony obsahuje název prostředku odkaz na nadřazený virtuální počítač a typ odráží vnořenou konfiguraci.  

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
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Pomocí rozhraní příkazového řádku Azure můžete nasadit rozšíření virtuálního počítače agenta Log Analytics do existujícího virtuálního počítače. Hodnotu *myworkspacekey svým* níže nahraďte klíčem pracovního prostoru a hodnotou *MYWORKSPACEID* s vaším ID pracovního prostoru. Tyto hodnoty najdete v pracovním prostoru Log Analytics v Azure Portal v části *Upřesnit nastavení*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí rozhraní příkazového řádku Azure CLI. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstup spuštění rozšíření se zaznamená do následujícího souboru:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Kódy chyb a jejich významy

| Kód chyby | Význam | Možná akce |
| :---: | --- | --- |
| 9 | Povolit s názvem předčasně | [Aktualizujte agenta Azure Linux](./update-linux-agent.md) na nejnovější dostupnou verzi. |
| 10 | Virtuální počítač je už připojený k Log Analyticsmu pracovnímu prostoru. | Pokud chcete připojit virtuální počítač k pracovnímu prostoru určenému ve schématu rozšíření, nastavte stopOnMultipleConnections na hodnotu false ve veřejném nastavení nebo odeberte tuto vlastnost. Tento virtuální počítač se pro každý pracovní prostor, ke kterému je připojený, účtuje jednou. |
| 11 | Rozšíření se zadala neplatná konfigurace. | Pomocí předchozích příkladů nastavte všechny hodnoty vlastností, které jsou nezbytné pro nasazení. |
| 17 | Chyba instalace balíčku Log Analytics | 
| 19 | Chyba instalace balíčku OMI | 
| 20 | Chyba instalace balíčku SCX |
| 51 | Toto rozšíření není v operačním systému virtuálního počítače podporované. | |
| 52 | Toto rozšíření selhalo z důvodu chybějící závislosti. | Další informace o tom, která závislost chybí, najdete ve výstupu a v protokolech. |
| 53 | Toto rozšíření se nepovedlo kvůli chybějícím nebo chybným parametrům konfigurace. | Další informace o chybě najdete ve výstupu a v protokolech. Dále zkontrolujte správnost ID pracovního prostoru a ověřte, zda je počítač připojen k Internetu. |
| 55 | Nelze se připojit ke službě Azure Monitor nebo chybí požadované balíčky nebo je správce balíčků bázi dpkg uzamčen.| Ověřte, že systém buď má přístup k Internetu, nebo jestli je zadaný platný proxy server HTTP. Dále zkontrolujte správnost ID pracovního prostoru a ověřte, zda jsou nainstalovány nástroje pro kudrlinkou a tar. |

Další informace o řešení potíží najdete v [Průvodci odstraňováním potíží s Log Analytics-agenty pro Linux](../../azure-monitor/visualize/vmext-troubleshoot.md).

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
