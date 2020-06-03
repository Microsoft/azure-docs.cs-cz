---
title: Přidání potvrzení ve verzi Preview pro vlastní příkazy App-Speech Service
titleSuffix: Azure Cognitive Services
description: Naučte se přidávat potvrzení do příkazů ve vlastních příkazech ve verzi Preview.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 1cb0624012b22b6cae2c98bfa6ddc9495e09615d
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310464"
---
# <a name="add-confirmations-to-a-command-in-a-custom-commands-preview-application"></a>Přidání potvrzení do příkazu v aplikaci ve verzi Preview vlastního příkazu

V tomto článku se dozvíte, jak vytvořit potvrzení pro příkazy v aplikaci ve verzi Preview pro vlastní příkazy.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v následujících článcích:
> [!div class="checklist"]
> * [Rychlý Start: Vytvoření vlastní příkazy Preview aplikace](./quickstart-custom-speech-commands-create-new.md)
> * [Rychlý Start: Vytvoření vlastní příkazy aplikace ve verzi Preview s parametry](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Vytvoření příkazu SetAlarm

Chcete-li předvést potvrzení, vytvořte nový příkaz, který nastaví alarm.

1. V [studiu řeči](https://speech.microsoft.com/)otevřete aplikaci Custom Commands Preview, kterou jste vytvořili.
1. Vytvořte nový příkaz **SetAlarm** .
1. Přidejte parametr **DateTime** , který má následující konfiguraci:

   | Nastavení                           | Navrhovaná hodnota                     |  Popis                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | **Název**                              | **Datum a čas**                                | Popisný název parametru                                |
   | **Požadováno**                          | Zaškrtnuté                                 | Zaškrtávací políčko označující, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr |
   | **Odpověď na požadovaný parametr**   | **Jednoduchý editor – > jaký čas?**                              | Výzva k zadání hodnoty tohoto parametru, pokud není známa |
   | **Typ**                              | **Datum a čas**                                | Typ parametru, jako je číslo, řetězec, datum a čas nebo zeměpis   |
   | **Výchozí hodnoty data**                     | Pokud datum chybí, použijte dnešní datum.            | Výchozí hodnota proměnné, která se má použít, pokud není poskytnuta uživatelem  |  
   | **Výchozí hodnoty času**                     | Pokud chybí čas, použijte začátek dne.     |  Výchozí hodnota proměnné, která se má použít, pokud není poskytnuta uživatelem|

1. Přidejte příklady vět.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Přidejte pravidlo dokončení pro potvrzení výsledku. Použijte následující konfiguraci:

   | Nastavení    | Navrhovaná hodnota                               |Popis                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | **Název pravidla**  | **Nastavit alarm**                                               |    Název popisující účel pravidla |
   | **Akce**    | **Odeslat odezvu na řeč – > OK, sada alarmů pro {DateTime}**    |Akce, která se má provést, když je podmínka pravidla pravdivá

## <a name="try-it-out"></a>Vyzkoušejte si to.

1. V horní části pravého podokna vyberte **vlak** .

1. Po dokončení školení vyberte **test**a potom zkuste tyto interakce:
    - Vstup: nastavit alarm pro zítřejší čas v poledne
    - Výstup: OK, sada alarmů pro 2020-05-02 12:00:00
    - Vstup: Nastavení alarmu
    - Výstup: Jaký čas?
    - Vstup: 17:00
    - Výstup: OK, sada alarmů pro 2020-05-01 17:00:00

## <a name="add-interaction-rules-for-the-confirmation"></a>Přidat pravidla interakce pro potvrzení

Vytvořit potvrzení přidáním pravidel interakce.

1. V příkazu **SetAlarm** v prostředním podokně vyberte **Přidat** a pak vyberte **pravidla interakce**  >  **Potvrdit příkaz**.

    Toto pravidlo vyzve uživatele k potvrzení data a času alarmu. Použijte tato nastavení:

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Název pravidla**             | **Potvrdit datum a čas**                                                                | Název popisující účel pravidla          |
   | **Podmínky**            | **Povinný parametr-> DateTime**                                                    | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | **Akce**               | **Poslat odezvu na řeč – > určitě chcete nastavit alarm pro {DateTime}?**     | Akce, která se má provést, když je podmínka pravidla pravdivá |
   | **Očekávání**          | **Očekává se potvrzení od uživatele.**                                                 | Očekává se další zapínání.                      |
   | **Stav po spuštění**  | **Počkat na vstup uživatele**                                                            | Stav pro uživatele po zapnutí                  |
  
      > [!div class="mx-imgBorder"]
      > ![Vytvořit požadovanou odpověď parametru](media/custom-speech-commands/add-validation-set-temperature.png)

1. Přidejte pravidlo interakce pro přijaté potvrzení (uživatel uvedli "Ano"). Použijte následující konfiguraci:

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Název pravidla**             | **Přijato potvrzení**                                                            | Název popisující účel pravidla          |
   | **Podmínky**            | **Potvrzení bylo úspěšné & vyžadován parametr-> data a času.**                      | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | **Stav po spuštění** | **Spustit pravidla dokončení**                                                          | Stav uživatele po zapnutí                   |

1. Přidejte pravidlo interakce pro zamítnuté potvrzení (uživatel uvedli "ne"). Použijte následující konfiguraci:

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Název pravidla**             | **Zamítnuté potvrzení**                                                                   | Název popisující účel pravidla          |
   | **Podmínky**            | **Potvrzení bylo zamítnuto & požadován parametr-> data a času.**                               | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | **Akce**               | **Vymazat hodnotu parametru – > datum a čas & odeslat reakci na řeč – > žádný problém, čas?**  | Akce, která se má provést, když je podmínka pravidla pravdivá |
   | **Stav po provedení** | **Počkat na vstup**                                                                   | Stav uživatele po zapnutí                   |
   | **Očekávání**          | **Očekává se vstup parametrů z uživatelského > DateTime.**                           | Očekává se další zapínání.                      |

## <a name="try-out-the-changes"></a>Vyzkoušejte změny

1. Vyberte **vlak**.

1. Po dokončení školení vyberte **test**a potom zkuste tyto interakce:

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
> [Přidání opravy s jedním krokem do příkazu v aplikaci ve verzi Preview pro vlastní příkazy](./how-to-custom-speech-commands-one-step-correction.md)
