---
title: Použití služby Azure Security Center doporučení k vylepšení zabezpečení | Dokumentace Microsoftu
description: " Další informace o použití zásady a doporučení zabezpečení v Azure Security Center můžete zmírnit útok na zabezpečení. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/2/2019
ms.author: rkarlin
ms.openlocfilehash: 5ff59a9ed7dc44bb7a4176f7a174be1e7d0ec9b6
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104902"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Použití služby Azure Security Center doporučení k vylepšení zabezpečení
Tak, že konfigurace zásad zabezpečení a pak implementace doporučení poskytovaných službou Azure Security Center můžete snížit riziko zabezpečení významné události. V tomto článku se dozvíte, jak používat zásady zabezpečení a doporučení ve službě Security Center můžete zmírnit útok na zabezpečení.

Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných bezpečnostní kontroly.

## <a name="scenario"></a>Scénář
Tento scénář popisuje, jak používat Security Center Chcete-li snížit riziko bezpečnostních incidentů monitorování doporučení služby Security Center a provádění akcí. Tento scénář používá fiktivní společnosti, Contoso a rolí uváděné v Centru zabezpečení [Průvodce plánováním a provozem](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). V tomto scénáři Zaměřujeme na role následujících osob:

![Scénář role](./media/security-center-using-recommendations/scenario-roles.png)

Contoso nedávno provedla migraci některých místních prostředků do Azure. Contoso chce chránit svoje prostředky a snížit ohrožení zabezpečení svých prostředků v cloudu.

## <a name="use-azure-security-center"></a>Použití Azure Security Center
David ze společnosti Contoso na zabezpečení IT, má již vybrána k začlenění Security Center na předplatná společnosti Contoso ke službě Azure Security Center k zabránění a zjišťování ohrožení zabezpečení. 

Security Center automaticky analyzuje stav zabezpečení prostředků Azure společnosti Contoso a použije výchozí zásady zabezpečení. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří **doporučení** podle nastavení v zásadách zabezpečení ovládací prvky. 

David spustí standardní vrstva zabezpečení Azure, ve všech svých předplatných zobrazíte kompletní sadu doporučení a funkce zabezpečení, které jsou k dispozici. Jan také podporu připojí všechny jeho existujících místních serverů, které se ještě nemigrovaly do cloudu tak, aby mohl využívat hybridní Security Center v rámci jeho [Windows](quick-onboard-windows-computer.md) a [Linux](quick-onboard-linux-computer.md) servery.

Jan je vlastníkem úlohy v cloudu. Jan je zodpovědná za použití kontrolních mechanismů pro zabezpečení v souladu se zásadami zabezpečení společnosti Contoso. 

Jan provede následující úlohy:

- Doporučení pro monitorování zabezpečení poskytne Security Center
- Vyhodnocení doporučení týkající se zabezpečení a rozhodnout, jestli má použít nebo zavřít
- Použití doporučení k zabezpečení

### <a name="remediate-threats-using-recommendations"></a>Opravit hrozby pomocí doporučení
Jako součást své denní aktivity sledování Jeff přihlásí do Azure a otevře službu Security Center. 

1. Jan vybere předplatné jeho zatížení.

2. Jan zkontroluje jeho **zabezpečení skóre** získat celkový obrázek zabezpečené předplatná jsou a zjistí, že je jeho skóre 548.

3. Jan má rozhodnout, která doporučení pro zpracování první. Aby Jan klikne na zabezpečené skóre a začne zpracovávat doporučení v závislosti na tom, kolik zvyšuje jeho [zabezpečení skóre dopad](security-center-secure-score.md).

4. Protože Jan má velké množství připojených virtuálních počítačů a serverů, Jeff rozhodne zaměřit na **výpočetní prostředky a aplikace**.

5. Když Jan klikne **výpočetní prostředky a aplikace**, mohl se zobrazí seznam doporučení a zpracovává podle má zabezpečení skóre dopad.

6. Jan má mnoho internetového virtuální počítače, a protože jejich porty jsou přístupné, je obavy, že útočník by mohl získat kontrolu nad servery. Aby Jan rozhodne použít (**přístup k virtuálním počítačům just-in-time**) [zabezpečení center-just v time.md].

Jan pokračuje procházení s vysokou prioritou a doporučení se střední prioritou a vytváří rozhodnutí na implementaci. Pro jednotlivá doporučení Jeff zjistí podrobné informace o zadaný službou Security Center k pochopení, které prostředky se to týká, jaký vliv má zabezpečené skóre je, co každý znamená, že doporučení a nápravné kroky pro každý problém se dá zmírnit.

## <a name="conclusion"></a>Závěr
Monitorování doporučení ve službě Security Center pomáhá eliminovat ohrožení zabezpečení, než dojde k útoku. Když jste opravovat doporučení, zlepšit zabezpečené skóre a stavu zabezpečení vašich úloh. Security Center automaticky zjišťuje nové prostředky, nasazení, vyhodnocuje proti vaší zásady zabezpečení a poskytuje nové doporučení pro jejich zabezpečení.


## <a name="next-steps"></a>Další postup
Ujistěte se, že máte proces monitorování na místě, ve kterém můžete pravidelně kontrolovat doporučení ve službě Security Center tak, že zajistíte bezpečnost vašich prostředků v čase.

Tento scénář vám ukázali, jak používat zásady zabezpečení a doporučení ve službě Security Center můžete zmírnit útok na zabezpečení. Zobrazit [scénáři reakce na incidenty](security-center-incident-response.md) se naučíte být při reakci na incidenty připravený plán předtím, než k útoku dojde.

Zjistěte, jak reagovat na hrozby s využitím [reakce na incidenty](security-center-incident-response.md).
