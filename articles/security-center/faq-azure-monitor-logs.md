---
title: Nejčastější dotazy centra zabezpečení Azure – dotazy týkající se existujících mma
description: Tyto nejčastější dotazy odpovídají na otázky pro zákazníky, kteří už používají Microsoft Monitoring Agent a vzhledem k Azure Security Center, produkt, který vám pomůže předcházet, detekovat a reagovat na hrozby.
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
ms.openlocfilehash: 5c433140c3982813e372fd3f63243a96197d220c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661885"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Nejčastější dotazy pro zákazníky, kteří už používají protokoly Azure Monitoru<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Přepíše Centrum zabezpečení všechna existující připojení mezi virtuálními aplikacemi a pracovními prostory?

Pokud virtuální počítač už má Microsoft Monitoring Agent nainstalován jako rozšíření Azure, Security Center nepřepíše existující připojení pracovního prostoru. Místo toho Security Center používá existující pracovní prostor. Virtuální počítač bude chráněn za předpokladu, že "Zabezpečení" nebo "SecurityCenterFree" řešení byla nainstalována v pracovním prostoru, do kterého je vykazování. 

Řešení Security Center je nainstalováno v pracovním prostoru vybraném na obrazovce Shromažďování dat, pokud již není k dispozici, a řešení se použije pouze na příslušné virtuální počítačky. Když přidáte řešení, ve výchozím nastavení se automaticky nasadí na všechny agenty Windows a Linux připojených k pracovnímu prostoru Log Analytics. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) umožňuje použít obor pro vaše řešení.

> [!TIP]
> Pokud je agent monitorování Microsoftu nainstalovaný přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center nenainstaluje agenta monitorování Microsoftu a monitorování zabezpečení je omezené.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Instaluje Centrum zabezpečení řešení do stávajících pracovních prostorů Služby Log Analytics? Jaké jsou důsledky fakturace?
Když Security Center zjistí, že virtuální počítač je už připojený k pracovnímu prostoru, který jste vytvořili, Security Center povolí řešení v tomto pracovním prostoru podle vaší cenové úrovně. Řešení se použijí jenom na příslušné virtuální počítače Azure prostřednictvím [cílení řešení](../operations-management-suite/operations-management-suite-solution-targeting.md), takže fakturace zůstane stejná.

- **Bezplatná úroveň** – Security Center nainstaluje řešení SecurityCenterFree do pracovního prostoru. Úroveň Free se vám nebude účtovat.
- **Standardní úroveň** – Security Center nainstaluje řešení zabezpečení do pracovního prostoru.

   ![Řešení výchozího pracovního prostoru](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Pracovní prostory již mám ve svém prostředí, mohu je použít ke shromažďování bezpečnostních dat?
Pokud virtuální počítač už má Microsoft Monitoring Agent nainstalován jako rozšíření Azure, Security Center používá existující připojený pracovní prostor. Řešení Security Center je nainstalováno v pracovním prostoru, pokud již není k dispozici, a řešení se použije pouze na příslušné virtuální počítače prostřednictvím [cílení řešení](../operations-management-suite/operations-management-suite-solution-targeting.md).

Když Security Center nainstaluje Microsoft Monitoring Agent na virtuálních počítačích, používá výchozí pracovní prostor (y) vytvořené Security Center.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Již mám řešení zabezpečení na svých pracovních prostorech. Jaké jsou důsledky fakturace?
Řešení zabezpečení & auditu se používá k povolení funkcí standardní úrovně Security Center pro virtuální počítače Azure. Pokud je řešení & audit zabezpečení již nainstalováno v pracovním prostoru, centrum zabezpečení používá existující řešení. Fakturace se nemění.