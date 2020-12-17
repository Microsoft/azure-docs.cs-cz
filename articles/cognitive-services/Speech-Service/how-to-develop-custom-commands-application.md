---
title: 'Postupy: vývoj aplikací pro vlastní příkazy – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: V tomto postupu se naučíte vyvíjet a přizpůsobovat aplikace pro vlastní příkazy. Vlastní příkazy usnadňují vytváření bohatých aplikací pro hlasové zpracování hlasu optimalizovaných pro práci na prvním hlasu a jsou nejvhodnější pro práci s úlohami nebo pro scénáře příkazů a řízení, zejména pro zařízení Internet věcí (IoT), okolí a zařízení s podporou přeplňování.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 98c0459e0b67102458169147b1d39e98e2b3e2b1
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632748"
---
# <a name="develop-custom-commands-applications"></a>Vývoj aplikací pro vlastní příkazy

V tomto postupu se naučíte vyvíjet a konfigurovat aplikace s vlastními příkazy. Vlastní příkazy usnadňují vytváření bohatých aplikací pro hlasové zpracování hlasu optimalizovaných pro práci na prvním hlasu a jsou nejvhodnější pro práci s úlohami nebo pro scénáře příkazů a řízení, zejména pro zařízení Internet věcí (IoT), okolí a zařízení s podporou přeplňování.

V tomto článku vytvoříte aplikaci, která může zapnout a vypnout televizní vysílání, nastavit teplotu a nastavit alarm. Po vytvoření těchto základních příkazů jsou pokryty následující možnosti přizpůsobení příkazů:

* Přidávání parametrů do příkazů
* Přidání konfigurací do parametrů příkazu
* Sestavování pravidel interakce
* Vytváření šablon generování jazyka pro odezvy řeči
* Používání vlastního hlasu 

## <a name="create-application-with-simple-commands"></a>Vytvoření aplikace s jednoduchými příkazy

Nejdřív Začněte vytvořením prázdné vlastní aplikace s příkazy. Podrobnosti najdete v [rychlém](quickstart-custom-commands-application.md)startu. Tentokrát namísto importu projektu vytvoříte prázdný projekt.

