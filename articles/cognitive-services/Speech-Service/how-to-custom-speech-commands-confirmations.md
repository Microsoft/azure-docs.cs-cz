---
title: 'Postup: Přidání potvrzení do vlastního příkazu (náhled)'
titleSuffix: Azure Cognitive Services
description: V tomto článku, jak implementovat potvrzení pro příkaz ve vlastní příkazy.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456495"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Postup: Přidání potvrzení do vlastního příkazu (náhled)

V tomto článku se dozvíte, jak přidat potvrzení příkazu.

## <a name="prerequisites"></a>Požadavky

Musíte mít dokončeny kroky v následujících článcích:

- [Úvodní příručka: Vytvoření vlastního příkazu (náhled)](./quickstart-custom-speech-commands-create-new.md)
- [Úvodní příručka: Vytvoření vlastního příkazu s parametry (náhled)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Vytvoření příkazu SetAlarm

Chcete-li demonstrovat ověření, vytvořme nový příkaz, který uživateli umožní nastavit budík.

1. Otevření dříve vytvořené aplikace Vlastní příkazy v [aplikaci Speech Studio](https://speech.microsoft.com/)
1. Vytvoření nového příkazového **příkazu SetAlarm**
1. Přidání parametru s názvem DateTime

   | Nastavení           | Navrhovaná hodnota                                          | Popis                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Name (Název)              | DateTime                                                 | Popisný název parametru Command                                                    |
   | Požaduje se          | true                                                     | Zaškrtávací políčko označující, zda je před dokončením příkazu vyžadována hodnota tohoto parametru. |
   | Šablona odpovědi | "- V kolik hodin?"                                           | Výzva k žádosti o hodnotu tohoto parametru, pokud není známa                              |
   | Typ              | DateTime                                                 | Typ parametru, například Číslo, Řetězec nebo Čas data                                      |
   | Výchozí datum     | Pokud datum chybí použití dnes                             |                                                                                                  |
   | Výchozí čas     | Pokud čas chybí, použijte začátek dne                      |                                                                                                  | 

1. Přidání některých ukázkových vět
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Přidání pravidla dokončení pro potvrzení výsledku

   | Nastavení    | Navrhovaná hodnota                                         | Popis                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla  | Nastavení budíku                                               | Název popisující účel pravidla          |
   | Akce    | SpeechResponse - "- Ok, budík nastaven pro {DateTime}"       | Akce, která má být v případě, že je splněna podmínka pravidla |

## <a name="try-it-out"></a>Vyzkoušejte si to.

Vyberte panel Test a vyzkoušejte několik interakcí.

- Vstup: Nastavení budíku na zítřek v poledne
- Výstup: "Ok, budík nastaven na 12/06/2019 12:00:00"

- Vstup: Nastavení budíku
- Výstup: "V kolik?"
- Vstup: 17:00
- Výstup: "Ok, budík nastaven na 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>Přidání rozšířených pravidel pro potvrzení

1. Přidejte pro potvrzení rozšířené pravidlo. 

    Toto pravidlo požádá uživatele o potvrzení data a času alarmu a očekává potvrzení (ano/ne) pro další tah.

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Potvrdit čas data                                                                | Název popisující účel pravidla          |
   | Podmínky            | Povinný parametr - datetime                                                    | Podmínky, které určují, kdy může být pravidlo spuštěno    |   
   | Akce               | SpeechResponse - "- Opravdu chcete nastavit budík pro {DateTime}?"       | Akce, která má být v případě, že je splněna podmínka pravidla |
   | Stát po provedení | Čekat na vstup                                                                   | Stav pro uživatele po otočení                  |
   | Očekávání          | Potvrzení                                                                     | Očekávání pro další tah                      |

1. Přidání rozšířeného pravidla pro zpracování úspěšného potvrzení (uživatel řekl ano)

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Přijaté potvrzení                                                            | Název popisující účel pravidla          |
   | Podmínky            | Úspěšné potvrzení & povinný parametr - DateTime                           | Podmínky, které určují, kdy může být pravidlo spuštěno    |   
   | Stát po provedení | Připraveno k dokončení                                                             | Stav uživatele po otočení                   |

1. Přidání rozšířeného pravidla pro zpracování zamítnutého potvrzení (uživatel řekl ne)

   | Nastavení               | Navrhovaná hodnota                                                                  | Popis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Zamítnuto potvrzení                                                                   | Název popisující účel pravidla          |
   | Podmínky            | DeniedConfirmation & povinný parametr - DateTime                               | Podmínky, které určují, kdy může být pravidlo spuštěno    |   
   | Akce               | ClearParameter - DateTime & SpeechResponse - "- Žádný problém, v kolik hodin pak?"     | Akce, která má být v případě, že je splněna podmínka pravidla |
   | Stát po provedení | Čekat na vstup                                                                   | Stav uživatele po otočení                   |
   | Očekávání          | ElicitParameters - DateTime                                                      | Očekávání pro další tah                      |

## <a name="try-it-out"></a>Vyzkoušejte si to.

Vyberte panel Test a vyzkoušejte několik interakcí.

- Vstup: Nastavení budíku na zítřek v poledne
- Výstup: "Opravdu chcete nastavit budík na 12/07/2019 12:00:00?"
- Vstup: Ne
- Výstup: "Žádný problém, v kolik tedy?"
- Vstup: 17:00
- Výstup: "Opravdu chcete nastavit budík na 12/06/2019 17:00:00?"
- Vstup: Ano
- Výstup: "Ok, budík nastaven na 12/06/2019 17:00:00"

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postup: Přidání jednokrokové opravy do vlastního příkazu (náhled)](./how-to-custom-speech-commands-one-step-correction.md)