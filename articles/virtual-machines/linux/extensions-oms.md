---
title: Rozšíření virtuálního počítače OMS Azure pro Linux | Microsoft Docs
description: Nasaďte agenta OMS na virtuální počítač s Linuxem pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: danis
ms.openlocfilehash: 2927e2e64c78ac01a5ed9aa49a88e599ea36deb2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="oms-virtual-machine-extension-for-linux"></a>OMS rozšíření virtuálního počítače pro Linux

## <a name="overview"></a>Přehled

Log Analytics poskytuje možnosti nápravy monitorování, výstrahy a výstrahy v cloudové a místní prostředky. Rozšíření virtuálního počítače OMS agenta pro Linux je publikována a společnost Microsoft podporuje. Rozšíření nainstaluje agenta OMS na virtuálních počítačích Azure a zaregistruje virtuální počítače do existující pracovní prostor analýzy protokolů. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače OMS pro Linux.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření agenta OMS se může spouštět na těchto Linuxových distribucích.

| Distribuce | Verze |
|---|---|
| CentOS Linux | 5, 6 a 7 |
| Oracle Linux | 5, 6 a 7 |
| Red Hat Enterprise Linux Server | 5, 6 a 7 |
| Debian GNU/Linux | 6, 7 a 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS |
| SUSE Linux Enterprise Server | 11 a 12 |

### <a name="agent-and-vm-extension-version"></a>Verze agenta a rozšíření virtuálního počítače
Následující tabulka obsahuje mapování verze virtuálního počítače OMS rozšíření a OMS Agent sady pro jednotlivé verze. Je k dispozici odkaz na poznámky k verzi sady agenta OMS. Poznámky k verzi obsahují podrobnosti na oprav chyb a nových funkcí dostupných pro daného agenta verze.  

| Verze rozšíření virtuálního počítače s Linuxem OMS | Verze agenta OMS sady | 
|--------------------------------|--------------------------|
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

Azure Security Center automaticky zřídí agenta OMS a připojí ho do pracovního prostoru analýzy protokolů výchozí vytvořené ASC ve vašem předplatném Azure. Pokud používáte Azure Security Center, se nespustí provede kroky v tomto dokumentu. To přepíše nakonfigurované prostoru a dělí připojení ke službě Azure Security Center.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření OMS agenta pro Linux vyžaduje, aby cílový virtuální počítač je připojený k Internetu. 

## <a name="extension-schema"></a>Rozšíření schématu

Následujícím kódu JSON znázorňuje schéma pro rozšíření agenta OMS. Rozšíření vyžaduje ID a klíč pracovního prostoru z cílového pracovního prostoru analýzy protokolů; Tyto hodnoty mohou být [nalezen v pracovním prostoru analýzy protokolů](../../log-analytics/log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key) na portálu Azure. Vzhledem k tomu, že klíč pracovního prostoru by měl být považován za citlivá data, by měly být uložené v chráněném nastavení konfigurace. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaná a dešifrovat jenom na cílový virtuální počítač. Všimněte si, že **workspaceId** a **workspaceKey** malých a velkých písmen.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota nebo příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Vydavatele | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.4 |
| ID pracovního prostoru (např.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (např.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure se dá nasadit pomocí šablon Azure Resource Manager. Šablony jsou ideální, pokud nasazujete jednu nebo více virtuálních počítačů, které vyžadují konfiguraci nasazení post jako je registrace k analýze protokolů. Ukázka šablonu Resource Manager, která obsahuje rozšíření virtuálního počítače agenta OMS naleznete na [Azure rychlý Start Galerie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Konfigurace JSON pro rozšíření virtuálního počítače můžete vnořit prostředek virtuálního počítače nebo umístěn na kořenový server WSUS nebo nejvyšší úrovně šablony JSON Resource Manager. Umístění konfigurace JSON ovlivňuje hodnota název prostředku a typem. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

V následujícím příkladu se předpokládá, že rozšíření virtuálního počítače je vnořit prostředek virtuálního počítače. Při vnoření rozšíření prostředků, JSON je umístěn v `"resources": []` objektu virtuálního počítače.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Při vkládání rozšíření JSON v kořenovém adresáři šablony, názvu prostředku obsahuje odkaz na nadřazený virtuální počítač a typ odráží vnořené konfigurace.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
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

Rozhraní příkazového řádku Azure můžete použít k nasazení rozšíření virtuálního počítače agenta OMS na existující virtuální počítač. Nahraďte *workspaceId* a *workspaceKey* s těmi, která z pracovního prostoru analýzy protokolů. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.4 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Z portálu Azure a pomocí rozhraní příkazového řádku Azure je možné načíst data o stavu nasazení rozšíření. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí rozhraní příkazového řádku Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstupu spuštění rozšíření je zaznamenána do následujícího souboru:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Kódy chyb a jejich významů

| Kód chyby | Význam | Možné akce |
| :---: | --- | --- |
| 9 | Povolit názvem předčasně | [Aktualizovat Azure Linux Agent](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) na nejnovější dostupnou verzi. |
| 10 | Virtuální počítač je již připojen k pracovnímu prostoru analýzy protokolů | Připojit virtuální počítač do pracovního prostoru, určená ve schématu rozšíření, nastavena na hodnotu false v nastavení veřejných stopOnMultipleConnections nebo odeberte tuto vlastnost. Tento virtuální počítač získá účtován pro každý pracovní prostor po připojení. |
| 11 | Neplatná konfigurace poskytuje rozšíření | Postupujte podle předchozích ukázkách nastavit všechny hodnoty vlastností nezbytné pro nasazení. |
| 12 | Správce balíčků dpkg je uzamčena. | Zajistěte, aby všechny dpkg operace aktualizace na počítači dokončení a akci opakujte. |
| 20 | Selhání instalace balíčku SCX. |
| 51 | Toto rozšíření není podporována na operační systém Virtuálního počítače | |
| 55 | Nelze připojit ke službě Microsoft Operations Management Suite | Zkontrolujte, jestli systém má přístup k Internetu nebo že bylo zadáno platný proxy server HTTP. Kromě toho zkontrolujte správnost ID pracovního prostoru. |

Další informace o odstraňování potíží naleznete na [Průvodce odstraňováním potíží OMS agenta pro Linux](../../log-analytics/log-analytics-azure-vmext-troubleshoot.md).

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/en-us/support/options/) a vyberte Get podpory. Informace o používání Azure podporovat, najdete v tématu [podporu Microsoft Azure – nejčastější dotazy](https://azure.microsoft.com/en-us/support/faq/).
