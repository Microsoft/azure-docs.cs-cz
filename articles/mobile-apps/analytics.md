---
title: Vysvětlení využití mobilních aplikací a chování uživatelů pomocí Visual Studio App Center a služeb Azure
description: Seznamte se se službami, jako je App Center, které vám pomohou při rozhodování o chytrých firmách tím, že porozumíte tomu, jak uživatelé používají vaši mobilní aplikaci.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241082"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analýza a pochopení používání mobilních aplikací
Jak dobře rozumíte tomu, jak uživatelé používají vaše aplikace? Kolik aktivních uživatelů má vaše aplikace a jak se mění využití v průběhu času? Jaké funkce používají a které z nich se používají nejvíce? Kde jsou tito uživatelé založeni? Kolik uživatelů používá nejnovější verzi aplikace? Všechny tyto otázky jsou důležité pochopit, aby se vaše aplikace do úspěšného podnikání. Chcete-li odpovědět na tyto druhy otázek analýzy využití, musíte shromažďovat údaje o využití z vašich aplikací.

Čím více se podíváte dolů do dat, tím více můžete najít způsoby, jak zlepšit svou aplikaci a udržet vaše uživatele spokojené. Je důležité použít data k vyhledání užitečných přehledů a k tomu, aby byli uživatelé spokojeni.

## <a name="importance-of-analytics"></a>Význam analýzy
- Seznamte se s uživateli, jak interagují s vaší aplikací a co je přivádí zpět k doladění aplikace a poskytuje skvělé prostředí pro růst vašeho podnikání.
- Sledujte metriky využití a přizpůsobte se informovaně o tom, jak prodávat vaši aplikaci a jak lépe sloužit svým zákazníkům.
- Změřte výkon aplikace.
- Zjistěte, které části jednotky aplikace hodnotu a výkon.
- Získejte přehledy založené na datech o problémech, které se týkají změn a uchovávání informací.

Pomocí následujících služeb můžete povolit analýzu mobilních aplikací.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Analytics](/appcenter/analytics/) vám umožní rozšířit okruh uživatelů tím, že se zaměříte na to, co je důležité. Nabízí podrobné přehledy a přehledy o uživatelských relacích, špičkových zařízeních, verzích operačního systému a behaviorální analýze. Pomocí rozsáhlé analýzy aplikací můžete snadno vytvářet vlastní události a sledovat cokoli.

   **Klíčové funkce**
   - Sledujte vzory využití, přijetí uživatele maješ k jiným metrikám zapojení zdarma.
   - Identifikujte trendy, chování uživatelů a zapojení prostřednictvím vlastních událostí.
   - Získejte předem uvedené metriky a podrobné přehledy o využití aplikací (denní, týdenní, měsíční), relací, vlastnostech zařízení a demografických údajích uživatelů na jednom řídicím panelu.
   - Průběžně exportujte všechna data App Center Analytics do Azure, abyste je unavili, a to díky neomezenému uchovávání. App Center Analytics podporuje export do úložiště objektů blob Azure a Azure Application Insights.
   - Integrujte s Azure Application Insights pro ještě hlubší přehledy, jako je uchovávání informací, analýza cesty a kohorty.
   - Pomocí jednořádkové integrace sady SDK můžete začít během několika minut.
   - Získejte podporu platformy pro iOS, Android, macOS, tvOS, Xamarin, React Native, Unity a Cordova.

   **Odkazy**
   - [Zaregistrujte se pomocí Centra aplikací](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Začínáme s Analýzou Centra aplikací](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor obsahuje [Application Insights](/azure/azure-monitor/app/app-insights-overview), který poskytuje nástroje pro shromažďování a analýzu telemetrie pro maximalizaci výkonu a monitorování mobilní aplikace. Přehledy aplikací můžete využít pomocí služby App Center Analytics k nastavení exportu do přehledů aplikací. Application Insights můžete dotazovat, segmentovat, filtrovat a analyzovat vlastní telemetrii událostí z vašich aplikací nad rámec analytických nástrojů, které Centrum aplikací poskytuje.

**Klíčové funkce**
   - Dotazování vlastní telemetrie událostí.
   - Filtrování telemetrie událostí s výkonnými možnostmi segmentace.
   - Analyzujte vzory převodu, uchovávání informací a navigace ve vaší aplikaci. Můžete použít:
     - Trychtýře pro analýzu a sledování míry konverze.
     - Uchovávání informací analyzovat, jak dobře vaše aplikace zachová uživatele v průběhu času.
     - Sešity pro kombinaci vizualizací a textu do sestavy, kterou lze sdílet.
     - Kohorty pro pojmenování a uložení konkrétních skupin uživatelů nebo událostí, aby na ně bylo možné snadno odkazovat z jiných analytických nástrojů.

**Odkazy**
- [Portál Azure](https://portal.azure.com/)
- [Analýza mobilní aplikace pomocí Centra aplikací a přehledů aplikací](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Použití analýzy Centra aplikací s přehledy aplikací](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) nabízí kompletní back-end platformu s herními službami, analýzami v reálném čase a liveops, které potřebujete k vytváření her připojených ke cloudu světové třídy. Tyto služby snižují překážky spuštění pro vývojáře her. Nabízejí jak velká, tak malá studia nákladově efektivní vývojová řešení, která se škálují s jejich hrami. Služby mohou pomoci studiím zapojit, udržet a zpeněžit hráče. S PlayFab mohou vývojáři používat inteligentní cloud k vytváření a provozu her, analýze herních dat a zlepšování celkových herních zážitků.

**Klíčové funkce**
   - Monitorujte řídicí panely v reálném čase.
   - Vyhodnoťte výkon své hry pomocí špičkových metrik.
   - Projděte si souhrny denního a měsíčního výkonu hry prostřednictvím automaticky generovaných přehledů. Sestavy si můžete zobrazit ve Správci her a nechat si je stáhnout nebo doručit do doručené pošty každý den.
   - Pomocí testování A/B můžete spustit experimenty a určit optimální nastavení pro určitou proměnnou.
   - Pomocí segmentace pro hráče definujte automatizované seskupení hráčů.
    
**Odkazy**
- [Portál PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analýza](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Rychlé starty](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
