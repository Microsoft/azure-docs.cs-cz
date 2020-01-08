---
title: 'Rychlý Start: Vytvoření vlastního příkazu s parametry (Preview) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku přidáte parametry do aplikace Custom Commands.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: fc859625be0706dd130cf7714d0fabf196740e91
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380997"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Rychlý Start: Vytvoření vlastního příkazu s parametry (Preview)

V [předchozím článku](./quickstart-custom-speech-commands-create-new.md)jsme vytvořili nový projekt Custom Commands, který bude reagovat na příkazy bez parametrů.

V tomto článku tuto aplikaci rozšíříme pomocí parametrů tak, aby bylo možné zvládnout zapnutí a vypnutí více zařízení.

## <a name="create-parameters"></a>Vytvoření parametrů

1. Otevřete projekt, který [jsme vytvořili dříve](./quickstart-custom-speech-commands-create-new.md) .
1. Vzhledem k tomu, že se příkaz bude nyní zpracovávat a vypnut, přejmenujte příkaz na "TurnOnOff".
   - Najeďte myší na název příkazu a výběrem ikony upravit změňte název.
1. Vytvořte nový parametr, který bude představovat, jestli uživatel chce zařízení zapnout nebo vypnout.
   - Vyberte ikonu `+` vedle části parametry.

   > [!div class="mx-imgBorder"]
   > ![vytvořit parametr](media/custom-speech-commands/create-on-off-parameter.png)

   | Nastavení            | Navrhovaná hodnota     | Popis                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name (Název)               | (OnOff)               | Popisný název pro parametr                                                                     |
   | Je globální          | unchecked           | Zaškrtávací políčko označující, zda je hodnota pro tento parametr globálně použita pro všechny příkazy v projektu |
   | Požaduje se           | checked             | Zaškrtávací políčko, které určuje, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr          |
   | Šablona odpovědi  | "-Zapnuto nebo vypnuto?"      | Výzva k zadání hodnoty tohoto parametru, pokud není známa                                       |
   | Typ               | Řetězec              | Typ parametru, například číslo, řetězec nebo datum a čas                                               |
   | Konfigurace      | Seznam řetězců         | V případě řetězců seznam řetězců omezuje vstupy na sadu možných hodnot.                                      |
   | Hodnoty seznamu řetězců | zapnuto, vypnuto             | Pro parametr seznamu řetězců, sada možných hodnot a jejich synonyma                                |

   - Potom znovu vyberte ikonu `+` a přidejte druhý parametr, který bude představovat název zařízení. V tomto příkladu TV a ventilátor

   | Nastavení            | Navrhovaná hodnota       | Popis                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name (Název)               | SubjectDevice         | Popisný název pro parametr                                                                     |
   | Je globální          | unchecked             | Zaškrtávací políčko označující, zda je hodnota pro tento parametr globálně použita pro všechny příkazy v projektu |
   | Požaduje se           | checked               | Zaškrtávací políčko, které určuje, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr          |
   | Šablona odpovědi  | ", Které zařízení?"     | Výzva k zadání hodnoty tohoto parametru, pokud není známa                                       |
   | Typ               | Řetězec                | Typ parametru, například číslo, řetězec nebo datum a čas                                               |
   | Konfigurace      | Seznam řetězců           | V případě řetězců seznam řetězců omezuje vstupy na sadu možných hodnot.                                      |
   | Hodnoty seznamu řetězců | TV, ventilátor               | Pro parametr seznamu řetězců, sada možných hodnot a jejich synonyma                                |
   | Synonyma (TV)      | televize, řekněte     | Volitelná synonyma pro každou možnou hodnotu parametru seznamu řetězců                                      |

## <a name="add-sample-sentences"></a>Přidat ukázkové věty

S parametry je užitečné přidat ukázkové věty, které pokrývají všechny možné kombinace. Příklad:

1. Úplné informace o parametrech – `"turn {OnOff} the {SubjectDevice}"`
1. Informace o částečném parametru – `"turn it {OnOff}"`
1. Žádné informace o parametru – `"turn something"`

Ukázkové věty s různými množstvími informací umožňují, aby aplikace Custom Commands vyřešila řešení jednoho snímku a řešení vícenásobného zapínání s částečnými informacemi.

V takovém případě upravte ukázkové věty tak, aby používaly parametry, jak je navrženo níže.

> [!TIP]
> V editoru ukázkových vět použijte složené závorky, které odkazují na vaše parametry. - `turn {OnOff} the {SubjectDevice}` pomocí dokončování karet můžete odkazovat na dříve vytvořené parametry.

> [!div class="mx-imgBorder"]
> ![ukázkové věty s parametry](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Přidat parametry do pravidla dokončení

Upravte pravidlo dokončení, které jste vytvořili v [předchozím rychlém](./quickstart-custom-speech-commands-create-new.md)startu:

1. Přidejte novou podmínku a vyberte požadovaný parametr. Vyberte `OnOff` i `SubjectDevice`
1. Upravit akci odpovědi na řeč pro použití `OnOff` a `SubjectDevice`:

   ```
   Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Vyzkoušet

Otevřete panel test chat a vyzkoušejte několik interakcí.

- Vstup: vypnutí televizního vysílání
- Výstup: OK, vypnutí televizního vysílání

- Vstup: vypnutí televizoru
- Výstup: OK, vypnutí televizního vysílání

- Vstup: vypnout
- Výstup: které zařízení?
- Vstup: TV
- Výstup: OK, vypnutí televizního vysílání

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Rychlý Start: připojení k vlastnímu příkazu aplikace pomocí sady Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md)
