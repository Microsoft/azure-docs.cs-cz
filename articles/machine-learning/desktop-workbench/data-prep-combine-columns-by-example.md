---
title: Sloučení sloupců podle příkladu transformaci pomocí Azure Machine Learning Workbench
description: Referenční dokument pro transformaci "zkombinovat sloupce podle příkladu.
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 621601ad3576aad13f2f71062ee2351cf1a394c8
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643206"
---
# <a name="combine-columns-by-example-transformation"></a>Sloučení sloupců podle příkladu transformace
Tato transformace umožňuje uživateli přidat nový sloupec kombinací hodnot z více sloupců. Uživatel může zadat oddělovač nebo obsahují příklady kombinovaná hodnoty k provedení této transformace. Když uživatel zadá příklady kombinaci, transformace je zpracována stejným **podle příkladu** modul, který je používán **odvodit sloupec podle příkladu** transformace.

## <a name="how-to-perform-this-transformation"></a>Jak provést tuto transformaci

Pokud chcete provést tuto transformaci, postupujte takto:
1. Vyberte dvě nebo více sloupců, které chcete zkombinovat do jednoho sloupce. 
2. Vyberte **zkombinovat sloupce podle příkladu** z **transformuje** nabídky. Nebo klikněte pravým tlačítkem na záhlaví libovolného vybraného sloupce a vyberte **zkombinovat sloupce podle příkladu** v místní nabídce. Otevře se Editor transformovat a vedle správné nejvíce vybraný sloupec je přidán nový sloupec. Nový sloupec obsahuje kombinované hodnoty oddělené oddělovačem výchozí. Vybrané sloupce lze identifikovat pomocí zaškrtávacích políček v záhlaví sloupců. Přidávání a odebírání sloupců z výběru lze provést pomocí zaškrtávacích políček.
3. Kombinovaná hodnota ve sloupci nově vytvořený, můžete aktualizovat. Aktualizovaná hodnota slouží jako příklad další transformaci.
4. Klikněte na tlačítko **OK** transformaci přijměte.

### <a name="transform-editor-advanced-mode"></a>Transformace editor: rozšířeném režimu

Rozšířený režim poskytuje pohodlnější a pestřejší prostředí pro kombinování sloupce. 

Výběr **oddělovač** pod **zkombinovat sloupce podle** umožňuje uživateli zadat řetězce v **oddělovač** textového pole. Karty navýšení kapacity **oddělovač** textové pole pro zobrazení náhledu na výsledky v data gird. Stisknutím klávesy **OK** Potvrdit transformaci.

Výběr **příklady** pod **zkombinovat sloupce podle** umožňuje uživatelům poskytnout příklady kombinovaná hodnot. Ke zvýšení úrovně řádků jako příklad, dvakrát klikněte na řádků v mřížce. Zadejte očekávaný výstup do textového pole na řádku se zvýšenou úrovní. Karty navýšení kapacity **oddělovač** textové pole pro zobrazení náhledu na výsledky v data gird. Stisknutím klávesy **OK** Potvrdit transformaci. 

Uživatele můžete přepínat mezi **základním režimu** a **rozšířený režim** kliknutím na odkazy v editoru transformace.

### <a name="transform-editor-send-feedback"></a>Transformace editor: váš názor

Kliknutím na **poslat svůj názor** odkaz otevře **zpětnou vazbu** dialogové okno s pole komentáře naplněna příklady uživatel poskytl. Uživatel by měl zkontrolovat obsah pole komentáře a poskytují další podrobnosti o nám pomůže zjistit potíže. Pokud uživatel nechce sdílet data s Microsoftem, uživatel by měl odstranit předem vyplněná ukázková data před kliknutím na tlačítko **odeslat zpětnou vazbu** tlačítko. 

### <a name="editing-existing-transformation"></a>Úprava existující transformace

Uživatel může upravovat existující **zkombinovat sloupce podle příkladu** transformovat tak, že vyberete **upravit** kroku transformace. Kliknutím na **upravit** otevře Editor transformace v **základním režimu**. Uživatel můžete zadat **rozšířený režim** kliknutím na odkaz v záhlaví. Všechny příklady, které byly poskytnuty při vytváření transformace jsou uvedeny zde.

## <a name="example-using-separators"></a>Příklad použití oddělovače

Čárkou následovanou mezerou se používá jako oddělovač v tomto příkladu zkombinovat *ulice*, *Město*, *stavu*, a *ZIP* sloupce.

|Ulice|Město|Stav|PSČ|Sloupec|
|:----|:----|:----|:----|:----|
|16011 N.E. způsob 36th|REDMOND|WA|98052|16011 N.E. 36th way, REDMOND, WA, 98052|
|16021 N.E. způsob 36th|REDMOND|WA|98052|16021 N.E. 36th way, REDMOND, WA, 98052|
|16031 N.E. způsob 36th|REDMOND|WA|98052|16031 N.E. 36th way, REDMOND, WA, 98052|
|16041 N.E. způsob 36th|REDMOND|WA|98052|16041 N.E. 36th way, REDMOND, WA, 98052|
|16051 N.E. způsob 36th|REDMOND|WA|98052|16051 N.E. 36th way, REDMOND, WA, 98052|
|16061 N.E. způsob 36th|REDMOND|WA|98052|16061 N.E. 36th way, REDMOND, WA, 98052|
|3460 157th zacházíme NE|REDMOND|WA|98052|NE modulu 3460 157th ulici, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|N.E. modulu 3350 157th uložit, REDMOND, WA, 98052|
|3240 157th zacházíme N.E.|REDMOND|WA|98052|N.E. modulu 3240 157th ulici, REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Příklad použití podle příkladu

Hodnota v **tučné** byla poskytnuta jako příklad.

|Datum|Měsíc|Rok|Hodina|Minuta|1 sekunda|Kombinované sloupec|
|:----|:----|:----|:----|:----|:----|:----|
|13|Říj|2016|15|01|23|**13. října 2016 15:01:23 PDT**|
|16|Říj|2016|16|22|33|16. října 2016 15:01:33 PDT|
|17|Říj|2016|12|43|12|17. října 2016 15:01:12 PDT|
|12|Lis|2016|14|22|44|12. listopadu 2016 15:01:44 PDT|
|23|Lis|2016|01|52|45|23. listopadu 2016 15:01:45 PDT|
|16|Led|2017|22|34|56|16. ledna 2016 15:01:56 PDT|
|23|Bře|2017|01|55|25|23. března 2016 15:01:25 PDT|
|16|Dub|2017|11|34|36|16. dubna 2016 15:01:36 PDT|