1. Do pole **název** zadejte název projektu jako `Smart-Room-Lite` (nebo něco jiného podle vašeho výběru).
1. V seznamu **jazyk** vyberte možnost **Angličtina (USA)**.
1. Vyberte nebo vytvořte prostředek LUIS podle vašeho výběru.

   > [!div class="mx-imgBorder"]
   > ![Vytvoření projektu](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Aktualizace prostředků LUIS (volitelné)

Můžete aktualizovat prostředek pro vytváření obsahu, který jste vybrali v okně **Nový projekt** , a nastavit předpověď prostředku. Prostředek předpovědi se používá pro rozpoznávání při publikování vlastní aplikace příkazů. Během fází vývoje a testování nepotřebujete předpověď prostředků.

### <a name="add-turnon-command"></a>Přidat příkaz TurnOn

V prázdné aplikaci pro vlastní příkazy **inteligentních místností – Lite** , kterou jste právě vytvořili, přidejte jednoduchý příkaz, který zpracuje utterance `turn on the tv` a odpoví zprávou `Ok, turning the tv on` .

1. Vytvořte nový příkaz výběrem **příkazu nový** v horní části levého podokna. Otevře se **nové okno příkazu** .
1. Zadejte hodnotu pole **název** jako **TurnOn**.
1. Vyberte **Vytvořit**.

V prostředním podokně jsou uvedeny různé vlastnosti příkazu. Nakonfigurujete následující vlastnosti příkazu. Vysvětlení všech vlastností konfigurace příkazu naleznete v [odkazech](./custom-commands-references.md).

| Konfigurace            | Popis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Příklady vět** | Příklad projevy, jak může uživatel aktivovat tento příkaz                                                                 |
| **Parametry**       | Informace potřebné k dokončení příkazu                                                                                |
| **Pravidla dokončení** | Akce, které mají být provedeny pro splnění příkazu. Například, pokud chcete reagovat na uživatele nebo komunikovat s jinou webovou službou. |
| **Pravidla interakce**   | Další pravidla pro zpracování složitějších nebo složitých situací                                                              |


> [!div class="mx-imgBorder"]
> ![Vytvoření příkazu](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Přidat ukázkové věty

Pojďme začít s **příkladem části věty** a zadat příklad, co může uživatel vyslovit.

1. V prostředním podokně vyberte část se **vzorovými větami** .
1. V pravém krajním podokně přidejte příklady:

    ```
    turn on the tv
    ```

1.  V horní části podokna vyberte **Uložit** .

Teď ale nemusíme mít parametry, takže se můžeme přesunout do oddílu **pravidla dokončení** .

#### <a name="add-a-completion-rule"></a>Přidat pravidlo dokončení

V dalším kroku musí mít příkaz pravidlo dokončení. Toto pravidlo přikáže uživateli, že je prováděna akce plnění. Chcete-li si přečíst další informace o pravidlech a pravidlech dokončení, přejít na [odkazy](./custom-commands-references.md).

1. Vyberte **výchozí pravidlo dokončení** a upravte ho následujícím způsobem:

    
    | Nastavení    | Navrhovaná hodnota                          | Popis                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Název**       | ConfirmationResponse                  | Název popisující účel pravidla          |
    | **Podmínky** | Žádné                                     | Podmínky, které určují, kdy se pravidlo dá spustit    |
    | **Akce**    | Odeslat odezvu na řeč > jednoduchý editor > první variaci > `Ok, turning the tv on` | Akce, která se má provést, když je podmínka pravidla pravdivá |

   > [!div class="mx-imgBorder"]
   > ![Vytvoření odpovědi na řeč](media/custom-commands/create-speech-response-action.png)

1. Akci uložte kliknutím na **Uložit** .
1. Zpátky v části **pravidla dokončování** vyberte **Uložit** a uložte všechny změny. 

    > [!NOTE]
    > Není nutné používat výchozí pravidlo dokončení, které je součástí příkazu. V případě potřeby můžete stávající výchozí pravidlo dokončování odstranit a přidat vlastní pravidlo.

### <a name="add-settemperature-command"></a>Přidat příkaz SetTemperature

Teď přidejte ještě jeden **SetTemperature** příkazu, který bude trvat jeden utterance, `set the temperature to 40 degrees` a odpovědět se zprávou `Ok, setting temperature to 40 degrees` .

Použijte postup, jak je znázorněno v příkazu **TurnOn** k vytvoření nového příkazu pomocí ukázkové věty "**nastavení teploty na 40 stupňů**".

Pak upravte existující pravidla dokončení **dokončeno** následujícím způsobem:

| Nastavení    | Navrhovaná hodnota                          |
| ---------- | ---------------------------------------- |
| Název  | ConfirmationResponse                  |
| Podmínky | Žádné                                     |
| Actions    | Odeslat odezvu na řeč > jednoduchý editor > první variaci > `Ok, setting temperature to 40 degrees` |

Výběrem **Uložit** uložte všechny změny do příkazu.

### <a name="add-setalarm-command"></a>Přidat příkaz SetAlarm

Vytvořte nový příkaz **SetAlarm** pomocí ukázkové věty "**Nastavení alarmu pro 9 – zítra**". Pak upravte existující pravidla dokončení **dokončeno** následujícím způsobem:

| Nastavení    | Navrhovaná hodnota                          |
| ---------- | ---------------------------------------- |
| Název pravidla  | ConfirmationResponse                  |
| Podmínky | Žádné                                     |
| Actions    | Odeslat odezvu na řeč > jednoduchý editor > první variaci >`Ok, setting an alarm for 9 am tomorrow` |

Výběrem **Uložit** uložte všechny změny do příkazu.

### <a name="try-it-out"></a>Vyzkoušet

Otestujte chování pomocí panelu chat testu. Vyberte ikonu **výuky** v pravém horním rohu okna. Po dokončení školení vyberte **test**. Vyzkoušejte následující utterance příklady prostřednictvím hlasu nebo textu:

- Zadáte: Nastavte teplotu na 40 stupňů.
- Očekávaná odpověď: OK, nastavení teploty na 40 stupňů
- Zadáte: zapnout televizor
- Očekávaná odpověď: OK, zapnutí televizního vysílání
- Zadáte: nastavte alarm pro 9 – zítra
- Očekávaná odpověď: OK, nastavení alarmu pro 9 – zítra

> [!div class="mx-imgBorder"]
> ![Testování pomocí webového chatu](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> Na panelu Test můžete vybrat možnost **zapnout podrobnosti** pro informace o tom, jakým způsobem byl zpracován Tento hlasový/textový vstup.

## <a name="add-parameters-to-commands"></a>Přidání parametrů do příkazů

V této části se dozvíte, jak do příkazů přidat parametry. Parametry jsou informace vyžadované příkazy k dokončení úkolu. Ve složitých scénářích lze také pomocí parametrů definovat podmínky, které aktivují vlastní akce.

### <a name="configure-parameters-for-turnon-command"></a>Konfigurace parametrů pro příkaz TurnOn

Začněte úpravou stávajícího příkazu **TurnOn** , abyste mohli zapnout a vypnout víc zařízení.

1. Teď, když příkaz bude nyní zpracovávat jak scénáře, tak i vypnutí, přejmenujte příkaz na **TurnOnOff**.
   1. V levém podokně vyberte příkaz **TurnOn** a potom v horní části podokna vyberte tlačítko se třemi tečkami (...) vedle **příkazu New** .
   
   1. Vyberte **Přejmenovat**. V oknech **příkazu přejmenovat** změňte **název** na **TurnOnOff**.

1. V dalším kroku přidáte do tohoto příkazu nový parametr, který představuje, jestli chce uživatel zařízení zapnout nebo vypnout.
   1. Vyberte  **Přidat** k dispozici v horní části prostředního podokna. V rozevíracím seznamu vyberte možnost **parametr**.
   1. V pravém podokně v části **parametry** přidejte do pole **název** hodnotu **(OnOff)**.
   1. Vyberte možnost **požadováno**. V okně **Přidat odpověď pro požadovaný parametr** vyberte **jednoduchý editor**. V **první variaci** přidejte
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
       | Povinné           | checked         | Zaškrtávací políčko, které určuje, zda je před dokončením příkazu nutné zadat hodnotu pro tento parametr |
       | Odpověď na povinný parametr      |Jednoduchý editor > `On or Off?`      | Výzva k zadání hodnoty tohoto parametru, pokud není známa |
       | Typ               | Řetězec          | Typ parametru, jako je číslo, řetězec, datum a čas nebo zeměpis   |
       | Konfigurace      | Přijměte předdefinované vstupní hodnoty z interního katalogu. | V případě řetězců to omezuje vstupy na sadu možných hodnot. |
       | Předdefinované vstupní hodnoty     | `on`, `off`           | Sada možných hodnot a jejich aliasů         |
       
        
   1. Pokud chcete přidat předdefinované vstupní hodnoty, vyberte **přidat předdefinované zadání** a v okně **Nová položka**  zadejte **název** , jak je uvedeno v tabulce výše. V tomto případě nepoužíváme aliasy, takže je můžete nechat prázdné.
   
      > [!div class="mx-imgBorder"]
      > ![Vytvořit parametr](media/custom-commands/create-on-off-parameter.png)

   1. Vyberte **Uložit** a uložte všechny konfigurace parametru.
 
#### <a name="add-subjectdevice-parameter"></a>Přidat parametr SubjectDevice 

1. Potom vyberte **Přidat** znovu a přidejte druhý parametr, který bude představovat název zařízení, které lze ovládat pomocí tohoto příkazu. Použijte následující konfiguraci.


    | Nastavení            | Navrhovaná hodnota       |
    | ------------------ | --------------------- |
    | Název               | `SubjectDevice`         |
    | Je globální          | unchecked             |
    | Povinné           | checked               |
    | Odpověď na povinný parametr     | Jednoduchý editor > `Which device do you want to control?`    | 
    | Typ               | Řetězec                |          |
    | Konfigurace      | Přijměte předdefinované vstupní hodnoty z interního katalogu. | 
    | Předdefinované vstupní hodnoty | `tv`, `fan`               |
    | Aliasy ( `tv` )      | `television`, `telly`     |

1. Vyberte **Uložit**.

#### <a name="modify-example-sentences"></a>Upravit ukázkové věty

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

#### <a name="modify-completion-rules-to-include-parameters"></a>Upravit pravidla dokončení pro zahrnutí parametrů

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

Vyzkoušejte změny tím, že vyberete ikonu **výuka** v pravém podokně. Po dokončení školení vyberte **test**. Zobrazí se okno **test aplikace** . Vyzkoušejte následující interakce.

- Vstup: vypnutí televizního vysílání
- Výstup: OK, vypnutí televizního vysílání
- Vstup: vypnutí televizoru
- Výstup: OK, vypnutí televizního vysílání
- Vstup: vypnout
- Výstup: které zařízení chcete ovládat?
- Vstup: TV
- Výstup: OK, vypnutí televizního vysílání

### <a name="configure-parameters-for-settemperature-command"></a>Konfigurace parametrů pro příkaz SetTemperature

Upravte příkaz **SetTemperature** tak, aby umožňoval nastavit teplotu podle pokynů uživatele.

Přidat novou **teplotu** parametrů s následující konfigurací

| Konfigurace      | Navrhovaná hodnota     |
| ------------------ | ----------------|
| Název               | `Temperature`           |
| Povinné           | checked         |
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
| Actions           | Odeslat > odpovědi na řeč `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-to-the-setalarm-command"></a>Konfigurace parametrů pro příkaz SetAlarm

Přidejte parametr s názvem **DateTime** s následující konfigurací.

   | Nastavení                           | Navrhovaná hodnota                     | 
   | --------------------------------- | ----------------------------------------|
   | Název                              | `DateTime`                               |
   | Povinné                          | checked                                 |
   | Odpověď na povinný parametr   | Jednoduchý editor > `For what time?`            | 
   | Typ                              | Datum a čas                                |
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
   | Actions    | Poslat odezvu na řeč – `Ok, alarm set for {DateTime}`  |

Otestujte všechny tři příkazy společně pomocí projevy souvisejících s různými příkazy. Všimněte si, že můžete přepínat mezi různými příkazy.

- Vstup: Nastavení alarmu
- Výstup: pro který čas?
- Vstup: zapnutí televizního vysílání
- Výstup: OK, zapnutí televizního vysílání
- Vstup: Nastavení alarmu
- Výstup: pro který čas?
- Vstup: 17:00
- Výstup: OK, sada alarmů pro 2020-05-01 17:00:00

## <a name="add-configurations-to-commands-parameters"></a>Přidání konfigurací do parametrů příkazů

V této části se dozvíte víc o rozšířené konfiguraci parametrů, včetně:

 - Jak hodnoty parametrů můžou patřit do množiny definované externě pro aplikace Custom Commands
 - Postup přidání klauzulí ověřování pro hodnotu parametrů

### <a name="configure-parameter-as-external-catalog-entity"></a>Konfigurovat parametr jako entitu externího katalogu

Vlastní příkazy umožňují konfigurovat parametry typu String, aby odkazovaly na externí katalogy hostované přes webový koncový bod. Díky tomu můžete externí katalog aktualizovat nezávisle, aniž byste museli provádět úpravy aplikace Custom Commands. Tento přístup je užitečný v případech, kdy položky katalogu můžou být velké v čísle.

Znovu použijte parametr **SubjectDevice** z příkazu **TurnOnOff** . Aktuální konfigurace tohoto parametru **přijímá předdefinované vstupy z interního katalogu**. To odkazuje na statický seznam zařízení, jak je definováno v konfiguraci parametru. Chceme přesunout obsah tohoto obsahu do externího zdroje dat, který se dá aktualizovat nezávisle.

Pokud to chcete provést, Začněte přidáním nového webového koncového bodu. V levém podokně v části přejít na **koncové body webu** a přidejte nový webový koncový bod s následující konfigurací.

| Nastavení | Navrhovaná hodnota |
|----|----|
| Název | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Metoda | GET |


Pokud navrhovaná hodnota pro adresu URL nefunguje, je nutné nakonfigurovat a hostovat jednoduchý koncový bod webu, který vrátí kód JSON sestávající ze seznamu zařízení, která lze ovládat. Webový koncový bod by měl vracet formát JSON následujícím způsobem:
    
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

Potom přejděte na stránku nastavení parametrů **SubjectDevice** a změňte vlastnosti na následující.

| Nastavení | Navrhovaná hodnota |
| ----| ---- |
| Konfigurace | Přijmout předdefinované vstupy z externího katalogu |                               
| Koncový bod katalogu | getzařízení |
| Metoda | GET |

Pak vyberte **Uložit**.

> [!IMPORTANT]
> Možnost konfigurace parametru pro příjem vstupů z externího katalogu se nezobrazuje, pokud v levém podokně není nastaven koncový bod webu v části **koncový bod webu** .

Vyzkoušejte si ji výběrem možnosti **výuka** a počkejte na dokončení školení. Po dokončení školení vyberte **test** a vyzkoušejte pár interakcí.

* Vstup: zapnout
* Výstup: které zařízení chcete ovládat?
* Vstup: světla
* Výstup: OK, zapnutí světla

> [!NOTE]
> Všimněte si, jak můžete teď řídit všechna zařízení hostovaná na webovém koncovém bodu. Pro otestování nových změn a opětovném publikování aplikace je stále nutné vyškolit aplikaci.

### <a name="add-validation-to-parameters"></a>Přidání ověření do parametrů

**Ověření** jsou určena pro určité typy parametrů, které vám umožní nakonfigurovat omezení hodnoty parametru a zobrazit výzvu k opravě, pokud hodnoty nespadají do omezení. Úplný seznam typů parametrů, které rozšiřují konstrukci ověřování, najdete v [odkazech](./custom-commands-references.md) .

Otestujte ověřování pomocí příkazu **SetTemperature** . Pomocí následujících kroků přidejte ověření pro parametr **teploty** .

1. V levém podokně vyberte příkaz **SetTemperature** .
1. V prostředním podokně vyberte možnost  **teplota** .
1. V pravém podokně vyberte **Přidat ověření** .
1. V okně **nové ověření** nakonfigurujte ověřování následujícím způsobem a vyberte **vytvořit**.


    | Konfigurace parametru | Navrhovaná hodnota | Popis |
    | ---- | ---- | ---- |
    | Min. hodnota | `60` | Pro parametry Number minimální hodnota, kterou tento parametr může předpokládat |
    | Max. hodnota | `80` | Pro číselné parametry maximální hodnoty, které tento parametr může předpokládat |
    | Neúspěšná reakce |  Jednoduchý editor > první variace > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Výzva k zadání nové hodnoty, pokud se ověření nepovede |

    > [!div class="mx-imgBorder"]
    > ![Přidat ověření rozsahu](media/custom-commands/add-validations-temperature.png)

Vyzkoušejte si to tak, že vyberete ikonu **výuka** , která se nachází nahoře v pravém podokně. Po dokončení školení vyberte **test** a vyzkoušejte pár interakcí:

- Vstup: Nastavte teplotu na 72 stupňů.
- Výstup: OK, nastavení teploty na 72 stupňů
- Vstup: Nastavte teplotu na 45 stupňů.
- Výstup: je nám líto, ale dá se nastavit jenom teplota mezi 60 a 80 stupnemi.
- Vstup: nastavte místo toho 72 stupňů.
- Výstup: OK, nastavení teploty na 72 stupňů

## <a name="add-interaction-rules"></a>Přidání pravidel interakce

Pravidla interakce jsou *Další pravidla* pro zpracování konkrétních nebo složitých situací. I když si nemůžete vytvářet vlastní pravidla interakce, v tomto příkladu můžete použít pravidla interakce pro následující cílené scénáře:

* Potvrzení příkazů
* Přidání opravy s jedním krokem do příkazů

Další informace o pravidlech interakce najdete v části [odkazy](./custom-commands-references.md) .

### <a name="add-confirmations-to-a-command"></a>Přidání potvrzení do příkazu

Chcete-li přidat potvrzení, použijte příkaz **SetTemperature** . Chcete-li dosáhnout potvrzení, vytvořte pravidla interakce pomocí následujících kroků.

1. V levém podokně vyberte příkaz **SetTemperature** .
1. Přidejte pravidla interakce výběrem možnosti **Přidat** v prostředním podokně. Pak vyberte příkaz **pravidla interakce**  >  **Potvrdit**.

    Tato akce přidá tři pravidla interakce, která uživateli požádá o potvrzení data a času alarmu a očekává u dalšího tahu potvrzení (ano/ne).

    1. Upravte pravidlo interakce **příkazu potvrdit** jako pro následující konfiguraci:
        1. Pro **potvrzení teploty** změňte **název** .
        1. Přidejte novou podmínku jako **požadovanou**  >  **teplotu** parametrů.
        1. Přidat novou akci jako **typ**  >  **Odeslat odpověď na řeč** opravdu  >  **chcete nastavit teplotu jako {0} stupňů?**
        1. V části **očekávání** ponechte výchozí hodnotu **očekávaného potvrzení od uživatele** .
      
         > [!div class="mx-imgBorder"]
         > ![Vytvořit požadovanou odpověď parametru](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Upravte pravidlo interakce **úspěšného potvrzení** , aby se zpracovalo úspěšné potvrzení (uživatel uvedli Ano).
      
          1. Úprava **názvu** na **potvrzenou teplotu byla úspěšná**.
          1. Nechejte již existující **potvrzení o úspěšném** dokončení.
          1. Přidejte novou podmínku jako **typ**  >  **teplotní parametry Required**  >  .
          1. Ponechte výchozí hodnotu **stav po spuštění** jako **pravidla dokončení provedení**.

    1. Upravte pravidlo interakce **potvrzení zamítnutí** pro zpracování scénářů, pokud je potvrzení zamítnuto (uživatel neuvedli).

          1. Změna **názvu** na **potvrzovací teplotu byla zamítnuta**.
          1. Ponechejte podmínku pro již existující **potvrzení bylo odepřeno** .
          1. Přidejte novou podmínku jako **typ**  >  **teplotní parametry Required**  >  .
          1. Přidejte novou akci jako **typ**  >  **Odeslat odezvu na řeč**  >  **bez problému. Jakou teplotu pak?**
          1. Ponechte výchozí hodnotu **stavu po spuštění** jako **čekání na vstup uživatele**.

> [!IMPORTANT]
> V tomto článku jste použili vestavěnou možnost potvrzení. Můžete také ručně přidat pravidla interakce jednu po jedné.
   
Vyzkoušejte změny výběrem možnosti **výuka**, počkat na dokončení školení a vyberte **test**.

- **Vstup**: Nastavte teplotu na 80 stupňů.
- **Výstup**: jste si jisti, že chcete nastavit teplotu jako 80 stupňů?
- **Vstup**: ne
- **Výstup**: žádný problém. Jakou teplotu pak?
- **Vstup**: 72 stupňů
- **Výstup**: jste si jisti, že chcete nastavit teplotu jako 72 stupňů?
- **Vstup**: Ano
- **Výstup**: OK, nastavení teploty na 83 stupňů

### <a name="implement-corrections-in-a-command"></a>Implementace oprav v příkazu

V této části nakonfigurujete jednorázovou opravu, která se používá poté, co již byla provedena akce plnění. V případě, že se ještě příkaz nesplní, uvidíte také příklad, jak je oprava povolená ve výchozím nastavení. Pokud chcete přidat opravu, když se příkaz nedokončí, přidejte nový parametr **AlarmTone**.

V levém podokně vyberte příkaz **SetAlarm** a přidejte nový parametr **AlarmTone**.
        
- **Název**  >  **AlarmTone**
- **Typ**  >  **Řetězec**
- **Výchozí hodnota**  >  **CHIMES**
- **Konfigurace**  >  **Přijměte předdefinované vstupní hodnoty z interního katalogu** .
- **Předdefinované vstupní hodnoty**  >  **CHIMES**, **Jingle** a **echo** jako jednotlivé předdefinované vstupy


Dále aktualizujte odpověď pro parametr **DateTime** na **připraveno k nastavení alarmu pomocí tónové hodnoty jako {AlarmTone}. Pro jakou dobu?**. Pak upravte pravidlo dokončení následujícím způsobem:

1. Vyberte stávající pravidlo pro dokončení **ConfirmationResponse**.
1. V pravém podokně umístěte ukazatel myši na stávající akci a vyberte **Upravit**.
1. Aktualizujte reakci na řeč na **OK a nastavte alarm pro {DateTime}. Výstražný tón je {AlarmTone}.**

> [!IMPORTANT]
> Výstražný tón lze změnit bez explicitní konfigurace v probíhajícím příkazu, například v případě, že příkaz ještě nebyl dokončen. *Oprava je ve výchozím nastavení povolená pro všechny parametry příkazu bez ohledu na to, jestli je příkaz ještě vyplněný.*

#### <a name="correction-when-command-is-completed"></a>Oprava po dokončení příkazu

Vlastní platforma příkazy také nabízí možnost opravy v rámci jednoho kroku i v případě, že byl příkaz dokončen. Tato funkce není ve výchozím nastavení povolená. Musí být explicitně nakonfigurovaný. Pomocí následujícího postupu můžete nakonfigurovat opravu s jedním krokem.

1. V příkazu **SetAlarm** přidejte do příkazového řádku pravidlo interakce s **příkazem aktualizovat předchozí** a aktualizujte dříve nastavené alarmy. Chcete-li **aktualizovat předchozí alarm**, přejmenujte výchozí **název** pravidla interakce.
1. Nechte výchozí podmínku **předchozí příkaz se musí aktualizovat** tak, jak je.
1. Přidejte novou podmínku jako **typ**  >  **parametru Required**  >  **DateTime**.
1. Přidejte novou akci jako **typ**  >  **Odeslat**.  >  **jednoduchý editor** odpovědi  >  **na řeč aktualizuje předchozí čas alarmu na {DateTime}.**
1. Ponechte výchozí hodnotu **stav po spuštění** jako **příkaz dokončeno**.

Vyzkoušejte změny výběrem možnosti **výuka**, počkat na dokončení školení a vyberte **test**.

- **Vstup**: nastavte alarm.
- **Výstup**: připraveno k nastavení alarmu pomocí tónové volby jako Chimes. Pro jakou dobu?
- **Vstup**: nastavte alarm se zvukem jako Jingle pro 9 – zítra.
- **Výstup**: OK, sada alarmů pro 2020-05-21 09:00:00. Výstražný tón je Jingle.
- **Vstup**: Ne, 8 dop.
- **Výstup**: aktualizace předchozí doby alarmu na 2020-05-29 08:00.

> [!NOTE]
> V reálné aplikaci budete také muset v části **Akce** tohoto pravidla pro opravu odeslat zpět aktivitu klientovi nebo zavolat koncový bod HTTP, který aktualizuje čas alarmu v systému. Tato akce by měla být výhradně odpovědná za aktualizaci doby alarmu a nikoli jakýchkoli dalších atributů příkazu. V tomto případě by to byl výstražný tón.

## <a name="add-language-generation-templates-for-speech-responses"></a>Přidání šablon generování jazyka pro hlasové odpovědi

Šablony generování jazyka umožňují přizpůsobit odpovědi odeslané klientovi a zavádět odchylky v odpovědích. Přizpůsobení generování jazyka lze dosáhnout:

* Použití šablon generování jazyka
* Použití adaptivních výrazů

Šablony vlastních příkazů jsou založené na [šablonách LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)pro BotFramework. Vzhledem k tomu, že vlastní příkazy v případě potřeby vytvoří novou šablonu LG (to znamená pro odezvy řeči v parametrech nebo akcích), nemusíte zadávat název šablony LG. Takže místo definování šablony jako:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Stačí definovat pouze tělo šablony bez názvu, a to následujícím způsobem.

> [!div class="mx-imgBorder"]
> ![Příklad editoru šablon](./media/custom-commands/template-editor-example.png)


Tato změna zavádí variaci odpovědí na převod řeči odesílaných klientovi. Pro stejné utterance by tedy bylo možné odpovídající odpověď na řeč náhodně vyskladnit z poskytnutých možností.

Využití šablon LG vám také umožňuje definovat složité odezvy řeči pro příkazy pomocí adaptivních výrazů. Další podrobnosti najdete ve [formátu šablon LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) . Vlastní příkazy ve výchozím nastavení podporují všechny možnosti s následujícími drobnými rozdíly:

* Entity šablon LG jsou reprezentovány jako $ {EntityName}. Ve vlastních příkazech nepoužíváme entity, ale parametry se dají použít jako proměnné s jedním z těchto reprezentacemi $ {parameterName} nebo {parameterName}.
* Složení a rozšíření šablon nejsou ve vlastních příkazech podporovány. Důvodem je to, že soubor nikdy neupravujte `.lg` přímo, ale pouze odpovědi na automaticky vytvořené šablony.
* Vlastní funkce, které jsou vložené pomocí LG, se ve vlastních příkazech nepodporují. Předdefinované funkce jsou pořád podporovány.
* Možnosti (Strict, replaceNull & lineBreakStyle) se ve vlastních příkazech nepodporují.

### <a name="add-template-responses-to-turnonoff-command"></a>Přidat odpovědi šablony do příkazu TurnOnOff

Úpravou příkazu **TurnOnOff** přidejte nový parametr s následující konfigurací:

| Nastavení            | Navrhovaná hodnota       | 
| ------------------ | --------------------- | 
| Název               | `SubjectContext`         | 
| Je globální          | unchecked             | 
| Povinné           | unchecked               | 
| Typ               | Řetězec                |
| Výchozí hodnota      | `all` |
| Konfigurace      | Přijměte předdefinované vstupní hodnoty z interního katalogu. | 
| Předdefinované vstupní hodnoty | `room`, `bathroom`, `all`|

#### <a name="modify-completion-rule"></a>Upravit pravidlo dokončení

Upravte oddíl **Actions** pro stávající pravidlo dokončení **ConfirmationResponse**. V automaticky otevíraném okně **Upravit akci** přepněte na **Editor šablon** a nahraďte text následujícím příkladem.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Proveďte **analýzu** a **testování** aplikace následujícím způsobem. Všimněte si proměnlivosti odezvy z důvodu použití více alternativ hodnoty šablony a také použití adaptivních výrazů.

* Vstup: zapnutí televizního vysílání
* Výstup: OK, zapnutí televizního vysílání
* Vstup: zapnutí televizního vysílání
* Výstup: Hotovo, zapnuto v televizi
* Vstup: vypnout světla
* Výstup: OK, vypnout všechna světla
* Vstup: vypnout kvadranty místností
* Výstup: OK, vypnutí místnostních indikátorů

## <a name="use-custom-voice"></a>Použití sady Vlastní hlas

Dalším způsobem přizpůsobení odpovědí vlastních příkazů je výběr vlastního výstupního hlasu. Pomocí následujícího postupu přepnete výchozí hlas na vlastní hlas.

1. V aplikaci Custom Commands (vlastní příkazy) v levém podokně vyberte **Nastavení** .
1. V prostředním podokně vyberte **vlastní hlas** .
1. V tabulce vyberte požadovaný vlastní nebo veřejný hlas.
1. Vyberte **Uložit**.

> [!div class="mx-imgBorder"]
> ![Ukázky vět s parametry](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Pro **veřejné hlasy** jsou **typy neuronové** dostupné jenom pro konkrétní oblasti. Pokud chcete ověřit dostupnost, přečtěte si téma [standardní a neuronové hlasy podle oblasti nebo koncového bodu](./regions.md#standard-and-neural-voices).
> - **Vlastní hlasy** si můžete vytvořit na stránce vlastní hlasového projektu. Viz Začínáme [s vlastním hlasem](./how-to-custom-voice.md).

Aplikace teď bude ve vybraném hlasu reagovat místo na výchozí hlas.

## <a name="next-steps"></a>Další kroky

* Naučte se [integrovat vlastní aplikace s příkazy](how-to-custom-commands-setup-speech-sdk.md) pomocí klientské aplikace pomocí sady Speech SDK.
* [Nastavte průběžné nasazování](how-to-custom-commands-deploy-cicd.md) pro vlastní aplikace s příkazy pomocí Azure DevOps. 
                      