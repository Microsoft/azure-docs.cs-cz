---
title: 'Úvodní příručka: Vytvoření vlastního příkazu s parametry (náhled) – služba řeč'
titleSuffix: Azure Cognitive Services
description: V tomto článku přidáte parametry do aplikace Vlastní příkazy.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348536"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Úvodní příručka: Vytvoření vlastního příkazu s parametry (náhled)

V [předchozím článku](./quickstart-custom-speech-commands-create-new.md)jsme vytvořili nový projekt vlastních příkazů, který reaguje na příkazy bez parametrů.

V tomto článku rozšíříme tuto aplikaci o parametry tak, aby zvládla zapnutí a vypnutí více zařízení.

## <a name="create-parameters"></a>Vytvořit parametry

1. Otevření projektu, který [jsme vytvořili dříve](./quickstart-custom-speech-commands-create-new.md)
1. Vzhledem k tomu, že příkaz bude nyní zpracovávat zapnutí a vypnutí, přejmenujte příkaz na "TurnOnOff"
   - Najeďte nad názvem příkazu a vyberte ikonu úprav, chcete-li změnit název
1. Vytvoření nového parametru představujícího, zda chce uživatel zařízení zapnout nebo vypnout.
   - Výběr `+` ikony vedle oddílu Parametry

   > [!div class="mx-imgBorder"]
   > ![Vytvořit parametr](media/custom-speech-commands/create-on-off-parameter.png)

   | Nastavení            | Navrhovaná hodnota     | Popis                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name (Název)               | Onoff               | Popisný název parametru                                                                     |
   | Je globální          | unchecked           | Zaškrtávací políčko označující, zda je hodnota tohoto parametru globálně použita pro všechny příkazy v projektu. |
   | Požaduje se           | checked             | Zaškrtávací políčko označující, zda je před dokončením příkazu vyžadována hodnota tohoto parametru.          |
   | Šablona odpovědi  | "- Zapnutí nebo vypnutí?"      | Výzva k žádosti o hodnotu tohoto parametru, pokud není známa                                       |
   | Typ               | Řetězec              | Typ parametru, například Číslo, Řetězec nebo Čas data                                               |
   | Konfigurace      | Seznam řetězců         | Pro řetězce seznam řetězců omezuje vstupy na sadu možných hodnot                                      |
   | Hodnoty seznamu řetězců | zapnuto, vypnuto             | Pro parametr String List sada možných hodnot a jejich synonyma                                |

   - Dále vyberte `+` ikonu znovu a přidejte druhý parametr představující název zařízení. V tomto příkladu televizor a ventilátor

   | Nastavení            | Navrhovaná hodnota       | Popis                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name (Název)               | PředmětZařízení         | Popisný název parametru                                                                     |
   | Je globální          | unchecked             | Zaškrtávací políčko označující, zda je hodnota tohoto parametru globálně použita pro všechny příkazy v projektu. |
   | Požaduje se           | checked               | Zaškrtávací políčko označující, zda je před dokončením příkazu vyžadována hodnota tohoto parametru.          |
   | Šablona odpovědi  | "- Které zařízení?"     | Výzva k žádosti o hodnotu tohoto parametru, pokud není známa                                       |
   | Typ               | Řetězec                | Typ parametru, například Číslo, Řetězec nebo Čas data                                               |
   | Konfigurace      | Seznam řetězců           | Pro řetězce seznam řetězců omezuje vstupy na sadu možných hodnot                                      |
   | Hodnoty seznamu řetězců | tv, ventilátor               | Pro parametr String List sada možných hodnot a jejich synonyma                                |
   | Synonyma (tv)      | televize, televize     | Volitelná synonyma pro každou možnou hodnotu parametru seznamu řetězců                                      |

## <a name="add-sample-sentences"></a>Přidat vzorové věty

S parametry je užitečné přidat ukázkové věty, které pokrývají všechny možné kombinace. Například:

1. Úplné informace o parametrech -`"turn {OnOff} the {SubjectDevice}"`
1. Informace o dílčích parametrech -`"turn it {OnOff}"`
1. Žádné informace o parametrech -`"turn something"`

Ukázkové věty s různým množstvím informací umožňují aplikaci Custom Commands vyřešit rozlišení jednoho výstřelu i rozlišení s více otočeními s částečnými informacemi.

S ohledem na to upravte ukázkové věty tak, aby používaly níže uvedené parametry.

> [!TIP]
> V editoru ukázkových vět použijte složené závorky, abyste odkazovali na parametry. - `turn {OnOff} the {SubjectDevice}`Pomocí dokončování tabulátoru můžete odkazovat na dříve vytvořené parametry.

> [!div class="mx-imgBorder"]
> ![Ukázkové věty s parametry](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Přidat parametry do pravidla dokončení

Upravte pravidlo dokončení, které jste [vytvořili](./quickstart-custom-speech-commands-create-new.md)v předchozím rychlém startu :

1. Přidejte novou podmínku a vyberte Povinný parametr. Vyberte `OnOff` obě a`SubjectDevice`
1. Upravte akci Odezva `OnOff` `SubjectDevice`řeči, která se má použít, a :

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Vyzkoušejte si to.

Otevřete panel Testovací chat a vyzkoušejte několik interakcí.

- Vstup: vypnutí televizoru
- Výstup: Ok, vypnutí televizoru

- Vstup: vypnutí televizoru
- Výstup: Ok, vypnutí televizoru

- Vstup: vypněte jej
- Výstup: Které zařízení?
- Vstup: tv
- Výstup: Ok, vypnutí televizoru

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Úvodní příručka: Použití vlastních příkazů s vlastním hlasem (náhled)](./quickstart-custom-speech-commands-select-custom-voice.md)
