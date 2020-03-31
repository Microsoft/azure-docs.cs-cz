---
title: Funkce a rozšíření virtuálních počítačů Azure
description: Zjistěte, co jsou rozšíření virtuálních počítačů Azure a jak je používat s virtuálními počítači Azure
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072978"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Funkce a rozšíření virtuálních počítačů Azure
Rozšíření virtuálních počítačů Azure (VM) jsou malé aplikace, které poskytují úlohy konfigurace a automatizace po nasazení na virtuálních počítačích Azure, můžete použít existující image a pak je přizpůsobit jako součást nasazení a dostat vás z podnikání vlastních obrazové budovy.

Platforma Azure hostuje mnoho rozšíření, která sahají od konfigurace virtuálních počítačů, monitorování, zabezpečení a aplikací nástrojů. Vydavatelé vezmou aplikaci, pak ji zabalí do rozšíření a zjednoduší instalaci, takže vše, co musíte udělat, je poskytnout povinné parametry. 

 Existuje velký výběr rozšíření první a třetí strany, pokud aplikace v úložišti rozšíření neexistuje, pak můžete použít rozšíření vlastní skript a nakonfigurovat virtuální počítač s vlastní skripty a příkazy.

Příklady klíčových scénářů, pro které se rozšíření používají:
* Konfigurace virtuálního počítače, můžete použít Powershell DSC (konfigurace požadovaného stavu), Chef, Loutka a vlastní script rozšíření k instalaci agentů konfigurace virtuálních počítačů a nakonfigurovat virtuální počítač. 
* AV produkty, jako je Symantec, ESET.
* Nástroj pro zranitelnost virtuálního počítače, například Qualys, Rapid7, HPE.
* Nástroje pro monitorování virtuálních stránek a aplikací, jako je DynaTrace, Azure Network Watcher, Site24x7 a Stackify.

Rozšíření lze svázat s novým nasazením virtuálních počítače. Mohou být například součástí většího nasazení, konfigurace aplikací při poskytování virtuálních počítačů nebo spustit proti všem podporovaným systémům provozovaným rozšířením po nasazení.

## <a name="how-can-i-find-what-extensions-are-available"></a>Jak najdu, jaká rozšíření jsou k dispozici?
Dostupná rozšíření můžete zobrazit v okně virtuálního počítače na portálu, v rámci rozšíření to představuje jen malé množství, pro úplný seznam můžete použít nástroje rozhraní příkazového příkazu, najdete [v tématu Zjišťování rozšíření virtuálních zařízení pro Linux](features-linux.md) a zjišťování rozšíření [virtuálních zařízení pro Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Jak mohu nainstalovat rozšíření?
Rozšíření virtuálních počítačen Azure se můžou spravit buď pomocí Azure CLI, Azure PowerShellu, šablon Azure Resource Manager u portálu Azure. Chcete-li vyzkoušet rozšíření, můžete přejít na portál Azure, vyberte rozšíření vlastní skript, pak předat příkaz / skript a spustit rozšíření.

Pokud chcete stejné rozšíření, které jste přidali na portálu pomocí cli nebo šablony Správce prostředků, podívejte se na různé rozšíření dokumentace, jako je [například Windows Custom Script Extension](custom-script-windows.md) a Linux Custom Script [Extension](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Jak mohu spravovat životní cyklus rozšíření aplikace?
K instalaci nebo odstranění rozšíření se nemusíte připojovat přímo k virtuálnímu virtuálnímu zařízení. Vzhledem k tomu, že životní cyklus rozšíření Azure se spravuje mimo virtuální počítač a je integrovaný do platformy Azure, získáte také integrovaný stav rozšíření.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Ještě něco bych měl myslet na rozšíření?
Rozšíření instalovat aplikace, stejně jako všechny aplikace existují některé požadavky, pro rozšíření je seznam podporovaných operačních oken a linuxových operačních systémů a musíte mít nainstalované agenty virtuálních zařízení Azure. Některé jednotlivé aplikace rozšíření virtuálního připojení může mít své vlastní požadavky prostředí, jako je například přístup ke koncovému bodu.

## <a name="troubleshoot-extensions"></a>Poradce při potížích s rozšířeními

Informace o řešení potíží pro každé rozšíření naleznete v části **Poradce při potížích a podpoře** v přehledu rozšíření. Zde je seznam dostupných informací o řešení potíží:

| Obor názvů | Řešení potíží |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Závislost monitoru Azure pro Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| Microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Závislost monitoru Azure pro Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Šifrování disku Azure pro Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| šifrování microsoft.azure.security.azuredisk | [Šifrování disku Azure pro Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| Rozšíření microsoft.compute.customscriptextension | [Vlastní skript pro Windows](custom-script-windows.md#troubleshoot-and-support) |
| Microsoft.ostcextensions.customscriptforlinux | [Požadovaná konfigurace stavu pro Linux](dsc-linux.md#troubleshoot-and-support) |
| soubor microsoft.powershell.dsc | [Konfigurace požadovaného stavu pro windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [Rozšíření ovladače GPU NVIDIA pro Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [Rozšíření ovladače GPU NVIDIA pro Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Antimalwarové rozšíření pro Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Azure Monitor pro Linux](oms-linux.md#troubleshoot-and-support)
| Microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Azure Monitor pro Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension stackify.linuxagentextension stackify.linuxagentextension stackify.linuxagent.stackifylinuxagentextension stackify.linux | [Stackify Retrace pro Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| rozšíření vmaccessforlinux.microsoft.ostcextensions | [Resetování hesla (VMAccess) pro Linux](vmaccess.md#troubleshoot-and-support) |
| Microsoft.recoveryservices.vmsnapshot | [Snímek pro Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft.recoveryservices.vmsnapshot | [Snímek pro Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Další kroky
* Další informace o tom, jak linuxový agent a rozšíření fungují, najdete v [tématu rozšíření virtuálních virtuálních počítače Azure a funkce pro Linux](features-linux.md).
* Další informace o tom, jak agent hosta Windows a rozšíření fungují, najdete v [tématu rozšíření virtuálních](features-windows.md)aplikací Azure a funkce pro Windows .  
* Informace o instalaci agenta hosta Windows najdete v [tématu Přehled agenta virtuálních strojů Azure Windows](agent-windows.md).  
* Informace o instalaci agenta Linuxu najdete v [tématu Přehled agenta virtuálních strojů Azure Linuxu](agent-linux.md).  

