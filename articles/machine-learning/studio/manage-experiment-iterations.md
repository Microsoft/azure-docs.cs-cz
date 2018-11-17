---
title: Správa iterací experimentů v nástroji Machine Learning Studio | Dokumentace Microsoftu
description: Správa iterací experimentů v nástroji Azure Machine Learning Studio
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 3fd9a87ace7b78b394dc0c44a030efc5b4dffb09
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51820065"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio"></a>Správa iterací experimentů v nástroji Azure Machine Learning Studio
Vývojem modelu prediktivní analýzy je iterativní proces - úpravou různých funkcí a parametrů experimentu sloučit výsledky až se ujistíte, že máte natrénován efektivní model. Klíč k tomuto procesu se ke sledování různých iterací experimentu parametry a konfigurací.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Předchozí běhy své experimenty můžete zkontrolovat v okamžiku, aby bylo možné výzva, opakování a nakonec potvrďte nebo upravit předchozí předpoklady. Při spuštění experimentu Machine Learning Studio uchovává historii spuštění, včetně datovou sadu, modulu a připojení k portu a parametry. Tato historie také shromažďuje výsledky, informace o modulu runtime, jako je spuštění a zastavení čas, zprávy protokolu a stav spuštění. Se můžete podívat zpět v některé z těchto spuštění kdykoli zkontrolovat chronologii experiment a průběžné výsledky. Předchozích spuštění experimentu můžete použít i ke spuštění na vaší cestě k vytvoření jednoduché, komplexní nebo dokonce komplet modelování řešení do nového fáze dotaz a zjišťování.

> [!NOTE]
> Při zobrazení předchozích spuštění experimentu, že tato verze experimentu je uzamčen a nelze jej upravit. Můžete však uložit kopii ji kliknutím **uložit jako** a poskytnutí nového názvu pro kopírování. Machine Learning Studio se otevře nová kopie, které potom můžete upravit a spustit. Je k dispozici v této kopii experimentu **EXPERIMENTY** seznamu spolu se všechny vaše další pokusy.
> 
> 

## <a name="viewing-the-prior-run"></a>Zobrazit předchozí spuštění
Až budete mít otevřenou experiment, který jste aspoň jednou spustili, můžete zobrazit předchozí spuštění experimentu kliknutím **předchozího spuštění** v podokně vlastností.

Předpokládejme například, vytvoření experimentu a verzemi ho v 11:23, 11:42 a 11:55. Když otevřete poslední spuštění experimentu (11:55) a klikněte na tlačítko **předchozího spuštění**, se otevře verze jste spustili v 11:42.

## <a name="viewing-the-run-history"></a>Zobrazení historie spuštění
Kliknutím můžete zobrazit všechna předchozí provedená experimentu **zobrazit historii spuštění** v otevřete experiment.

Předpokládejme například, vytvoření experimentu s [lineární regrese] [ linear-regression] modulu a chcete je sledovat účinek změnit hodnotu řetězce **rychlost učení** na vaše výsledky testu. Spuštěním experimentu několikrát s různými hodnotami pro tento parametr, následujícím způsobem:

| Hodnota rychlost učení | Počáteční čas spuštění |
| --- | --- |
| 0.1 |9/11/2014 16:18:58: 00 |
| 0.2 |9/11/2014 16:24:33: 00 |
| 0.4 |9/11/2014 16:28:36: 00 |
| 0.5 |9/11/2014 16:33:31: 00 |

Vyberete-li **zobrazit HISTORII spuštění**, zobrazí se seznam všech těchto spuštění:

![Příklad historie spuštění][runhistory]

Kliknutím na některý z těchto spuštění zobrazíte snímek experiment v době, kdy jste spustili. Konfigurace, hodnoty parametrů, komentáře a výsledky jsou všechny zachovány získáte úplný záznam, ve kterých běží experimentu.

> [!TIP]
> Dokumentování iterace experimentu, můžete upravit název pokaždé, když jej spustíte, můžete aktualizovat **Souhrn** experimentu ve vlastnostech podokno kde můžete přidat nebo aktualizovat komentáře u jednotlivých modulů k zaznamenání vaší změny. Komentáře title, summary a modul se uloží s každé spuštění testu.
> 
> 

Seznam experimenty v **EXPERIMENTY** kartu v nástroji Machine Learning Studio vždy zobrazí nejnovější verzi experiment. Pokud otevřete předchozího spuštění experimentu (pomocí **předchozího spuštění** nebo **zobrazit HISTORII spuštění**), mohli vrátit k verzi konceptu kliknutím **zobrazit HISTORII spuštění** a výběrem možnosti iterace, který má **stavu** z **upravit**.

## <a name="iterating-on-a-previous-run"></a>Na předchozím spuštění iterace
Po kliknutí na **předchozího spuštění** nebo **zobrazit HISTORII spuštění** a otevřete předchozím spuštění, dokončení experimentu si můžete prohlédnout v režimu jen pro čtení.

Pokud chcete začít iterace experimentu od toho, jak jste nakonfigurovali pro předchozím spuštění, můžete to provést otevřením spuštění a kliknutím na **uložit jako**. Tím se vytvoří nový experiment s název nového, prázdného historie, spuštění a všechny komponenty a hodnoty parametrů z předchozích spuštění. Tento nový experiment je uveden v **EXPERIMENTY** kartu v Machine Learning Studio domovskou stránku kde můžete upravit a jeho spuštění zahajuje nový spusťte historie pro tuto iteraci experimentu. 

Předpokládejme například, že máte experimentu je znázorněno v předchozí části historie spuštění. Chcete sledovat, co se stane, když jste nastavili **rychlost učení** 0.4, a zkuste jiné hodnoty pro parametr **počet školení epoch** parametru.

1. Klikněte na tlačítko **zobrazit HISTORII spuštění** a otevřete iterace experimentu, který jste spustili v 16:28:36: 00 (ve kterém nastavíte hodnotu parametru na 0.4).
2. Klikněte na tlačítko **uložit jako**.
3. Zadejte nový název a klikněte na tlačítko **OK** značky zaškrtnutí. Se vytvoří novou kopii experimentu.
4. Upravit **počet školení epoch** parametru.
5. Klikněte na tlačítko **spustit**.

Můžete teď můžete pokračovat upravit a spustit tuto verzi experimentu, vytváření nových historii spuštění k zaznamenání práce.

<!-- Images -->
[runhistory]:./media/manage-experiment-iterations/viewrunhistory.jpg


<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
