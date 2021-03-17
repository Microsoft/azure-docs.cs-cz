---
title: 'ML Studio (Classic): zobrazení & opětovného spuštění experimentů – Azure'
description: Spravujte experimenty v Azure Machine Learning Studio (Classic). Předchozí běhy experimentů můžete kdykoli projít, abyste mohli vyvolávat výzvu, znovu přejít a nakonec buď potvrdit nebo Upřesnit předchozí předpoklady.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 419a696da1244afab7aa03cd8c4521ea819a5298
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515950"
---
# <a name="manage-experiment-runs-in-azure-machine-learning-studio-classic"></a>Správa experimentů v Azure Machine Learning Studio (Classic)

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Vývoj modelu prediktivní analýzy je iterativní proces – při úpravě různých funkcí a parametrů experimentu se vaše výsledky konvergují, dokud nebudete přesvědčeni, že máte vyškolený a efektivní model. Klíč k tomuto procesu sleduje různé iterace parametrů experimentu a konfigurací.

Předchozí běhy experimentů můžete kdykoli projít, abyste mohli vyvolávat výzvu, znovu přejít a nakonec buď potvrdit nebo Upřesnit předchozí předpoklady. Když spustíte experiment, Machine Learning Studio (Classic) udržuje historii spuštění, včetně datových sad, modulů a připojení portů a parametrů. Tato historie také zachycuje výsledky, běhové informace, například časy spuštění a zastavení, zprávy protokolu a stav spuštění. Kdykoli se můžete podívat na kterékoli z těchto běhů, abyste si zkontrolovali chronologii experimentu a mezilehlé výsledky. Můžete dokonce použít předchozí spuštění experimentu a spustit ho do nové fáze dotazování a zjišťování ve vaší cestě k vytváření jednoduchých, komplexních nebo dokonce strukturovaných řešení modelování.

> [!NOTE]
> Když si zobrazíte předchozí spuštění experimentu, bude tato verze experimentu uzamčena a nelze ji upravit. Kopii můžete ale uložit kliknutím na **Uložit jako** a zadáním nového názvu pro kopii. Machine Learning Studio (Classic) otevře novou kopii, kterou pak můžete upravit a spustit. Tato kopie experimentu je k dispozici v seznamu **experimenty** společně se všemi vašimi dalšími experimenty.
> 
> 

## <a name="view-the-prior-run"></a>Zobrazit předchozí spuštění
Po otevření experimentu, který jste spustili aspoň jednou, si můžete prohlédnout předchozí spuštění experimentu kliknutím na **předchozí spuštění** v podokně Vlastnosti.

Předpokládejme například, že vytvoříte experiment a spustíte jeho verze v 11:23, 11:42 a 11:55. Pokud otevřete poslední spuštění experimentu (11:55) a kliknete na **předchozí spuštění**, otevře se verze, kterou jste spustili v 11:42.

## <a name="view-the-run-history"></a>Zobrazit historii spuštění
Kliknutím na **Zobrazit historii spuštění** v otevřeném experimentu můžete zobrazit všechna předchozí spuštění experimentu.

Předpokládejme například, že vytvoříte experiment s modulem [lineární regrese][linear-regression] a chcete sledovat účinek změny hodnoty **studijních** kurzů na výsledky experimentů. Experiment spouštíte několikrát s různými hodnotami pro tento parametr, a to následujícím způsobem:

| Hodnota studijní frekvence | Čas spuštění |
| --- | --- |
| 0.1 |9/11/2014 4:18:58 ODP. |
| 0.2 |9/11/2014 4:24:33 ODP. |
| 0.4 |9/11/2014 4:28:36 odp. |
| 0,5 |9/11/2014 4:33:31 ODP. |

Pokud kliknete na **Zobrazit historii spuštění**, zobrazí se seznam všech těchto spuštění:

![Ukázka historie spuštění](./media/manage-experiment-iterations/viewrunhistory.jpg)

Kliknutím na libovolné z těchto spuštění zobrazíte snímek experimentu v době, kdy jste ho spustili. Všechny konfigurace, hodnoty parametrů, komentáře a výsledky jsou zachovány, aby vám poskytovaly úplný záznam o tom, co experiment spustíte.

> [!TIP]
> Chcete-li zdokumentovat iterace experimentu, můžete změnit název pokaždé, když ho spustíte, můžete aktualizovat **Souhrn** experimentu v podokně vlastnosti a můžete přidat nebo aktualizovat komentáře v jednotlivých modulech k zaznamenání změn. Komentáře název, souhrn a modul se ukládají při každém spuštění experimentu.
> 
> 

Seznam experimentů na kartě **experimenty** v Machine Learning Studio (Classic) vždy zobrazuje nejnovější verzi experimentu. Pokud jste otevřeli předchozí spuštění experimentu (pomocí **předchozího spuštění** nebo **zobrazení historie spuštění**), můžete se vrátit k verzi konceptu kliknutím na **Zobrazit historii spuštění** a výběrem iterace, která má **stav** **Upravit**.

## <a name="run-a-previous-experiment"></a>Spustit předchozí experiment
Když kliknete na **předchozí spuštění** nebo **si zobrazíte historii spuštění** a otevřete předchozí běh, můžete zobrazit dokončený experiment v režimu jen pro čtení.

Pokud chcete zahájit iteraci experimentu počínaje způsobem, který jste nakonfigurovali pro předchozí spuštění, můžete to udělat tak, že otevřete běh a kliknete na **Uložit jako**. Tím se vytvoří nový experiment s novým názvem, prázdnou historií spuštění a všemi komponentami a hodnotami parametrů předchozího běhu. Tento nový experiment je uvedený na kartě **experimenty** na domovské stránce Machine Learning Studio (Classic) a můžete ho upravit a spustit a zahájit novou historii spuštění pro tuto iteraci experimentu. 

Předpokládejme například, že máte historii spuštění experimentu uvedenou v předchozí části. Chcete sledovat, co se stane, když nastavíte parametr **studijní frekvence** na 0,4, a vyzkoušíte jiné hodnoty pro **počet epochs parametrů školení** .

1. Klikněte na **Zobrazit historii spuštění** a otevřete iteraci experimentu, který jste spustili v 4:28:36. odp. (ve kterém nastavíte hodnotu parametru na 0,4).
2. Klikněte na **Uložit jako**.
3. Zadejte nový název a zaškrtněte políčko **OK** . Vytvoří se nová kopie experimentu.
4. Upravte **počet epochs parametrů školení** .
5. Klikněte na **Spustit**.

Teď můžete dál upravovat a spouštět tuto verzi experimentu a sestavovat novou historii spuštění pro záznam vaší práce.

<!-- Module References -->
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression