---
title: Rozšíření virtuálního počítače Azure Log Analytics pro Linux | Dokumentace Microsoftu
description: Nasaďte agenta Log Analytics na virtuální počítač s Linuxem pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/05/2018
ms.author: roiyz
ms.openlocfilehash: 858c586151c8a0dd5378794a5057c22a423c43cf
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782735"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics rozšíření virtuálního počítače pro Linux

## <a name="overview"></a>Přehled

Log Analytics poskytuje možnosti nápravy monitorování, výstrahy a oznámení napříč cloudovou a místní prostředky. Rozšíření virtuálního počítače agenta Log Analytics pro Linux je publikována a podporované společností Microsoft. Rozšíření nainstaluje agenta Log Analytics ve službě Azure virtual machines a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro virtuální počítač rozšíření Log Analytics pro Linux.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření agenta Log Analytics je možné spouštět proti těmto Linuxových distribucí.

| Distribuce | Verze |
|---|---|
| CentOS Linux | 6 a 7 (x86/x64) |
| Amazon Linux | 2017.09 | 
| Oracle Linux | 6 a 7 (x86/x64) |
| Red Hat Enterprise Linux Server | 6 a 7 (x86/x64) |
| Debian GNU/Linux | 8 a 9 (x86/x64) |
| Ubuntu | 14.04 LTS, 16.04 LTS a 18.04 LTS (x86/x64) |
| SUSE Linux Enterprise Server | 12 (x86/x64) |

>[!NOTE]
>OpenSSL nižší než verze 1.x se nepodporuje na libovolné platformě a verze 1.10 je podporován pouze na platformách x86_64 (64 bitů).  
>

### <a name="agent-and-vm-extension-version"></a>Verze agenta a rozšíření virtuálního počítače
Následující tabulka obsahuje mapování verzi rozšíření virtuálního počítače Log Analytics a agenta Log Analytics sady pro každou vydanou verzí. Odkaz na poznámky k verzi sady agenta Log Analytics je součástí. Poznámky k verzi obsahují informace o opravy a nové funkce, které jsou k dispozici pro daného agenta verze.  

| Verze rozšíření log Analytics virtuálního počítače s Linuxem | Verze sady prostředků log Analytics agenta | 
|--------------------------------|--------------------------|
| 1.7.7 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
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

Následující kód JSON ukazuje schéma pro rozšíření agenta Log Analytics. Rozšíření vyžaduje ID pracovního prostoru a klíč pracovního prostoru z pracovního prostoru Log Analytics cíl; Tyto hodnoty mohou být [nalezen ve vašem pracovním prostoru Log Analytics](../../log-analytics/log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key) na webu Azure Portal. Protože klíč pracovního prostoru by měl být považován za citlivá data, by měl být uložený v chráněném nastavení konfigurace. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaný a dešifrovat jenom na cílovém virtuálním počítači. Všimněte si, že **ID pracovního prostoru** a **workspaceKey** jsou malá a velká písmena.

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

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| vydavatele | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| ID pracovního prostoru (např.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (např.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Šablony jsou ideální při nasazování jedné nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení, jako je připojení ke službě Log Analytics. Ukázka šablony Resource Manageru, který obsahuje rozšíření virtuálního počítače agenta Log Analytics můžete najít na [Galerie Azure rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

JSON konfigurace pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo objektu umístěn na kořenový server WSUS nebo nejvyšší úrovni šablony JSON Resource Manageru. Umístění konfigurace JSON má vliv na hodnotu názvu prostředku a typů. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

V následujícím příkladu se předpokládá, že rozšíření virtuálního počítače je vnořená do prostředku virtuálního počítače. Při vnoření rozšíření prostředků, ve formátu JSON je umístěn v `"resources": []` objekt virtuálního počítače.

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
  "apiVersion": "2015-06-15",
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

Azure CLI slouží k nasazení do existujícího virtuálního počítače rozšíření virtuálního počítače agenta Log Analytics. Nahradit *ID pracovního prostoru* a *workspaceKey* soubory z pracovního prostoru Log Analytics. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.7 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
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
| 17 | Selhání instalace balíčku OMS | 
| 19 | Selhání instalace balíčku (OMI) | 
| 20 | Selhání instalace balíčku SCX |
| 51 | Toto rozšíření nepodporuje operační systém Virtuálního počítače | |
| 55 | Nejde se připojit ke službě OMS nebo chybějící požadované balíčky nebo dpkg balíček správce uzamčeno| Zkontrolujte, že systém má přístup k Internetu nebo že byl poskytnut správný proxy server HTTP. Kromě toho zkontrolujte správnost ID pracovního prostoru a ověřte, zda jsou nainstalované nástroje curl a cíl. |

Další informace o odstraňování potíží najdete na [Průvodce odstraňováním potíží OMS Agent pro Linux](../../log-analytics/log-analytics-azure-vmext-troubleshoot.md).

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
