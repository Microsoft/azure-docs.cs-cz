---
title: Kohorty využití Azure Application Insights | Microsoft Docs
description: Analýza různých sad nebo uživatelů, relací, událostí nebo operací, které mají něco společného
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d12244bce117d6a631fdb67dc460af18bc77028c
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995743"
---
# <a name="application-insights-cohorts"></a>Application Insights kohorty

Kohorta je sada uživatelů, relací, událostí nebo operací, které mají něco společného. V Azure Application Insights jsou kohorty definovány dotazem Analytics. V případech, kdy je potřeba analyzovat konkrétní sadu uživatelů nebo událostí opakovaně, vám kohorty může poskytnout větší flexibilitu, abyste přesně vyjádřili sadu, které vás zajímá.

![Podokno kohorty](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorty oproti základním filtrům

Kohorty se používají způsobem podobným filtrům. Ale definice kohorty jsou sestavené z vlastních analytických dotazů, takže jsou mnohem flexibilnější a komplexní. Na rozdíl od filtrů můžete ukládat kohorty, aby je ostatní členové týmu mohli znovu použít.

Můžete definovat kohorta uživatele, u kterých se všechna vyzkoušela nová funkce ve vaší aplikaci. Tento kohorta můžete uložit do prostředku Application Insights. V budoucnu je snadné analyzovat tuto uloženou skupinu konkrétních uživatelů.

> [!NOTE]
> Po vytvoření jsou kohorty k dispozici z nástrojů Uživatelé, relace, události a Toky uživatelů.

## <a name="example-engaged-users"></a>Příklad: zapojení uživatelů

Váš tým definuje určitého uživatele jako kdokoli, kdo v daném měsíci používá aplikaci pětkrát nebo vícekrát. V této části definujete kohorta těchto spolupracujících uživatelů.

1. Otevřete nástroj kohorty.

2. Vyberte kartu **Galerie šablon** . Zobrazí se kolekce šablon pro různé kohorty.

3. Vyberte možnost **Uživatelé, kteří se používají**.

    Pro tento kohorta existují tři parametry:
    * **Aktivity**, kde si zvolíte, které události a zobrazení stránek se počítají jako "využití".
    * **Období**– definice měsíce.
    * **UsedAtLeastCustom**počet pokusů, kolikrát uživatelé potřebují použít nějakou dobu v rámci období, aby se mohli počítat jako zařazené.

4. Změňte **UsedAtLeastCustom** na **5 dní**a nechte **dobu** ve výchozím nastavení 28 dnů.

    ![Zapojení uživatelé](./media/usage-cohorts/003.png)

    Tento kohorta nyní představuje všechna ID uživatelů odesílaná pomocí vlastní události nebo zobrazení stránky o 5 samostatných dní v posledních 28 dnech.

5. Vyberte **Uložit**.

   > [!TIP]
   > Dejte své kohorta jméno, například "propojující uživatelé (5 a dny)." V závislosti na Application Insights tom, jestli chcete, aby se kohorta viděli, uložte ho do sestav "Moje sestavy" nebo "sdílené sestavy".

6. Vyberte **zpět do galerie**.

### <a name="what-can-you-do-by-using-this-cohort"></a>K čemu se dá využít tento kohorta?

Otevřete nástroj Uživatelé. V rozevíracím seznamu **Zobrazit** vyberte kohorta, který jste vytvořili v části **Uživatelé, kteří patří do**.

Nástroj pro uživatele je teď filtrovaný na tento kohorta uživatelů:

![Podokno uživatelů filtrované na konkrétní kohorta](./media/usage-cohorts/004.png)

Několik důležitých věcí, které je potřeba si všimnout:

* Tuto sadu nemůžete vytvořit prostřednictvím normálních filtrů. Logika data je pokročilejší.
* Tento kohorta můžete dál filtrovat pomocí normálních filtrů v nástroji Uživatelé. Takže i když je kohorta definovaná na 28 dní, můžete přesto upravit časový rozsah v nástroji Uživatelé na 30, 60 nebo 90 dnů.

Tyto filtry podporují složitější otázky, které není možné vyjádřit prostřednictvím Tvůrce dotazů. Příkladem jsou _lidé, kteří zapracovali během posledních 28 dnů. Jak se tyto osoby chovají během posledních 60 dnů?_

## <a name="example-events-cohort"></a>Příklad: Events kohorta

Můžete také vytvořit kohorty událostí. V této části definujete kohorta událostí a zobrazení stránek. Pak uvidíte, jak je používat z jiných nástrojů. Tento kohorta může definovat sadu událostí, které váš tým považuje za _aktivní používání_ nebo sadu související s určitou novou funkcí.

1. Otevřete nástroj kohorty.

2. Vyberte kartu **Galerie šablon** . Zobrazí se kolekce šablon pro různé kohorty.

3. Vyberte **Výběr událostí**.

    ![Snímek obrazovky pro výběr událostí](./media/usage-cohorts/006.png)

4. V rozevíracím seznamu **aktivity** vyberte události, které chcete mít v kohorta.

5. Uložte kohorta a pojmenujte ho.

## <a name="example-active-users-where-you-modify-a-query"></a>Příklad: aktivní uživatelé, kde můžete upravit dotaz

Předchozí dva kohorty byly definovány pomocí rozevíracích seznamů. Můžete ale také definovat kohorty pomocí analytických dotazů pro celkovou flexibilitu. Pokud chcete zjistit, jak, vytvořte kohorta uživatelů ze Spojeného království.

![Animovaný obrázek procházející použitím nástroje kohorty](./media/usage-cohorts/cohorts0001.gif)

1. Otevřete nástroj kohorty, vyberte kartu **Galerie šablon** a vyberte **prázdné uživatele kohorta**.

    ![Prázdná kohorta uživatelé](./media/usage-cohorts/001.png)

    Existují tři části:
   * Textový oddíl Markdownu, kde popíšete kohorta podrobněji pro ostatní uživatele v týmu.

   * Oddíl Parameters, kde můžete nastavit vlastní parametry, jako jsou **aktivity** a další rozevírací seznamy v předchozích dvou příkladech.

   * Část dotazu, kde můžete definovat kohorta pomocí analytického dotazu.

     V části dotaz [napíšete dotaz Analytics](/azure/kusto/query). Dotaz vybere určitou sadu řádků, které popisují kohorta, který chcete definovat. Nástroj kohorty pak implicitně přidá "| provede Shrnutí podle klauzule user_Id dotazu. Tato data jsou zobrazená pod dotazem v tabulce, takže můžete zajistit, aby dotaz vrátil výsledky.

     > [!NOTE]
     > Pokud se dotaz nezobrazuje, zkuste změnit velikost oddílu, aby se zajistilo jeho větší a odhalte dotaz. Animovaný soubor GIF na začátku této části znázorňuje chování při změně velikosti.

2. Zkopírujte následující text a vložte ho do editoru dotazů:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Vyberte **Spustit dotaz**. Pokud se v tabulce nezobrazí ID uživatelů, přejděte do země nebo oblasti, kde má vaše aplikace uživatele.

4. Uložte a pojmenujte kohorta.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

_Definoval (a) jsem kohorta uživatele z určité země nebo oblasti. Po porovnání tohoto kohorta v nástroji Uživatelé, který stačí nastavit filtr v dané zemi nebo oblasti, se zobrazí různé výsledky. Proč?_

Kohorty a filtry se liší. Předpokládejme, že máte kohorta uživatele ze Spojeného království (definované jako v předchozím příkladu), a porovnáte jeho výsledky s nastavením filtru "země nebo oblast = Spojené království".

* Verze kohorta zobrazuje všechny události uživatelů, kteří odeslali jednu nebo více událostí z Spojeného království v aktuálním časovém rozsahu. Pokud rozdělíte zemi nebo oblast, pravděpodobně se zobrazí řada zemí a oblastí.
* Verze Filters zobrazuje pouze události ze Spojeného království. Pokud však rozdělíte podle země nebo oblasti, zobrazí se pouze Spojené království.

## <a name="learn-more"></a>Další informace

* [Analytický dotazovací jazyk](../log-query/get-started-portal.md?toc=%252fazure%252fazure-monitor%252ftoc.json)
* [Uživatelé, relace, události](usage-segmentation.md)
* [Toky uživatelů](usage-flows.md)
* [Přehled využití](usage-overview.md)