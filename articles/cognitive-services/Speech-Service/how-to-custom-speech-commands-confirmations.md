---
title: 'Postupy: Přidání potvrzení k vlastnímu příkazu (Preview)'
titleSuffix: Azure Cognitive Services
description: V tomto článku, jak implementovat potvrzení pro příkaz ve vlastních příkazech.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: bf1b79c1b5d7b9dfd93b354c6b6ff5a512bb74a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858230"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Postupy: Přidání potvrzení k vlastnímu příkazu (Preview)

V tomto článku se dozvíte, jak přidat potvrzení do příkazu.

## <a name="prerequisites"></a>Požadavky

Je nutné provést kroky v následujících článcích:
> [!div class="checklist"]
> *  [Rychlý Start: Vytvoření vlastního příkazu (Preview)](./quickstart-custom-speech-commands-create-new.md)
> * [Rychlý Start: Vytvoření vlastního příkazu s parametry (Preview)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Vytvoření příkazu SetAlarm

K předvedení potvrzení si vytvoříme nový příkaz, který uživateli umožní nastavit alarm.

1. Otevřete dříve vytvořenou aplikaci Custom Commands v nástroji [Speech Studio](https://speech.microsoft.com/).
1. Vytvoří nový příkaz `SetAlarm`.
1. Přidejte parametr s názvem `DateTime` s následující konfigurací.

   | Nastavení                           | Navrhovaná hodnota                     |  Popis                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | Name                              | DateTime                                | Popisný název pro parametr                                |
   | Požaduje se                          | checked                                 | Zaškrtávací políčko, které určuje, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr |
   | Odpověď na povinný parametr   | Jednoduchý editor – > jaký čas?                              | Výzva k zadání hodnoty tohoto parametru, pokud není známa |
   | Typ                              | DateTime                                | Typ parametru, jako je číslo, řetězec, datum a čas nebo zeměpis   |
   | Výchozí hodnoty data                     | Pokud datum chybí, použijte dnešní den.            | Výchozí hodnota proměnné, která se má použít, pokud není poskytnuta uživatelem  |  
   | Výchozí hodnoty času                     | V případě chybějícího času použít začátek dne     |  Výchozí hodnota proměnné, která se má použít, pokud není poskytnuta uživatelem|

1. Přidejte příklady vět.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Přidejte pravidlo dokončení pro potvrzení výsledku.

   | Nastavení    | Navrhovaná hodnota                               |Popis                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | Název pravidla  | Nastavit alarm                                               |    Název popisující účel pravidla |
   | Akce    | Odeslat odezvu na řeč – OK, sada alarmů pro {DateTime}    |Akce, která se má provést, když je podmínka pravidla pravdivá

## <a name="try-it-out"></a>Vyzkoušejte si to.

1. Vyberte `Train` ikonu, která se nachází v pravém podokně.

1. Po dokončení školení vyberte `Test`.
    - Vstup: nastavit alarm pro zítřejší čas v poledne
    - Výstup: OK, sada alarmů pro 2020-05-02 12:00:00
    - Vstup: Nastavení alarmu
    - Výstup: Jaký čas?
    - Vstup: 17:00
    - Výstup: OK, sada alarmů pro 2020-05-01 17:00:00

## <a name="add-the-advanced-rules-for-confirmation"></a>Přidat Rozšířená pravidla pro potvrzení

Potvrzení se dosahují přidáním pravidel interakce.

1. V existujícím `SetAlarm` příkazu přidejte v prostředním podokně `+Add` ikonu **pravidla interakce** a pak vyberte příkaz **pravidla** -> interakce**Potvrdit**.

    Toto pravidlo vyzve uživatele k potvrzení data a času alarmu a očekává se potvrzení (ano/ne) pro další tah.

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Potvrdit datum a čas                                                                | Název popisující účel pravidla          |
   | Podmínky            | Povinný parametr-> DateTime                                                    | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | Akce               | Poslat odezvu na řeč – > určitě chcete nastavit alarm pro {DateTime}?     | Akce, která se má provést, když je podmínka pravidla pravdivá |
   | Očekávání          | Očekává se potvrzení od uživatele.                                                 | Očekává se další zapínání.                      |
   | Stav po spuštění  | Počkat na vstup uživatele                                                            | Stav pro uživatele po zapnutí                  |
  
      > [!div class="mx-imgBorder"]
      > ![Vytvořit požadovanou odpověď parametru](media/custom-speech-commands/add-validation-set-temperature.png)

1. Přidejte další pravidlo interakce pro zpracování úspěšného potvrzení (uživatel uvedli Ano).

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Přijato potvrzení                                                            | Název popisující účel pravidla          |
   | Podmínky            | Potvrzení bylo úspěšné & vyžadován parametr-> data a času.                      | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | Stav po spuštění | Spustit pravidla dokončení                                                          | Stav uživatele po zapnutí                   |

1. Přidejte rozšířené pravidlo pro zpracování potvrzení zamítnuté (uživatel se mu nic nejedná).

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Zamítnuté potvrzení                                                                   | Název popisující účel pravidla          |
   | Podmínky            | Potvrzení bylo zamítnuto & požadován parametr-> data a času.                               | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | Akce               | Vymazat hodnotu parametru – > datum a čas & odeslat reakci na řeč – > žádný problém, čas?  | Akce, která se má provést, když je podmínka pravidla pravdivá |
   | Stav po provedení | Počkat na vstup                                                                   | Stav uživatele po zapnutí                   |
   | Očekávání          | Očekává se zadání parametrů z hodnoty DateTime uživatele >.                           | Očekává se další zapínání.                      |

## <a name="try-out-the-changes"></a>Vyzkoušejte změny

Vyberte `Train`, počkat na školení dokončeno a vyberte `Test`.

- Vstup: nastavit alarm pro zítřejší čas v poledne
- Výstup: jste si jisti, že chcete nastavit alarm pro 2020-05-02 12:00:00?
- Vstup: ne
- Výstup: žádný problém, čas?
- Vstup: 17:00
- Výstup: "Opravdu chcete nastavit alarm pro 2020-05-01 17:00:00?"
- Vstup: Ano
- Výstup: OK, sada alarmů pro 2020-05-01 17:00:00

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: Přidání opravy s jedním krokem k vlastnímu příkazu (Preview)](./how-to-custom-speech-commands-one-step-correction.md)