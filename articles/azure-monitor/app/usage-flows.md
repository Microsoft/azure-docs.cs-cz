---
title: Azure Application Insights uživatelské toky analyzuje toky navigace
description: Analyzujte, jak uživatelé přecházejí mezi stránkami a funkcemi vaší webové aplikace.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8622ede9e6f7fba2fde2e0b2e90eb31520a23d04
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892440"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analýza vzorců navigace uživatelů pomocí toků uživatelů v přehledech aplikací

![Nástroj Točesa uživatelů přehledů aplikací](./media/usage-flows/00001-flows.png)

Nástroj Toče user toky vizualizuje, jak uživatelé přecházejí mezi stránkami a funkcemi vašeho webu. Je to skvělé pro odpovědi na otázky, jako jsou:

* Jak uživatelé přejít od stránky na vašem webu?
* Na co uživatelé kliknou na stránku na vašem webu?
* Kde jsou místa, která uživatelé konve nejvíce z vašeho webu?
* Existují místa, kde uživatelé opakují stejnou akci znovu a znovu?

Nástroj Toky uživatele začíná počátečním zobrazením stránky, vlastní událostí nebo výjimkou, kterou zadáte. Vzhledem k této počáteční události toky uživatelů zobrazuje události, které se stalo před a po během uživatelských relací. Čáry s různou tloušťkou ukazují, kolikrát byli uživatelé sledováni každou cestou. Uzly **Se spuštěním speciální relace** ukazují, kde následující uzly zahájily relaci. Uzly **Ukončené relace** ukazují, kolik uživatelů neodeslalo žádná zobrazení stránek nebo vlastní události po předchozím uzlu, a zvýraznilo místo, kde uživatelé pravděpodobně opustili váš web.

