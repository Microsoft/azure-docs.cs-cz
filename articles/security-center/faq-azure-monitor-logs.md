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
ms.openlocfilehash: 1a52b2fec6155959a570f2438a59c14d9f79f368
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701956"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Nejčastější dotazy pro zákazníky, kteří už používají protokoly Azure Monitor<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Přepíší Security Center všechna existující připojení mezi virtuálními počítači a pracovními prostory?

Pokud už má virtuální počítač Log Analytics agenta nainstalovaného jako rozšíření Azure, Security Center existující připojení k pracovnímu prostoru nepřepisuje. Místo toho Security Center používá existující pracovní prostor. Virtuální počítač bude chráněn za předpokladu, že řešení "Security" nebo "SecurityCenterFree" bylo nainstalováno do pracovního prostoru, do kterého se hlásí. 

Řešení Security Center je nainstalováno v pracovním prostoru vybraném na obrazovce shromažďování dat, pokud již není přítomno, a toto řešení je použito pouze pro příslušné virtuální počítače. Když přidáte řešení, automaticky se nasadí ve výchozím nastavení na všechny agenty systému Windows a Linux připojené k vašemu pracovnímu prostoru Log Analytics. [Cílení na řešení](../azure-monitor/insights/solution-targeting.md) umožňuje použít obor pro vaše řešení.

> [!TIP]
> Pokud je agent Log Analytics nainstalovaný přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center agenta Log Analytics nenainstaluje a monitorování zabezpečení je omezené.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Je Security Center instalovat řešení do stávajících pracovních prostorů Log Analytics? Jaké jsou důsledky fakturace?
Když Security Center identifikuje, že je virtuální počítač už připojený k pracovnímu prostoru, který jste vytvořili, Security Center umožňuje řešení v tomto pracovním prostoru podle vaší cenové konfigurace. Řešení se aplikují jenom na relevantní virtuální počítače Azure, a to prostřednictvím [cílení na řešení](../azure-monitor/insights/solution-targeting.md), takže účtování zůstane stejné.

- **Azure Defender off** – Security Center do pracovního prostoru nainstaluje řešení "SecurityCenterFree". Nebudeme se vám účtovat.
- **Azure Defender on** – Security Center do pracovního prostoru nainstaluje řešení zabezpečení.

   ![Řešení ve výchozím pracovním prostoru](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Již mám v prostředí pracovní prostory, můžu je použít ke shromažďování dat zabezpečení?
Pokud už má virtuální počítač Log Analytics agenta nainstalovaného jako rozšíření Azure, Security Center používá existující připojený pracovní prostor. V pracovním prostoru je nainstalováno Security Center řešení, pokud již není přítomno a řešení se používá pouze pro příslušné virtuální počítače prostřednictvím [cílení na řešení](../azure-monitor/insights/solution-targeting.md).

Když Security Center nainstaluje agenta Log Analytics na virtuální počítače, použije výchozí pracovní prostory vytvořené Security Center, pokud Security Center neukazuje na existující pracovní prostor.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Již mám řešení zabezpečení v pracovních prostorech. Jaké jsou důsledky fakturace?
Řešení auditu & zabezpečení se používá k povolení **Azure Defenderu pro servery**. Pokud je v pracovním prostoru již nainstalováno řešení auditu zabezpečení &, Security Center používá existující řešení. Fakturace se nemění.