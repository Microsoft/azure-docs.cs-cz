---
title: 'Kurz: Monitorování prostoru pomocí služby Azure Digital Twins | Microsoft Docs'
description: Zjistěte, jak zřizovat prostředky prostorových a sledování pracovních podmínek s Dvojčaty digitální Azure pomocí kroků v tomto kurzu.
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: dkshir
ms.openlocfilehash: 465dd2a69ad42b8b6a88268eb35a1aa7d8d922c5
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229392"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>Kurz: Zřízení sestavení a sledování práce pomocí digitální dvojče Azure

Tento kurz ukazuje, jak pomocí služby Azure Digital Twins monitorovat požadované teplotní podmínky a úroveň komfortu v prostorech. Poté co [konfigurace budovy ukázka](tutorial-facilities-setup.md), můžete zajistit vaše sestavení a spuštění vlastní funkce pro vaše data ze senzorů pomocí kroků v tomto kurzu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Definujte podmínky, které chcete monitorovat.
> * Vytvoření uživatelem definované funkce (UDF).
> * Simulace data ze senzorů.
> * Získání výsledků uživatelsky definované funkce.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že máte [dokončení nastavení Azure digitální dvojče](tutorial-facilities-setup.md). Než budete pokračovat, ujistěte se, že máte následující:

- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Spuštěná instance služby Digital Twins. 
- Pracovní počítač se staženými a extrahovanými [ukázkami služby Digital Twins v jazyce C#](https://github.com/Azure-Samples/digital-twins-samples-csharp). 
- [Sada .NET core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) na vývojovém počítači sestavení a spuštění ukázky. Spustit `dotnet --version` ověření nainstalovanou správnou verzi. 
- [Visual Studio Code](https://code.visualstudio.com/) pro zkoumání vzorového kódu. 

## <a name="define-conditions-to-monitor"></a>Definice podmínek, které se mají monitorovat

Můžete definovat sadu konkrétní podmínky pro monitorování v datech ze senzorů nebo zařízení volá *procesy pro hledání shody*. Potom můžete definovat funkce volané *uživatelem definované funkce*. Uživatelem definované funkce spustit vlastní logiku na data, která pochází z mezery a zařízení, když dojde k podmínky určené procesy pro hledání shody. Další informace najdete v článku [zpracování dat a uživatelem definovaných funkcí](concepts-user-defined-functions.md). 

Z **obsazení quickstart** ukázkový projekt, otevřete soubor **src\actions\provisionSample.yaml** ve Visual Studio Code. Všimněte si části, která začíná typem **matchers** (Pravidla shody). Každá položka v rámci tohoto typu se zadaným vytvoří předávaný **název**. Předávaný bude monitorovat senzoru typu **dataTypeValue**. Všimněte si, jak souvisí s prostor s názvem *fokus místnosti A1*, který má **zařízení** uzel, který obsahuje pár senzory. Ke zřízení předávaný, který bude sledovat jednu z těchto senzorů, ujistěte se, že jeho **dataTypeValue** odpovídá senzoru **datový typ**. 

Přidejte následující předávaný pod stávající procesy pro hledání shody. Ujistěte se, že jsou zarovnány klíče a mezery nejsou nahrazeny karty.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Tato předávaný bude sledovat SAMPLE_SENSOR_TEMPERATURE senzor, který jste přidali v [první kurz](tutorial-facilities-setup.md). Tyto řádky jsou taky k dispozici v *provisionSample.yaml* souboru jako komentovaná řádky. Můžete je Odkomentujte tak, že odeberete `#` před každý řádek znak.

<a id="udf"></a>

## <a name="create-a-user-defined-function"></a>Vytvoření uživatelem definované funkce

Uživatelem definované funkce můžete použít k přizpůsobení zpracování dat snímače. Jsou to vlastní kód jazyka JavaScript, který můžete spustit v rámci vaší instance Azure digitální dvojče při splnění konkrétních podmínek jak je popsáno procesy pro hledání shody. Můžete vytvořit procesy pro hledání shody a uživatelem definované funkce pro každou senzor, který chcete monitorovat. Další informace najdete v článku [zpracování dat a uživatelem definovaných funkcí](concepts-user-defined-functions.md). 

V souboru se vzorovým provisionSample.yaml, vyhledejte oddíl, který začíná s typem **userdefinedfunctions**. Uživatelem definované funkce se zřídí v této části danou **název**. Tato UDF funguje na seznam procesy pro hledání shody v rámci **matcherNames**. Všimněte si, že pro uživatelem definovanou funkci můžete v elementu **script** zadat vlastní soubor JavaScriptu.

Všimněte si také části **roleassignments**. Uživatelem definované funkce přiřazuje role místo správce. Tato role umožňuje přistupovat k událostem, které pocházejí z některého z zřízené mezery. 

1. Konfigurace UDF zahrnout předávaný teploty přidáním nebo uncommenting následujícího řádku `matcherNames` uzel provisionSample.yaml souboru:

    ```yaml
            - Matcher Temperature
    ```

1. Otevřete soubor **src\actions\userDefinedFunctions\availability.js** v editoru. Jedná se o soubor odkazuje **skript** element provisionSample.yaml. Uživatelem definované funkce v tomto souboru podmínky hledá, když se v místnosti zjistí bez pohybu a úrovně k oxidu uhličitého jsou nižší než 1 000 správy portfolia projektů. 

   Upravte soubor jazyka JavaScript na monitorování teplotu a další podmínky. Přidejte následující řádky kódu hledat podmínky, při zjištění bez pohybu v místnosti, úrovně k oxidu uhličitého jsou nižší než 1 000 správy portfolia projektů a teploty je nižší než 78 stupních Fahrenheita.

   > [!NOTE]
   > Tato část upraví soubor *src\actions\userDefinedFunctions\availability.js* tak zapisovat uživatelem definovanou funkci dozvíte podrobně jedním ze způsobů. Však můžete přímo použít soubor [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) v nastavení aplikace. Tento soubor obsahuje všechny potřebné změny pro tento kurz. Pokud místo toho tento soubor použijete, nezapomeňte použít správný název souboru pro **skript** klíče v [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    a. Na začátek souboru přidejte pod komentář `// Add your sensor type here` následující řádky pro senzor teploty:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Přidejte následující řádky po příkazu, který definuje `var motionSensor`, níže komentář `// Add your sensor variable here`:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Přidejte následující řádek po příkazu, který definuje `var carbonDioxideValue`, níže komentář `// Add your sensor latest value here`:

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. Odeberte následující řádky kódu pod komentářem `// Modify this line to monitor your sensor value`:

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    Nahraďte je následujícími řádky:

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. Odeberte následující řádky kódu pod komentářem `// Modify these lines as per your sensor`:

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    Nahraďte je následujícími řádky:

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. Odeberte následující blok kódu *if-else* pod komentářem `// Modify this code block for your sensor`:

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);

            // Set up custom notification for air quality
            parentSpace.Notify(JSON.stringify(availableFresh));
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    A nahraďte ho následujícím blokem *if-else*:

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    Upravená uživatelem definovaná funkce bude hledat podmínku, kdy se místnost stane dostupnou a bude mít úroveň oxidu uhličitého a teplotu v rámci přípustných mezí. Když dojde ke splnění této podmínky, pomocí příkazu `parentSpace.Notify(JSON.stringify(alert));` vygeneruje oznámení. Bez ohledu na splnění podmínky nastaví hodnotu monitorovaného prostoru s odpovídající zprávou.

    g. Uložte soubor.

1. Otevřete okno příkazového řádku a přejděte do složky **obsazení quickstart\src**. Spusťte následující příkaz pro zřizování Prostorové řady grafu a uživatelem definované funkce:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Chcete-li zabránit neoprávněnému přístupu k vaší digitální dvojče rozhraní API pro správu, **obsazení quickstart** aplikace vyžaduje, abyste se přihlásit pomocí přihlašovacích údajů k účtu Azure. Ukládá svoje přihlašovací údaje na krátkou dobu, takže možná nebudete muset přihlásit pokaždé, když jej spustíte. Při prvním spuštění tohoto programu, a když po, který vyprší platnost uložených přihlašovacích údajů, aplikace vás přesměruje na přihlašovací stránce a poskytuje specifické pro relaci kód k zadání na této stránce. Podle pokynů se přihlaste pomocí svého účtu Azure.

1. Po ověření účtu spuštění aplikace podle konfigurace v provisionSample.yaml vytváření prostorový graf vzorku. Počkejte, dokud nedokončí zřizování. Bude trvat několik minut. Poté podívejte se na zprávy v příkazovém okně a Všimněte si, jak se vytvoří prostorový graf. Všimněte si, jak aplikace služby IoT hub vytvoří v kořenovém uzlu nebo `Venue`.

1. Z výstupu v příkazovém okně, zkopírujte hodnotu `ConnectionString`v části `Devices` části do schránky. Bude nutné tuto hodnotu pro simulaci zařízení připojení v další části.

    ![Ukázka zřizování](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Pokud se zobrazí chybová zpráva podobná "vstupně-výstupní operace byla přerušena z důvodu ukončení vlákna nebo na žádost aplikace" uprostřed zřizování, spusťte příkaz znovu. K tomu může dojít v případě, že klient HTTP vypršel časový limit z potíže se sítí.

<a id="simulate"></a>

## <a name="simulate-sensor-data"></a>Simulace dat ze senzorů

V této části použijete projektu s názvem *připojení zařízení* ve vzorku. Data ze senzorů pro detekci pohybu, teplotu a oxidu uhličitého budete simulovat. Tento projekt generuje náhodné hodnoty senzorů a s použitím připojovacího řetězce zařízení odesílá je do centra IoT.

1. V samostatném příkazovém řádku přejděte do ukázkové digitální dvojče Azure a pak **připojení zařízení** složky.

1. Spuštěním tohoto příkazu se ujistěte, že jsou správně závislosti projektu:

    ```cmd/sh
    dotnet restore
    ```

1. Otevřít [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) souboru ve svém editoru a upravte následující hodnoty:

   a. **DeviceConnectionString**: Přiřaďte hodnotu `ConnectionString` v okně výstup z předchozí části. Zkopírujte tento řetězec kompletně v uvozovkách, tak simulátor můžete připojit správně prostřednictvím služby IoT hub.

   b. **HardwareId** v rámci **senzorů** pole: Protože jste simulace událostí ze senzorů zřízené k vaší instanci Azure digitální dvojče, by měl odpovídat ID hardwaru a názvy snímačů v tomto souboru `sensors` uzel provisionSample.yaml souboru.

      Přidáte novou položku senzoru teploty. **Senzorů** uzlu v souboru appsettings.json by měl vypadat nějak takto:

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. Spuštěním tohoto příkazu zahajte simulaci událostí zařízení pro teplotu, pohyb a úroveň oxidu uhličitého:

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE]
   > Protože ukázkové simulace nekomunikuje přímo s vaší instancí digitální dvojče, nevyžaduje ověření.

## <a name="get-results-of-the-user-defined-function"></a>Načte výsledky uživatelem definované funkce

Uživatelem definovaná funkce se spustí pokaždé, když vaše instance přijme data ze senzorů a zařízení. Tato část se dotazuje vaší instanci Azure digitální dvojče k získání požadovaných výsledků uživatelem definované funkce. Zobrazí se vám skoro v reálném čase, pokud je k dispozici, že vzduchu je aktuální a teploty je vhodné místnosti. 

1. Otevřete okno příkazového řádku, který jste použili ke zřízení ukázku nebo nové příkazové okno a přejděte na **obsazení quickstart\src** složky ukázku znovu.

1. Spusťte následující příkaz a po zobrazení výzvy se přihlaste:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

V okně Výstup ukazuje, jak uživatelem definované funkce běží a zachycuje události z simulaci zařízení. 

   ![Výstup pro UDF](./media/tutorial-facilities-udf/udf-running.png)

Pokud je splněna podmínka monitorovaných, uživatelem definované funkce nastaví hodnotu místa s odpovídající zprávou, jak jsme viděli [starší](#udf). `GetAvailableAndFreshSpaces` Funkce vytiskne zprávu na konzole.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete zastavit v tuto chvíli seznámení digitální dvojče Azure, bez obav odstraňte prostředky vytvořené v tomto kurzu:

1. V levé nabídce v [webu Azure portal](http://portal.azure.com)vyberte **všechny prostředky**, vyberte skupinu prostředků digitální dvojče a vyberte **odstranit**.

    > [!TIP]
    > Pokud zaznamenal/zaznamenala jste potíže odstraníte instanci digitální dvojče, aktualizace služby se týká jenom s opravou. Zkuste to prosím znovu odstraníte instanci.

2. V případě potřeby odstraňte ukázkové aplikace na svém počítači práce.

## <a name="next-steps"></a>Další postup

Teď, když máte zřízenou prostory vaší a vytvoření rozhraní pro aktivaci vlastní oznámení můžete přejít na jednu z následujících kurzů:

> [!div class="nextstepaction"]
> [Kurz: Dostávat oznámení od prostory vaší digitální dvojče Azure pomocí Logic Apps](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Kurz: Vizualizujte a analyzujte události z prostory vaší digitální dvojče Azure pomocí služby Time Series Insights](tutorial-facilities-analyze.md)
