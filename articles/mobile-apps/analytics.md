---
title: Pochopení používání mobilních aplikací a chování uživatelů pomocí Visual Studio App Center a služeb Azure
description: Přečtěte si o službách, jako je App Center, které vám pomůžou učinit inteligentní rozhodnutí v podniku tím, že porozumí, jak uživatelé používají vaši mobilní aplikaci.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d60e9437a2f33788eb183cfcad0f3a10d71fb79d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795793"
---
# <a name="analyze-and-understand-usage-of-mobile-applications"></a>Analýza a pochopení používání mobilních aplikací
Jak dobře rozumíte tomu, jak uživatelé používají vaše aplikace? Kolik aktivních uživatelů vaše aplikace má a jak se v průběhu času mění využití? Jaké funkce používá a které jsou používány nejvíc? Kde jsou tito uživatelé vycházející? Kolik uživatelů používá nejnovější verzi aplikace? Všechny tyto otázky jsou důležité k tomu, aby se vaše aplikace mohla poznat do úspěšného podnikání. K zodpovězení těchto druhů dotazů týkajících se analýzy využití je potřeba shromažďovat data o využití z vašich aplikací.

Další informace najdete v datech. tím více můžete najít možnosti pro vylepšení vaší aplikace a udržení spokojenosti uživatelů. Je důležité použít data k vyhledání užitečných přehledů a udržení spokojenosti uživatelů.

## <a name="importance-of-analytics"></a>Důležitost analýz
- **Seznamte se s** uživateli, jak komunikují s vaší aplikací a co jim umožňuje doladit vaši aplikaci a poskytovat Skvělé prostředí pro růst vaší firmy.
- **Sledujte** metriky využití a zajistěte si informovaná rozhodnutí o uvedení vaší aplikace na trh a lepší obsluhu svého zákazníka.
- **Změřte** výkon aplikace.
- **Zjistěte** , které části vaší aplikace jsou v hodnotě a výkonu.
- **Přehledy na základě dat** o problémech týkajících se změn a uchovávání.

K povolení analýzy mobilních aplikací použijte následující služby.

## <a name="visual-studio-app-center"></a>Centrum aplikací Visual Studio
[App Center Analytics](/appcenter/analytics/) umožňuje rozšířit cílovou skupinu tím, že se zaměříme na to, co je důležité, s podrobnými vytvářením sestav a přehledem o uživatelských relacích, hlavních zařízeních, verzích operačních systémů a analýzách chování. Snadno vytvářejte vlastní události, abyste mohli sledovat cokoli s rozsáhlou analýzou aplikací.

   **Klíčové funkce**
   - Zadarmo **Sledujte** vzory využití, přijetí uživatelů a další metriky služby Engagement.
   - **Identifikujte** trendy, chování uživatelů a zapojení prostřednictvím vlastních událostí.
   - Předem připravené **metriky** a **podrobné** informace o využití aplikací (denně, týdně, měsíčně), relacích, vlastnostech zařízení a demografických údajích uživatele na **jednom řídicím panelu**.
   - **Průběžně exportujte veškerá vaše analytická data do Azure** a neomezenou dobu uchovávání. Podporuje export do Azure Blob Storage a Azure Application Insights.
   - **Integrace s Azure Application Insights** pro ještě hlubší přehledy, jako je uchovávání dat, analýza trychtýře a kohorty
   - **Jedna řádková integrace sady SDK** pro začátek během několika minut.
   - **Podpora platforem** – iOS, Android, MacOS, TvOS, Xamarin, reaguje na nativní, Unity, Cordova.

   **Odkazy**
   - [Zaregistrujte se App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Začínáme s App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor obsahuje [Application Insights](/azure/azure-monitor/app/app-insights-overview) , která poskytuje nástroje pro shromažďování a analýzu telemetrie pro zajištění maximálního výkonu a monitorování vaší mobilní aplikace. Můžete využít výhod Application Insights pomocí App Center analýzy a nastavit export na Application Insights. Application Insights se může dotazovat, segmentovat, filtrovat a analyzovat vlastní telemetrii událostí z vašich aplikací, a to za rámec analytických nástrojů, které poskytuje App Center.

**Klíčové funkce**
   - **Dotazování** vlastní telemetrie událostí
   - **Vyfiltrujte** telemetrii událostí pomocí výkonných funkcí segmentace.
   - **Analýza** převodů, uchovávání a navigačních vzorů ve vaší aplikaci.
     - **Trychtýře** pro analýzy a monitorování konverzního poměru.
     - **Uchovávání informací** o tom, jak dobře vaše aplikace zachovává uživatele v průběhu času.
     - **Sešity** pro kombinaci vizualizací a textu do sestavy s možností sdílení.
     - **Kohorty** pro pojmenování a ukládání konkrétních skupin uživatelů nebo událostí, aby se na ně dalo snadno odkazovat v jiných analytických nástrojích.

**Odkazy**
- [Azure Portal](https://portal.azure.com/)
- [Analýza mobilní aplikace pomocí App Center a Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Analýza využití pomocí Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>PlayFab Azure
[Azure PlayFab](https://playfab.com/) nabízí kompletní back-end platformu se Game Services, analýzou v reálném čase a LiveOps, kterou potřebujete k vytvoření světové třídy zapojené do cloudu. Tyto služby omezují překážky, které se spouštějí pro vývojáře her, a nabízejí jak velká, tak malá studia vývojová řešení, která se škálují s jejich hrami a umožňují zapojit, uchovávat a monetizovat přehrávače. PlayFab umožňuje vývojářům pomocí inteligentního cloudu vytvářet a provozovat hry, analyzovat herní data a zdokonalovat celkové herní prostředí.

**Klíčové funkce**
   - **Monitorujte** řídicí panely v reálném čase.
   - **Vyhodnoťte** výkon své hry díky špičkovým metrikám.
   - **Sestava** pro kontrolu souhrnů denního a měsíčního výkonu vaší hry prostřednictvím automaticky generovaných sestav, které se dají zobrazit ve hře her a stahovat nebo doručovat do vaší doručené pošty denně.
   - **Testování a/B** pro spouštění experimentů a určení optimálního nastavení pro konkrétní proměnnou.
   - **Segmentace** pro hráče umožňuje definovat automatizované seskupení přehrávačů.
    
**Odkazy**
- [Portál PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analýzy](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Rychlé starty](/gaming/playfab/#pivot=documentation&panel=quickstarts)    
