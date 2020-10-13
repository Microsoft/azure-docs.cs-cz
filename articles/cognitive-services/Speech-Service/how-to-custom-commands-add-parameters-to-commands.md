---
title: 'Postupy: Přidání jednoduchých příkazů do vlastních příkazů Application-Speech Service'
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak přidat parametry do vlastních příkazů.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0ed237debc2395ed307658b2d57a541574f9478a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284145"
---
# <a name="add-parameters-to-commands"></a>Přidání parametrů do příkazů

V tomto článku se dozvíte, jak přidat parametry do vlastních příkazů. Parametry jsou informace vyžadované příkazy k dokončení úkolu. Ve složitých scénářích lze také pomocí parametrů definovat podmínky, které aktivují vlastní akce.

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * [Postupy: vytváření aplikací pomocí jednoduchých příkazů](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>Konfigurace parametrů pro příkaz TurnOn

Úpravou stávajícího příkazu **TurnOn** zapněte a vypněte více zařízení.

1. Vzhledem k tomu, že příkaz nyní zpracuje scénář zapnuto i vypnuto, přejmenujte příkaz na **TurnOnOff**.
   1. V levém podokně vyberte příkaz **TurnOn** a potom v horní části podokna vyberte tlačítko se třemi tečkami (...) vedle **příkazu New** .
   
   1. Vyberte **Přejmenovat**. V oknech **příkazu přejmenovat** změňte **název** na **TurnOnOff**.

1. V dalším kroku přidáte do tohoto příkazu nový parametr, který představuje, jestli chce uživatel zařízení zapnout nebo vypnout.
   1. Vyberte  **Přidat** k dispozici v horní části prostředního podokna. V rozevíracím seznamu vyberte možnost **parametr**.
   1. V pravém podokně v části **parametry** přidejte do pole **název** hodnotu **(OnOff)**.
   1. Vyberte možnost **požadováno**. V okně **Přidat odpověď pro požadovaný parametr** vyberte **jednoduchý editor**. V **první variaci**přidejte
        ```
        On or Off?
        ```
   1. Vyberte **Aktualizovat**.

       > [!div class="mx-imgBorder"]
       > ![Vytvořit požadovanou odpověď parametru](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Teď nakonfigurujeme vlastnosti parametrů. Vysvětlení všech vlastností konfigurace příkazu naleznete v [odkazech](./custom-commands-references.md). Nakonfigurujte vlastnosti parametru následujícím způsobem:
      

       | Konfigurace      | Navrhovaná hodnota     | Popis                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Název               | `OnOff`           | Popisný název pro parametr                                                                           |
       | Je globální          | unchecked       | Zaškrtávací políčko označující, zda je hodnota pro tento parametr globálně použita pro všechny příkazy v aplikaci|
       | Vyžadováno           | checked         | Zaškrtávací políčko, které určuje, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr |
       | Odpověď na povinný parametr      |Jednoduchý editor > `On or Off?`      | Výzva k zadání hodnoty tohoto parametru, pokud není známa |
       | Typ               | Řetězec          | Typ parametru, jako je číslo, řetězec, datum a čas nebo zeměpis   |
       | Konfigurace      | Přijměte předdefinované vstupní hodnoty z interního katalogu. | V případě řetězců to omezuje vstupy na sadu možných hodnot. |
       | Předdefinované vstupní hodnoty     | `on`, `off`           | Sada možných hodnot a jejich aliasů         |
       
        
   1. Pokud chcete přidat předdefinované vstupní hodnoty, vyberte **přidat předdefinované zadání** a v okně **Nová položka**  zadejte **název** , jak je uvedeno v tabulce výše. V tomto případě nepoužíváme aliasy, takže je můžete nechat prázdné.
   
      > [!div class="mx-imgBorder"]
      > ![Vytvořit parametr](media/custom-commands/create-on-off-parameter.png)

   1. Vyberte **Uložit** a uložte všechny konfigurace parametru.
 
 ### <a name="add-subjectdevice-parameter"></a>Přidat parametr SubjectDevice 

   1. Potom vyberte **Přidat** znovu a přidejte druhý parametr, který bude představovat název zařízení, které lze ovládat pomocí tohoto příkazu. Použijte následující konfiguraci.
   

       | Nastavení            | Navrhovaná hodnota       |
       | ------------------ | --------------------- |
       | Name               | `SubjectDevice`         |
       | Je globální          | unchecked             |
       | Vyžadováno           | checked               |
       | Odpověď na povinný parametr     | Jednoduchý editor > `Which device do you want to control?`    | 
       | Typ               | Řetězec                |          |
       | Konfigurace      | Přijměte předdefinované vstupní hodnoty z interního katalogu. | 
       | Předdefinované vstupní hodnoty | `tv`, `fan`               |
       | Aliasy ( `tv` )      | `television`, `telly`     |

   1. Vyberte **Uložit**.

### <a name="modify-example-sentences"></a>Upravit ukázkové věty

Pro příkazy s parametry je užitečné přidat ukázkové věty, které pokrývají všechny možné kombinace. Například:

* Úplné informace o parametrech – `turn {OnOff} the {SubjectDevice}`
* Informace o částečném parametru – `turn it {OnOff}`
* Žádné informace o parametru – `turn something`

Příklady vět s různými stupni informací umožňují, aby aplikace Custom Commands vyřešila řešení jednoho snímku a řešení vícenásobného zapínání s částečnými informacemi.

V takovém případě upravte ukázkové věty tak, aby používaly parametry, jak je navrženo níže:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Vyberte **Uložit**.

> [!TIP]
> V editoru vzorových vět použijte složené závorky, které odkazují na vaše parametry. - `turn {OnOff} the {SubjectDevice}` Použijte kartu pro automatické dokončování s dříve vytvořenými parametry.

### <a name="modify-completion-rules-to-include-parameters"></a>Upravit pravidla dokončení pro zahrnutí parametrů

Upravte stávající pravidlo dokončení **ConfirmationResponse**.

1. V části **podmínky** vyberte **Přidat podmínku**.
1. V okně **Nová podmínka** v seznamu **typ** vyberte **požadované parametry**. V níže uvedeném seznamu pro kontrolu proveďte kontrolu **(OnOff)** i **SubjectDevice**.
1. Ponechte nezaškrtnuté políčko- **globální** .
1. Vyberte **Vytvořit**.
1. V části **Akce** upravte existující akci **Odeslat hlasovou odpověď** ukázáním na akci a výběrem tlačítka upravit. Tentokrát Využijte nově vytvořené parametry **(OnOff)** a **SubjectDevice** .

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Vyberte **Uložit**.

### <a name="try-it-out"></a>Vyzkoušet
1. V pravém podokně vyberte ikonu **výuky** .

1. Po dokončení školení vyberte **test**. Zobrazí se okno **test aplikace** .
 Zkuste pár interakcí.

    - Vstup: vypnutí televizního vysílání
    - Výstup: OK, vypnutí televizního vysílání
    - Vstup: vypnutí televizoru
    - Výstup: OK, vypnutí televizního vysílání
    - Vstup: vypnout
    - Výstup: které zařízení chcete ovládat?
    - Vstup: TV
    - Výstup: OK, vypnutí televizního vysílání

## <a name="configure-parameters-for-settemperature-command"></a>Konfigurace parametrů pro příkaz SetTemperature

Upravte příkaz **SetTemperature** tak, aby umožňoval nastavit teplotu podle pokynů uživatele.

Přidat novou **teplotu** parametrů s následující konfigurací

| Konfigurace      | Navrhovaná hodnota     |
| ------------------ | ----------------|
| Name               | `Temperature`           |
| Vyžadováno           | checked         |
| Odpověď na povinný parametr      | Jednoduchý editor > `What temperature would you like?`
| Typ               | Číslo          |


Upravte příklad projevy na následující hodnoty.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Upravte existující pravidla dokončení podle následujících konfigurací.

| Konfigurace      | Navrhovaná hodnota     |
| ------------------ | ----------------|
| Podmínky         | Požadovaný parametr > teplotu           |
| Akce           | Odeslat > odpovědi na řeč `Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>Vyzkoušet

**Prohlaste** a **otestujte** změny s několika interakcemi.

- Vstup: nastavit teplotu
- Výstup: jakou teplotu chcete?
- Vstup: 50 stupňů
- Výstup: OK, nastavení teploty na 50 stupňů

## <a name="configure-parameters-to-the-setalarm-command"></a>Konfigurace parametrů pro příkaz SetAlarm

Přidejte parametr s názvem **DateTime** s následující konfigurací.

   | Nastavení                           | Navrhovaná hodnota                     | 
   | --------------------------------- | ----------------------------------------|
   | Name                              | `DateTime`                               |
   | Vyžadováno                          | checked                                 |
   | Odpověď na povinný parametr   | Jednoduchý editor > `For what time?`            | 
   | Typ                              | DateTime                                |
   | Výchozí hodnoty data                     | Pokud datum chybí, použijte dnešní den.            |
   | Výchozí hodnoty času                     | V případě chybějícího času použít začátek dne     |


> [!NOTE]
> V tomto článku jsme předvedli použití typů parametrů String, Number a DateTime. Pro seznam všech podporovaných typů parametrů a jejich vlastností přejít na [odkazy](./custom-commands-references.md).


Upravte příklad projevy na následující hodnoty.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Upravte existující pravidla dokončení podle následujících konfigurací.

   | Nastavení    | Navrhovaná hodnota                               |
   | ---------- | ------------------------------------------------------- |
   | Akce    | Poslat odezvu na řeč – `Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>Vyzkoušet

**Výuka** a **testování** změn.
- Vstup: nastavit alarm pro zítřejší čas v poledne
- Výstup: OK, sada alarmů pro 2020-05-02 12:00:00
- Vstup: Nastavení alarmu
- Výstup: Jaký čas?
- Vstup: 17:00
- Výstup: OK, sada alarmů pro 2020-05-01 17:00:00


## <a name="try-out-all-the-commands"></a>Vyzkoušejte všechny příkazy

Otestujte všechny tři příkazy společně pomocí projevy souvisejících s různými příkazy. Všimněte si, že můžete přepínat mezi různými příkazy.

- Vstup: Nastavení alarmu
- Výstup: pro který čas?
- Vstup: zapnutí televizního vysílání
- Výstup: OK, zapnutí televizního vysílání
- Vstup: Nastavení alarmu
- Výstup: pro který čas?
- Vstup: 17:00
- Výstup: OK, sada alarmů pro 2020-05-01 17:00:00

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: přidání konfigurací do parametrů příkazů](./how-to-custom-commands-add-parameter-configuration.md)
