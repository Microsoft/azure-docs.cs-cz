---
title: Ukázka řešení Service Map v Azure vlastním tempem | Microsoft Docs
description: Service Map je řešení v Azure, které automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi těmito službami. Tato ukázka vlastním tempem vás provede použitím řešení Service Map a umožní vám identifikovat a diagnostikovat simulovaný problém ve webové aplikaci.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: monitoring
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: b406cb2a9e96e3ba3514ed08c20483df57de9c71
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="self-paced-demo---service-map"></a>Ukázka vlastním tempem – Service Map
Tato ukázka vlastním tempem vás provede použitím [řešení Service Map](monitoring-service-map.md) v Azure a umožní vám identifikovat a diagnostikovat simulovaný problém ve webové aplikaci. Service Map automaticky rozpozná komponenty aplikace v systémech Windows a Linux a mapuje komunikaci mezi službami. Také konsoliduje data shromážděná ostatními službami a řešeními a pomáhá analyzovat výkon a identifikovat případné potíže. Můžete také využít [prohledávání protokolu ve službě Log Analytics](../log-analytics/log-analytics-log-searches.md) a přejít k podrobnostem shromážděných dat s cílem identifikovat hlavní problém.


## <a name="scenario-description"></a>Popis scénáře
Právě jste dostali oznámení, že aplikace ACME Customer Portal má potíže s výkonem. Jedinou informací, kterou máte, je, že tyto potíže začaly dnes asi ve 4:00 ráno PST. Nejste si úplně jistí všemi komponentami, na kterých portál závisí (kromě sady webových serverů). 

## <a name="components-and-features-used"></a>Použité komponenty a funkce
- [Řešení Service Map](monitoring-service-map.md)
- [Prohledávání protokolu služby Log Analytics](../log-analytics/log-analytics-log-searches.md)


## <a name="walkthrough"></a>Názorný postup

