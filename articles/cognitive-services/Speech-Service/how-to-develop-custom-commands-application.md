---
title: 'Postupy: vývoj aplikací pro vlastní příkazy – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Naučte se vyvíjet a přizpůsobovat aplikace pro vlastní příkazy. Tyto aplikace hlasových příkazů jsou nejvhodnější pro dokončování úkolů nebo příkazy a řízení.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 1a002b6efbe2603ae254c19f9e3cc7377198cea2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935814"
---
# <a name="develop-custom-commands-applications"></a>Vývoj aplikací pro vlastní příkazy

V tomto článku se dozvíte, jak vyvíjet a konfigurovat aplikace s vlastními příkazy. Funkce Custom Commands pomáhá vytvářet bohatě hlasové aplikace, které jsou optimalizované pro prostředí s prvními možnostmi interakce hlasu. Tato funkce nejlépe vyhovuje scénářům dokončování úloh nebo příkazů a řízení. Je zvláště vhodné pro zařízení Internet věcí (IoT) a pro zařízení s okolním i nebezobslužným prostředím.

V tomto článku vytvoříte aplikaci, která může zapnout a vypnout televizní vysílání, nastavit teplotu a nastavit alarm. Po vytvoření těchto základních příkazů se dozvíte o následujících možnostech přizpůsobení příkazů:

* Přidávání parametrů do příkazů
* Přidání konfigurací do parametrů příkazu
* Sestavování pravidel interakce
* Vytváření šablon pro generování jazyka pro odezvy řeči
* Používání vlastních hlasových nástrojů

## <a name="create-an-application-by-using-simple-commands"></a>Vytvoření aplikace pomocí jednoduchých příkazů

Začněte vytvořením prázdné vlastní aplikace s příkazy. Podrobnosti najdete v [rychlém](quickstart-custom-commands-application.md)startu. V této aplikaci namísto importu projektu vytvoříte prázdný projekt.

1. Do pole **název** zadejte název projektu *chytrá místnost-Lite* (nebo jiný název, který si zvolíte).
1. V seznamu **jazyk** vyberte možnost **Angličtina (USA)**.
1. Vyberte nebo vytvořte prostředek LUIS.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky se zobrazeným oknem nový projekt](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Aktualizace prostředků LUIS (volitelné)

Můžete aktualizovat prostředek pro vytváření obsahu, který jste vybrali v okně **Nový projekt** . Můžete také nastavit předpověď prostředku. 

K rozpoznávání se používá prostředek předpovědi, pokud je vaše aplikace pro vlastní příkazy publikována. Během fází vývoje a testování nepotřebujete předpověď prostředků.

### <a name="add-a-turnon-command"></a>Přidání příkazu TurnOn

V prázdné aplikaci pro vlastní příkazy inteligentních místností – Lite, kterou jste vytvořili, přidejte příkaz. Příkaz zpracuje utterance, `Turn on the tv` . Bude reagovat na zprávu `Ok, turning the tv on` .

1. Vytvořte nový příkaz výběrem **příkazu nový** v horní části levého podokna. Otevře se **nové okno příkazu** .
1. Do pole **název** zadejte hodnotu `TurnOn` .
1. Vyberte **Vytvořit**.

V prostředním podokně jsou uvedeny vlastnosti příkazu. 

Následující tabulka popisuje vlastnosti konfigurace příkazu. Další informace najdete v tématu [Koncepty a definice vlastních příkazů](./custom-commands-references.md).

| Konfigurace            | Popis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Příklady vět | Příklad projevy může uživatel spustit tento příkaz.                                                                 |
| Parametry       | Informace potřebné k dokončení příkazu                                                                                |
| Pravidla dokončení | Akce, které mají být provedeny pro splnění příkazu. Příklady: reakce na uživatele nebo komunikace s webovou službou. |
| Pravidla interakce   | Další pravidla, která budou zpracovávat konkrétnější nebo složitou situaci.                                                              |


> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s informacemi o tom, kde vytvořit příkaz](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Přidat ukázkové věty

V části **příklad vět** můžete zadat příklad toho, co může uživatel vyslovit.

1. V prostředním podokně vyberte **příklady vět**.
1. V pravém podokně přidejte příklady:

    ```
    Turn on the tv
    ```

1.  V horní části podokna vyberte **Save (Uložit**).

Ještě nemáte parametry, takže se můžete přesunout do oddílu **pravidla dokončení** .

#### <a name="add-a-completion-rule"></a>Přidat pravidlo dokončení

Dále bude příkaz potřebovat pravidlo dokončení. Toto pravidlo přikáže uživateli, že je prováděna akce plnění. 

Další informace o pravidlech a pravidlech dokončení najdete v tématu [Koncepty a definice vlastních příkazů](./custom-commands-references.md).

1. Vyberte výchozí pravidlo dokončení **dokončeno**. Pak ho upravte takto:

    
    | Nastavení    | Navrhovaná hodnota                          | Popis                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Název**       | `ConfirmationResponse`                  | Název popisující účel pravidla          |
    | **Podmínky** | Žádné                                     | Podmínky, které určují, kdy se pravidlo dá spustit    |
    | **Akce**    | **Odeslat odezvu**  >  na řeč **Jednoduchý editor**  >  **První variace** > `Ok, turning the tv on` | Akce, která se má provést, když je podmínka pravidla pravdivá |

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s informacemi o tom, kde vytvořit reakci na řeč](media/custom-commands/create-speech-response-action.png)