> [!NOTE]
> Prostředek Application Insights musí obsahovat zobrazení stránek nebo vlastní události, aby bylo nutné používat nástroj Toky uživatelů. [Přečtěte si, jak nastavit aplikaci tak, aby automaticky shromažďovala zobrazení stránek pomocí sady Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Začněte výběrem počáteční události

![Výběr počáteční události pro toky uživatelů](./media/usage-flows/00002-flows-initial-event.png)

Chcete-li začít odpovídat na otázky pomocí nástroje Toky uživatelů, zvolte počáteční zobrazení stránky, vlastní událost nebo výjimku, která bude sloužit jako výchozí bod pro vizualizaci:

1. Klikněte na odkaz v názvu **Co uživatelé dělají po...?** **Edit**
2. V rozevíracím seznamu **Počáteční událost** vyberte zobrazení stránky, vlastní událost nebo výjimku.
3. Klepněte na **tlačítko Vytvořit graf**.

Sloupec "Krok 1" vizualizace ukazuje, co uživatelé dělali nejčastěji těsně po počáteční události, seřazené shora dolů od většiny k nejméně častému. Sloupce "Krok 2" a následující sloupce ukazují, co uživatelé poté udělali, a vytvořili obrázek o všech způsobech, jakými uživatelé procházeli vaším webem.

Ve výchozím nastavení nástroj Toky uživatelů náhodně vzorkuje pouze posledních 24 hodin zobrazení stránek a vlastní události z vašeho webu. Časový rozsah můžete zvýšit a změnit vyváženost výkonu a přesnosti náhodného vzorkování v nabídce Úpravy.

Pokud pro vás některá zobrazení stránky, vlastní události a výjimky nejsou relevantní, klikněte na **x** uuzlů, které chcete skrýt. Po výběru uzlů, které chcete skrýt, klikněte pod vizualizací na tlačítko **Vytvořit graf.** Pokud chcete zobrazit všechny skryté uzly, klikněte na tlačítko **Upravit** a podívejte se do části **Vyloučené události.**

Pokud chybí zobrazení stránky nebo vlastní události, které očekáváte, že se zobrazí ve vizualizaci:

* V nabídce **Úpravy** zkontrolujte část **Vyloučené události.**
* Pomocí tlačítek plus **Others** na jiných uzlech můžete do vizualizace zahrnout méně časté události.
* Pokud je zobrazení stránky nebo vlastní událost, kterou očekáváte, uživatelé odesílány zřídka, zkuste zvýšit časový rozsah vizualizace v nabídce **Úpravy.**
* Ujistěte se, že zobrazení stránky, vlastní událost nebo výjimka, kterou očekáváte, bude sbírat sada Application Insights SDK ve zdrojovém kódu vašeho webu. [Přečtěte si další informace o shromažďování vlastních událostí.](../../azure-monitor/app/api-custom-events-metrics.md)

Pokud chcete ve vizualizaci zobrazit další kroky, použijte rozbalovací části **Předchozí a** **Další kroky** nad vizualizací.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Kam po návštěvě stránky nebo funkce jdou uživatelé a na co kliknou?

![Použití toků uživatelů k pochopení, na které uživatelé klikají](./media/usage-flows/00003-flows-one-step.png)

Pokud je počáteční událostí zobrazení stránky, první sloupec (krok 1) vizualizace je rychlý způsob, jak pochopit, co uživatelé udělali ihned po návštěvě stránky. Zkuste web otevřít v okně vedle vizualizace Toky uživatelů. Porovnejte očekávání interakce uživatelů se stránkou se seznamem událostí ve sloupci Krok 1. Prvek ui na stránce, který se zdá být pro váš tým nevýznamný, může často patřit k nejpoužívanějším na stránce. To může být skvělý výchozí bod pro vylepšení návrhu na vašich stránkách.

Pokud je počáteční událost vlastní událostí, první sloupec zobrazuje, co uživatelé provedli hned po provedení této akce. Stejně jako u zobrazení stránky zvažte, zda pozorované chování uživatelů odpovídá cílům a očekáváním vašeho týmu. Pokud je vybraná počáteční událost "Přidána položka do nákupního košíku", podívejte se například, zda se krátce poté ve vizualizaci zobrazí "Přejít na pokladnu" a "Dokončený nákup". Pokud se chování uživatelů liší od vašich očekávání, použijte vizualizaci, abyste pochopili, jak jsou uživatelé "uvězněni" současným návrhem vašeho webu.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Kde jsou místa, která uživatelé konve nejvíce z vašeho webu?

Sledujte uzly **Ukončeno relace,** které se ve sloupci ve vizualizaci zobrazují vysoko, zejména v rané fázi toku. To znamená, že mnoho uživatelů pravděpodobně chrlit z vašeho webu po předchozí cestu stránek a interakcí uživatelského uživatelského nastavení. Někdy se očekává, že konve - například po dokončení nákupu na webu elektronického obchodu - ale obvykle je konve známkou problémů s návrhem, špatného výkonu nebo jiných problémů s vaším webem, které lze zlepšit.

Mějte na paměti, že uzly **ukončené relace** jsou založeny pouze na telemetrii shromážděné tímto prostředkem Application Insights. Pokud Application Insights neobdrží telemetrická data pro určité interakce s uživatelem, uživatelé mohou mít stále interakci s vaším webem těmito způsoby poté, co nástroj Toky uživatelů říká, že relace skončila.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Existují místa, kde uživatelé opakují stejnou akci znovu a znovu?

Vyhledejte zobrazení stránky nebo vlastní událost, která se opakuje mnoho uživatelů v průběhu následujících kroků ve vizualizaci. To obvykle znamená, že uživatelé provádějí na vašem webu opakované akce. Pokud zjistíte opakování, přemýšlejte o změně návrhu webu nebo přidání nových funkcí, abyste snížili opakování. Například přidání funkce hromadné úpravy, pokud zjistíte, že uživatelé provádějí opakované akce na každém řádku prvku tabulky.

## <a name="common-questions"></a>Časté dotazy

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Představuje počáteční událost první událost, která se zobrazí v relaci nebo kdy se zobrazí v relaci?

Počáteční událost ve vizualizaci představuje pouze první čas, kdy uživatel odeslal toto zobrazení stránky nebo vlastní událost během relace. Pokud uživatelé mohou odeslat počáteční událost vícekrát v relaci, pak sloupec "Krok 1" pouze ukazuje, jak se uživatelé chovají po *první* instanci počáteční události, ne všechny instance.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Některé uzly ve vizualizaci jsou příliš vysoké úrovni. Například uzel, který jen říká, že "Kliknutí na tlačítko." Jak ji mohu rozdělit na podrobnější uzly?

Použijte **volby Rozdělit podle** v nabídce **Úpravy:**

1. V nabídce **Událost** vyberte událost, kterou chcete rozdělit.
2. Zvolte dimenzi v nabídce **Dimenze.** Pokud máte například událost s názvem "Kliknutí na tlačítko", zkuste vlastní vlastnost nazvanou Název tlačítka.

## <a name="next-steps"></a>Další kroky

* [Přehled použití](usage-overview.md)
* [Uživatelé, relace a události](usage-segmentation.md)
* [Uchovávání](usage-retention.md)
* [Přidání vlastních událostí do aplikace](../../azure-monitor/app/api-custom-events-metrics.md)