### <a name="1-connect-to-the-oms-experience-center"></a>1. Připojení k centru OMS Experience Center
Tento názorný postup vás provede použitím centra [Operations Management Suite Experience Center](https://experience.mms.microsoft.com/), které poskytuje kompletní prostředí Log Analytics s ukázkovými daty. Začněte tím, že použijete tento odkaz a zadáte informace, a potom vyberte scénář **Insight and Analytics**.


### <a name="2-start-service-map"></a>2. Spuštění řešení Service Map
Řešení Service Map spustíte tak, že klikněte na dlaždici **Service Map**.

![Dlaždice Service Map](media/monitoring-walkthrough-servicemap/tile.png)

Zobrazí se konzola Service Map. V levém podokně je uvedený seznam počítačů ve vašem prostředí s nainstalovaným agentem Service Map. V tomto seznamu vyberete počítač, který chcete zobrazit.

![Seznam počítačů](media/monitoring-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3. Zobrazení počítače
Víte, že webové servery mají názvy AcmeWFE001 a AcmeWFE002, takže bude asi rozumné začít s nimi. Klikněte na **AcmeWFE001**. Zobrazí se mapa pro AcmeWFE001 a všechny jeho závislosti. Uvidíte, které procesy běží na vybraném počítači a se kterými externími službami komunikuje.

![Webový server](media/monitoring-walkthrough-servicemap/web-server.png)

Zajímá vás výkon webové aplikace, proto klikněte na proces **AcmeAppPool (Fond aplikací služby IIS)**. Zobrazí se podrobnosti pro tento proces a zvýrazní se jeho závislosti. 

![Fond aplikací](media/monitoring-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4. Změna časového intervalu

Slyšeli jste, že problém začal ve 4:00, takže se podíváme, co se v té době dělo. Klikněte na **Časový rozsah** a změňte čas na 4:00 PST (ponechte aktuální datum a proveďte úpravu pro vaši časovou zónu) s délkou 20 minut.

![Výběr času](./media/monitoring-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5. Zobrazení výstrahy

Vidíte, že u závislosti **acmetomcat** je zobrazená výstraha, takže tohle je asi potenciální problém. Kliknutím na ikonu výstrahy v **acmetomcat** zobrazte podrobnosti o této výstraze. Vidíte, že máte kritické využití procesoru, a je možné rozbalit další podrobnosti. Toto je pravděpodobná příčina nízkého výkonu. 

![Výstrahy](./media/monitoring-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6. Zobrazení výkonu

Podívejme se na **acmetomcat** blíž. Klikněte v pravém horním rohu závislosti **acmetomcat** a vyberte **Načíst serverovou mapu**. Zobrazí se detaily a závislosti pro tento počítač. Teď se můžete na tyto čítače výkonu podívat podrobněji a potvrdit svoje podezření. Vyberte kartu **Výkon**. Zobrazí se [čítače výkonu shromážděné službou Log Analytics](../log-analytics/log-analytics-data-sources-performance-counters.md) v příslušném časovém rozsahu. Vidíte, že u procesoru a paměti se pravidelně opakují špičky.

![Výkon](./media/monitoring-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7. Zobrazení sledování změn
Podívejme se, jestli můžeme zjistit příčinu tohoto vysokého využití. Klikněte na kartu **Souhrn**. Poskytuje informace, které služba Log Analytics získala z tohoto počítače, jako jsou neúspěšná připojení, kritické výstrahy a změny softwaru. Oddíly se souvisejícími informacemi z nedávné doby by už měly být rozbalené. Můžete rozbalit i další sekce a prohlédnout si informace, které obsahují.


Pokud ještě není otevřené **Sledování změn**, rozbalte ho. Zobrazuje informace shromážděné [řešením Change Tracking](../log-analytics/log-analytics-change-tracking.md). Vypadá to, že se během tohoto časového intervalu změnil software. Podrobnosti zobrazíte kliknutím na **Software**. Do počítače se právě kolem 4:00 ráno přidal proces zálohování a právě ten asi bude příčinou nadměrné spotřeby prostředků.

![Sledování změn](./media/monitoring-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8. Zobrazení podrobností v prohledávání protokolu
Můžete to dál ověřit tak, že se podíváte na podrobné informace o výkonu, které jsou shromážděné v pracovním prostoru Log Analytics. Klikněte znovu na kartu **Výstrahy** a potom klikněte na jednu z výstrah **Vysoké využití procesoru**. Klikněte na **Zobrazit v Hledání v protokolu**. Otevře se okno Prohledávání protokolu, kde můžete využít [prohledávání protokolu](../log-analytics/log-analytics-log-searches.md) pro libovolná data uložená v pracovním prostoru. Řešení Service Map pro nás už vytvořilo dotaz pro načtení výstrahy, která nás zajímá. 

![Prohledávání protokolů](./media/monitoring-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9. Otevření uloženého hledání
Podívejme se, jestli můžeme o sběru údajů o výkonu, který tuto výstrahu způsobil, zjistit něco víc a ověřit si podezření, že problémy jsou způsobené právě tímto procesem zálohování. Změňte časový rozsah na **6 hodin**. Potom klikněte na **Oblíbené** a posuňte se dolů na uložená hledání pro **Service Map**. Tyto dotazy jste vytvořili speciálně pro tuto analýzu. Klikněte na **Top 5 Processes by CPU for acmetomcat** (Hlavních 5 procesů podle CPU pro acmetomcat).

![Uložené hledání](./media/monitoring-walkthrough-servicemap/saved-search.png)


Tento dotaz vrátí seznam 5 procesů které nejvíc využívají procesor, pro **acmetomcat**. Můžete si tento dotaz prohlédnout, abyste získali představu o dotazovacím jazyku, který se pro prohledávání protokolu používá. Pokud by vás zajímaly procesy v jiných počítačích, můžete tento dotaz změnit tak, aby načítal příslušné informace.

V tomto případě vidíte, že proces zálohování konzistentně využívá přibližně 60 % kapacity procesoru aplikačního serveru. Je tedy zřejmé, že problémy s výkonem způsobuje právě tento nový proces. Zjevným řešením by teď bylo odebrání tohoto nového zálohovacího softwaru z aplikačního serveru. Mohli byste využít platformu Desired State Configuration spravovanou službou Azure Automation a definovat zásady, které zajistí, že se tento proces v těchto důležitých systémech nikdy nespustí.


## <a name="summary-points"></a>Souhrn v bodech
- [Mapa služeb](monitoring-service-map.md) poskytuje přehled celé aplikace i v případě, že nevíte o všech serverech a závislostech.
- Service Map poskytuje informace o datech, která shromáždila ostatní řešení pro správu, a pomáhá odhalit potíže s aplikací a její podpůrnou infrastrukturou.
- [Prohledávání protokolu](../log-analytics/log-analytics-log-searches.md) umožňuje přejít k podrobnostem specifických dat shromážděných v pracovním prostoru Log Analytics.  

## <a name="next-steps"></a>Další kroky
- Přečtěte si víc o řešení [Service Map](monitoring-service-map.md).
- [Nasaďte a nakonfigurujte](monitoring-service-map-configure.md) Service Map.
- Přečtěte si víc o službě [Log Analytics](../log-analytics/log-analytics-overview.md), která se pro Service Map vyžaduje a ve které jsou uložená provozní data ukládaná agenty.