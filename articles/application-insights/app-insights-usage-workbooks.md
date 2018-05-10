---
title: Prozkoumat a sdílet data o využití s interaktivní sešity ve službě Azure Application Insights | Microsoft docs
description: Demografické údaje analýzy uživatelů vaší webové aplikace.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: mbullwin; daviste
ms.openlocfilehash: a871378b3e2cc0b34c925593c6f01952de3aa08e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Prozkoumat a sdílet data o využití s interaktivní sešity ve službě Application Insights

Sešity kombinovat [Azure Application Insights](app-insights-overview.md) vizualizaci dat, [analytické dotazy](app-insights-analytics.md)a text do interaktivní dokumenty. Sešity jsou upravovat ostatní členové týmu s přístupem ke stejné prostředků Azure. To znamená, že jsou k dispozici jiným uživatelům čtení sešit, tím je snadno prozkoumat, rozšířit a zkontrolujte chyby dotazy a ovládacích prvků používaná k vytvoření sešitu.

Sešity jsou užitečné pro scénáře, jako jsou:

* Zkoumání využití aplikace, pokud neznáte metriky týkající se předem: počtu uživatelů, míru uchování, převod sazby atd. Na rozdíl od jiných nástrojů analýzy využití ve službě Application Insights sešity umožňují kombinovat více typů vizualizace a analýzy, přitom výborně hodí pro tento druh zkoumání volného tvaru.
* Vysvětlení si s týmem, jaký je výkon nově vydané funkce, uživatelem zobrazující počty pro klíče interakce a další metriky.
* Sdílení výsledky A / B experimentovat ve vaší aplikaci s jinými členy týmu. Můžete vysvětlit cílů experimentu s textem a pak zobrazit každý využití metriky a analýzy dotaz byl použit k vyhodnocení experiment, společně s zrušte značek pro jestli jednotlivé metriky byla výše - nebo pod cíl.
* Dopad výpadku sestavy o využití vaší aplikace a kombinování dat, text vysvětlení a diskuzi o další kroky, abyste zabránili výpadkům v budoucnu.

