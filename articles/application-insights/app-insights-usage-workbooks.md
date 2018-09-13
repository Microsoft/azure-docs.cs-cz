---
title: Prozkoumat a sdílet data o využití s interaktivní sešity ve službě Azure Application Insights | Dokumentace Microsoftu
description: Demografické analýza uživatelů vaší webové aplikace.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/12/2017
ms.reviewer: daviste
ms.author: mbullwin
ms.openlocfilehash: 016a26acc153fba1c38d926fd5389d02755c2ff5
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643269"
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Prozkoumat a sdílet data o využití s interaktivní sešity ve službě Application Insights

Sešity kombinovat [Azure Application Insights](app-insights-overview.md) vizualizace dat [analytických dotazů](app-insights-analytics.md)a textu do dokumentů interaktivní. Sešity jsou upravovat ostatní členové týmu přístup do stejného prostředku Azure. To znamená, že dotazy a ovládací prvky použité k vytvoření sešitu jsou k dispozici jiným uživatelům čtení sešitu, díky kterým jsou snadno zkoumat, rozšířit a zkontrolujte chyby.

Sešity jsou užitečné pro scénáře, jako jsou:

* Zkoumání využití aplikací, pokud neznáte metriky, které vás zajímají předem: počet uživatelů, míry uchovávání dat, míry úspěšnosti atd. Na rozdíl od jiných nástrojů pro analýzu využití ve službě Application Insights pracovní sešity umožňují kombinovat více typů vizualizace a analýzy, díky kterým jsou skvělé pro tento druh zkoumání volném.
* Ke svému týmu vysvětlující, jaký je výkon nově vydané funkci, uživatelem zobrazují počty pro klíče interakce a další metriky.
* Sdílení výsledků A / B experimentovat ve vaší aplikaci s dalšími členy týmu. Vysvětlete cíle pro experiment s textem a potom ukazují jednotlivých metrika využití a dotazu Analytics k vyhodnocení, experiment, spolu s vymazat značek pro Určuje, zda jednotlivé metriky se nad nebo pod cíl.
* Dopad výpadku vyvářet využití aplikací, kombinování dat, text vysvětlení a diskuzi o dalších krocích, aby se zabránilo výpadkům v budoucnu.

