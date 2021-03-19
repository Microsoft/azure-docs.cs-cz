---
title: Azure Application Insights Toky uživatelů analyzují navigační toky
description: Analyzujte, jak uživatelé přecházejí mezi stránkami a funkcemi vaší webové aplikace.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a31ab24b96fa44787d08801a0680f94ff98bb5a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87309265"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analýza uživatelských vzorů navigace pomocí Toky uživatelů v Application Insights

![Nástroj Application Insights Toky uživatelů](./media/usage-flows/flows.png)

Nástroj Toky uživatelů vizualizuje, jak uživatelé pohybují mezi stránkami a funkcemi vaší lokality. Je skvělé pro zodpovězení otázek, jako jsou:

* Jak uživatelé přecházejí ze stránky na webu?
* Co uživatelé na webu na stránce klikní?
* Kde jsou místa, která uživatelé na svém webu přecházejí?
* Existují místa, kde uživatelé opakují stejnou akci přes a?

Nástroj Toky uživatelů začíná od počátečního zobrazení stránky, vlastní události nebo výjimky, kterou zadáte. Při této počáteční události Toky uživatelů zobrazuje události, ke kterým došlo před a následně během uživatelských relací. Řádky s různou tloušťkou ukazují, kolikrát byly jednotlivé cesty následovány uživateli. V uzlech, které jsou **spuštěny** jinak, se zobrazí, kde následující uzly zahájily relaci. Uzly **ukončení relace** ukazují, kolik uživatelů neodeslalo žádné zobrazení stránky nebo vlastní události za předchozím uzlem a zvýrazní, kde uživatelé pravděpodobně webový server opustí.

