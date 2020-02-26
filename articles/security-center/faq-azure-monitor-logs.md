---
title: Nejčastější dotazy k Azure Security Center – otázky týkající se existujících modelů
description: Tato Nejčastější dotazy vám odpoví na otázky pro zákazníky, kteří už používají Microsoft Monitoring Agent a zvažuje Azure Security Center, produkt, který pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně.
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
ms.openlocfilehash: 528ff47be2b18cb7d9b938e988383a9e81be67fb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599506"
---
# Nejčastější dotazy pro zákazníky, kteří už používají protokoly Azure Monitor<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center přepsat existující připojení mezi virtuálními počítači a pracovním prostorům?

Pokud je virtuální počítač už Microsoft Monitoring Agent nainstalován jako rozšíření Azure, Security Center nemůže přepsat existující připojení pracovního prostoru. Security Center místo toho využívá existující pracovní prostor. Virtuální počítač bude chráněn za předpokladu, že řešení "Security" nebo "SecurityCenterFree" bylo nainstalováno do pracovního prostoru, do kterého se hlásí. 

Řešení Security Center je nainstalováno v pracovním prostoru vybraném na obrazovce shromažďování dat, pokud již není přítomno, a toto řešení je použito pouze pro příslušné virtuální počítače. Když přidáte řešení, se automaticky nasadí ve výchozím nastavení pro všechny agenty Windows a Linuxem, připojený k pracovnímu prostoru Log Analytics. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) umožňuje použít obor pro vaše řešení.

Pokud agenta Microsoft Monitoring Agent je nainstalovaný přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center není možné nainstalovat agenta Microsoft Monitoring Agent a monitorování zabezpečení je omezený.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center se nenainstaluje řešení v mé existující pracovní prostory Log Analytics? Jaké jsou důsledky fakturace?
Když Security Center identifikuje, že virtuální počítač je již připojen k pracovnímu prostoru, který jste vytvořili, Security Center umožňuje řešení na tento pracovní prostor podle cenové úrovně. Řešení se aplikují jenom na relevantní virtuální počítače Azure, a to prostřednictvím [cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md), takže účtování zůstane stejné.

- **Úroveň Free** – Security Center do pracovního prostoru nainstaluje řešení ' SecurityCenterFree '. Za bezplatnou úroveň se vám nebude účtovat.
- **Úroveň Standard** – Security Center do pracovního prostoru nainstaluje řešení zabezpečení.

   ![Řešení na výchozího pracovního prostoru][1]

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Už mám pracovní prostory v Moje prostředí, můžu využít k shromažďování dat zabezpečení?
Pokud je virtuální počítač už Microsoft Monitoring Agent nainstalován jako rozšíření Azure, Security Center používá existující připojený pracovní prostor. V pracovním prostoru je nainstalováno Security Center řešení, pokud již není přítomno a řešení se používá pouze pro příslušné virtuální počítače prostřednictvím [cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md).

Security Center nainstaluje agenta Microsoft Monitoring Agent na virtuálních počítačích, použije výchozí pracovních prostorů vytvořených službou Security Center.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Už mám řešení zabezpečení na Moje pracovní prostory. Jaké jsou důsledky fakturace?
Řešení zabezpečení a Audit se používá k povolení funkce Security Center úrovně Standard úrovně pro virtuální počítače Azure. Pokud řešení Security and Audit je již nainstalován v pracovním prostoru, Security Center používá existující řešení. Není žádná změna ve fakturaci.


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/solutions.png