1. Akci uložte kliknutím na **Uložit** .
1. Zpátky v části **pravidla dokončování** vyberte **Uložit** a uložte všechny změny. 

    > [!NOTE]
    > Nemusíte používat výchozí pravidlo dokončení, které je součástí příkazu. Můžete odstranit výchozí pravidlo dokončení a přidat vlastní pravidlo.

### <a name="add-a-settemperature-command"></a>Přidání příkazu SetTemperature

Nyní přidejte jeden další příkaz, `SetTemperature` . Tento příkaz bude mít jednu utterance, `Set the temperature to 40 degrees` a odpovědět se zprávou `Ok, setting temperature to 40 degrees` .

Chcete-li vytvořit nový příkaz, postupujte podle kroků, které jste použili pro `TurnOn` příkaz, ale použijte ukázkovou větu `Set the temperature to 40 degrees` .

Pak upravte existující pravidla dokončení **dokončeno** následujícím způsobem:

| Nastavení    | Navrhovaná hodnota                          |
| ---------- | ---------------------------------------- |
| **Název**  | `ConfirmationResponse`                  |
| **Podmínky** | Žádné                                     |
| **Akce**    | **Odeslat odezvu**  >  na řeč **Jednoduchý editor**  >  **První variace** > `Ok, setting temperature to 40 degrees` |

Výběrem **Uložit** uložte všechny změny do příkazu.

### <a name="add-a-setalarm-command"></a>Přidání příkazu SetAlarm

Vytvoří nový `SetAlarm` příkaz. Použijte ukázkovou větu `Set an alarm for 9 am tomorrow` . Pak upravte existující pravidla dokončení **dokončeno** následujícím způsobem:

| Nastavení    | Navrhovaná hodnota                          |
| ---------- | ---------------------------------------- |
| **Název**  | `ConfirmationResponse`                  |
| **Podmínky** | Žádné                                     |
| **Akce**    | **Odeslat odezvu**  >  na řeč **Jednoduchý editor**  >  **První variace** > `Ok, setting an alarm for 9 am tomorrow` |

Výběrem **Uložit** uložte všechny změny do příkazu.

### <a name="try-it-out"></a>Vyzkoušet

Otestujte chování aplikace pomocí podokna test: 

1. V pravém horním rohu podokna vyberte ikonu **výuka** . 
1. Po dokončení školení vyberte **test**. 

Vyzkoušejte následující příklady utterance pomocí hlasu nebo textu:

