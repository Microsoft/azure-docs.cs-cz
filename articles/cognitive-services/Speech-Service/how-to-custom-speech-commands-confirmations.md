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
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75456495"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Postupy: Přidání potvrzení k vlastnímu příkazu (Preview)

V tomto článku se dozvíte, jak přidat potvrzení do příkazu.

## <a name="prerequisites"></a>Požadavky

Je nutné provést kroky v následujících článcích:

- [Rychlý Start: Vytvoření vlastního příkazu (Preview)](./quickstart-custom-speech-commands-create-new.md)
- [Rychlý Start: Vytvoření vlastního příkazu s parametry (Preview)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Vytvoření příkazu SetAlarm

K předvedení ověření můžete vytvořit nový příkaz, který uživateli umožní nastavit alarm.

1. Otevřete dříve vytvořenou aplikaci Custom Commands v [studiu řeči](https://speech.microsoft.com/)
1. Vytvoření nového příkazu **SetAlarm**
1. Přidat parametr s názvem DateTime

   | Nastavení           | Navrhovaná hodnota                                          | Popis                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Název              | DateTime                                                 | Popisný název pro parametr příkazu                                                    |
   | Požaduje se          | true                                                     | Zaškrtávací políčko, které určuje, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr |
   | Šablona odpovědi | "– Jaký čas?"                                           | Výzva k zadání hodnoty tohoto parametru, pokud není známa                              |
   | Typ              | DateTime                                                 | Typ parametru, například číslo, řetězec nebo datum a čas                                      |
   | Výchozí hodnoty data     | Pokud datum chybí, použijte dnešní den.                             |                                                                                                  |
   | Výchozí hodnoty času     | V případě chybějícího času použít začátek dne                      |                                                                                                  | 

1. Přidat ukázkové věty
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Přidat pravidlo pro dokončení pro potvrzení výsledku

   | Nastavení    | Navrhovaná hodnota                                         | Popis                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla  | Nastavit alarm                                               | Název popisující účel pravidla          |
   | Akce    | SpeechResponse-"-OK, sada alarmů pro {DateTime}"       | Akce, která se má provést, když je podmínka pravidla pravdivá |

## <a name="try-it-out"></a>Vyzkoušejte si to.

Vyberte panel test a vyzkoušejte několik interakcí.

- Vstup: nastavit alarm pro zítřejší čas v poledne
- Výstup: "OK, sada alarmů pro 12/06/2019 12:00:00"

- Vstup: Nastavení alarmu
- Výstup: "jaký čas?"
- Vstup: 17:00
- Výstup: "OK, sada alarmů pro 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>Přidat Rozšířená pravidla pro potvrzení

1. Přidejte rozšířené pravidlo pro potvrzení. 

    Toto pravidlo vyzve uživatele k potvrzení data a času alarmu a očekává se potvrzení (ano/ne) pro další tah.

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Potvrdit datum a čas                                                                | Název popisující účel pravidla          |
   | Podmínky            | Povinný parametr-DateTime                                                    | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | Akce               | SpeechResponse-"– Opravdu chcete nastavit alarm pro {DateTime}?"       | Akce, která se má provést, když je podmínka pravidla pravdivá |
   | Stav po provedení | Počkat na vstup                                                                   | Stav pro uživatele po zapnutí                  |
   | Očekávání          | Potvrzení                                                                     | Očekává se další zapínání.                      |

1. Přidejte rozšířené pravidlo pro zpracování úspěšného potvrzení (uživatel uvedli Ano).

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Přijato potvrzení                                                            | Název popisující účel pravidla          |
   | Podmínky            | SuccessfulConfirmation & požadován parametr-DateTime                           | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | Stav po provedení | Připraveno k dokončení                                                             | Stav uživatele po zapnutí                   |

1. Přidejte rozšířené pravidlo pro zpracování potvrzení zamítnuté (uživatel se mu nic nejedná).

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Zamítnuté potvrzení                                                                   | Název popisující účel pravidla          |
   | Podmínky            | DeniedConfirmation & požadován parametr-DateTime                               | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | Akce               | ClearParameter-DateTime & SpeechResponse-"– žádný problém, čas?"     | Akce, která se má provést, když je podmínka pravidla pravdivá |
   | Stav po provedení | Počkat na vstup                                                                   | Stav uživatele po zapnutí                   |
   | Očekávání          | ElicitParameters – DateTime                                                      | Očekává se další zapínání.                      |

## <a name="try-it-out"></a>Vyzkoušejte si to.

Vyberte panel test a vyzkoušejte několik interakcí.

- Vstup: nastavit alarm pro zítřejší čas v poledne
- Výstup: "Opravdu chcete nastavit alarm pro 12/07/2019 12:00:00?"
- Vstup: ne
- Výstup: "žádný problém, čas?"
- Vstup: 17:00
- Výstup: "Opravdu chcete nastavit alarm pro 12/06/2019 17:00:00?"
- Vstup: Ano
- Výstup: "OK, sada alarmů pro 12/06/2019 17:00:00"

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: Přidání opravy s jedním krokem k vlastnímu příkazu (Preview)](./how-to-custom-speech-commands-one-step-correction.md)