---
title: Přidání ověření ve službě Custom Commands Preview – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se, jak přidat ověření do parametru příkazu v aplikaci ve verzi Preview vlastního příkazu.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 6686016f109fad4ee8b7f4e494b1374a6003658c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310406"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>Přidání ověření do parametru příkazu v aplikaci ve verzi Preview vlastního příkazu

V tomto článku se dozvíte, jak přidat ověření do parametrů a zobrazit výzvy k opravě.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v následujících článcích:

> [!div class="checklist"]
 
> * [Rychlý Start: Vytvoření vlastní příkazy Preview aplikace](./quickstart-custom-speech-commands-create-new.md)
> * [Rychlý Start: Vytvoření vlastní příkazy aplikace ve verzi Preview s parametry](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Vytvoření příkazu SetTemperature

Pokud chcete předvést ověření, vytvořte nový příkaz, který uživatelům umožní nastavit teplotu.

1. V [studiu řeči](https://speech.microsoft.com/)otevřete aplikaci Custom Commands Preview, kterou jste vytvořili.
1. Vytvořte nový příkaz **SetTemperature** .
1. Přidejte parametr teploty, který má následující konfiguraci:

   | Konfigurace parametru           | Navrhovaná hodnota    |Popis                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **Název**              | **Teplota**                       | Popisný název pro parametr                                |
   | **Požadováno**          | Zaškrtnuté                           | Zaškrtávací políčko, které určuje, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr |
   | **Odpověď na povinný parametr**     | **Jednoduchý editor – > jakou teplotu chcete?**  | Výzva k zadání hodnoty tohoto parametru, pokud není známa |
   | **Typ**              | **Číselná**                            | Typ parametru, jako je číslo, řetězec, datum a čas nebo zeměpis   |

1. Přidejte ověření pro parametr teploty.

    1. Na stránce konfigurace **parametrů** pro parametr teploty vyberte **Přidat ověření** v části **validy** .

    1. V místním okně **nové ověřování** nakonfigurujte ověřování následujícím způsobem:
  
       | Konfigurace parametru         | Navrhovaná hodnota                                          | Popis                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **Min. hodnota**        | **60**               | Pro parametry Number minimální hodnota, kterou tento parametr může předpokládat |
       | **Max. hodnota**        | **80**               | Pro číselné parametry maximální hodnoty, které tento parametr může předpokládat |
       | **Odezva na selhání – jednoduchý editor**| **První variace – je nám líto, ale dá se nastavit jenom mezi 60 a 80 stupnemi.**      | Výzva k zadání nové hodnoty, pokud se ověření nepovede                                       |

       > [!div class="mx-imgBorder"]
       > ![Přidat ověření rozsahu](media/custom-speech-commands/validations-add-temperature.png)

1. Vyberte **Vytvořit**.

1. Přidejte příklady vět.

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Přidejte pravidlo dokončení, které má následující konfiguraci. Toto pravidlo potvrdí výsledek.

   | Nastavení    | Navrhovaná hodnota                                           |Popis                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Name       | Potvrzovací zpráva                                      |Název popisující účel pravidla |
   | **Podmínky** | **Požadované parametry – teplota**                       |Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | **Akce**    | **Poslat odezvu na řeč – OK, nastavit teplotu na {teploty} stupňů** | Akce, která se má provést, když je podmínka pravidla pravdivá |

> [!TIP]
> V tomto příkladu se k potvrzení výsledku používá odezva na řeč. Příklady dokončení příkazu s akcí klienta naleznete v tématu How to: reactioning Commands [to the Client with the Speech SDK](./how-to-custom-speech-commands-fulfill-sdk.md).

## <a name="try-it-out"></a>Vyzkoušejte si to.

1. Vyberte **vlak**.

1. Po dokončení školení vyberte **test**a potom zkuste tyto interakce:

    - Vstup: Nastavte teplotu na 72 stupňů.
    - Výstup: OK, nastavení teploty na 72 stupňů
    - Vstup: Nastavte teplotu na 45 stupňů.
    - Výstup: je nám líto, ale dá se nastavit jenom mezi 60 a 80 stupně.
    - Vstup: nastavte místo toho 72 stupňů.
    - Výstup: OK, nastavení teploty na 72 stupňů

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání potvrzení do příkazu v aplikaci ve verzi Preview pro vlastní příkazy](./how-to-custom-speech-commands-confirmations.md)
