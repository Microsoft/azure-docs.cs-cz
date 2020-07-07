---
title: Nejčastější dotazy k Azure Security Center – otázky týkající se existujících agentů Log Analytics
description: Tato Nejčastější dotazy vám odpoví na otázky pro zákazníky, kteří už používají agenta Log Analytics a zvažuje Azure Security Center, produkt, který pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně.
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
ms.openlocfilehash: 2fe306cf7d17f0789c5e134c3fcad3f8f07a0b80
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82612822"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Nejčastější dotazy pro zákazníky, kteří už používají protokoly Azure Monitor<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Přepíší Security Center všechna existující připojení mezi virtuálními počítači a pracovními prostory?

Pokud už má virtuální počítač Log Analytics agenta nainstalovaného jako rozšíření Azure, Security Center existující připojení k pracovnímu prostoru nepřepisuje. Místo toho Security Center používá existující pracovní prostor. Virtuální počítač bude chráněn za předpokladu, že řešení "Security" nebo "SecurityCenterFree" bylo nainstalováno do pracovního prostoru, do kterého se hlásí. 

Řešení Security Center je nainstalováno v pracovním prostoru vybraném na obrazovce shromažďování dat, pokud již není přítomno, a toto řešení je použito pouze pro příslušné virtuální počítače. Když přidáte řešení, automaticky se nasadí ve výchozím nastavení na všechny agenty systému Windows a Linux připojené k vašemu pracovnímu prostoru Log Analytics. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) umožňuje použít obor pro vaše řešení.

> [!TIP]
> Pokud je agent Log Analytics nainstalovaný přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center agenta Log Analytics nenainstaluje a monitorování zabezpečení je omezené.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Je Security Center instalovat řešení do stávajících pracovních prostorů Log Analytics? Jaké jsou důsledky fakturace?
Když Security Center identifikuje, že je virtuální počítač už připojený k pracovnímu prostoru, který jste vytvořili, Security Center umožňuje řešení v tomto pracovním prostoru podle vaší cenové úrovně. Řešení se aplikují jenom na relevantní virtuální počítače Azure, a to prostřednictvím [cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md), takže účtování zůstane stejné.

- **Úroveň Free** – Security Center do pracovního prostoru nainstaluje řešení ' SecurityCenterFree '. Za bezplatnou úroveň se vám nebude účtovat.
- **Úroveň Standard** – Security Center do pracovního prostoru nainstaluje řešení zabezpečení.

   ![Řešení ve výchozím pracovním prostoru](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Již mám v prostředí pracovní prostory, můžu je použít ke shromažďování dat zabezpečení?
Pokud už má virtuální počítač Log Analytics agenta nainstalovaného jako rozšíření Azure, Security Center používá existující připojený pracovní prostor. V pracovním prostoru je nainstalováno Security Center řešení, pokud již není přítomno a řešení se používá pouze pro příslušné virtuální počítače prostřednictvím [cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md).

Když Security Center nainstaluje agenta Log Analytics na virtuální počítače, použije výchozí pracovní prostory vytvořené Security Center, pokud Security Center neukazuje na existující pracovní prostor.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Již mám řešení zabezpečení v pracovních prostorech. Jaké jsou důsledky fakturace?
Řešení auditu & zabezpečení se používá k povolení funkcí služby Security Center úrovně Standard pro virtuální počítače Azure. Pokud je v pracovním prostoru již nainstalováno řešení auditu zabezpečení &, Security Center používá existující řešení. Fakturace se nemění.