- Zadáte: *nastavte teplotu na 40 stupňů* .
- Očekávaná odpověď: OK, nastavení teploty na 40 stupňů
- Zadáte: *zapnout televizor*
- Očekávaná odpověď: OK, zapnutí televizního vysílání
- Zadáte: *nastavte alarm pro 9 – zítra*
- Očekávaná odpověď: OK, nastavení alarmu pro 9 – zítra

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky znázorňující test v rozhraní webové konverzace](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> V podokně test můžete vybrat možnost **zapnout podrobnosti** pro informace o způsobu zpracování tohoto hlasového vstupu nebo textového vstupu.

## <a name="add-parameters-to-commands"></a>Přidání parametrů do příkazů

V této části se dozvíte, jak do příkazů přidat parametry. Příkazy vyžadují parametry k dokončení úkolu. Ve složitých scénářích lze pomocí parametrů definovat podmínky, které spouštějí vlastní akce.

### <a name="configure-parameters-for-a-turnon-command"></a>Konfigurace parametrů pro příkaz TurnOn

Začněte úpravou stávajícího `TurnOn` příkazu pro zapnutí a vypnutí více zařízení.

1. Teď, když příkaz zpracuje jak scénáře zapnuto, tak i vypnutí, přejmenujte příkaz jako *TurnOnOff*.
   1. V podokně na levé straně vyberte příkaz **TurnOn** . Pak v horní části podokna klikněte na tlačítko se třemi tečkami (**...**) vedle **příkazu nový** .
   
   1. Vyberte **Přejmenovat**. V okně **příkaz k přejmenování** změňte název na *TurnOnOff*.

1. Přidejte do příkazu nový parametr. Parametr představuje, zda uživatel chce zařízení zapnout nebo vypnout.
   1. V horní části podokna uprostřed vyberte  **Přidat**. V rozevírací nabídce vyberte možnost **parametr**.
   1. V podokně na pravé straně v části **parametry** v poli **název** přidejte `OnOff` .
   1. Vyberte možnost **požadováno**. V okně **Přidat odpověď pro požadovaný parametr** vyberte **jednoduchý editor**. Do pole **první variace** přidejte *nebo vypněte?*.
   1. Vyberte **Aktualizovat**.

       > [!div class="mx-imgBorder"]
       > ![Snímek obrazovky, který zobrazuje oddíl přidat odpověď pro požadovaný parametr s vybranou kartou Simple Editor.](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Nakonfigurujte vlastnosti parametru pomocí následující tabulky. Informace o všech vlastnostech konfigurace příkazu najdete v tématu [Koncepty a definice vlastních příkazů](./custom-commands-references.md).
      

       | Konfigurace      | Navrhovaná hodnota     | Popis                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Název**               | `OnOff`           | Popisný název parametru                                                                           |
       | **Je globální**          | Nevybrané       | Zaškrtávací políčko označující, zda je hodnota pro tento parametr globálně použita pro všechny příkazy v aplikaci.|
       | **Povinné**           | Vybráno         | Zaškrtávací políčko označující, zda je hodnota pro tento parametr požadována před dokončením příkazu. |
       | **Odpověď na povinný parametr**      |**Jednoduchý editor** > `On or Off?`      | Výzva s výzvou k zadání hodnoty tohoto parametru, pokud není známa. |
       | **Typ**               | **Řetězec**          | Typ parametru, jako je číslo, řetězec, datum a čas nebo zeměpis.   |
       | **Konfigurace**      | **Přijměte předdefinované vstupní hodnoty z interního katalogu.** | V případě řetězců toto nastavení omezuje vstupy na sadu možných hodnot. |
       | **Předdefinované vstupní hodnoty**     | `on`, `off`           | Sada možných hodnot a jejich aliasů.         |
       
        
   1. Pokud chcete přidat předdefinované vstupní hodnoty, vyberte **přidat předdefinovaný vstup**. V okně **Nová položka**  zadejte *název* , jak je znázorněno v předchozí tabulce. V takovém případě nepoužíváte aliasy, takže toto pole můžete nechat prázdné.
   
      > [!div class="mx-imgBorder"]
      > ![Snímek obrazovky ukazující, jak vytvořit parametr](media/custom-commands/create-on-off-parameter.png)

   1. Vyberte **Uložit** a uložte všechny konfigurace parametru.
 
#### <a name="add-a-subjectdevice-parameter"></a>Přidání parametru SubjectDevice

1. Chcete-li přidat druhý parametr představující název zařízení, která lze ovládat pomocí tohoto příkazu, vyberte možnost **Přidat**. Použijte následující konfiguraci.


    | Nastavení            | Navrhovaná hodnota       |
    | ------------------ | --------------------- |
    | **Název**               | `SubjectDevice`         |
    | **Je globální**          | Nevybrané             |
    | **Povinné**           | Vybráno               |
    | **Odpověď na povinný parametr**     | **Jednoduchý editor** > `Which device do you want to control?`    | 
    | **Typ**               | **Řetězec**                |          |
    | **Konfigurace**      | **Přijměte předdefinované vstupní hodnoty z interního katalogu.** | 
    | **Předdefinované vstupní hodnoty** | `tv`, `fan`               |
    | **Aliasy** ( `tv` )      | `television`, `telly`     |

1. Vyberte **Uložit**.

#### <a name="modify-example-sentences"></a>Upravit ukázkové věty

Pro příkazy, které používají parametry, je užitečné přidat ukázkové věty, které pokrývají všechny možné kombinace. Například:

* Úplné informace o parametru: `turn {OnOff} the {SubjectDevice}`
* Informace o částečném parametru: `turn it {OnOff}`
* Žádné informace o parametru: `turn something`

Příklady vět, které používají různé stupně informací, umožňují aplikaci Custom Commands vyřešit jak rozlišení na jednom snímku, tak řešení vícenásobného zapnutí pomocí částečných informací.

V takovém případě upravte ukázkové věty pro použití těchto navrhovaných parametrů:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Vyberte **Uložit**.

> [!TIP]
> V editoru example-vět použijte složené závorky, které odkazují na vaše parametry. Například, `turn {OnOff} the {SubjectDevice}`.
> Použijte kartu pro automatické dokončování dříve vytvořených parametrů.

#### <a name="modify-completion-rules-to-include-parameters"></a>Upravit pravidla dokončení pro zahrnutí parametrů

Upravte stávající pravidlo dokončení `ConfirmationResponse` .

1. V části **podmínky** vyberte **Přidat podmínku**.
1. V okně **Nová podmínka** v seznamu **typ** vyberte **požadované parametry**. V následujícím seznamu vyberte možnost **(OnOff)** a **SubjectDevice**.
1. Nechejte  nevybranou možnost Nevybráno.
1. Vyberte **Vytvořit**.
1. V části **Akce** upravte akci **Odeslat odezvu na řeč** tak, že na ni najedete myší a vyberete tlačítko Upravit. Tentokrát použijte nově vytvořené `OnOff` `SubjectDevice` parametry a:

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Vyberte **Uložit**.

Vyzkoušejte změny tím, že v horní části podokna napravo vyberete ikonu **výuka** . 

Po dokončení školení vyberte **test**. Zobrazí se okno **test aplikace** . Vyzkoušejte následující interakce:

- Vstup: vypnutí *televizního vysílání*
- Výstup: OK, vypnutí televizního vysílání
- Vstup: vypnutí *televizoru*
- Výstup: OK, vypnutí televizního vysílání
- Vstup: *vypnout*
- Výstup: které zařízení chcete ovládat?
- Vstup: *TV*
- Výstup: OK, vypnutí televizního vysílání

### <a name="configure-parameters-for-a-settemperature-command"></a>Konfigurace parametrů pro příkaz SetTemperature

Upravte `SetTemperature` příkaz tak, aby umožňoval nastavit teplotu jako uživatel.

Přidejte `Temperature` parametr. Použijte následující konfiguraci:

| Konfigurace      | Navrhovaná hodnota     |
| ------------------ | ----------------|
| **Název**               | `Temperature`           |
| **Povinné**           | Vybráno         |
| **Odpověď na povinný parametr**      | **Jednoduchý editor** > `What temperature would you like?`
| **Typ**               | `Number`          |


Upravte vzorový projevy tak, aby používal následující hodnoty.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Upravte existující pravidla dokončení. Použijte následující konfiguraci.

| Konfigurace      | Navrhovaná hodnota     |
| ------------------ | ----------------|
| **Podmínky**         | **Povinný parametr**  >  **Teplota**           |
| **Akce**           | **Odeslat odezvu na řeč** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>Konfigurace parametrů pro příkaz SetAlarm

Přidejte parametr s názvem `DateTime` . Použijte následující konfiguraci.

   | Nastavení                           | Navrhovaná hodnota                     | 
   | --------------------------------- | ----------------------------------------|
   | **Název**                              | `DateTime`                               |
   | **Povinné**                          | Vybráno                                 |
   | **Odpověď na povinný parametr**   | **Jednoduchý editor** > `For what time?`            | 
   | **Typ**                              | **Datum a čas**                                |
   | **Výchozí hodnoty data**                     | Pokud datum chybí, použijte dnešní den.            |
   | **Výchozí hodnoty času**                     | Pokud chybí čas, použijte začátek dne.     |


> [!NOTE]
> Tento článek většinou používá typy parametrů String, Number a DateTime. Seznam všech podporovaných typů parametrů a jejich vlastností najdete v tématu [Koncepty a definice vlastních příkazů](./custom-commands-references.md).


Upravte příklad projevy. Použijte následující hodnoty.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Upravte existující pravidla dokončení. Použijte následující konfiguraci.

   | Nastavení    | Navrhovaná hodnota                               |
   | ---------- | ------------------------------------------------------- |
   | **Akce**    | **Odeslat odezvu na řeč** > `Ok, alarm set for {DateTime}`  |

Otestujte tři příkazy společně pomocí projevy souvisejících s různými příkazy. (Můžete přepínat mezi různými příkazy.)

- Vstup: *Nastavení alarmu*
- Výstup: pro který čas?
- Vstup: *Zapnutí televizního vysílání*
- Výstup: OK, zapnutí televizního vysílání
- Vstup: *Nastavení alarmu*
- Výstup: pro který čas?
- Vstup: *5 odp* .
- Výstup: OK, sada alarmů pro 2020-05-01 17:00:00

## <a name="add-configurations-to-command-parameters"></a>Přidání konfigurací do parametrů příkazu

V této části se dozvíte víc o rozšířené konfiguraci parametrů, včetně:

 - Jak hodnoty parametrů mohou patřit do množiny, která je definována mimo aplikaci Custom Commands.
 - Postup přidání klauzulí ověřování pro hodnoty parametru.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Konfigurace parametru jako entity externího katalogu

Funkce Custom Commands umožňuje nakonfigurovat parametry typu String, aby odkazovaly na externí katalogy hostované přes webový koncový bod. Proto můžete externí katalog aktualizovat nezávisle bez úpravy aplikace Custom Commands. Tento přístup je užitečný v případech, kdy jsou položky katalogu mnoho.

Znovu použijte `SubjectDevice` parametr z `TurnOnOff` příkazu. Aktuální konfigurace tohoto parametru **přijímá předdefinované vstupy z interního katalogu**. Tato konfigurace odkazuje na statický seznam zařízení v konfiguraci parametru. Přesuňte tento obsah do externího zdroje dat, který lze aktualizovat nezávisle.

Chcete-li přesunout obsah, Začněte přidáním nového webového koncového bodu. V podokně na levé straně přejdete do části **koncové body webu** . Tam přidejte nový webový koncový bod. Použijte následující konfiguraci.

| Nastavení | Navrhovaná hodnota |
|----|----|
| **Název** | `getDevices` |
| **Adresa URL** | `https://aka.ms/speech/cc-sampledevices` |
| **Metoda** | **Čtěte** |


Pokud navrhovaná hodnota pro adresu URL nefunguje, nakonfigurujte a hostovat webový koncový bod, který vrátí soubor JSON, který se skládá ze seznamu zařízení, která lze ovládat. Webový koncový bod by měl vrátit soubor JSON zformátovaný následujícím způsobem:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Potom přejdete na stránku nastavení parametru **SubjectDevice** . Nastavte následující vlastnosti.

| Nastavení | Navrhovaná hodnota |
| ----| ---- |
| **Konfigurace** | **Přijmout předdefinované vstupy z externího katalogu** |                               
| **Koncový bod katalogu** | `getDevices` |
| **Metoda** | **Čtěte** |

Pak vyberte **Uložit**.

> [!IMPORTANT]
> Možnost konfigurace parametru pro příjem vstupů z externího katalogu se nezobrazuje, pokud nemáte nastavený koncový **bod webu v** podokně na levé straně.

Vyzkoušejte si to tak, že vyberete **vlak**. Po dokončení školení vyberte **test** a vyzkoušejte pár interakcí.

* Vstup: *zapnout*
* Výstup: které zařízení chcete ovládat?
* Vstup: *světla*
* Výstup: OK, zapnutí světla

> [!NOTE]
> Teď můžete řídit všechna zařízení hostovaná na webovém koncovém bodu. Přesto ale potřebujete, aby aplikace provedla testování nových změn a pak znovu publikovala aplikaci.

### <a name="add-validation-to-parameters"></a>Přidání ověření do parametrů

*Ověření* jsou konstrukce, které se vztahují na určité typy parametrů, které umožňují konfigurovat omezení pro hodnotu parametru. Zobrazí výzvu k automatickým opravám, pokud hodnoty nespadají do omezení. Seznam typů parametrů, které přesahují konstrukci ověřování, najdete v tématu [Koncepty a definice vlastních příkazů](./custom-commands-references.md).

Ověření platnosti pomocí `SetTemperature` příkazu Pomocí následujících kroků přidejte ověření pro `Temperature` parametr.

1. V podokně na levé straně vyberte příkaz **SetTemperature** .
1. V prostředním podokně vyberte možnost **teplota**.
1. V pravém podokně vyberte **Přidat ověření**.
1. V okně **nové ověření** nakonfigurujte ověřování, jak je znázorněno v následující tabulce. Potom vyberte **Vytvořit**.


    | Konfigurace parametru | Navrhovaná hodnota | Popis |
    | ---- | ---- | ---- |
    | **Minimální hodnota** | `60` | Pro parametry Number minimální hodnota, kterou tento parametr může předpokládat |
    | **Maximální hodnota** | `80` | Pro číselné parametry maximální hodnoty, které tento parametr může předpokládat |
    | **Neúspěšná reakce** |  **Jednoduchý editor**  >  **První variace** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Výzva k zadání nové hodnoty v případě neúspěšného ověření |

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky ukazující, jak přidat ověřování rozsahu](media/custom-commands/add-validations-temperature.png)

Vyzkoušejte si to tak, že vyberete ikonu **výuka** v horní části podokna na pravé straně. Po dokončení školení vyberte **test**. Vyzkoušejte pár interakcí:

- Vstup: *nastavte teplotu na 72 stupňů* .
- Výstup: OK, nastavení teploty na 72 stupňů
- Vstup: *nastavte teplotu na 45 stupňů* .
- Výstup: je nám líto, ale dá se nastavit jenom teplota mezi 60 a 80 stupnemi.
- Vstup: *nastavte místo toho 72 stupňů* .
- Výstup: OK, nastavení teploty na 72 stupňů

## <a name="add-interaction-rules"></a>Přidání pravidel interakce

Pravidla interakce jsou *Další* pravidla, která zpracovávají konkrétní nebo složitou situaci. I když si nemůžete vytvářet vlastní pravidla interakce, v tomto příkladu použijete pravidla interakce pro následující scénáře:

* Potvrzení příkazů
* Přidání opravy s jedním krokem do příkazů

Další informace o pravidlech interakce najdete v tématu [Koncepty a definice vlastních příkazů](./custom-commands-references.md).

### <a name="add-confirmations-to-a-command"></a>Přidání potvrzení do příkazu

Chcete-li přidat potvrzení, použijte `SetTemperature` příkaz. Chcete-li dosáhnout potvrzení, vytvořte pravidla interakce pomocí následujících kroků:

1. V podokně na levé straně vyberte příkaz **SetTemperature** .
1. V prostředním podokně přidejte pravidla interakce výběrem možnosti **Přidat**. Pak vyberte příkaz **pravidla interakce**  >  **Potvrdit**.

    Tato akce přidá tři pravidla interakce. Pravidla požádají uživatele o potvrzení data a času alarmu. Pro další tah očekávají potvrzení (Ano nebo ne).

    1. Upravte pravidlo interakce **příkazu potvrdit** pomocí následující konfigurace:
        1. Změňte název a **potvrďte tak teplotu**.
        1. Přidat novou podmínku: teplota **požadovaných parametrů**  >  .
        1. Přidat novou akci: **typ**  >  **Odeslat odezvu na řeč** opravdu  >  **chcete nastavit teplotu jako {teploty} stupňů?**
        1. V části **očekávání** ponechte výchozí hodnotu **očekávaného potvrzení od uživatele**.
      
         > [!div class="mx-imgBorder"]
         > ![Snímek obrazovky ukazující, jak vytvořit požadovanou odpověď parametru](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Upravte pravidlo interakce **úspěšného potvrzení** , aby se zpracovalo úspěšné potvrzení (uživatel uvedli Ano).
      
          1. Změňte název na **potvrzenou teplotu úspěšně**.
          1. Ponechte stávající **potvrzení na úspěšné** splnění podmínky.
          1. Přidat novou podmínku: **Zadejte**  >  **teplotu požadovaných parametrů**  >  .
          1. Ponechte výchozí hodnotu **stav po spuštění** jako **pravidla dokončení**.

    1. Upravte pravidlo interakce **potvrzení zamítnutí** pro zpracování scénářů, pokud je potvrzení zamítnuto (uživatel neuvedli).

          1. Změňte název na **potvrzovací teplotu zamítnutý**.
          1. Nechejte stávající **potvrzení stavu zamítnutí** .
          1. Přidat novou podmínku: **Zadejte**  >  **teplotu požadovaných parametrů**  >  .
          1. Přidat novou akci: **typ**  >  **Odeslat odezvu na řeč**  >  **bez problému. Jakou teplotu pak?**.
          1. Změňte výchozí hodnotu **stavu po spuštění** , aby se **čekalo na vstup uživatele**.

> [!IMPORTANT]
> V tomto článku použijete vestavěnou možnost potvrzení. Můžete také ručně přidat pravidla interakce jednu po jedné.
   
Vyzkoušejte změny výběrem možnosti **výuka**. Po dokončení školení vyberte **test**.

- **Vstup**: *nastavte teplotu na 80 stupňů* .
- **Výstup**: jste si jisti, že chcete nastavit teplotu jako 80 stupňů?
- **Vstup**: *ne*
- **Výstup**: žádný problém. Jakou teplotu pak?
- **Vstup**: *72 stupňů*
- **Výstup**: jste si jisti, že chcete nastavit teplotu jako 72 stupňů?
- **Vstup**: *Ano*
- **Výstup**: OK, nastavení teploty na 72 stupňů

### <a name="implement-corrections-in-a-command"></a>Implementace oprav v příkazu

V této části nakonfigurujete opravu s jedním krokem. Tato oprava se používá po spuštění akce splnění. V případě, že se ještě příkaz nesplní, uvidíte také příklad, jak je oprava povolená ve výchozím nastavení. Chcete-li přidat opravu, když příkaz není dokončen, přidejte nový parametr `AlarmTone` .

V levém podokně vyberte příkaz **SetAlarm** . Pak přidejte nový parametr **AlarmTone**.
        
- **Jméno** > `AlarmTone`
- **Typ**  >  **Řetězec**
- **Výchozí hodnota**  >  **CHIMES**
- **Konfigurace**  >  **Přijměte předdefinované vstupní hodnoty z interního katalogu** .
- **Předdefinované vstupní hodnoty**  >  **CHIMES**, **Jingle** a **echo** (tyto hodnoty jsou jednotlivé předdefinované vstupy.)


Dále aktualizujte odpověď pro parametr **DateTime** na **připraveno k nastavení alarmu pomocí tónové hodnoty jako {AlarmTone}. Pro jakou dobu?**. Pak upravte pravidlo dokončení následujícím způsobem:

1. Vyberte stávající pravidlo pro dokončení **ConfirmationResponse**.
1. V pravém podokně najeďte myší na stávající akci a vyberte **Upravit**.
1. Aktualizujte odpověď na řeč na `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` .

> [!IMPORTANT]
> Výstražný tón se může změnit bez explicitní konfigurace v probíhajícím příkazu. Například se může změnit, když se příkaz ještě nedokončil. Oprava je *ve výchozím nastavení* povolená pro všechny parametry příkazu bez ohledu na číslo zapnutí, pokud je příkaz ještě vyplněný.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Implementace opravy po dokončení příkazu

Platforma Custom Commands umožňuje opravu jedním krokem i v případě, že byl příkaz dokončen. Tato funkce není ve výchozím nastavení povolená. Musí být explicitně nakonfigurovaný. 

Pomocí následujícího postupu můžete nakonfigurovat opravu s jedním krokem:

1. V příkazu **SetAlarm** přidejte do příkazového řádku pravidlo interakce s **příkazem aktualizovat předchozí** a aktualizujte dříve nastavené alarmy. Přejmenujte pravidlo interakce jako **aktualizace předchozí alarm**.
1. Nechejte výchozí podmínku: **předchozí příkaz se musí aktualizovat**.
1. Přidat novou podmínku: **Zadejte typ**  >  DateTime **povinný parametr**  >  .
1. Přidat novou akci: **typ**  >  **Odeslat hlas odpověď**  >  **Simple Editor**  >  **aktualizace předchozího času alarmu na {DateTime}**.
1. Ponechte výchozí hodnotu **stavu po spuštění** jako **příkaz dokončeno**.

Vyzkoušejte změny výběrem možnosti **výuka**. Počkejte na dokončení školení a pak vyberte **test**.

- **Vstup**: *nastavte alarm.*
- **Výstup**: připraveno k nastavení alarmu pomocí tónové volby jako Chimes. Pro jakou dobu?
- **Vstup**: *nastavte alarm se zvukem jako Jingle pro 9 – zítra.*
- **Výstup**: OK, sada alarmů pro 2020-05-21 09:00:00. Výstražný tón je Jingle.
- **Vstup**: *Ne, 8 dop.*
- **Výstup**: aktualizace předchozí doby alarmu na 2020-05-29 08:00.

> [!NOTE]
> V reálné aplikaci budete také muset v části **Akce** tohoto pravidla pro opravu odeslat zpět aktivitu klientovi nebo zavolat koncový bod HTTP, který aktualizuje čas alarmu v systému. Tato akce by měla být výhradně zodpovědná za aktualizaci času alarmu. Neměl by být zodpovědný za jakýkoli jiný atribut příkazu. V takovém případě by se měl jednat o výstražný tón tohoto atributu.

## <a name="add-language-generation-templates-for-speech-responses"></a>Přidání šablon pro generování jazyka pro odezvy řeči

Šablony jazykové generace (LG) umožňují přizpůsobit odpovědi odeslané klientovi. Zavádějí do odpovědí odchylky. Generování jazyka můžete dosáhnout pomocí:

* Šablony pro generování jazyka.
* Adaptivní výrazy.

Šablony vlastních příkazů jsou založené na [šablonách LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)architektury bot Framework. Vzhledem k tomu, že funkce Custom Commands vytvoří v případě potřeby novou šablonu LG (pro odezvy řeči v parametrech nebo akcích), nemusíte zadávat název šablony LG. 

Takže šablonu nemusíte definovat takto:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Místo toho můžete definovat text šablony bez názvu, například takto:

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s příkladem editoru šablon](./media/custom-commands/template-editor-example.png)


Tato změna zavádí změnu v odpovědích na řeč, které se odesílají klientovi. V případě utterance se v rámci nabízených možností náhodně vybírají odpovídající odpověď na řeč.

Když využijete výhod šablon LG, můžete také definovat složité odezvy řeči pro příkazy pomocí adaptivních výrazů. Další informace najdete v tématu [Formát šablon LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). 

Ve výchozím nastavení funkce Custom Commands podporuje všechny funkce s následujícími drobnými rozdíly:

* V šablonách LG se entity reprezentují jako `${entityName}` . Funkce Custom Commands nepoužívá entity. Můžete ale použít parametry jako proměnné buď pomocí reprezentace, `${parameterName}` nebo `{parameterName}` reprezentace.
* Funkce Custom Commands nepodporuje kompozici šablon a rozšíření, protože soubor *. LG* nikdy Neupravujte přímo. Můžete upravovat pouze odpovědi automaticky vytvořených šablon.
* Funkce Custom Commands nepodporuje vlastní funkce, které LG vkládání. Jsou podporovány předdefinované funkce.
* Funkce Custom Commands nepodporuje možnosti, jako například `strict` , `replaceNull` a `lineBreakStyle` .

### <a name="add-template-responses-to-a-turnonoff-command"></a>Přidání odpovědí šablony do příkazu TurnOnOff

Úpravou `TurnOnOff` příkazu přidejte nový parametr. Použijte následující konfiguraci.

| Nastavení            | Navrhovaná hodnota       | 
| ------------------ | --------------------- | 
| **Název**               | `SubjectContext`         | 
| **Je globální**          | Nevybrané             | 
| **Povinné**           | Nevybrané               | 
| **Typ**               | **Řetězec**                |
| **Výchozí hodnota**      | `all` |
| **Konfigurace**      | **Přijměte předdefinované vstupní hodnoty z interního katalogu.** | 
| **Předdefinované vstupní hodnoty** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Upravit pravidlo dokončení

Upravte oddíl **Actions (akce** ) stávajícího pravidla dokončení **ConfirmationResponse**. V okně **Upravit akci** přepněte na **Editor šablon**. Pak text nahraďte následujícím příkladem.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Proveďte výuku a otestování aplikace pomocí následujícího vstupu a výstupu. Všimněte si variace odpovědí. Variace je vytvořena více alternativami hodnoty šablony a také pomocí adaptivních výrazů.

* Vstup: *Zapnutí televizního vysílání*
* Výstup: OK, zapnutí televizního vysílání
* Vstup: *Zapnutí televizního vysílání*
* Výstup: Hotovo, zapnuto v televizi
* Vstup: vypnout *světla*
* Výstup: OK, vypnout všechna světla
* Vstup: vypnout *kvadranty místností*
* Výstup: OK, vypnutí místnostních indikátorů

## <a name="use-a-custom-voice"></a>Použití vlastního hlasu

Dalším způsobem přizpůsobení odpovědí vlastních příkazů je výběr výstupního hlasu. Pomocí následujícího postupu přepnete výchozí hlas na vlastní hlas:

1. V aplikaci Custom Commands klikněte v podokně na levé straně na **Nastavení**.
1. V prostředním podokně vyberte **vlastní hlas**.
1. V tabulce vyberte vlastní hlas nebo veřejný hlas.
1. Vyberte **Uložit**.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s ukázkovými větami a parametry](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> Pro veřejné hlasy jsou typy neuronové k dispozici pouze pro konkrétní oblasti. Další informace najdete v tématu [podporované oblasti služby Speech](./regions.md#standard-and-neural-voices).
>
> Vlastní hlasy můžete vytvořit na stránce **vlastní hlasový** projekt. Další informace najdete v tématu [Začínáme s vlastním hlasem](./how-to-custom-voice.md).

Aplikace teď bude ve vybraném hlasu reagovat místo na výchozí hlas.

## <a name="next-steps"></a>Další kroky

* Naučte se [integrovat vlastní aplikace s příkazy](how-to-custom-commands-setup-speech-sdk.md) pomocí klientské aplikace pomocí sady Speech SDK.
* [Nastavte průběžné nasazování](how-to-custom-commands-deploy-cicd.md) pro vlastní aplikace s příkazy pomocí Azure DevOps. 
                      
