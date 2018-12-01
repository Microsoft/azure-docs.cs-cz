---
title: Analýza vzorů navigace uživatele pomocí toků uživatelů ve službě Azure Application Insights | Dokumentace Microsoftu
description: Analyzujte, jak uživatelé přecházejí mezi stránky a funkce vaší webové aplikace.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 05853cb494ef6cc615bea7eecb1cfac288b46f3b
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721948"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analýza vzorů navigace uživatele pomocí toků uživatelů ve službě Application Insights

![Nástroj Application Insights toky uživatelů](./media/app-insights-usage-flows/00001-flows.png)

Nástroj toky uživatelů vizualizuje, jak uživatelé přecházejí mezi stránky a funkce vaší lokality. Je velmi vhodná pro odpovědi na otázky jako:

* Jak uživatelé přecházejí ze stránky na webu?
* Co uživatelé kliknou na stránce na webu?
* Kde jsou nejvíce místa, které změny uživatelů z vaší lokality?
* Jsou místa, kde uživatelé pořád dokola opakujte stejnou akci?

Nástroj toky uživatelů začíná od zobrazení úvodní stránky, vlastní události nebo výjimku, která zadáte. Zadaný tento počáteční událost, toky uživatelů zobrazuje události, ke kterým došlo před a poté během uživatelské relace. Řádky různou tloušťky zobrazit kolikrát jednotlivé cesty následovalo uživatelů. Speciální **spuštěna relace** uzly ukazují, kde dalších uzlů zahájení relace. **Relace ukončeno** uzly ukazují, kolik uživatelů odesílá žádné zobrazení stránek nebo vlastní události po předchozím uzlu, zvýraznění, kde uživatelé pravděpodobně vlevo vašeho webu.