> [!NOTE]
> Váš Application Insights prostředek musí obsahovat zobrazení stránky nebo vlastní události pro použití nástroje Toky uživatelů. [Naučte se, jak nastavit aplikaci tak, aby automaticky shromáždila zobrazení stránek pomocí Application Insights JavaScript SDK](./javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Začněte tím, že vyberete počáteční událost.

![Vyberte počáteční událost pro Toky uživatelů](./media/usage-flows/initial-event.png)

Chcete-li začít s odpovědí na dotazy pomocí nástroje Toky uživatelů, vyberte počáteční zobrazení stránky, vlastní událost nebo výjimku, která slouží jako výchozí bod pro vizualizaci:

1. Klikněte na odkaz v nadpisu **co uživatelé vydávat po...?** nebo klikněte na tlačítko **Upravit** .
2. V rozevíracím seznamu **Počáteční události** vyberte zobrazení stránky, vlastní událost nebo výjimku.
3. Klikněte na **vytvořit graf**.

Sloupec "krok 1" ve vizualizaci ukazuje, co uživatelé provedli nejčastěji hned po počáteční události, seřazené shora dolů od nejmenších po nejnižší, jak často. V části "Step 2" a následující sloupce se dozvíte, co uživatelé provedli a vytvořili jsme obrázek všech způsobů, kterými uživatelé přešli přes web.

Ve výchozím nastavení nástroj Toky uživatelů náhodně vzorkuje pouze posledních 24 hodin zobrazení stránky a vlastní událost z vašeho webu. Můžete zvýšit časový rozsah a změnit rovnováhu výkonu a přesnosti náhodného vzorkování v nabídce Upravit.

Pokud se některé zobrazení stránky, vlastní události a výjimky netýkají, klikněte na **X** na uzlech, které chcete skrýt. Po výběru uzlů, které chcete skrýt, klikněte na tlačítko **vytvořit graf** pod vizualizací. Chcete-li zobrazit všechny uzly, které jste skryli, klikněte na tlačítko **Upravit** a podívejte se na část **vyloučené události** .

Pokud chybí zobrazení stránky nebo vlastní události, které očekáváte, že se zobrazí ve vizualizaci:

* V nabídce **Upravit** se podívejte na část **vyloučené události** .
* Pomocí tlačítek plus v **ostatních** uzlech zahrňte do vizualizace méně časté události.
* Pokud uživatelé neočekávají zobrazení stránky nebo vlastní událost, zkuste zvýšit časový rozsah vizualizace v nabídce **Upravit** .
* Ujistěte se, že zobrazení stránky, vlastní událost nebo výjimka, které očekáváte, se shromažďují sadou Application Insights SDK ve zdrojovém kódu vašeho webu. [Přečtěte si další informace o shromažďování vlastních událostí.](./api-custom-events-metrics.md)

Pokud chcete zobrazit další kroky ve vizualizaci, použijte v rozevíracích seznamech **předchozí kroky** a **Další kroky** nad vizualizací.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Po navštívení stránky nebo funkce, kde uživatelé přejdou a co na ně klikne?

![Použití Toky uživatelů k pochopení, kde uživatel klikne](./media/usage-flows/one-step.png)

Pokud vaše počáteční událost představuje zobrazení stránky, je prvním sloupcem ("krok 1") vizualizace rychlý způsob, jak zjistit, co uživatelé ihned po návštěvě stránky navštívili. Zkuste otevřít web v okně vedle Toky uživatelů vizualizace. Porovnejte vaše očekávání, jak uživatelé pracují se stránkou, do seznamu událostí ve sloupci "krok 1". Často je prvek uživatelského rozhraní na stránce, který se jeví jako nevýznamný pro váš tým, může vycházet z nejvíce používaného na stránce. Může se jednat o skvělý výchozí bod pro vylepšení návrhu webu.

Pokud je vaše počáteční událost vlastní událostí, zobrazí se v prvním sloupci, co uživatelé prováděli hned po provedení této akce. Stejně jako u zobrazení stránky zvažte, zda chování vašich uživatelů vyhovuje cílům a očekáváním vašeho týmu. Pokud vaše vybraná počáteční událost je "přidaná položka do nákupního košíku", například, podívejte se, jestli se ve vizualizaci krátce zobrazí možnost přejít na rezervaci a dokončený nákup. Pokud se chování uživatele liší od vašich očekávání, pomocí vizualizace porozumíte tomu, jak uživatelé získávají "zachycení" aktuálním návrhem vašeho webu.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Kde jsou místa, která uživatelé na svém webu přecházejí?

Sledujte, že se ve sloupci ve vizualizaci **ukončí** uzly, které se ve vizualizaci nacházejí v horní části, obzvláště včas v toku. To znamená, že mnoho uživatelů se pravděpodobně z vaší lokality rozchází za následující cestu stránek a interakce uživatelského rozhraní. Někdy se očekává změny – po dokončení nákupu na webu elektronického obchodování, například, obvykle se jedná o znaménko problémů s návrhem, špatný výkon nebo jiné problémy s webem, které je možné zlepšit.

Pamatujte na to, že uzly **ukončené relace** jsou založené jenom na telemetrie shromážděné tímto Application InsightSM prostředku. Pokud Application Insights neobdrží telemetrie pro určité interakce s uživatelem, můžou uživatelé dál spolupracovat s webem, a to i po skončení Toky uživatelůho nástroje říká, že relace skončila.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Existují místa, kde uživatelé opakují stejnou akci přes a?

Vyhledejte zobrazení stránky nebo vlastní událost, která se opakuje mnoha uživateli v dalších krocích ve vizualizaci. To obvykle znamená, že uživatelé na svém webu provádějí opakující se akce. Pokud zjistíte opakování, zamyslete se nad změnou návrhu webu nebo přidáním nových funkcí pro snížení opakování. Například přidání funkce hromadných úprav, pokud najdete uživatele, kteří provádějí opakující se akce na každém řádku elementu tabulky.

## <a name="common-questions"></a>Časté dotazy

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Představuje počáteční událost při prvním zobrazení události v relaci nebo při každém zobrazení v relaci?

Počáteční událost na vizualizaci představuje pouze první, kdy uživatel poslal zobrazení stránky nebo vlastní událost během relace. Pokud uživatelé mohou v relaci několikrát odeslat počáteční událost, pak sloupec "krok 1" ukazuje pouze to, jak se uživatelé chovají po *první* instanci počáteční události, ne na všechny instance.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Některé uzly ve vizualizaci jsou příliš vysoké úrovně. Například uzel, který přesně říká "kliknutí na tlačítko". Jak ho můžu rozdělit do podrobnějších uzlů?

Použijte možnosti **rozdělit podle** v nabídce **Upravit** :

1. V nabídce **událost** vyberte událost, kterou chcete rozdělit.
2. V nabídce **dimenze** vyberte dimenzi. Například pokud máte událost s názvem "kliknuli na tlačítko", "Vyzkoušejte si vlastní vlastnost nazvanou" název tlačítka ".

## <a name="next-steps"></a>Další kroky

* [Přehled využití](usage-overview.md)
* [Uživatelé, relace a události](usage-segmentation.md)
* [Toalet](usage-retention.md)
* [Přidání vlastních událostí do aplikace](./api-custom-events-metrics.md)

