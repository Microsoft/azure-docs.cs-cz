---
title: 'Postupy: Přidání opravy s jedním krokem k vlastnímu příkazu (Preview) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku vyvysvětlíme, jak implementovat opravy v jednom kroku pro příkaz ve vlastních příkazech.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858264"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Postupy: Přidání opravy s jedním krokem k vlastnímu příkazu (Preview)

V tomto článku se dozvíte, jak do příkazu přidat potvrzení jedním krokem.

K aktualizaci příkazu, který se právě dokončil, se používá krokování s jedním krokem. To znamená, že pokud jste právě nastavili alarm, můžete si změnit svůj názor a aktualizovat čas alarmu. Příkladem takového případu je následující:

- Vstup: nastavit alarm pro zítřejší čas v poledne
- Výstup: OK, sada alarmů pro 2020-05-02 12:00:00
- Vstup: Ne, zítra ve 13:00
- Výstup: OK

Scénář reálného světa, který je obecně doprovázen klientem, který provádí akci v důsledku dokončení příkazu – v tomto článku se předpokládá, že jako vývojář máte mechanizmus aktualizace alarmu ve vaší back-endové aplikaci.

## <a name="prerequisites"></a>Požadavky

Je nutné provést kroky v následujících článcích:
> [!div class="checklist"]

> * [Rychlý Start: Vytvoření vlastního příkazu (Preview)](./quickstart-custom-speech-commands-create-new.md)
> * [Rychlý Start: Vytvoření vlastního příkazu s parametry (Preview)](./quickstart-custom-speech-commands-create-parameters.md)
> * [Postupy: Přidání potvrzení k vlastnímu příkazu (Preview)](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>Přidat pravidla interakce pro opravy na jednom kroku 

Abychom předvedli krokování s jedním krokem, rozšíříme příkaz **SetAlarm** vytvořený v tématu [Postupy: Přidání potvrzení do vlastního příkazu (Preview)](./how-to-custom-speech-commands-confirmations.md).
1. Přidejte pravidlo interakce a aktualizujte dříve nastavené alarmy. 

    Toto pravidlo vyzve uživatele k potvrzení data a času alarmu a očekává se potvrzení (ano/ne) pro další tah.

   | Nastavení               | Navrhovaná hodnota                                                  | Popis                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Aktualizovat předchozí alarm                                            | Název popisující účel pravidla          |
   | Podmínky            | Předchozí příkaz se musí aktualizovat & požadovaný parametr-> data a času.                | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | Akce               | Odeslat odezvu na řeč – > jednoduchý editor – > aktualizace předchozího alarmu na {DateTime}      | Akce, která se má provést, když jsou podmínky pravidla pravdivé |
   | Stav po spuštění | Příkaz dokončen        | Stav uživatele po zapnutí                   |

1. Přesuňte pravidlo, které jste právě vytvořili, do horní části pravidel interakce (vyberte pravidlo na panelu a klikněte na šipku nahoru v části `...` ikona v horní části podokna uprostřed).
   > [!div class="mx-imgBorder"]
   > ![Přidat ověření rozsahu](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > V reálné aplikaci můžete v části akce tohoto pravidla také odeslat zpět aktivitu klientovi nebo zavolat koncový bod HTTP a aktualizovat tak alarm v systému.

## <a name="try-it-out"></a>Vyzkoušejte si to.

Vyberte `Train`, počkat na školení dokončeno a vyberte `Test`.

- Vstup: nastavit alarm pro zítřejší čas v poledne
- Výstup: jste si jisti, že chcete nastavit alarm pro 2020-05-02 12:00:00
- Vstup: Ano
- Výstup: OK, sada alarmů pro 2020-05-02 12:00:00
- Vstup: Ne, zítra v 2pm
- Výstup: aktualizace předchozího alarmu na 2020-05-02 14:00:00
