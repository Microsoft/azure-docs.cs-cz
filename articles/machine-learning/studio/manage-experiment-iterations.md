---
title: Správa iterací experimentu
titleSuffix: ML Studio (classic) - Azure
description: Jak spravovat iterace experimentů v Azure Machine Learning Studio (klasické). Předchozí spuštění experimentů můžete kdykoli zkontrolovat, abyste mohli napadnout, znovu a nakonec potvrdit nebo upřesnit předchozí předpoklady.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 5c8a278f09fdb3b605020e4c2fcf7aa2776906e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204303"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Správa iterací experimentů v Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Vývoj modelu prediktivní analýzy je iterativní proces - při úpravě různých funkcí a parametrů experimentu se výsledky sbíhají, dokud nebudete přesvědčeni, že máte vyškolený a efektivní model. Klíčem k tomuto procesu je sledování různých iterací parametrů experimentu a konfigurací.



Předchozí spuštění experimentů můžete kdykoli zkontrolovat, abyste mohli napadnout, znovu a nakonec potvrdit nebo upřesnit předchozí předpoklady. Při spuštění experimentu Machine Learning Studio (klasické) udržuje historii spuštění, včetně datové sady, modulu a portpřipojení a parametry. Tato historie také zachycuje výsledky, informace za běhu, jako jsou časy spuštění a zastavení, zprávy protokolu a stav spuštění. Můžete se podívat zpět na některý z těchto spuštění kdykoli zkontrolovat chronologii experimentu a průběžné výsledky. Můžete dokonce použít předchozí spuštění experimentu k zahájení do nové fáze dotazování a objevování na vaší cestě k vytváření jednoduchých, složitých nebo dokonce řešení modelování souborů.

> [!NOTE]
> Při zobrazení předchozího spuštění experimentu je tato verze experimentu uzamčena a nelze ji upravovat. Kopii však můžete uložit klepnutím na tlačítko **ULOŽIT JAKO** a zadáním nového názvu kopie. Machine Learning Studio (klasické) otevře novou kopii, kterou pak můžete upravit a spustit. Tato kopie experimentu je k dispozici v seznamu **EXPERIMENTY** spolu se všemi ostatními experimenty.
> 
> 

## <a name="viewing-the-prior-run"></a>Zobrazení předchozího spuštění
Pokud máte otevřený experiment, který jste spustit alespoň jednou, můžete zobrazit předchozí spuštění experimentu klepnutím na **předchozí spuštění** v podokně vlastností.

Předpokládejme například, že vytvoříte experiment a spustíte jeho verze v 11:23, 11:42 a 11:55. Pokud otevřete poslední spuštění experimentu (11:55) a klepnete na **předchozí spuštění**, otevře se verze spuštěná v 11:42.

## <a name="viewing-the-run-history"></a>Zobrazení historie běhu
Všechny předchozí spuštění experimentu můžete zobrazit kliknutím na **zobrazit historii spuštění** v otevřeném experimentu.

Předpokládejme například, že vytvoříte experiment s modulem [lineární regrese][linear-regression] a chcete sledovat vliv změny hodnoty **rychlosti učení** na výsledky experimentu. Experiment spustíte vícekrát s různými hodnotami pro tento parametr, a to následovně:

| Hodnota míry učení | Čas spuštění spuštění |
| --- | --- |
| 0.1 |11.9.2014 16:18:58 |
| 0.2 |11.9.2014 16:24:33 |
| 0,4 |11.9.2014 16:28:36 |
| 0,5 |11.9.2014 16:33:31 |

Klepnete-li na tlačítko **ZOBRAZIT HISTORII SPUŠTĚNÍ**, zobrazí se seznam všech těchto spuštění:

![Příklad historie spuštění](./media/manage-experiment-iterations/viewrunhistory.jpg)

Kliknutím na některou z těchto spuštění zobrazíte snímek experimentu v době, kdy jste experiment spustili. Konfigurace, hodnoty parametrů, komentáře a výsledky jsou zachovány, aby vám úplný záznam o spuštění experimentu.

> [!TIP]
> Chcete-li dokumentovat iterace experimentu, můžete upravit název při každém spuštění, můžete aktualizovat **souhrn** experimentu v podokně vlastností a můžete přidat nebo aktualizovat komentáře k jednotlivým modulům pro záznam změn. Komentáře k názvu, souhrnu a modulu jsou uloženy při každém spuštění experimentu.
> 
> 

Seznam experimentů na kartě **EXPERIMENTY** v Machine Learning Studiu (klasika) vždy zobrazuje nejnovější verzi experimentu. Pokud otevřete předchozí spuštění experimentu (pomocí **předchozího spuštění** nebo **zobrazit historii spuštění**), můžete se vrátit k verzi konceptu klepnutím na zobrazit historii **spuštění** a výběrem iterací, která má **stav** **upravitelné**.

## <a name="iterating-on-a-previous-run"></a>Iterace při předchozím spuštění
Když klepnete na **tlačítko Předchozí spuštění** nebo Zobrazit historii **spuštění** a otevřete předchozí spuštění, můžete zobrazit dokončený experiment v režimu jen pro čtení.

Pokud chcete zahájit iteraci experimentu počínaje způsobem, jakým jste jej nakonfigurovali pro předchozí spuštění, můžete to provést otevřením spuštění a klepnutím na tlačítko **ULOŽIT JAKO**. Tím se vytvoří nový experiment s novým názvem, prázdnou historií spuštění a všemi součástmi a hodnotami parametrů předchozího spuštění. Tento nový experiment je uveden na kartě **EXPERIMENTY** na domovské stránce Machine Learning Studio (klasická) a můžete jej upravit a spustit a spustit novou historii spuštění pro tuto iteraci experimentu. 

Předpokládejme například, že máte historii spuštění experimentu zobrazenou v předchozí části. Chcete sledovat, co se stane, když nastavíte rychlost **učení** parametr 0,4 a zkuste různé hodnoty pro **počet epoch školení.**

1. Klepněte na tlačítko **ZOBRAZIT HISTORII SPUŠTĚNÍ** a otevřete iteraci experimentu, který jste spustili v 16:28:36 (ve kterém nastavíte hodnotu parametru na 0,4).
2. Klepněte na tlačítko **ULOŽIT JAKO**.
3. Zadejte nový název a klikněte na zaškrtávač **OK.** Vytvoří se nová kopie experimentu.
4. Upravte parametr **Počet tréninkových epoch.**
5. Klepněte na tlačítko **SPUSTIT**.

Nyní můžete pokračovat v úpravách a spouštění této verze experimentu a vytvářet novou historii spuštění pro záznam vaší práce.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
