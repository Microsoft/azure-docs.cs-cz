---
title: Analýza uživatelů, relací a událostí v Azure Application Insights
description: Demografická analýza uživatelů vaší webové aplikace.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 56059304026b060e2215ce73e0e94e3200573a14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670980"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Analýza uživatelů, relací a událostí v přehledech aplikací

Zjistěte, kdy lidé používají vaši webovou aplikaci, jaké stránky je nejvíce zajímají, kde se uživatelé nacházejí a jaké prohlížeče a operační systémy používají. Analyzujte telemetrii podnikání a využití pomocí [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Snímek obrazovky s uživateli přehledů aplikací](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Začínáme

Pokud ještě nevidíte data v okně uživatelů, relací nebo událostí na portálu Application Insights, [přečtěte si, jak začít s nástroji pro využití](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Nástroj segmentace uživatelů, relací a událostí

Tři z použití nože použít stejný nástroj k řezu a kostky telemetrie z webové aplikace ze tří perspektiv. Filtrováním a rozdělením dat můžete odhalit přehledy o relativním využití různých stránek a funkcí.

* **Nástroj Uživatelé**: Kolik lidí vaši aplikaci a její funkce používalo.  Uživatelé se počítají pomocí anonymních ID uložených v souborech cookie prohlížeče. Jedna osoba používající různé prohlížeče nebo počítače bude započítána jako více než jeden uživatel.
* **Nástroj relace**: Kolik návštěv aktivit uživatelů obsahovalo určité stránky a funkce aplikace. Relace se počítá po půl hodině nečinnosti uživatele nebo po 24 hodinách nepřetržitého používání.
* **Nástroj Události**: Jak často se používají určité stránky a funkce aplikace. Zobrazení stránky se započítá, když prohlížeč načte stránku z vaší aplikace za předpokladu, že jste [ji instrumentovali](../../azure-monitor/app/javascript.md). 

    Vlastní událost představuje jeden výskyt něčeho, co se děje ve vaší aplikaci, často interakci uživatele, jako je kliknutí na tlačítko nebo dokončení některé úlohy. Vložíte kód do aplikace pro [generování vlastních událostí](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Dotazování pro určité uživatele

Prozkoumejte různé skupiny uživatelů úpravou možností dotazu v horní části nástroje Uživatelé:

* Zobrazit: Zvolte kohortu uživatelů k analýze.
* Kdo použil: Zvolte vlastní události a zobrazení stránek.
* Během: Zvolte časový rozsah.
* Podle: Zvolte, jak zobrazit data, a to buď podle časového období, nebo podle jiné vlastnosti, jako je prohlížeč nebo město.
* Rozdělit podle: Zvolte vlastnost, podle které chcete data rozdělit nebo segmentovat. 
* Přidat filtry: Omezte dotaz na určité uživatele, relace nebo události na základě jejich vlastností, jako je prohlížeč nebo město. 
 
## <a name="saving-and-sharing-reports"></a>Ukládání a sdílení sestav 
Sestavy Users můžete uložit, buď soukromé, ale pouze pro vás v části Moje sestavy, nebo sdílet se všemi ostatními s přístupem k tomuto prostředku Application Insights v části Sdílené sestavy.

Sdílení odkazu na sestavu Uživatelé, Návštěvy nebo Události. Klikněte na **Sdílet** na panelu nástrojů a pak odkaz zkopírujte.

Sdílení kopie dat v sestavě Uživatelé, Návštěvy nebo Události. Klikněte na **Sdílet** na panelu nástrojů a potom kliknutím na **ikonu Wordu** vytvořte wordový dokument s daty. Nebo klikněte na **ikonu Wordu** nad hlavním grafem.

## <a name="meet-your-users"></a>Seznamte se s uživateli

V části **Seznamte se s uživateli** zobrazuje informace o pěti ukázkových uživatelích, které odpovídá aktuální dotaz. Zvažování a zkoumání chování jednotlivců, kromě agregátů, může poskytnout přehled o tom, jak lidé skutečně používají vaši aplikaci.

## <a name="next-steps"></a>Další kroky

- Chcete-li povolit možnosti využití, začněte odesílat [vlastní události](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud už vlastní události nebo zobrazení stránek odesíláte, prozkoumejte nástroje využití a zjistěte, jak uživatelé vaši službu používají.
    - [Trychtýře](usage-funnels.md)
    - [Uchovávání](usage-retention.md)
    - [Toky uživatele](usage-flows.md)
    - [Workbooks](../../azure-monitor/app/usage-workbooks.md)
    - [Přidání kontextu uživatele](usage-send-user-context.md)
