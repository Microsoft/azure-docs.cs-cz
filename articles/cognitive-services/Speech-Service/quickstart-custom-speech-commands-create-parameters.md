---
title: 'Rychlý Start: Vytvoření vlastní příkazy aplikace ve verzi Preview pomocí parametrů – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku přidáte parametry do vlastní aplikace příkazů, aby bylo možné zapnout nebo vypnout více zařízení.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: nitinme
ms.openlocfilehash: 940b3604487fbef7736c0d8f4f9299563b0b86d5
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509300"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>Rychlý Start: Vytvoření vlastní příkazy Preview aplikace s parametry

V [předchozím článku](./quickstart-custom-speech-commands-create-new.md)jste vytvořili jednoduchou aplikaci Custom Commands bez parametrů.

V tomto článku tuto aplikaci rozšíříte pomocí parametrů, aby bylo možné zapnout nebo vypnout více zařízení.

## <a name="create-parameters"></a>Vytvoření parametrů

1. Otevřete projekt, který jste vytvořili v [předchozím článku](./quickstart-custom-speech-commands-create-new.md).

   Existující příkaz upravíte tak, aby se mohl použít k zapnutí a vypnutí více zařízení.
1. Vzhledem k tomu, že příkaz nyní zpracuje i vypne, přejmenujte ho na **TurnOnOff**.
   1. V levém podokně vyberte příkaz **TurnOn** a potom v horní části podokna vyberte tlačítko se třemi tečkami (**...**) vedle **příkazu New** .
   
   1. Vyberte **Přejmenovat**. V okně **příkaz k přejmenování** změňte **název** na **TurOnOff**. Vyberte **Uložit**.

1. Vytvořte parametr, který reprezentuje, jestli chce uživatel zařízení zapnout nebo vypnout.
   1. V horní části podokna uprostřed vyberte **Přidat** . V rozevíracím seznamu vyberte možnost **parametr**.
   1. V pravém podokně v části **parametry** přidejte hodnotu do pole **název** .
   1. Vyberte možnost **požadováno**. V okně **Přidat odpověď pro požadovaný parametr** vyberte **jednoduchý editor**. Do pole **první variace** zadejte tento text:
        ```
        On or Off?
        ```
   1. Vyberte **Aktualizovat**.

       > [!div class="mx-imgBorder"]
       > ![Vytvořit požadovanou odpověď parametru](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. Nastavte zbývající vlastnosti parametru následujícím způsobem:
       

    | Konfigurace      | Navrhovaná hodnota     | Popis                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **Název**               | **(OnOff)**           | Popisný název parametru                                                                  |
    | **Je globální**          | Vymazán       | Zaškrtávací políčko, které určuje, zda je hodnota parametru globálně použita pro všechny příkazy v aplikaci.|
    | **Požadováno**           | Vybráno         | Zaškrtávací políčko, které určuje, zda je hodnota parametru požadována.  |
    | **Odpověď na povinný parametr**      |**Jednoduchý editor-> zapnuto nebo vypnuto?**      | Výzva k zadání hodnoty parametru, pokud není známa. |
    | **Typ**               | **Řetězec**          | Typ parametru. Například číslo, řetězec, datum a čas, geografie.   |
    | **Konfigurace**      | **Přijměte předdefinované vstupní hodnoty z interního katalogu.** | V případě řetězců toto nastavení omezuje vstupy na sadu možných hodnot. |
    | **Předdefinované vstupní hodnoty**     | **zapnuto**, **vypnuto**             | Sada možných hodnot a jejich aliasů.         |
       


    > [!div class="mx-imgBorder"]
    > ![Vytvořit parametr](media/custom-speech-commands/create-on-off-parameter.png)

1. Vyberte **Uložit** a nastavení se uloží.

 1. Vyberte **Přidat** znovu a přidejte druhý parametr. Tento parametr představuje název zařízení. Použijte tato nastavení:
   

       | Nastavení            | Navrhovaná hodnota       | Popis                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **Název**               | **SubjectDevice**         | Popisný název pro parametr                                                                     |
       | **Je globální**          | Vymazán             | Zaškrtávací políčko, které určuje, zda je hodnota parametru globálně použita pro všechny příkazy v aplikaci. |
       | **Požadováno**           | Vybráno               | Zaškrtávací políčko, které určuje, zda je hodnota parametru požadována.          |
       | **Jednoduchý editor**      | **Které zařízení?**    | Výzva k zadání hodnoty parametru, pokud není známa.                                       |
       | **Typ**               | **Řetězec**                | Typ parametru. Například číslo, řetězec, datum a čas, geografie.                                                |
       | **Konfigurace**      | **Přijměte předdefinované vstupní hodnoty z interního katalogu.** | V případě řetězců toto nastavení omezuje vstupy na sadu možných hodnot.       |
       | **Předdefinované vstupní hodnoty** | **TV**, **ventilátor**               | Sada možných hodnot a jejich aliasů.                               |
       | **Aliasy** (TV)      | **televize**, **řekněte**     | Volitelné aliasy pro každou z předdefinovaných vstupních hodnot.                                 |

## <a name="add-example-sentences"></a>Přidat ukázkové věty

Pro příkazy, které mají parametry, je vhodné přidat ukázkové věty, které pokrývají všechny možné kombinace. Například:

- Úplné informace o parametru:`turn {OnOff} the {SubjectDevice}`
- Informace o částečném parametru:`turn it {OnOff}`
- Žádné informace o parametru:`turn something`

Příklady vět, které mají různé množství informací, umožňují aplikaci Custom Commands vyřešit jak rozlišení na jednom snímku, tak řešení pro vícenásobné zapínání, které obsahuje částečné informace.

V takovém případě upravte ukázkové věty na použití parametrů, jak je navrženo zde:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> V editoru ukázkových vět použijte složené závorky, které odkazují na vaše parametry: `turn {OnOff} the {SubjectDevice}` .
>
> Použijte kartu pro automatické dokončování definované dříve vytvořenými parametry.

## <a name="add-parameters-to-completion-rules"></a>Přidat parametry do pravidel dokončení

Upravte pravidlo dokončení, které jste vytvořili v [předchozím rychlém](./quickstart-custom-speech-commands-create-new.md)startu.

1. V části **podmínky** vyberte **Přidat podmínku**.
1. V okně **Nová podmínka** v seznamu **typ** vyberte **požadované parametry**. V seznamu vyberte obě **(OnOff)** a **SubjectDevice**.
1. Vyberte **Vytvořit**.
1. V části **Akce** upravte existující akci **Odeslat hlasovou odpověď** ukázáním na akci a výběrem tlačítka upravit. Tentokrát použijte nové `OnOff` `SubjectDevice` parametry a:

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Vyzkoušejte si to.
1. V horní části pravého podokna vyberte **vlak** .

1. Po dokončení školení vyberte **test**.
    
    Zobrazí se okno **test aplikace** .

1. Zkuste pár interakcí.

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Rychlý Start: použití vlastních příkazů s vlastním hlasem (Preview)](./quickstart-custom-speech-commands-select-custom-voice.md)