> [!NOTE]
> Prostředku Application Insights musí obsahovat zobrazení stránek nebo vlastních událostí k používání sešitů. [Zjistěte, jak nastavit aplikaci shromažďovat zobrazení stránky automaticky pomocí Application Insights JavaScript SDK](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Úpravy, změna uspořádání, klonování a odstranění oddílů sešitu

Sešit je učiněna oddílů, které jsou: nezávisle upravitelné využití vizualizace, grafy, tabulky, text nebo analýza výsledků dotazu.

Upravit obsah části sešitu, klikněte **upravit** tlačítko níže a pravé části sešitu.

![Application Insights sešity část ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/editing-controls.png)

1. Po dokončení úprav oddíl, klikněte na tlačítko **udělat úpravy** v levém dolním rohu části.

2. Chcete-li vytvořit duplicitní oddíl, klikněte na tlačítko **klonovat v této části** ikonu. Vytvoření duplicitní části je skvělá pro způsob, jak iterovat dotazu bez ztráty předchozí iterací.

3. Chcete-li přesunout nahoru oddíl v sešitu, klikněte na tlačítko **přesunout nahoru** nebo **přesunout dolů** ikonu.

4. Pokud chcete trvale odebrat oddíl, klikněte **odebrat** ikonu.

## <a name="adding-usage-data-visualization-sections"></a>Přidání oddílů vizualizace dat využití

Sešity nabízí čtyři typy vizualizací analytics předdefinované využití. Všechny odpovědi na běžné otázky o využití vaší aplikace. Pokud chcete přidat, tabulky a grafy než tyto čtyři oddíly, přidejte částech dotazu Analytics (viz níže).

Přidání uživatelů, relací, události nebo uchování části k sešitu, použijte **přidat uživatele** nebo jiné odpovídající tlačítko v dolní části sešitu nebo v dolní části všechny části.

![Část uživatelé v sešitech](./media/app-insights-usage-workbooks/users-section.png)

**Uživatelé** části zodpovědět "počet uživatelů, kteří zobrazit některé stránky nebo použít některé funkce mé lokality?"

**Relace** části zodpovědět "kolik relace jinak uživatelé vynaložili na zobrazení některých stránky nebo pomocí některé funkce mé lokality?"

**Události** části zodpovědět "kolikrát uživatelé zobrazit některé stránku nebo pomocí některé funkce mé lokality?"

Každý z těchto typů tři části nabízí stejné sady ovládací prvky a vizualizací:

* [Další informace o úpravách části uživatelů, relací a události](app-insights-usage-segmentation.md)
* Přepnutí hlavní graf, histogram mřížky, automatické přehledy a vizualizací ukázka uživatele pomocí **zobrazit graf**, **zobrazit mřížky**, **zobrazit Statistika**, a **ukázka tito uživatelé** zaškrtávací políčka v horní části každé části.

![Uchování části v sešitech](./media/app-insights-usage-workbooks/retention-section.png)

**Uchování** části zodpovědět "Lidí, kteří zobrazit některé stránky nebo použití některých funkcí na jeden den nebo týden, kolik se vrátila zpět v následných dne nebo týdne?"

* [Další informace o úpravách části uchování](app-insights-usage-retention.md)
* Můžete přepínat pomocí graf volitelné celkové uchovávání **zobrazit celkový uchování grafu** zaškrtávacího políčka v horní části.

## <a name="adding-application-insights-analytics-sections"></a>Přidání Application Insights Analytics oddílů

![Část analýzy v sešitech](./media/app-insights-usage-workbooks/analytics-section.png)

Přidat oddíl dotazu Application Insights Analytics k sešitu, použijte **přidat Analytics dotazu** tlačítko v dolní části sešitu nebo v dolní části všechny části.

Oddílů dotazu Analytics můžete přidat libovolný dotazy přes data Application Insights do sešitů. Tato možnost znamená, že Analytics částech dotazu by měla být vaše přejděte – chcete pro odpovědi na všechny otázky o vašem webu než čtyři uvedené výše pro uživatele, relace, události a uchovávání dat, jako je:

* Kolik výjimky váš web výjimku během časového období stejné jako odmítnout využití?
* Co se distribuce časů načtení stránky pro zobrazení některých stránky uživatele?
* Počet uživatelů, kteří zobrazit některé sada stránek ve vaší lokalitě, ale není některých dalších nastavení stránek? To může být užitečné zjistit, jestli máte clustery uživatelů, kteří používají různé podmnožiny váš web funkci (pomocí `join` operátor s `kind=leftanti` modifikátor v jazyce dotazu analýzy protokolů).

Použití [analýzy protokolů dotazu referenční informace k jazyku](https://docs.loganalytics.io/) Další informace o zápis dotazů.

## <a name="adding-text-and-markdown-sections"></a>Přidávání textu a části Markdownu

Přidávání záhlaví, vysvětlení a komentáře k sešity pomáhá zapněte sadu tabulky a grafy do komentáře. Části textu v podpoře sešity [syntaxe Markdownu](https://daringfireball.net/projects/markdown/) pro formátování textu, jako je záhlaví, tučné, kurzíva a seznamy s odrážkami.

Přidat oddíl text k sešitu, použijte **přidat text** tlačítko v dolní části sešitu nebo v dolní části všechny části.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Ukládání a sdílení sešitů se svým týmem

Sešity jsou uloženy v rámci prostředek Application Insights, buď v **Moje sestavy** oddíl, který je soukromý nebo ve **sdílené sestavy** oddíl, který je přístupné všem uživatelům přístup k prostředku Application Insights. Chcete-li zobrazit všechny soubory v prostředku, klikněte na tlačítko **otevřete** tlačítka na panelu akcí.

Chcete-li sdílet sešit, který je aktuálně v **Moje sestavy**:

1. Klikněte na tlačítko **otevřete** na panelu akcí
2. Klikněte na tlačítko "..." vedle sešit, který chcete sdílet
3. Klikněte na tlačítko **přesunout do sdílené sestavy**.

Chcete-li sdílet sešit s odkazem nebo e-mailem, klikněte na tlačítko **sdílet** na panelu akcí. Uvědomte si, že příjemci propojení nutné přístup k tomuto prostředku na portálu Azure k zobrazení sešitu. Chcete-li úpravy, příjemci potřebovat aspoň oprávnění přispěvatele pro prostředek.

Chcete-li připnout odkaz k sešitu na řídicí panel služby Azure:

1. Klikněte na tlačítko **otevřete** na panelu akcí
2. Klikněte na tlačítko "..." vedle sešit, který chcete připnout
3. Klikněte na tlačítko **připnout na řídicí panel**.

## <a name="next-steps"></a>Další postup

## <a name="next-steps"></a>Další postup
- Pokud chcete povolit použití možnosti, zahájit odesílání [vlastních událostí](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [stránky zobrazení](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud jste již odeslat vlastní události nebo zobrazení stránky, prozkoumejte využití nástroje se dozvíte, jak uživatelé používat služby.
    - [Uživatelé, relace, události](app-insights-usage-segmentation.md)
    - [Trychtýře](usage-funnels.md)
    - [Uchování](app-insights-usage-retention.md)
    - [Toky uživatele](app-insights-usage-flows.md)
    - [Přidat uživatelský kontext](app-insights-usage-send-user-context.md)
    
