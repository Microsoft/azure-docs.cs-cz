---
title: Pochopení používání mobilních aplikací a chování uživatelů pomocí Visual Studio App Center a služeb Azure
description: Přečtěte si o službách, jako je App Center, které vám pomůžou učinit inteligentní rozhodnutí v podniku tím, že porozumí, jak uživatelé používají vaši mobilní aplikaci.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ebe07ec76e5b852dbe7d030ad8859d59ce5cd074
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451057"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analýza a pochopení použití mobilní aplikace
Jak dobře rozumíte tomu, jak uživatelé používají vaše aplikace? Kolik aktivních uživatelů vaše aplikace má a jak se v průběhu času mění využití? Jaké funkce používá a které jsou používány nejvíc? Kde jsou tito uživatelé vycházející? Kolik uživatelů používá nejnovější verzi aplikace? Všechny tyto otázky jsou důležité k tomu, aby se vaše aplikace mohla poznat do úspěšného podnikání. K zodpovězení těchto druhů dotazů týkajících se analýzy využití je potřeba shromažďovat data o využití z vašich aplikací.

Další informace najdete v datech. tím více můžete najít způsob, jak vylepšit vaši aplikaci a zajistit, aby se uživatelé pořád spokojeni. Je důležité použít data k vyhledání užitečných přehledů a udržování spokojenosti uživatelů.

## <a name="importance-of-analytics"></a>Důležitost analýz
- Seznamte se s uživateli, jak komunikují s vaší aplikací a co jim umožňuje doladit vaši aplikaci a poskytovat Skvělé prostředí pro růst vaší firmy.
- Sledujte metriky využití a zajistěte si informovaná rozhodnutí o uvedení vaší aplikace na trh a lepší poskytování vašich zákazníků.
- Změřte výkon aplikace.
- Zjistěte, které části hodnoty a výkonu vaší aplikační jednotky.
- Získejte přehledy o problémech založených na datech, které se týkají změn a uchovávání.

K povolení analýzy mobilních aplikací použijte následující služby.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Analytics](/appcenter/analytics/) umožňuje rozšířit cílovou skupinu tím, že se zaměříte na to, co je důležité. Nabízí obsáhlé vytváření sestav a přehled o uživatelských relacích, hlavních zařízeních, verzích operačních systémů a analýzách chování. Snadno vytvářejte vlastní události, abyste mohli sledovat cokoli s rozsáhlou analýzou aplikací.

   **Klíčové funkce**
   - Zadarmo Sledujte vzory využití, přijetí uživatelů a další metriky služby Engagement.
   - Identifikujte trendy, chování uživatelů a zapojení prostřednictvím vlastních událostí.
   - Získejte předem připravené metriky a podrobné informace o využití aplikací (denně, týdně, měsíčně), relacích, vlastnostech zařízení a demografických údajích uživatele na jednom řídicím panelu.
   - Průběžně exportujte všechna vaše App Center analytická data do Azure a neomezenou dobu uchovávání. App Center Analytics podporuje export do služby Azure Blob Storage a Azure Application Insights.
   - Integraci s Azure Application Insights pro ještě hlubší přehledy, jako je například uchovávání, analýza trychtýře a kohorty.
   - Použijte jednu integraci sady SDK, abyste mohli začít během několika minut.
   - Získejte podporu platforem pro iOS, Android, macOS, tvOS, Xamarin, reagují na nativní, Unity a Cordova.

   **Odkazy**
   - [Zaregistrujte se App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Začínáme s App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor zahrnuje [Application Insights](/azure/azure-monitor/app/app-insights-overview), které poskytují nástroje pro shromažďování a analýzu telemetrie pro maximalizaci výkonu a monitorování vaší mobilní aplikace. Můžete využít výhod Application Insights pomocí App Center Analytics k nastavení exportu na Application Insights. Application Insights se může dotazovat, segmentovat, filtrovat a analyzovat vlastní telemetrii událostí z vašich aplikací, a to nad rámec analytických nástrojů, které poskytují App Center.

**Klíčové funkce**
   - Dotazování vlastní telemetrie událostí.
   - Vyfiltrujte telemetrii událostí pomocí výkonných funkcí segmentace.
   - Analýza převodů, uchovávání a navigačních vzorů ve vaší aplikaci. Můžete použít:
     - Trychtýře pro analýzu a monitorování sazeb převodu.
     - Uchovávání informací, aby bylo možné analyzovat, jak dobře vaše aplikace zachovává uživatele v průběhu času.
     - Sešity pro kombinování vizualizací a textu do sestavy ke sdílení.
     - Kohorty se na název a uložte konkrétní skupiny uživatelů nebo událostí, aby na ně bylo možné snadno odkazovat z jiných analytických nástrojů.

**Odkazy**
- [portál Azure](https://portal.azure.com/)
- [Analýza mobilní aplikace pomocí App Center a Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Použití App Center Analytics s Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) nabízí ucelenou platformu back-endu se Game Services, analýzou v reálném čase a LiveOps, kterou potřebujete k vytváření špičkových her připojených ke cloudu. Tyto služby omezují překážky, které se spouštějí pro vývojáře her. Nabízejí jak velká, tak malá studia vývojová řešení, která se škálují s jejich hrami. Služby mohou přispět k studiaí hráčů, zachovat a monetizovat. Díky PlayFab můžou vývojáři pomocí inteligentního cloudu vytvářet a provozovat hry, analyzovat herní data a zlepšovat celkové herní prostředí.

**Klíčové funkce**
   - Monitorujte řídicí panely v reálném čase.
   - Vyhodnoťte výkon své hry díky špičkovým metrikám.
   - Prohlédněte si souhrnné výsledky denního a měsíčního výkonu vaší hry prostřednictvím automaticky generovaných sestav. Sestavy můžete zobrazit ve Správci her a stáhnout nebo doručit do složky Doručená pošta denně.
   - Použijte testování a/B pro spouštění experimentů a určení optimálního nastavení pro konkrétní proměnnou.
   - Pomocí segmentace můžete v přehrávačích definovat automatizované seskupení přehrávačů.
    
**Odkazy**
- [Portál PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analýzy](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Rychlé starty](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