> [!NOTE]
> Prostředek Application Insights může obsahovat zobrazení stránek nebo vlastní události a sešity použít. [Zjistěte, jak nastavit aplikaci shromažďovat zobrazení stránek automaticky pomocí Application Insights JavaScript SDK](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Úpravy, uspořádání, klonování a odstranění sešitu oddíly

Sešit je poznamenali oddílů: vizualizace nezávisle upravovat využití, grafy, tabulky, textové nebo výsledky dotazu Analytics.

Upravit obsah sešitu oddíl, klikněte **upravit** tlačítko níže a pravé části sešitu.

![Application Insights sešity části ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/editing-controls.png)

1. Po dokončení úprav oddíl, klikněte na tlačítko **provést úpravy** v levém dolním rohu části.

2. K vytvoření duplicitní oddíl, klikněte na tlačítko **klonovat tento oddíl** ikonu. Vytvoření duplicitní části je skvělý způsob, jak iterovat dotazu bez ztráty předchozími iteracemi.

3. Posunout nahoru oddíl v sešitu, klikněte na tlačítko **nahoru** nebo **přesunout dolů** ikonu.

4. Pokud chcete trvale odebrat oddíl, klikněte na tlačítko **odebrat** ikonu.

## <a name="adding-usage-data-visualization-sections"></a>Přidání části vizualizace dat využití

Sešity nabízí čtyři typy vizualizace analýz využití integrované. Každé odpovědi na běžné otázky o využití vaší aplikace. Pro přidání tabulek a grafů jiné než tyto čtyři oddíly, přidejte části dotazu Analytics (viz níže).

Chcete-li přidat uživatele, relace, události nebo uchovávání části vašeho sešitu, použijte **Add Users** nebo jiné příslušné tlačítko v dolní části sešitu nebo v dolní části libovolné části.

![Část Uživatelé sešitů](./media/app-insights-usage-workbooks/users-section.png)

**Uživatelé** oddíly odpovědět "kolik uživatelů zobrazit některé stránky nebo použít některé funkce z mé lokality?"

**Relace** oddíly odpovědět "kolik relací uživatelé stráví zobrazení některé stránky nebo použití některých funkcí webu?"

**Události** oddíly odpovědět "kolikrát uživatelům zobrazit některé stránky nebo použití některých funkcí webu?"

Každý z těchto typů tři části nabízí stejné sady ovládacích prvků a vizualizací:

* [Další informace o úpravách části Uživatelé, relace a události](app-insights-usage-segmentation.md)
* Přepnout hlavního grafu, histogram mřížky, automatické přehledy a ukázka uživatelé vizualizací pomocí **zobrazit graf**, **zobrazit mřížku**, **zobrazit přehledy**a **Ukázka tito uživatelé** zaškrtávací políčka v horní části každé části.

![V sešitech části uchování](./media/app-insights-usage-workbooks/retention-section.png)

**Uchování** oddíly odpovědět "Lidí, kteří zobrazit některé stránky, nebo použít některé funkce u jednoho dne nebo týdne, kolik nevrátili v následných dne nebo týdne?"

* [Další informace o úpravách části uchování](app-insights-usage-retention.md)
* Můžete přepínat pomocí grafu volitelné celková doba uchování **zobrazit celkový graf uchování** zaškrtávacího políčka v horní části.

## <a name="adding-application-insights-analytics-sections"></a>Přidání Application Insights Analytics oddílů

![V sešitech části Analytics](./media/app-insights-usage-workbooks/analytics-section.png)

Chcete-li přidat oddíl dotaz Application Insights Analytics k sešitu, použijte **dotazu Analytics přidat** tlačítko v dolní části sešitu nebo v dolní části libovolné části.

Části dotazu Analytics umožňují přidat libovolné dotazy na data Application Insights do sešitů. Díky této flexibilitě znamená, že oddíly dotazu Analytics by měla být vaše go-to pro odpovídání na jakékoliv otázky týkající se vaší lokality jiné než čtyři uvedené výše pro uživatele, relace, události a uchovávání dat, jako je:

* Kolik výjimky váš web vyvolat za stejné období jako pokles využití?
* Jaký byl distribuční časy načtení stránek uživatelům, kteří zobrazují některé stránky?
* Kolik uživatelů zobrazit některé sadu stránek na webu, ale není některých dalších nastavení stránek? To může být užitečné k pochopení, pokud máte clustery uživatelů, kteří používají různé podmnožiny funkčnosti vaší lokality (použít `join` operátor s `kind=leftanti` modifikátor v dotazovací jazyk Log Analytics).

Použití [dotazovací referenční informace k jazyku Log Analytics](https://docs.loganalytics.io/) Další informace o psaní dotazů.

## <a name="adding-text-and-markdown-sections"></a>Přidání textu a části Markdownu

Přidání záhlaví, vysvětlení a poznámky k sešity pomůže proměnit sadu tabulek a grafů komentáře. Části textu v sešitech technické podpory [syntaxe Markdownu](https://daringfireball.net/projects/markdown/) pro formátování textu, jako jsou nadpisy, tučné, kurzíva a seznamy s odrážkami.

Chcete-li přidat do sešitu část textu, použijte **přidat text** tlačítko v dolní části sešitu nebo v dolní části libovolné části.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Ukládání a sdílení sešitů s týmem

Sešity jsou uloženy v rámci prostředku Application Insights, buď v **Moje sestavy** oddíl, který je pro vás nebo v privátní **sdílené sestavy** oddíl, který je přístupné všem uživatelům s přístupem k Prostředek služby Application Insights. Chcete-li zobrazit všechny soubory v prostředku, klikněte na tlačítko **otevřít** tlačítko na panelu akcí.

Sdílet sešit, který je v současné době **Moje sestavy**:

1. Klikněte na tlačítko **otevřít** na panelu akcí
2. Klikněte na tlačítko "..." vedle sešit, který chcete sdílet
3. Klikněte na tlačítko **přesunout do sdílených sestav**.

Chcete-li sdílet sešit s odkazem nebo e-mailem, klikněte na tlačítko **sdílet** na panelu akcí. Uvědomte si, že příjemci odkazu potřebují přístup k tomuto prostředku na webu Azure Portal k zobrazení tohoto sešitu. K provádění úprav, příjemci potřebovat aspoň oprávnění přispěvatele pro prostředek.

Pokud chcete připnout odkaz k sešitu na řídicí panel Azure:

1. Klikněte na tlačítko **otevřít** na panelu akcí
2. Klikněte na tlačítko "..." vedle sešit, který chcete připnout
3. Klikněte na tlačítko **připnout na řídicí panel**.

## <a name="next-steps"></a>Další postup

## <a name="next-steps"></a>Další postup
- Povolit použití prostředí, spusťte odesílání [vlastních událostí](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud jste již posílat vlastní události nebo zobrazení stránek, prozkoumejte nástroje využití se dozvíte, jak uživatelé vaši službu používat.
    - [Uživatelé, relace, události](app-insights-usage-segmentation.md)
    - [Trychtýře](usage-funnels.md)
    - [Uchování](app-insights-usage-retention.md)
    - [Toky uživatele](app-insights-usage-flows.md)
    - [Přidat kontext uživatele](app-insights-usage-send-user-context.md)
    
