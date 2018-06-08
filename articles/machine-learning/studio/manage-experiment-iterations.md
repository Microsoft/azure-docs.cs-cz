---
title: Správa iterací experimentu v nástroji Machine Learning Studio | Microsoft Docs
description: Správa iterací experimentu v nástroji Azure Machine Learning Studio
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6a53530f-20d5-40ae-9b49-7b499ccb44b7
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 4dcae0bb3cb89e65079b88f7be68ddf360ce1b8c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834807"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio"></a>Správa iterací experimentů v nástroji Azure Machine Learning Studio
Vývoj model prediktivní analýzy je iterativní proces - úpravou různých funkcí a parametry experimentu výsledky zpřesňují, dokud nebudete přesvědčeni, že máte natrénován efektivní model. Klíč pro tento proces je sledování různých iterací experimentu parametry a konfigurace.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Můžete zkontrolovat předchozích spuštění experimentů kdykoli chcete-li challenge, pokroku a nakonec potvrďte nebo Upřesnit předchozí předpoklady. Při spuštění experimentu, Machine Learning Studio uchovává historii spustit, včetně datovou sadu, modul a připojení k portu a parametry. Tato historie taky zaznamená výsledky, informace o běhu programu, například spuštění a zastavení časy, zprávy protokolu a stav spuštění. Můžete najít zpět na kterémkoli z těchto spustí kdykoli zkontrolovat časovou posloupnost experiment a mezilehlých výsledků. Předchozího spuštění experimentu můžete použít i ke spuštění na cestu ke vytvoření jednoduché, komplexní nebo i řešení modelování komplet do nové fázi dotaz a zjišťování.

> [!NOTE]
> Při zobrazení předchozího spuštění experimentu, že tato verze experimentu je uzamčen a nelze jej upravit. Můžete však uložení kopie ho kliknutím na **uložit jako** a poskytuje nový název kopie. Machine Learning Studio otevře novou kopii, která pak můžete upravit a spustit. Je k dispozici v této kopie experimentu **EXPERIMENTY** seznamu společně s všechny ostatní experimentů.
> 
> 

## <a name="viewing-the-prior-run"></a>Zobrazení předchozího spuštění
Až budete mít otevřenou experimentu, který jste spustili alespoň jednou, předchozí spuštění experimentu můžete zobrazit kliknutím **předchozí spustit** v podokně vlastností.

Předpokládejme například, můžete vytvořit nový experiment a spustit verze v 11:23 11:42 a 11:55. Pokud otevřete posledním spuštění experimentu (11:55) a klikněte na tlačítko **předchozí spustit**, verze, které jste spustili na 11:42 je otevřen.

## <a name="viewing-the-run-history"></a>Zobrazení historie spouštění
Kliknutím můžete zobrazit všechny předchozí spustí experimentu **zobrazit historii běhů** v experimentu otevřete.

Předpokládejme například, můžete vytvořit nový experiment s [lineární regrese] [ linear-regression] modulu a chcete sledovat účinek změna hodnoty **rychlost učení** na vaše výsledky experimentu. Můžete spustit experiment vícekrát s různými hodnotami pro tento parametr, následujícím způsobem:

| Hodnota míry učení | Počáteční čas spuštění |
| --- | --- |
| 0.1 |11/9/2014 4:18:58 pm |
| 0.2 |11/9/2014 4:24:33 pm |
| 0.4 |11/9/2014 4:28:36 pm |
| 0.5 |11/9/2014 4:33:31 pm |

Pokud kliknete na tlačítko **zobrazit HISTORII BĚHŮ**, zobrazí se seznam všech těchto spuštění:

![Příklad historie spouštění][runhistory]

Kliknutím na jakýkoli z těchto používá pro zobrazení snímek experimentu v době, kdy jste ho spustili. Konfigurace, hodnoty parametrů, komentáře a výsledky jsou všechny zachována tak, abyste získali, ze kterých běží experimentu.

> [!TIP]
> K dokumentu vaší iterací experimentu, můžete upravit název pokaždé, když spustíte ji, můžete aktualizovat **Souhrn** experimentu ve vlastnostech panelu a přidat nebo aktualizovat komentáře na jednotlivé moduly zaznamenat vaše změny. Název, souhrn a modul komentáře se ukládají s každé spuštění experimentu.
> 
> 

Seznam experimenty v **EXPERIMENTY** karta v nástroji Machine Learning Studio vždy zobrazuje nejnovější verzi experimentu. Pokud otevřete předchozího spuštění experimentu (pomocí **předchozí spustit** nebo **zobrazit HISTORII BĚHŮ**), mohli vrátit k verzi konceptu kliknutím **zobrazit HISTORII BĚHŮ** a výběr iterace, který má **stavu** z **upravit**.

## <a name="iterating-on-a-previous-run"></a>Iterace v předchozího spuštění
Když kliknete na tlačítko **předchozí spustit** nebo **zobrazit HISTORII BĚHŮ** a otevřete předchozího spuštění, dokončení experimentu můžete zobrazit v režimu jen pro čtení.

Pokud chcete začít iterace experimentu počínaje způsob, jak jste nakonfigurovali pro předchozího spuštění, můžete k tomu otevřením spustit a kliknutím na **uložit jako**. Tím se vytvoří nový experiment, s nový název, prázdnou historie, spouštění a spusťte všechny součásti a předchozí hodnoty parametru. Tento nový experiment, je uvedena ve **EXPERIMENTY** ve domovské stránce Machine Learning Studio a můžete upravit a spustit, inicializaci novou spusťte historie pro tento iteraci experimentu. 

Předpokládejme například, že máte experiment spustit historie uvedené v předchozí části. Chcete pozorovat, co se stane, když nastavíte **rychlost učení** do 0.4 a zkuste to různé hodnoty pro parametr **počet školení epoch** parametr.

1. Klikněte na tlačítko **zobrazit HISTORII BĚHŮ** a otevřete iterace experimentu, který jste spustili ve 4:28:36 (ve kterém nastavíte hodnotu parametru na 0.4).
2. Klikněte na tlačítko **uložit jako**.
3. Zadejte nový název a klikněte na **OK** zaškrtnutí. Se vytvoří novou kopii tohoto experimentu.
4. Změnit **počet školení epoch** parametr.
5. Klikněte na tlačítko **spustit**.

Můžete teď můžete pokračovat upravte a spusťte tuto verzi experimentu, vytváření nové historie spouštění k zaznamenání práci.

<!-- Images -->
[runhistory]:./media/manage-experiment-iterations/viewrunhistory.jpg


<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
