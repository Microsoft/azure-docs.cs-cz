---
title: Kohorty využití přehledů aplikací Azure | Dokumenty společnosti Microsoft
description: Analýza různých sad nebo uživatelů, relací, událostí nebo operací, které mají něco společného
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0c552e8f3e732c49da02b2f5704fb9cf312fb3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671082"
---
# <a name="application-insights-cohorts"></a>Kohorty Application Insights

Kohorta je sada uživatelů, relací, událostí nebo operací, které mají něco společného. V Azure Application Insights kohorty jsou definovány analytický dotaz. V případech, kdy musíte opakovaně analyzovat určitou sadu uživatelů nebo událostí, vám kohorty mohou poskytnout větší flexibilitu, abyste mohli vyjádřit přesně sadu, která vás zajímá.

![Podokno Kohorty](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorty versus základní filtry

Kohorty se používají podobným způsobem jako filtry. Definice kohort jsou však sestaveny z vlastních analytických dotazů, takže jsou mnohem přizpůsobivější a složitější. Na rozdíl od filtrů můžete uložit kohorty, aby je ostatní členové vašeho týmu mohli znovu použít.

Můžete definovat kohortu uživatelů, kteří všichni vyzkoušeli novou funkci ve vaší aplikaci. Tuto kohortu můžete uložit do prostředku Application Insights. Je snadné analyzovat tuto uloženou skupinu konkrétních uživatelů v budoucnu.

> [!NOTE]
> Po vytvoření jsou kohorty dostupné z nástrojů Uživatelé, Relace, Události a Toky uživatelů.

## <a name="example-engaged-users"></a>Příklad: Angažovaní uživatelé

Váš tým definuje angažovaného uživatele jako každého, kdo používá vaši aplikaci pětkrát nebo vícekrát v daném měsíci. V této části definujete kohortu těchto zapojených uživatelů.

1. Otevřete nástroj Kohorty.

2. Vyberte kartu **Galerie šablon.** Zobrazí se kolekce šablon pro různé kohorty.

3. Vyberte **angažovaní uživatelé – podle použitých dnů**.

    Pro tuto kohortu existují tři parametry:
    * **Aktivity**, kde zvolíte, které události a zobrazení stránek se počítají jako "využití".
    * **Období**, definice měsíce.
    * **UsedAtLeastCustom**, kolikrát uživatelé potřebují použít něco v rámci období počítat jako zabývá.

4. Změnit **UsedAtLeastCustom** na **5 + dny**a nechte **období** na výchozí 28 dnů.

    ![Angažovaní uživatelé](./media/usage-cohorts/003.png)

    Nyní tato kohorta představuje všechna ID uživatelů odeslaná s jakoukoli vlastní událostí nebo zobrazením stránky v 5 samostatných dnech v posledních 28.

5. Vyberte **Uložit**.

   > [!TIP]
   > Dejte své kohortě jméno, například "Angažovaní uživatelé (5+ dní)." Uložte jej do "Moje sestavy" nebo "Sdílené sestavy", v závislosti na tom, zda chcete, aby ostatní uživatelé, kteří mají přístup k tomuto prostředku Application Insights vidět tuto kohortu.

6. Vyberte **Možnost Zpět do galerie**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Co můžete dělat pomocí této kohorty?

Otevřete nástroj Uživatelé. V rozevíracím poli **Zobrazit** vyberte kohortu, kterou jste vytvořili v části **Uživatelé, kteří patří do .**

Nyní je nástroj Uživatelé filtrován na tuto kohortu uživatelů:

![Podokno Uživatelé filtrované podle určité kohorty](./media/usage-cohorts/004.png)

Několik důležitých věcí, které si všimnete:

* Tuto sadu nelze vytvořit pomocí normálních filtrů. Logika data je pokročilejší.
* Tuto kohortu můžete dále filtrovat pomocí normálních filtrů v nástroji Uživatelé. Takže i když je kohorta definována v 28denních oknech, stále můžete upravit časový rozsah v nástroji Uživatelé na 30, 60 nebo 90 dní.

Tyto filtry podporují složitější otázky, které nelze vyjádřit prostřednictvím tvůrce dotazů. Příkladem jsou _lidé, kteří se v posledních 28 dnech angažovali. Jak se ti samí lidé chovali posledních 60 dní?_

## <a name="example-events-cohort"></a>Příklad: Kohorta událostí

Můžete také vytvořit kohorty událostí. V této části definujete kohortu událostí a zobrazení stránek. Pak uvidíte, jak je používat z jiných nástrojů. Tato kohorta může definovat sadu událostí, které váš tým považuje za _aktivní použití_ nebo sadu související s určitou novou funkcí.

1. Otevřete nástroj Kohorty.

2. Vyberte kartu **Galerie šablon.** Zobrazí se kolekce šablon pro různé kohorty.

3. Vyberte **výběr událostí**.

    ![Snímek obrazovky s výběrem událostí](./media/usage-cohorts/006.png)

4. V rozevíracím seznamu **Aktivity** vyberte události, které chcete v kohortě mít.

5. Uložte kohortu a pojmenujte ji.

## <a name="example-active-users-where-you-modify-a-query"></a>Příklad: Aktivní uživatelé, u kterých upravujete dotaz

Předchozí dvě kohorty byly definovány pomocí rozevíracích seznamů. Kohorty však můžete definovat také pomocí analytických dotazů pro úplnou flexibilitu. Chcete-li zjistit, jak, vytvořte kohortu uživatelů ze Spojeného království.

![Animovaný obrázek prochází pomocí nástroje Kohorty](./media/usage-cohorts/cohorts0001.gif)

1. Otevřete nástroj Kohorty, vyberte kartu **Galerie šablon** a vyberte **kohorta Prázdné uživatelé**.

    ![Kohorta prázdných uživatelů](./media/usage-cohorts/001.png)

    Existují tři části:
   * Textová sekce Markdown, kde kohortu podrobněji popisujete pro ostatní ve vašem týmu.

   * Část parametrů, kde můžete vytvořit vlastní parametry, jako **aktivity** a další rozevírací pole z předchozích dvou příkladů.

   * Část dotazu, kde definujete kohortu pomocí analytického dotazu.

     V části dotazu [napíšete analytický dotaz](/azure/kusto/query). Dotaz vybere určitou sadu řádků, které popisují kohortu, kterou chcete definovat. Nástroj Kohorty pak implicitně přidá "| shrnout podle user_Id" klauzule k dotazu. Tato data jsou zobrazena pod dotazem v tabulce, takže se můžete ujistit, že dotaz vrací výsledky.

     > [!NOTE]
     > Pokud dotaz nevidíte, zkuste oddíl zpřesnit tak, aby byl vyšší, a dotaz zjevte. Animovaný .gif na začátku této části ilustruje chování změny velikosti.

2. Zkopírujte a vložte do editoru dotazů následující text:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Vyberte **Spustit dotaz**. Pokud se v tabulce nezobrazují ID uživatelů, změňte na zemi nebo oblast, kde má vaše aplikace uživatele.

4. Uložit a pojmenovat kohortu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

_Definoval jsem kohortu uživatelů z určité země/oblasti. Když porovnám tuto kohortu v nástroji Uživatelé pouze s nastavením filtru pro tuto zemi nebo oblast, vidím různé výsledky. Proč?_

Kohorty a filtry se liší. Předpokládejme, že máte kohortu uživatelů ze Spojeného království (definované jako v předchozím příkladu) a porovnat jeho výsledky s nastavením filtru "Země nebo oblast = Spojené království."

* Verze kohorty zobrazuje všechny události od uživatelů, kteří odeslali jednu nebo více událostí ze Spojeného království v aktuálním časovém rozsahu. Pokud rozdělíte podle země nebo oblasti, pravděpodobně se zobrazí mnoho zemí a oblastí.
* Verze filtrů zobrazuje pouze události ze Spojeného království. Pokud se však rozdělíte podle země nebo oblasti, zobrazí se pouze Spojené království.

## <a name="learn-more"></a>Další informace

* [Analytický dotazovací jazyk](https://go.microsoft.com/fwlink/?linkid=856587)
* [Uživatelé, relace, události](usage-segmentation.md)
* [Toky uživatelů](usage-flows.md)
* [Přehled použití](usage-overview.md)
