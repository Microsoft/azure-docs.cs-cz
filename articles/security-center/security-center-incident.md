---
title: Zpracování výstrah zabezpečení ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže používat funkce služby Azure Security Center k řešení bezpečnostních incidentů.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e8feb669-8f30-49eb-ba38-046edf3f9656
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2018
ms.author: rkarlin
ms.openlocfilehash: 68bcd2b1916ccdf68eaa31ed251661a6b7e1bca0
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106779"
---
# <a name="handling-security-incidents-in-azure-security-center"></a>Řešení bezpečnostních incidentů v Azure Security Center
Třídění a prošetřování výstrah zabezpečení může být časově náročné i pro nejzkušenější analytiky zabezpečení, a mnoho z nich dokonce ani pořádně neví, kde začít. Služba Security Center pomocí [analýzy](security-center-detection-capabilities.md) spojuje informace mezi odlišnými [výstrahami zabezpečení](security-center-managing-and-responding-alerts.md) a dokáže vám tak poskytnout ucelený přehled o útočné kampani a všech souvisejících výstrahách. Vy tak rychle porozumíte, jaké akce útočník podniká a které prostředky byly zasaženy.

Tento dokument popisuje, jak vám funkce výstrah zabezpečení služby Security Center mohou pomoci řešit bezpečnostní incidenty.

## <a name="what-is-a-security-incident"></a>Co je bezpečnostní incident?
Ve službě Security Center představuje bezpečnostní incident souhrn všech výstrah pro určitý prostředek, které odpovídají schématům [modelu kill chain](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/). Incidenty se zobrazí na dlaždici a v okně [Výstrahy zabezpečení](security-center-managing-and-responding-alerts.md). Incident odhalí seznam souvisejících výstrah, který vám umožní získat další informace o každém výskytu.

## <a name="managing-security-incidents"></a>Správa incidentů zabezpečení
Aktuální incidenty zabezpečení můžete zkontrolovat jejich prohlížením na dlaždici Výstrahy zabezpečení. Přejděte na web Azure Portal a podle následujících pokynů zobrazte podrobnosti o jednotlivých incidentech zabezpečení:

1. Na řídicím panelu Security Center uvidíte dlaždici **Výstrahy zabezpečení**.

    ![Dlaždice Výstrahy zabezpečení ve službě Security Center](./media/security-center-incident/security-center-incident-fig1.png)

2. Klikněte na tuto dlaždici a rozbalte ji. Pokud je zjištěn incident zabezpečení, zobrazí se v grafu výstrah zabezpečení, jak vidíte níže:

    ![Incident zabezpečení](./media/security-center-incident/security-center-incident-fig2.png)

3. Popis incidentu zabezpečení má v porovnání s ostatními výstrahami jinou ikonu. Po kliknutí na ikonu se zobrazí podrobnosti o daném incidentu.

    ![Incident zabezpečení](./media/security-center-incident/security-center-incident-fig3.png)

4. Na dlaždici **Incident** se zobrazí další podrobnosti o daném incidentu zabezpečení, které obsahují úplný popis, závažnost (která je v tomto případě vysoká), aktuální stav (v tomto případě je stále *aktivní*, což naznačuje, že uživatel ještě nepodnikl žádné akce – to lze provést kliknutím pravým tlačítkem na incident v okně **Výstrahy zabezpečení**), napadené prostředky (v tomto případě *VM1*), postup nápravy incidentu a v dolním podokně se nachází výstrahy, které byly součástí tohoto incidentu. Pokud chcete získat další informace o jednotlivých výstrahách, klikněte na ně a otevře se další okno, jak vidíte níže:

    ![Incident zabezpečení](./media/security-center-incident/security-center-incident-fig4.png)

Informace v tomto okně se budou lišit podle výstrah. Další informace o řešení těchto výstrah najdete v tématu [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md). Některé důležité informace týkající se této funkce:

* Nový filtr umožňuje přizpůsobit zobrazení na Pouze incident, Pouze výstrahy nebo obojí.
* Stejná výstraha může existovat jako součást incidentu (pokud se to na ni vztahuje) a zároveň může být viditelná jako samostatná výstraha.

## <a name="see-also"></a>Další informace najdete v tématech
V tomto dokumentu jste zjistili, jak ve službě Azure Security Center používat funkci incidentů zabezpečení. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Funkce detekce ve službě Azure Security Center](security-center-detection-capabilities.md)
* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md)
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
