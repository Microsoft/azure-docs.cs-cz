---
title: Kombinovat sloupce podle příkladu transformace pomocí Azure Machine Learning Workbench
description: V referenčním dokumentu pro transformaci 'kombinovat sloupce podle příkladu.
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: ecd2232dcf77715fdccd4e5518674500231f67ca
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830257"
---
# <a name="combine-columns-by-example-transformation"></a>Sloučení sloupců podle příkladu transformace
Tato transformace umožňuje uživateli přidat nový sloupec kombinací hodnoty z více sloupců. Uživatele můžete určit oddělovač nebo příklady kombinované hodnoty k provedení této transformace. Když uživatel obsahuje příklady kombinaci, transformaci používá stejný **podle příkladu** modul, který se používá v **odvozena sloupec podle příkladu** transformace.

## <a name="how-to-perform-this-transformation"></a>Jak provádět Tato transformace

K provedení této transformace, postupujte takto:
1. Vyberte minimálně dva sloupce, které chcete zkombinovat do jednoho sloupce. 
2. Vyberte **kombinovat sloupce příklad** z **transformuje** nabídky. Nebo klikněte pravým tlačítkem na záhlaví některého z vybraného sloupce a vyberte **kombinovat sloupce příklad** v místní nabídce. Otevře se Editor transformace a nový sloupec je přidán vedle správné nejvíce vybraném sloupci. Nový sloupec obsahuje kombinované hodnoty oddělené oddělovačem výchozí. Vybrané sloupce lze identifikovat podle políček v záhlaví sloupců. Přidávání a odebírání sloupců z výběru lze provést pomocí zaškrtávacích políček.
3. Kombinovaná hodnota ve sloupci nově vytvořený, můžete aktualizovat. Aktualizované hodnoty slouží jako příklad informace pro transformaci.
4. Klikněte na tlačítko **OK** tak, aby přijímal pro transformaci.

### <a name="transform-editor-advanced-mode"></a>Transformace editor: pokročilého režimu

Rozšířený režim poskytuje bohatší možnosti pro kombinace sloupců. 

Výběr **oddělovače** pod **kombinovat sloupce podle** umožňuje uživateli zadat řetězců v **oddělovače** textové pole. Na kartě z **oddělovače** textové pole k zobrazení náhledu na výsledky v data gird. Stiskněte klávesu **OK** potvrzení pro transformaci.

Výběr **příklady** pod **kombinovat sloupce podle** umožňuje uživateli příklady kombinované hodnoty. Ke zvýšení úrovně řádek jako příklad, dvakrát klikněte na řádky v mřížce. Zadejte očekávaný výstup do textového pole proti propagovaných řádek. Na kartě z **oddělovače** textové pole k zobrazení náhledu na výsledky v data gird. Stiskněte klávesu **OK** potvrzení pro transformaci. 

Uživatele můžete přepínat mezi **základní režimu** a **rozšířeném režimu** kliknutím na odkazy v editoru transformace.

### <a name="transform-editor-send-feedback"></a>Transformace editor: váš názor

Kliknutím na **odeslat názor** odkaz otevře **zpětné vazby** poskytl dialogové okno s pole komentáře naplněna příklady uživatele. Uživatel by měl zkontrolovat obsah pole komentáře a obsahují další podrobnosti, abychom tento problém pochopit. Pokud uživatel nechce sdílení dat s Microsoft, uživatel by měl odstranit data předem vyplněnou příklad před kliknutím na tlačítko **odeslat zpětnou vazbu** tlačítko. 

### <a name="editing-existing-transformation"></a>Úprava existující transformace

Uživatele můžete upravit stávající **kombinovat sloupec podle příkladu** transformace výběrem **upravit** možnost kroku transformace. Kliknutím na **upravit** otevře v editoru transformace **základní režimu**. Může uživatel zadat **rozšířeném režimu** kliknutím na odkaz v záhlaví. Všechny příklady, které byly poskytnuty během vytváření pro transformaci se zobrazí existuje.

## <a name="example-using-separators"></a>Příklad použití oddělovačů

Středník a mezerou se používá jako oddělovač v tomto příkladu se zkombinovat *ulici*, *města*, *stavu*, a *ZIP* sloupce.

|Ulice|Město|Stav|ZIP|Sloupec|
|:----|:----|:----|:----|:----|
|16011 N.E. způsob 36th|REDMOND|WA|98052|16011 N.E. 36th způsob, REDMOND, WA, 98052|
|16021 N.E. způsob 36th|REDMOND|WA|98052|16021 N.E. 36th způsob, REDMOND, WA, 98052|
|16031 N.E. způsob 36th|REDMOND|WA|98052|16031 N.E. 36th způsob, REDMOND, WA, 98052|
|16041 N.E. způsob 36th|REDMOND|WA|98052|16041 N.E. 36th způsob, REDMOND, WA, 98052|
|16051 N.E. způsob 36th|REDMOND|WA|98052|16051 N.E. 36th způsob, REDMOND, WA, 98052|
|16061 N.E. způsob 36th|REDMOND|WA|98052|16061 N.E. 36th způsob, REDMOND, WA, 98052|
|3460 157th způsob NE|REDMOND|WA|98052|NE modulu 3460 157th způsob, REDMOND, WA, 98052|
|3350 157th průměr N.E.|REDMOND|WA|98052|N.E. modulu 3350 157th průměr, REDMOND, WA, 98052|
|3240 157th způsob N.E.|REDMOND|WA|98052|N.E. modulu 3240 157th způsob, REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Příklad použití příklad

Hodnota v **tučné** byla poskytnuta jako příklad.

|Datum|Měsíc|Rok|Hodina|Minuta|1 sekunda|Kombinovaná sloupce|
|:----|:----|:----|:----|:----|:----|:----|
|13|Říj|2016|15|01|23|**13. října 2016 15:01:23 PDT**|
|16|Říj|2016|16|22|33|16. října 2016 15:01:33 PDT|
|17|Říj|2016|12|43|12|17. října 2016 15:01:12 PDT|
|12|Lis|2016|14|22|44|12. listopadu 2016 15:01:44 PDT|
|23|Lis|2016|01|52|45|23. listopadu 2016 15:01:45 PDT|
|16|Led|2017|22|34|56|16. ledna 2016 15:01:56 PDT|
|23|Bře|2017|01|55|25|23. března 2016 15:01:25 PDT|
|16|Dub|2017|11|34|36|16. dubna 2016 15:01:36 PDT|

