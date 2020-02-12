---
title: Správa iterací experimentu
titleSuffix: ML Studio (classic) - Azure
description: Jak spravovat iterace experimentů v Azure Machine Learning Studio (Classic). Předchozí běhy své experimenty můžete zkontrolovat v okamžiku, aby bylo možné výzva, opakování a nakonec potvrďte nebo upravit předchozí předpoklady.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 71a20f0de9907b04b9dcfd63c0003f2540a2e370
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153379"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Správa iterací experimentů v Azure Machine Learning Studio (Classic)
Vývojem modelu prediktivní analýzy je iterativní proces - úpravou různých funkcí a parametrů experimentu sloučit výsledky až se ujistíte, že máte natrénován efektivní model. Klíč k tomuto procesu se ke sledování různých iterací experimentu parametry a konfigurací.



Předchozí běhy své experimenty můžete zkontrolovat v okamžiku, aby bylo možné výzva, opakování a nakonec potvrďte nebo upravit předchozí předpoklady. Když spustíte experiment, Machine Learning Studio (Classic) udržuje historii spuštění, včetně datových sad, modulů a připojení portů a parametrů. Tato historie také shromažďuje výsledky, informace o modulu runtime, jako je spuštění a zastavení čas, zprávy protokolu a stav spuštění. Se můžete podívat zpět v některé z těchto spuštění kdykoli zkontrolovat chronologii experiment a průběžné výsledky. Předchozích spuštění experimentu můžete použít i ke spuštění na vaší cestě k vytvoření jednoduché, komplexní nebo dokonce komplet modelování řešení do nového fáze dotaz a zjišťování.

> [!NOTE]
> Při zobrazení předchozích spuštění experimentu, že tato verze experimentu je uzamčen a nelze jej upravit. Kopii můžete ale uložit kliknutím na **Uložit jako** a zadáním nového názvu pro kopii. Machine Learning Studio (Classic) otevře novou kopii, kterou pak můžete upravit a spustit. Tato kopie experimentu je k dispozici v seznamu **experimenty** společně se všemi vašimi dalšími experimenty.
> 
> 

## <a name="viewing-the-prior-run"></a>Zobrazit předchozí spuštění
Po otevření experimentu, který jste spustili aspoň jednou, si můžete prohlédnout předchozí spuštění experimentu kliknutím na **předchozí spuštění** v podokně Vlastnosti.

Předpokládejme například, vytvoření experimentu a verzemi ho v 11:23, 11:42 a 11:55. Pokud otevřete poslední spuštění experimentu (11:55) a kliknete na **předchozí spuštění**, otevře se verze, kterou jste spustili v 11:42.

## <a name="viewing-the-run-history"></a>Zobrazení historie spuštění
Kliknutím na **Zobrazit historii spuštění** v otevřeném experimentu můžete zobrazit všechna předchozí spuštění experimentu.

Předpokládejme například, že vytvoříte experiment s modulem [lineární regrese][linear-regression] a chcete sledovat účinek změny hodnoty **studijních** kurzů na výsledky experimentů. Spuštěním experimentu několikrát s různými hodnotami pro tento parametr, následujícím způsobem:

| Hodnota rychlost učení | Počáteční čas spuštění |
| --- | --- |
| 0.1 |9/11/2014 16:18:58: 00 |
| 0.2 |9/11/2014 16:24:33: 00 |
| 0,4 |9/11/2014 16:28:36: 00 |
| 0,5 |9/11/2014 16:33:31: 00 |

Pokud kliknete na **Zobrazit historii spuštění**, zobrazí se seznam všech těchto spuštění:

![Příklad historie spuštění](./media/manage-experiment-iterations/viewrunhistory.jpg)

Kliknutím na některý z těchto spuštění zobrazíte snímek experiment v době, kdy jste spustili. Konfigurace, hodnoty parametrů, komentáře a výsledky jsou všechny zachovány získáte úplný záznam, ve kterých běží experimentu.

> [!TIP]
> Chcete-li zdokumentovat iterace experimentu, můžete změnit název pokaždé, když ho spustíte, můžete aktualizovat **Souhrn** experimentu v podokně vlastnosti a můžete přidat nebo aktualizovat komentáře v jednotlivých modulech k zaznamenání změn. Komentáře title, summary a modul se uloží s každé spuštění testu.
> 
> 

Seznam experimentů na kartě **experimenty** v Machine Learning Studio (Classic) vždy zobrazuje nejnovější verzi experimentu. Pokud jste otevřeli předchozí spuštění experimentu (pomocí **předchozího spuštění** nebo **zobrazení historie spuštění**), můžete se vrátit k verzi konceptu kliknutím na **Zobrazit historii spuštění** a výběrem iterace, která má **stav** **Upravit**.

## <a name="iterating-on-a-previous-run"></a>Na předchozím spuštění iterace
Když kliknete na **předchozí spuštění** nebo **si zobrazíte historii spuštění** a otevřete předchozí běh, můžete zobrazit dokončený experiment v režimu jen pro čtení.

Pokud chcete zahájit iteraci experimentu počínaje způsobem, který jste nakonfigurovali pro předchozí spuštění, můžete to udělat tak, že otevřete běh a kliknete na **Uložit jako**. Tím se vytvoří nový experiment s název nového, prázdného historie, spuštění a všechny komponenty a hodnoty parametrů z předchozích spuštění. Tento nový experiment je uvedený na kartě **experimenty** na domovské stránce Machine Learning Studio (Classic) a můžete ho upravit a spustit a zahájit novou historii spuštění pro tuto iteraci experimentu. 

Předpokládejme například, že máte experimentu je znázorněno v předchozí části historie spuštění. Chcete sledovat, co se stane, když nastavíte parametr **studijní frekvence** na 0,4, a vyzkoušíte jiné hodnoty pro **počet epochs parametrů školení** .

1. Klikněte na **Zobrazit historii spuštění** a otevřete iteraci experimentu, který jste spustili v 4:28:36. odp. (ve kterém nastavíte hodnotu parametru na 0,4).
2. Klikněte na **Uložit jako**.
3. Zadejte nový název a zaškrtněte políčko **OK** . Se vytvoří novou kopii experimentu.
4. Upravte **počet epochs parametrů školení** .
5. Klikněte na **Spustit**.

Můžete teď můžete pokračovat upravit a spustit tuto verzi experimentu, vytváření nových historii spuštění k zaznamenání práce.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
