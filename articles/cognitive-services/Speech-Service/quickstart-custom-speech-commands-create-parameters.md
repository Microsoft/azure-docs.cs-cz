---
title: 'Rychlý Start: Vytvoření vlastního příkazu s parametry (Preview) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku přidáte parametry do aplikace Custom Commands.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853606"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>Rychlý Start: Vytvoření vlastní aplikace příkazů s parametry (Preview)

V [předchozím článku](./quickstart-custom-speech-commands-create-new.md)jste vytvořili jednoduchou aplikaci Custom Commands bez parametrů.

V tomto článku budete tuto aplikaci rozšíříte tak, aby používala parametry, aby mohla zpracovat zapnutí a vypnutí více zařízení.

## <a name="create-parameters"></a>Vytvoření parametrů

1. Otevřete projekt [, který jste vytvořili dříve](./quickstart-custom-speech-commands-create-new.md) .
1. Pojďme upravit existující příkaz pro zapnutí a vypnutí více zařízení.
1. Vzhledem k tomu, že příkaz bude nyní zpracován a vypnut, přejmenujte příkaz na `TurnOnOff`.
   - V levém podokně vyberte `TurnOn` příkaz a potom klikněte na `...` ikonu vedle `+ New command` v horní části podokna.
   
   - Vyberte `Rename` ikonu. V rozbalovacím seznamu **příkaz pro přejmenování** změňte **název** na `TurOnOff`. Potom vyberte **Uložit**.

1. V dalším kroku vytvoříte nový parametr, který bude představovat, jestli chce uživatel zařízení zapnout nebo vypnout.
   - V `+ Add` horní části podokna uprostřed vyberte ikonu k dispozici. V rozevíracím seznamu vyberte možnost **parametr**.
   - V pravém krajním podokně uvidíte konfigurační oddíl **parametry** .
   - Přidejte hodnotu pro **název**.
   - Zaškrtněte **požadovaná** zaškrtávací políčka. V okně **Přidat odpověď pro požadovaný parametr** vyberte **jednoduchý editor** a **první variaci**, přidat
        ```
        On or Off?
        ```
   - Vyberte **Aktualizovat**.

       > [!div class="mx-imgBorder"]
       > ![Vytvořit požadovanou odpověď parametru](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - Dále nakonfigurujeme zbývající vlastnosti parametru následujícím způsobem a výběrem možnosti `Save` Uložit konfiguraci do parametru.
       

       | Konfigurace      | Navrhovaná hodnota     | Popis                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Name               | (OnOff)           | Popisný název pro parametr                                                                           |
       | Je globální          | unchecked       | Zaškrtávací políčko označující, zda je hodnota pro tento parametr globálně použita pro všechny příkazy v aplikaci|
       | Požaduje se           | checked         | Zaškrtávací políčko, které určuje, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr |
       | Odpověď na povinný parametr      |Jednoduchý editor-> zapnuto nebo vypnuto?      | Výzva k zadání hodnoty tohoto parametru, pokud není známa |
       | Typ               | Řetězec          | Typ parametru, jako je číslo, řetězec, datum a čas nebo zeměpis   |
       | Konfigurace      | Přijměte předdefinované vstupní hodnoty z interního katalogu. | V případě řetězců to omezuje vstupy na sadu možných hodnot. |
       | Předdefinované vstupní hodnoty     | zapnuto, vypnuto             | Sada možných hodnot a jejich aliasů         |
       
        > [!div class="mx-imgBorder"]
        > ![Vytvořit parametr](media/custom-speech-commands/create-on-off-parameter.png)

   - Potom vyberte `+ Add` ikonu znovu a přidejte druhý parametr představující název zařízení s následující konfigurací.
   

       | Nastavení            | Navrhovaná hodnota       | Popis                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | Name               | SubjectDevice         | Popisný název pro parametr                                                                     |
       | Je globální          | unchecked             | Zaškrtávací políčko označující, zda je hodnota pro tento parametr globálně použita pro všechny příkazy v aplikaci |
       | Požaduje se           | checked               | Zaškrtávací políčko, které určuje, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr          |
       | Jednoduchý editor      | Které zařízení?    | Výzva k zadání hodnoty tohoto parametru, pokud není známa                                       |
       | Typ               | Řetězec                | Typ parametru, jako je číslo, řetězec, datum a čas nebo zeměpis                                                |
       | Konfigurace      | Přijměte předdefinované vstupní hodnoty z interního katalogu. | V případě řetězců seznam řetězců omezuje vstupy na sadu možných hodnot.       |
       | Předdefinované vstupní hodnoty | TV, ventilátor               | Sada možných hodnot a jejich aliasů                               |
       | Aliasy (TV)      | televize, řekněte     | Nepovinné aliasy pro každou možnou hodnotu předdefinovaných vstupních hodnot                                 |

## <a name="add-example-sentences"></a>Přidat ukázkové věty

S příkazy s parametry je užitečné přidat ukázkové věty, které pokrývají všechny možné kombinace. Příklad:

1. Úplné informace o parametrech –`turn {OnOff} the {SubjectDevice}`
1. Informace o částečném parametru –`turn it {OnOff}`
1. Žádné informace o parametru –`turn something`

Příklady vět s různými stupni informací umožňují, aby aplikace Custom Commands vyřešila řešení jednoho snímku a řešení vícenásobného zapínání s částečnými informacemi.

V takovém případě upravte ukázkové věty tak, aby používaly parametry, jak je navrženo níže.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> V editoru vzorových vět použijte složené závorky, které odkazují na vaše parametry. - `turn {OnOff} the {SubjectDevice}`Použijte kartu pro automatické dokončování s dříve vytvořenými parametry.

## <a name="add-parameters-to-completion-rules"></a>Přidat parametry do pravidel dokončení

Upravte pravidlo dokončení, které jsme vytvořili v [předchozím rychlém](./quickstart-custom-speech-commands-create-new.md)startu.

1. V části **podmínky** přidejte novou podmínku tak, že vyberete **+ Přidat podmínku** .
1. V nové místní **podmínce**vyberte `Required parameters` v rozevíracím seznamu **typ** . V níže uvedeném seznamu pro kontrolu `OnOff` a. `SubjectDevice`
1. Klikněte na **Vytvořit**.
1. V části **Akce** upravte existující akci odeslat hlasovou odpověď ukázáním na akci a kliknutím na ikonu Upravit. Tentokrát Pojďme využít nově vytvořené `OnOff` parametry a. `SubjectDevice`

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Vyzkoušejte si to.
1. Vyberte `Train` ikonu, která se nachází v pravém podokně.

1. Po dokončení školení vyberte `Test`.
    - Zobrazí se nový test, který se zobrazí **v okně aplikace** .
    - Zkuste pár interakcí.

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
> [Rychlý Start: použití vlastních příkazů s vlastním hlasem (Preview)](./quickstart-custom-speech-commands-select-custom-voice.md)