> [!NOTE]
> Prostředek Application Insights může obsahovat zobrazení stránek nebo vlastní události a použít nástroj toky uživatelů. [Zjistěte, jak nastavit aplikaci shromažďovat zobrazení stránek automaticky pomocí Application Insights JavaScript SDK](app-insights-javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Začněte tím, že vyberete nějakou počáteční událost.

![Zvolte nějakou počáteční událost pro toky uživatelů](./media/app-insights-usage-flows/00002-flows-initial-event.png)

Pokud chcete začít, odpovídání na dotazy pomocí nástroje toky uživatelů, zvolte zobrazení úvodní stránky, vlastní událost nebo výjimka, která bude sloužit jako výchozí bod pro vizualizaci:

1. Klikněte na odkaz v **co uživatelé dělají poté...?**  nadpisu, nebo klikněte na tlačítko **upravit** tlačítko.
2. Vyberte zobrazení stránky, vlastní událost nebo výjimka **počáteční událost** rozevíracího seznamu.
3. Klikněte na tlačítko **vytvořit graf**.

Ve sloupci "Krok 1" vizualizace zobrazí, co uživatelé měli nejčastěji bezprostředně po počáteční událost seřazené shora dolů z největší na nejméně často. "Step 2" a dalších sloupců zobrazit, co uživatelé měli po tomto datu, vytváření obrázek všechny způsoby, jak uživatelé přešli svého webu.

Ve výchozím nastavení nástroj toky uživatelů náhodně ukázky pouze posledních 24 hodin zobrazení stránek a vlastní události z vaší lokality. Můžete zvýšit časový rozsah a změňte vyvážení výkonu a přesnosti pro náhodné vzorkování v nabídce Úpravy.

Pokud některé zobrazení stránek, vlastní události a výjimky nejsou pro vás relevantní, klikněte na tlačítko **X** na uzlech, které chcete skrýt. Jakmile vyberete uzly, které chcete skrýt, klikněte na tlačítko **vytvořit graf** pod vizualizace. Pokud chcete zobrazit všechny uzly, které jste skryli, klikněte na tlačítko **upravit** tlačítko, pak si prohlédněte **vyloučené události** oddílu.

Pokud vlastní události nebo zobrazení stránek chybí, které byste měli vidět ve vizualizaci:

* Zkontrolujte **vyloučené události** tématu **upravit** nabídky.
* Pomocí tlačítka plus na **ostatní** uzly zahrnout méně časté události ve vizualizaci.
* Pokud zobrazení stránky nebo vlastní události, které očekáváte, že uživatelé zřídka přijde, zkuste zvětšit časové rozmezí vizualizaci v **upravit** nabídky.
* Ujistěte se, že zobrazení stránky, vlastní událost nebo výjimky, které očekáváte, že je nastavena na shromážděných službou Application Insights SDK ve zdrojovém kódu vašeho webu. [Další informace o shromažďování vlastního událostí.](app-insights-api-custom-events-metrics.md)

Pokud chcete zobrazit další kroky ve vizualizaci, použijte **předchozí kroky** a **další kroky** rozevírací seznamy nad vizualizací.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Po přečtení informací na stránce nebo funkce, kde uživatelé a co kliknou?

![Použijte, abyste pochopili, kde uživatelé kliknou na toky uživatelů](./media/app-insights-usage-flows/00003-flows-one-step.png)

Pokud počáteční událost zobrazení stránky, na první sloupec ("krok 1") vizualizace je rychlý způsob, jak pochopit uživatelů měli okamžitě po na stránce. Otevřete svůj web v okně vedle vizualizace toky uživatelů. Porovnejte vaše očekávání jak uživatelé pracují s stránky a seznam událostí ve sloupci "Krok 1". Prvek uživatelského rozhraní na stránce zdá se, že nevýznamné váš tým často, může být mezi nejčastěji používaným na stránce. Může být skvělým výchozím bodem pro vylepšení návrhu na váš web.

Pokud je vaše počáteční událost vlastní událost, první sloupec zobrazuje, uživatelé měli právě po provedení této akce. Stejně jako u zobrazení stránek, zvažte, jestli zjištěnou chování uživatelů odpovídá očekávání a cílů vašeho týmu. Pokud vaše vybrané počáteční událost "Přidání položky do nákupního košíku", například vypadat a zjistěte, jestli "Přejít na rezervaci" a "Dokončení nákupu" se zobrazí ve vizualizaci po krátké době. Pokud uživatel chování se liší od vaše očekávání, použijte vizualizaci pochopit, jak se zobrazuje uživatelům "zachycena" současný návrh vašeho webu.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Kde jsou nejvíce místa, které změny uživatelů z vaší lokality?

Sledujte **relace ukončena** uzly, které se zobrazují vysoce nahoru ve sloupci ve vizualizaci, zejména již v rané fázi v toku. To znamená, že mnoho uživatelů z vaší lokality pravděpodobně Měněná po provedení výše uvedená cesta stránek a interakce uživatelského rozhraní. Někdy se očekává – po dokončení nákupu na webu elektronického obchodování, například – ale obvykle je četnost změn znaménko problémy návrhu, nízkým výkonem nebo jiné problémy s webem, který se může zlepšit.

Mějte na paměti, která **relace ukončena** uzly jsou založeny pouze na telemetrii shromažďovanou ve tomuto prostředku Application Insights. Pokud Application Insights nepřijímá telemetrii pro určité uživatelské interakce, uživatele může stále mají zpracovat váš web těmito způsoby po nástroj toky uživatelů říká relace ukončena.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Jsou místa, kde uživatelé pořád dokola opakujte stejnou akci?

Hledejte zobrazení stránky nebo vlastní událost, která se opakuje mnoha uživateli v dalších krocích ve vizualizaci. To obvykle znamená, že uživatelé provádíte opakované akce ve vaší lokalitě. Pokud zjistíte opakování, představte si změny návrhu vaší lokality nebo přidání nové funkce omezit opakování. Pokud zjistíte, uživatelů, kteří provádějí opakované akce na každém řádku table element, například přidáním hromadné úpravy funkce.

## <a name="common-questions"></a>Časté dotazy

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Neobsahuje představují počáteční událost při prvním zobrazení události v relaci, nebo kdykoli, který se zobrazuje v relaci?

Počáteční událost na vizualizaci představuje pouze při prvním uživatel odeslal tohoto zobrazení stránky nebo vlastní událost během relace. Pokud uživatelé můžou posílat počáteční událost více než jednou v relaci, pak "Krok 1" sloupec zobrazuje pouze chování uživatelů po *první* instance počáteční událost, ne všechny instance.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Některé z uzlů v mé vizualizace je příliš vysoké úrovně. Například uzel, který právě říká "Kliknutí na tlačítko." Jak můžu ho rozložit na jednotlivé do podrobnější uzlů?

Použití **rozdělit podle** možnosti **upravit** nabídky:

1. Kliknutím na událost, kterou chcete rozdělit **události** nabídky.
2. Vyberte dimenze v **dimenze** nabídky. Například pokud máte událost s názvem "Kliknutí na tlačítko", použijte vlastní vlastnost s názvem "Název tlačítka."

## <a name="next-steps"></a>Další postup

* [Přehled využití](app-insights-usage-overview.md)
* [Uživatelé, relace a události](app-insights-usage-segmentation.md)
* [Uchování](app-insights-usage-retention.md)
* [Přidání vlastních událostí do vaší aplikace](app-insights-api-custom-events-metrics.md)