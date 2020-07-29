---
title: Funkce a rozšíření virtuálních počítačů Azure
description: Přečtěte si, jaká rozšíření virtuálních počítačů Azure jsou a jak se dají používat s virtuálními počítači Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74072978"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Funkce a rozšíření virtuálních počítačů Azure
Rozšíření virtuálních počítačů Azure jsou malé aplikace, které poskytují konfiguraci po nasazení a úlohy automatizace na virtuálních počítačích Azure. můžete použít existující image a pak je přizpůsobit jako součást nasazení. získáte tak vlastní vytváření vlastních imagí.

Platforma Azure je hostitelem mnoha rozšíření, která jsou v rozsahu od konfigurace virtuálních počítačů, monitorování, zabezpečení a aplikace nástrojů. Vydavatelé přebírají aplikaci, pak ji zabalí do rozšíření a zjednoduší instalaci, takže stačí, když zadáte povinné parametry. 

 Existuje velký výběr první a rozšíření od jiných dodavatelů, pokud aplikace v úložišti rozšíření neexistuje, můžete použít rozšíření vlastních skriptů a nakonfigurovat svůj virtuální počítač pomocí vlastních skriptů a příkazů.

Příklady klíčových scénářů, pro které jsou rozšíření používána:
* Konfigurace virtuálních počítačů: k instalaci agentů konfigurace virtuálních počítačů a konfiguraci virtuálního počítače můžete použít rozšíření PowerShell DSC (Konfigurace požadovaného stavu), saďte, Puppet a Custom Script Extensions. 
* ANTIVIROVÝch produktů, jako je Symantec, ESET.
* Nástroj pro ohrožení zabezpečení virtuálního počítače, jako je například Qualys, Rapid7, HPE.
* Nástroje pro monitorování virtuálních počítačů a aplikací, jako je DynaTrace, Azure Network Watcher, Site24x7 a Stackify.

Rozšíření lze seskupit pomocí nového nasazení virtuálního počítače. Můžou být třeba součástí rozsáhlejšího nasazení, konfigurace aplikací v zřizování virtuálních počítačů nebo spouštění pro všechny podporované systémy ovládanými rozšířeními po nasazení.

## <a name="how-can-i-find-what-extensions-are-available"></a>Jak můžu najít rozšíření, která jsou k dispozici?
Dostupná rozšíření si můžete zobrazit v okně virtuálního počítače na portálu. v části rozšíření to představuje jenom malou část. úplný seznam vám umožní použít nástroje CLI, které najdete v tématu [zjišťování rozšíření virtuálních počítačů pro Linux](features-linux.md) a [zjišťování rozšíření virtuálních počítačů pro Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Jak můžu nainstalovat rozšíření?
Rozšíření virtuálních počítačů Azure je možné spravovat pomocí Azure Resource Managerch šablon Azure CLI, Azure PowerShell, a Azure Portal. Pokud chcete vyzkoušet rozšíření, můžete přejít na Azure Portal, vybrat rozšíření vlastních skriptů a pak předat příkaz nebo skript a spustit rozšíření.

Pokud chcete přidat stejné rozšíření, které jste přidali na portálu pomocí rozhraní příkazového řádku nebo šablony Správce prostředků, přečtěte si část dokumentace k rozšíření, například [rozšíření vlastních skriptů Windows](custom-script-windows.md) a [rozšíření vlastních skriptů pro Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Návody spravovat životní cyklus aplikace rozšíření?
K instalaci nebo odstranění tohoto rozšíření se nemusíte připojovat k virtuálnímu počítači přímo. Protože životní cyklus aplikace rozšíření Azure je spravován mimo virtuální počítač a integrovaný do platformy Azure, získáte také integrovaný stav rozšíření.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Něco jiného, co by mělo být v souvislosti s rozšířeními uvažujete?
Rozšíření instalují aplikace, stejně jako u všech aplikací, pro rozšíření existuje seznam podporovaných systémů Windows a Linux operačních systémech a musíte mít nainstalované agenty virtuálních počítačů Azure. Některé aplikace rozšíření virtuálních počítačů můžou mít své vlastní požadavky na životní prostředí, jako je třeba přístup ke koncovému bodu.

## <a name="troubleshoot-extensions"></a>Poradce při potížích s rozšířeními

Informace o řešení potíží pro jednotlivá rozšíření najdete v části **řešení potíží a podpora** v tématu Přehled tohoto rozšíření. Tady je seznam informací o řešení problémů, které jsou k dispozici:

| Obor názvů | Řešení potíží |
|-----------|-----------------|
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentlinux | [Závislost Azure Monitor pro Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentwindows | [Azure Monitor závislost pro Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryptionforlinux | [Azure Disk Encryption pro Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryption | [Azure Disk Encryption pro Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| Microsoft. Compute. CustomScriptExtension | [Vlastní skript pro Windows](custom-script-windows.md#troubleshoot-and-support) |
| Microsoft. ostcextensions. customscriptforlinux | [Konfigurace požadovaného stavu pro Linux](dsc-linux.md#troubleshoot-and-support) |
| Microsoft. PowerShell. DSC | [Konfigurace požadovaného stavu pro Windows](dsc-windows.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverlinux | [Rozšíření ovladače NVIDIA GPU pro Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverwindows | [Rozšíření ovladače NVIDIA GPU pro Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. iaasantimalware | [Antimalwarové rozšíření pro Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| Microsoft. enterprisecloud. Monitoring. omsagentforlinux | [Azure Monitor pro Linux](oms-linux.md#troubleshoot-and-support)
| Microsoft. enterprisecloud. Monitoring. microsoftmonitoringagent | [Azure Monitor pro Windows](oms-windows.md#troubleshoot-and-support) |
| Stackify. linuxagent. extension. stackifylinuxagentextension | [Stackify přetrasování pro Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. Microsoft. ostcextensions | [Resetování hesla (VMAccess) pro Linux](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Snímek pro Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Snímek pro Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Další kroky
* Další informace o fungování agenta a rozšíření pro Linux najdete v tématu [rozšíření a funkce virtuálních počítačů Azure pro Linux](features-linux.md).
* Další informace o tom, jak funguje Agent hosta a rozšíření systému Windows, najdete v tématu [rozšíření a funkce virtuálních počítačů Azure pro Windows](features-windows.md).  
* Informace o instalaci agenta hosta systému Windows najdete v tématu [Přehled agenta virtuálního počítače Azure s Windows](agent-windows.md).  
* Informace o instalaci agenta pro Linux najdete v tématu [Přehled agenta virtuálních počítačů Azure Linux](agent-linux.md).  

