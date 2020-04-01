---
title: Nejčastější dotazy k Centru zabezpečení Azure – dotazy týkající se virtuálních počítačů
description: Nejčastější dotazy týkající se virtuálních počítačů v Azure Security Center, produktu, který vám pomůže předcházet hrozbám, zjišťovat je a reagovat na ně.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 0b7817539a26b7ebfa24efd086edf8b0ef82eae3
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436014"
---
# <a name="faq---questions-about-virtual-machines"></a>Časté otázky – otázky týkající se virtuálních počítačů


## <a name="what-types-of-virtual-machines-are-supported"></a>Jaké typy virtuálních počítačů jsou podporovány?

Monitorování a doporučení jsou k dispozici pro virtuální počítače (VM) vytvořené pomocí [klasických modelů nasazení správce prostředků](../azure-classic-rm.md).

Seznam podporovaných platforem najdete v tématu [Podporované platformy v Centru zabezpečení Azure.](security-center-os-coverage.md)


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Proč Azure Security Center nerozpozná antimalwarové řešení spuštěné na mém virtuálním počítači Azure?

Azure Security Center má přehled o antimalwaru nainstalovaných prostřednictvím rozšíření Azure. Centrum zabezpečení například není schopno detekovat antimalwarový software, který byl předinstalován na bitové kopii, kterou jste zadali, nebo pokud jste antimalwarový program nainstalovali do virtuálních počítačů pomocí vlastních procesů (například systémů správy konfigurace).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Proč se mi zobrazuje zpráva Chybějící data skenování pro můj virtuální virtuální virtuální hod?

Tato zpráva se zobrazí, pokud na virtuálním počítači nejsou žádná data kontroly. Naplnění dat kontroly po povolení shromažďování dat ve službě Azure Security Center může nějakou chvíli trvat (méně než hodinu). Tato zpráva se vám může zobrazit po počátečním naplnění dat kontroly, protože neexistují vůbec žádná data kontroly ani žádná nová data kontroly. Kontroly se nenaplňují u virtuálních počítačů v zastaveném stavu. Tato zpráva se může zobrazit také v případě, že data skenování nebyla naplněna v poslední době (v souladu se zásadami uchovávání informací pro agenta systému Windows, který má výchozí hodnotu 30 dní).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak často Security Center hledá chyby zabezpečení operačního systému, aktualizace systému a problémy s ochranou koncového bodu?

Níže jsou uvedeny časy latence pro centrum zabezpečení prohledává slabá místa, aktualizace a problémy:

- Konfigurace zabezpečení operačního systému – data jsou aktualizována do 48 hodin
- Aktualizace systému – data jsou aktualizována do 24 hodin
- Problémy s ochranou koncového bodu – data jsou aktualizována do 8 hodin

Security Center obvykle prohledá nová data každou hodinu a odpovídajícím způsobem aktualizuje doporučení. 

> [!NOTE]
> Security Center používá agenta Log Analytics ke shromažďování a ukládání dat. Další informace najdete v [tématu Migrace platformy Azure Security Center](security-center-platform-migration.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Proč se zobrazuje zpráva "Agent virtuálního soudu chybí?"

Agent virtuálního počítače musí být nainstalovaný na virtuálních počítačích, aby bylo možné povolit shromažďování dat. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. Informace o tom, jak nainstalovat agenta virtuálního počítače na jiné virtuální počítač, najdete v tématu blog post [Agent a rozšíření](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).