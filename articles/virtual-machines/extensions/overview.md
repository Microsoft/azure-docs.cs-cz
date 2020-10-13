---
title: Funkce a rozšíření virtuálních počítačů Azure
description: Další informace o rozšířeních virtuálních počítačů Azure
services: virtual-machines
author: amjads1
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/03/2020
ms.author: amjads
ms.openlocfilehash: 73738f339bea3e8e075530e5de564c1c0854d283
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87552046"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Funkce a rozšíření virtuálních počítačů Azure
Rozšíření jsou malé aplikace, které poskytují konfiguraci po nasazení a automatizaci na virtuálních počítačích Azure. Platforma Azure je hostitelem řady rozšíření, které pokrývá aplikace pro konfiguraci, monitorování, zabezpečení a pomůcku virtuálních počítačů. Vydavatelé přebírají aplikaci, zabalí ji do rozšíření a zjednoduší instalaci. Vše, co potřebujete udělat, je zadání povinných parametrů. 

## <a name="how-can-i-find-what-extensions-are-available"></a>Jak můžu najít rozšíření, která jsou k dispozici?
Dostupná rozšíření si můžete zobrazit tak, že vyberete virtuální počítač, vyberte **rozšíření** v nabídce vlevo. Pokud si chcete stáhnout úplný seznam rozšíření, přečtěte si téma [zjišťování rozšíření virtuálních počítačů pro Linux](features-linux.md) a [zjišťování rozšíření virtuálních počítačů pro Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Jak můžu nainstalovat rozšíření?
Rozšíření virtuálních počítačů Azure je možné spravovat pomocí Azure CLI, PowerShellu, Správce prostředků šablon a Azure Portal. Pokud chcete vyzkoušet rozšíření, klikněte na Azure Portal, vyberte rozšíření vlastních skriptů a pak předejte příkaz nebo skript pro spuštění rozšíření.

Další informace najdete v tématu rozšíření [vlastních skriptů pro Windows](custom-script-windows.md) a [rozšíření vlastních skriptů pro Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Návody spravovat životní cyklus aplikace rozšíření?
K instalaci nebo odstranění rozšíření se nemusíte připojovat k virtuálnímu počítači přímo. Životní cyklus rozšíření Azure je spravován mimo virtuální počítač a integrovaný do platformy Azure.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Něco jiného, co by mělo být v souvislosti s rozšířeními uvažujete?
Některé aplikace rozšíření virtuálních počítačů můžou mít své vlastní požadavky na životní prostředí, jako je třeba přístup ke koncovému bodu. Každé rozšíření obsahuje článek, který vysvětluje všechny požadované součásti, včetně operačních systémů, které jsou podporované.

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
| vmaccessforlinux. Microsoft. ostcextensions | [Resetování hesla pro Linux](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Snímek pro Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Snímek pro Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Další kroky
* Další informace o fungování agenta a rozšíření pro Linux najdete v tématu [rozšíření a funkce virtuálních počítačů Azure pro Linux](features-linux.md).
* Další informace o tom, jak funguje Agent hosta a rozšíření systému Windows, najdete v tématu [rozšíření a funkce virtuálních počítačů Azure pro Windows](features-windows.md).  
* Informace o instalaci agenta hosta systému Windows najdete v tématu [Přehled agenta virtuálního počítače Azure s Windows](agent-windows.md).  
* Informace o instalaci agenta pro Linux najdete v tématu [Přehled agenta virtuálních počítačů Azure Linux](agent-linux.md).  

