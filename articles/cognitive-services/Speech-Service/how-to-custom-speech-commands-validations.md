---
title: 'Postup: Přidání ověření do parametrů vlastního příkazu (náhled)'
titleSuffix: Azure Cognitive Services
description: V tomto článku vysvětlujeme, jak přidat ověření k parametru ve vlastních příkazech.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156450"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Postup: Přidání ověření do parametrů vlastního příkazu (náhled)

V tomto článku se dozvíte, jak přidat ověření parametrů a výzvu k opravě.

## <a name="prerequisites"></a>Požadavky

Musíte mít dokončeny kroky v následujících článcích:

- [Úvodní příručka: Vytvoření vlastního příkazu (náhled)](./quickstart-custom-speech-commands-create-new.md)
- [Úvodní příručka: Vytvoření vlastního příkazu s parametry (náhled)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Vytvoření příkazu SetTemperature

Chcete-li demonstrovat ověření, vytvořme nový příkaz, který uživateli umožní nastavit teplotu.

1. Otevření dříve vytvořené aplikace Vlastní příkazy v [aplikaci Speech Studio](https://speech.microsoft.com/)
1. Vytvořit novou sadu **příkazůTeplota**
1. Přidání parametru pro cílovou teplotu
1. Přidání ověření parametru teploty
   > [!div class="mx-imgBorder"]
   > ![Přidání ověření rozsahu](media/custom-speech-commands/validations-add-temperature.png)

   | Nastavení           | Navrhovaná hodnota                                          | Popis                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Name (Název)              | Teplota                                              | Popisný název parametru Command                                                    |
   | Požaduje se          | true                                                     | Zaškrtávací políčko označující, zda je před dokončením příkazu vyžadována hodnota tohoto parametru. |
   | Šablona odpovědi | "- Jakou teplotu byste chtěli?"                     | Výzva k žádosti o hodnotu tohoto parametru, pokud není známa                              |
   | Typ              | Číslo                                                   | Typ parametru, například Číslo, Řetězec nebo Čas data                                      |
   | Ověřování        | Min Hodnota: 60, Maximální hodnota: 80                             | Pro parametry Number je povolený rozsah hodnot pro parametr                             |
   | Šablona odpovědi | "- Omlouvám se, mohu nastavit pouze mezi 60 a 80 stupňů"      | V případě, že se ověření nezdaří, zobrazí se výzva k žádosti o aktualizovanou hodnotu                                       |

1. Přidání některých ukázkových vět

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Přidání pravidla dokončení pro potvrzení výsledku

   | Nastavení    | Navrhovaná hodnota                                           | Popis                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla  | Potvrzovací zpráva                                      | Název popisující účel pravidla          |
   | Podmínky | Požadovaný parametr - teplota                          | Podmínky, které určují, kdy může být pravidlo spuštěno    |
   | Akce    | SpeechResponse - "- Ok, nastavení na {Temperature} stupňů" | Akce, která má být v případě, že je splněna podmínka pravidla |

> [!TIP]
> Tento příklad používá odpověď na řeč k potvrzení výsledku. Příklady dokončení příkazu s akcí klienta viz: [Jak: Plnit příkazy na straně klienta pomocí sady Speech SDK (Preview)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Vyzkoušejte si to.

Vyberte panel Test a vyzkoušejte několik interakcí.

- Vstup: Nastavte teplotu na 72 stupňů
- Výstup: "Ok, nastavení na 72 stupňů"

- Vstup: Nastavte teplotu na 45 stupňů
- Výstup: "Omlouvám se, mohu nastavit pouze mezi 60 a 80 stupňů"
- Vstup: aby to 72 stupňů místo
- Výstup: "Ok, nastavení na 72 stupňů"

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postup: Přidání potvrzení do vlastního příkazu (náhled)](./how-to-custom-speech-commands-confirmations.md)
