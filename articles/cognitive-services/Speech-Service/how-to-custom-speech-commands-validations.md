---
title: 'Postupy: Přidání ověření do vlastních parametrů příkazu'
titleSuffix: Azure Cognitive Services
description: V tomto článku vysvětlujeme, jak do parametru ve vlastních příkazech přidat ověřování.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857186"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Postupy: Přidání ověření do vlastních parametrů příkazu (Preview)

V tomto článku přidáte ověření do parametrů a zobrazí výzvu k opravě.

## <a name="prerequisites"></a>Požadavky

Je nutné provést kroky v následujících článcích:

> [!div class="checklist"]
> * [Rychlý Start: Vytvoření vlastního příkazu](./quickstart-custom-speech-commands-create-new.md)
> * [Rychlý Start: Vytvoření vlastního příkazu s parametry](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Vytvoření příkazu SetTemperature

K předvedení ověření můžete vytvořit nový příkaz, který uživatelům umožní nastavit teplotu.

1. Otevřete dříve vytvořenou aplikaci Custom Commands v [studiu řeči](https://speech.microsoft.com/)
1. Vytvoření nového příkazu`SetTemperature`
1. Přidejte parametr pro cílovou teplotu.

   | Konfigurace parametru           | Navrhovaná hodnota    |Popis                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | Name              | Teplota                       | Popisný název pro parametr                                |
   | Požaduje se          | checked                           | Zaškrtávací políčko, které určuje, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr |
   | Odpověď na povinný parametr     | Jednoduchý editor – > jakou teplotu chcete?  | Výzva k zadání hodnoty tohoto parametru, pokud není známa |
   | Typ              | Číslo                            | Typ parametru, jako je číslo, řetězec, datum a čas nebo zeměpis   |

1. Přidejte ověření pro parametr teploty.

    - Na stránce konfigurace **parametrů** pro `Temperature` parametr vyberte `Add a validation` z oddílu validy.
    - V automaticky otevíraném okně pro **ověření** vyplňte následující hodnoty a vyberte **vytvořit**.

  
       | Konfigurace parametru         | Navrhovaná hodnota                                          | Popis                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | Min. hodnota        | 60               | Pro parametry Number minimální hodnota, kterou tento parametr může předpokládat |
       | Max. hodnota        | 80               | Pro číselné parametry maximální hodnoty, které tento parametr může předpokládat |
       | Odezva na selhání – jednoduchý editor| První variace – je nám líto, ale dá se nastavit jenom mezi 60 a 80 stupnemi.      | Výzva k zadání nové hodnoty, pokud se ověření nepovede                                       |

       > [!div class="mx-imgBorder"]
       > ![Přidat ověření rozsahu](media/custom-speech-commands/validations-add-temperature.png)

1. Přidat příklady vět

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Přidat pravidlo pro dokončení pro potvrzení výsledku

   | Nastavení    | Navrhovaná hodnota                                           |Popis                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Name       | Potvrzovací zpráva                                      |Název popisující účel pravidla |
   | Podmínky | Požadované parametry-`Temperature`                       |Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | Akce    | Poslat odezvu na řeč –`Ok, setting temperature to {Temperature} degrees` | Akce, která se má provést, když je podmínka pravidla pravdivá |

> [!TIP]
> V tomto příkladu se k potvrzení výsledku používá odezva na řeč. Příklady dokončení příkazu s akcí klienta najdete v tématu: [Postupy: plnění příkazů v klientovi pomocí sady Speech SDK](./how-to-custom-speech-commands-fulfill-sdk.md)


## <a name="try-it-out"></a>Vyzkoušejte si to.
1. Vyberte `Train` ikonu, která se nachází v pravém podokně.

1. Po dokončení školení vyberte `Test` a vyzkoušejte pár interakcí.

    - Vstup: Nastavte teplotu na 72 stupňů.
    - Výstup: OK, nastavení teploty na 72 stupňů
    - Vstup: Nastavte teplotu na 45 stupňů.
    - Výstup: je nám líto, ale dá se nastavit jenom mezi 60 a 80 stupně.
    - Vstup: nastavte místo toho 72 stupňů.
    - Výstup: OK, nastavení teploty na 72 stupňů

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: Přidání potvrzení k vlastnímu příkazu (Preview)](./how-to-custom-speech-commands-confirmations.md)
