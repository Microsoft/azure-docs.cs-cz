---
title: 'Postupy: Přidání ověření do vlastních parametrů příkazu (Preview)'
titleSuffix: Azure Cognitive Services
description: V tomto článku vysvětlujeme, jak do parametru ve vlastních příkazech přidat ověřování.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: c89c388f919ca95a331d1d406f5b1776c127ebad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446921"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Postupy: Přidání ověření do vlastních parametrů příkazu (Preview)

V tomto článku se dozvíte, jak přidat ověření do parametrů a zobrazit výzvu k opravě.

## <a name="prerequisites"></a>Požadavky

Je nutné provést kroky v následujících článcích:

- [Rychlý Start: Vytvoření vlastního příkazu (Preview)](./quickstart-custom-speech-commands-create-new.md)
- [Rychlý Start: Vytvoření vlastního příkazu s parametry (Preview)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Vytvoření příkazu SetTemperature

K předvedení ověření můžete vytvořit nový příkaz, který uživateli umožní nastavit teplotu.

1. Otevřete dříve vytvořenou aplikaci Custom Commands v [studiu řeči](https://speech.microsoft.com/)
1. Vytvoření nového příkazu **SetTemperature**
1. Přidat parametr pro cílovou teplotu
1. Přidat ověření pro parametr teploty
   > [!div class="mx-imgBorder"]
   > ![přidat ověřování rozsahu](media/custom-speech-commands/validations-add-temperature.png)

   | Nastavení           | Navrhovaná hodnota                                          | Popis                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Name (Název)              | Teplota                                              | Popisný název pro parametr příkazu                                                    |
   | Požaduje se          | true                                                     | Zaškrtávací políčko, které určuje, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr |
   | Šablona odpovědi | "– Jakou teplotu chcete?"                     | Výzva k zadání hodnoty tohoto parametru, pokud není známa                              |
   | Typ              | Číslo                                                   | Typ parametru, například číslo, řetězec nebo datum a čas                                      |
   | Ověřování        | Minimální hodnota: 60, maximální hodnota: 80                             | Pro číselné parametry, povolený rozsah hodnot pro parametr                             |
   | Šablona odpovědi | – Je nám líto, ale dá se nastavit jenom mezi 60 a 80 stupně.      | Výzva k zadání aktualizované hodnoty, pokud se ověření nepovede                                       |

1. Přidat ukázkové věty

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Přidat pravidlo pro dokončení pro potvrzení výsledku

   | Nastavení    | Navrhovaná hodnota                                           | Popis                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla  | Potvrzovací zpráva                                      | Název popisující účel pravidla          |
   | Podmínky | Požadovaná parametr-teplota                          | Podmínky, které určují, kdy se pravidlo dá spustit    |
   | Akce    | SpeechResponse-"-OK, nastavení na {teploty} stupňů" | Akce, která se má provést, když je podmínka pravidla pravdivá |

> [!TIP]
> V tomto příkladu se k potvrzení výsledku používá odezva na řeč. Příklady dokončení příkazu s akcí klienta najdete v tématu: [Postupy: plnění příkazů v klientovi pomocí sady Speech SDK (Preview)](./how-to-custom-speech-commands-fulfill-sdk.md) .

## <a name="try-it-out"></a>Vyzkoušet

Vyberte panel test a vyzkoušejte několik interakcí.

- Vstup: Nastavte teplotu na 72 stupňů.
- Výstup: "OK", nastavení na 72 stupňů "

- Vstup: Nastavte teplotu na 45 stupňů.
- Výstup: "je nám líto, ale dá se nastavit jenom mezi 60 a 80 stupni"
- Vstup: nastavte místo toho 72 stupňů.
- Výstup: "OK", nastavení na 72 stupňů "

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: Přidání potvrzení k vlastnímu příkazu (Preview)](./how-to-custom-speech-commands-confirmations.md)