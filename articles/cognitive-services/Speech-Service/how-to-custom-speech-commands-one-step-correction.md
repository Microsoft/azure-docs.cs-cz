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
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456451"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Postupy: Přidání opravy s jedním krokem k vlastnímu příkazu (Preview)

V tomto článku se dozvíte, jak do příkazu přidat potvrzení jedním krokem.

K aktualizaci příkazu, který se právě dokončil, se používá krokování s jedním krokem.

To znamená, že pokud jste právě nastavili alarm, můžete si změnit svůj názor a aktualizovat čas alarmu.

- Vstup: nastavit alarm pro zítřejší čas v poledne
- Výstup: "OK, sada alarmů pro 12/06/2019 12:00:00"
- Vstup: Ne, zítra ve 13:00
- Výstup: "OK

Mějte na paměti, že to znamená, že jako vývojář máte mechanizmus aktualizace alarmu v back-endové aplikaci.

## <a name="prerequisites"></a>Požadavky

Je nutné provést kroky v následujících článcích:

- [Rychlý Start: Vytvoření vlastního příkazu (Preview)](./quickstart-custom-speech-commands-create-new.md)
- [Rychlý Start: Vytvoření vlastního příkazu s parametry (Preview)](./quickstart-custom-speech-commands-create-parameters.md)
- [Postupy: Přidání potvrzení k vlastnímu příkazu (Preview)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Přidat Rozšířená pravidla pro opravy s jedním krokem 

Abychom předvedli krokování s jedním krokem, rozšíříme příkaz **SetAlarm** vytvořený v [potvrzení, jak](./how-to-custom-speech-commands-confirmations.md).
 
1. Přidejte rozšířené pravidlo, které aktualizuje předchozí alarm. 

    Toto pravidlo vyzve uživatele k potvrzení data a času alarmu a očekává se potvrzení (ano/ne) pro další tah.

   | Nastavení               | Navrhovaná hodnota                                                  | Popis                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Aktualizovat předchozí alarm                                            | Název popisující účel pravidla          |
   | Podmínky            | UpdateLastCommand & požadován parametr-DateTime                | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | Akce               | SpeechResponse-"-aktualizuje se předchozí alarm na {DateTime}"       | Akce, která se má provést, když je podmínka pravidla pravdivá |
   | Stav po provedení | Příkaz Complete                                                 | Stav uživatele po zapnutí                   |

1. Přesuňte pravidlo, které jste právě vytvořili, do horní části rozšířených pravidel (přejděte na pravidlo na panelu a klikněte na šipku nahoru).
   > [!div class="mx-imgBorder"]
   > ![přidat ověřování rozsahu](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> V reálné aplikaci můžete v části akce tohoto pravidla také odeslat zpět aktivitu klientovi nebo zavolat koncový bod HTTP a aktualizovat tak alarm v systému.

## <a name="try-it-out"></a>Vyzkoušet

Vyberte panel test a vyzkoušejte několik interakcí.

- Vstup: nastavit alarm pro zítřejší čas v poledne
- Výstup: "Opravdu chcete nastavit alarm pro 12/07/2019 12:00:00?"
- Vstup: Ano
- Výstup: "OK, sada alarmů pro 12/07/2019 12:00:00"
- Vstup: Ne, zítra ve 13:00
- Výstup: "aktualizace předchozího alarmu na 12/07/2019 13:00:00"
