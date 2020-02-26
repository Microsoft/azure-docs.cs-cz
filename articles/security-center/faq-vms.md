---
title: Nejčastější dotazy k Azure Security Center – otázky k virtuálním počítačům
description: Nejčastější dotazy k virtuálním počítačům v Azure Security Center, produkt, který pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně
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
ms.openlocfilehash: d84085115816a8fe1cba65e191ea391dd91a4aed
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599363"
---
# <a name="faq---questions-about-virtual-machines"></a>Nejčastější dotazy týkající se virtuálních počítačů


## <a name="what-types-of-virtual-machines-are-supported"></a>Jaké typy virtuálních počítačů jsou podporovány?

Monitorování a doporučení jsou k dispozici pro virtuální počítače vytvořené pomocí [modelu nasazení Classic i správce prostředků](../azure-classic-rm.md).

Seznam podporovaných platforem najdete [v tématu podporované platformy v Azure Security Center](security-center-os-coverage.md) .


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Proč Azure Security Center nerozpoznal antimalwarové řešení, které běží na mém virtuálním počítači Azure?

Azure Security Center obsahuje přehled antimalwarové nainstalované prostřednictvím rozšíření Azure. Například není Security Center schopna zjistit Antimalwarový program, který byl předem nainstalované na obrázku, který jste zadali, nebo pokud jste nainstalovali antimalwarové na vašich virtuálních počítačů pomocí vlastních procesů (jako jsou systémy pro správu konfigurace).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Proč se zobrazila zpráva "Chybí Data kontroly" pro můj virtuální počítač?

Tato zpráva se zobrazí, pokud na virtuálním počítači nejsou žádná data kontroly. Naplnění dat kontroly po povolení shromažďování dat ve službě Azure Security Center může nějakou chvíli trvat (méně než hodinu). Tato zpráva se vám může zobrazit po počátečním naplnění dat kontroly, protože neexistují vůbec žádná data kontroly ani žádná nová data kontroly. Kontroly se nenaplňují u virtuálních počítačů v zastaveném stavu. Tato zpráva se může zobrazit, pokud nebyla zadána data kontroly nedávno (v souladu se zásadami uchovávání informací pro agenta Windows, což je výchozí hodnota 30 dnů).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak často Security Center zjišťovat existenci ohrožení zabezpečení operačního systému, aktualizací systému a problémy s endpoint protection?

Níže jsou uvedeny doby latence Security Center prověřování chyb zabezpečení, aktualizací a problémů:

- Konfigurace zabezpečení operačního systému – data se aktualizuje do 48 hodin
- Aktualizace systému – data se aktualizuje během 24 hodin
- Problémy s Endpoint Protection – data se aktualizují v 8 hodin

Security Center obvykle kontroluje dostupnost nových dat. každou hodinu a odpovídajícím způsobem aktualizuje doporučení. 

> [!NOTE]
> Security Center používá Microsoft Monitoring Agent ke shromažďování a ukládání dat. Další informace najdete v tématu [migrace platformy Azure Security Center](security-center-platform-migration.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Proč se zobrazila zpráva "Agent virtuálního počítače je chybějící?"

Agent virtuálního počítače musí být nainstalovaný na virtuálních počítačích povolíte shromažďování dat. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. Informace o tom, jak nainstalovat agenta virtuálního počítače na jiné virtuální počítače, najdete v blogovém příspěvku [agenta a rozšíření virtuálního počítače](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).