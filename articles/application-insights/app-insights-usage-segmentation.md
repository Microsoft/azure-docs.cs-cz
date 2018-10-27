---
title: Uživatele, relace a události analýzy ve službě Azure Application Insights | Dokumentace Microsoftu
description: Demografické analýza uživatelů vaší webové aplikace.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 6fd8adab93f5741afe6d3eab0c50ca50a327fbff
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140328"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Uživatelé, relace a události analýzy ve službě Application Insights

Přečtěte si, když uživatelé používají webové aplikace, které stránky se nejvíce zajímají, kde se nachází vaši uživatelé, a jaké prohlížeče a operační systémy používají. Analýza telemetrie obchodní a využití pomocí [Azure Application Insights](app-insights-overview.md).

![Snímek obrazovky uživatele Application Insights](./media/app-insights-usage-segmentation/0001-users.png)

## <a name="get-started"></a>Začínáme

Pokud ještě se data v uživatelé, relace nebo události oknech na portálu služby Application Insights [zjistěte, jak začít pracovat s nástroji využití](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Nástroj segmentaci uživatelů, relací a události

Tři z použití oken použijte stejný nástroj k nařezání a rozčlenění telemetrii z vaší webové aplikace ze tří perspektivy. Pomocí filtrování a rozdělení dat, můžete získat informace o relativní využití různé stránky a funkce.

* **Nástroj Uživatelé**: kolik lidí používá vaše aplikace a jeho funkcí.  Uživatelé se počítají pomocí anonymní ID uložené v souborech cookie prohlížeče. Jedna osoba pomocí různých prohlížečích nebo počítače se budou počítat jako více než jeden uživatel.
* **Nástroj relace**: kolik relací aktivity uživatelů obsahovat některé stránky a funkce vaší aplikace. Relace se počítá za půl hodiny nečinnosti uživatele, nebo za 24 hodin nepřetržitého užívání.
* **Nástroj události**: jak často se používají některé stránky a funkce vaší aplikace. Zobrazení stránky se počítá načtení stránku z vaší aplikace v prohlížeči předpokladu, že máte [instrumentována ho](app-insights-javascript.md). 

    Vlastní událost představuje jeden výskyt něco děje ve vaše aplikace, často zásahu uživatele, jako je kliknutí na tlačítko nebo dokončení některých úkolů. Vložení kódu v aplikaci tak, aby [generování událostí na vlastní](app-insights-api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Dotazování pro určité uživatele

Prozkoumejte různé skupiny uživatelů úpravou dotazu v horní části nástroj Uživatelé:

* Zobrazit: Zvolte kohorta uživatelů k analýze.
* Kdo používá: Zvolte vlastní události a zobrazení stránek.
* Během: Vyberte časový rozsah.
* Podle: Zvolte, jak kontejneru dat, určitou dobu nebo jiné vlastnosti, jako je například prohlížeč nebo město.
* Rozděleno podle: Vyberte vlastnost podle kterého na dělené tunelové propojení nebo segment data. 
* Přidat filtry: Omezte dotaz určitých uživatelů, relací a události na základě jejich vlastností, jako je například prohlížeč nebo město. 
 
## <a name="saving-and-sharing-reports"></a>Ukládání a sdílení sestav 
Uživatelé sestavy, privátní právě pro vás v části Moje sestavy nebo sdílené můžete uložit s ostatními s přístupem k tomuto prostředku Application Insights v části sdílených sestav.

Sdílet odkaz na sestavu uživatelé, relace nebo události; Klikněte na tlačítko **sdílenou složku** na panelu nástrojů, zkopírujte odkaz.

Sdílet kopii dat v sestavě uživatelé, relace nebo události; Klikněte na tlačítko **sdílenou složku** na panelu nástrojů klikněte **ikona aplikace Word** vytvořit dokument aplikace Word s daty. Nebo klikněte na tlačítko **ikona aplikace Word** nad hlavním grafu.

## <a name="meet-your-users"></a>Seznamte se se svými uživateli

**Splňovat vaši uživatelé** části s informacemi o pět ukázkové uživatele odpovídá aktuální dotaz. Vzhledem k tomu a zkoumat chování osob, kromě agregací, může poskytnout přehled o tom, jak lidé skutečně používat vaší aplikace.

## <a name="next-steps"></a>Další postup

- Povolit použití prostředí, spusťte odesílání [vlastních událostí](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud jste již posílat vlastní události nebo zobrazení stránek, prozkoumejte nástroje využití se dozvíte, jak uživatelé vaši službu používat.
    - [Trychtýře](usage-funnels.md)
    - [Uchování](app-insights-usage-retention.md)
    - [Toky uživatele](app-insights-usage-flows.md)
    - [Workbooks](app-insights-usage-workbooks.md)
    - [Přidat kontext uživatele](app-insights-usage-send-user-context.md)