---
title: 'Kurz: Sledování místa na zařízení IoT – digitální dvojčata Azure| Dokumenty společnosti Microsoft'
description: Zjistěte, jak zřídit prostorové prostředky a sledovat pracovní podmínky pomocí Azure Digital Twins pomocí kroků v tomto kurzu.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 6cf6a8f7de181a81d60028e33ba2631815c8ca04
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75895375"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Kurz: Zajištění stavby a monitorování pracovních podmínek pomocí Azure Digital Twins Preview

Tento kurz ukazuje, jak používat Azure Digital Twins Preview ke sledování vašich prostor pro požadované teplotní podmínky a úroveň pohodlí. Po [konfiguraci ukázkové budovy](tutorial-facilities-setup.md)můžete zřídit vytváření a spouštět vlastní funkce na data senzorů pomocí kroků v tomto kurzu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Definujte podmínky pro sledování.
> * Vytvořte uživatelem definovanou funkci (UDF).
> * Simulujte data ze senzorů.
> * Získejte výsledky uživatelem definované funkce.

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že jste [dokončili nastavení Azure Digital Twins](tutorial-facilities-setup.md). Než budete pokračovat, ujistěte se, že máte následující:

- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Spuštěná instance služby Digital Twins. 
- [Digitální dvojčata C # ukázky](https://github.com/Azure-Samples/digital-twins-samples-csharp) stáhnout a extrahovat na vašem pracovním počítači. 
- [Sada .NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) ve vývojovém počítači pro sestavení a spuštění ukázky. Spusťte `dotnet --version` a ověřte, zda je nainstalována správná verze. 
- [Visual Studio Code](https://code.visualstudio.com/) pro zkoumání vzorového kódu. 

>[!TIP]
> Pokud zřizujete novou instanci, použijte jedinečný název instance Digitální dvojčata.

## <a name="define-conditions-to-monitor"></a>Definice podmínek, které se mají monitorovat

Můžete definovat sadu specifických podmínek pro sledování v datech zařízení nebo senzoru, *nazývaných matchery*. Poté můžete definovat funkce nazývané *uživatelem definované funkce*. Uživatelem definované funkce spouštějí vlastní logiku na datech, která pocházejí z vašich prostorů a zařízení, když dojde k podmínkám určeným matchers. Další informace naleznete [v článku Zpracování dat a uživatelem definované funkce](concepts-user-defined-functions.md). 

Ve Visual Studio Code otevřete soubor **src\actions\provisionSample.yaml** z ukázkového projektu **occupancy-quickstart**. Všimněte si části, která začíná typem **matchers** (Pravidla shody). Každá položka pod tímto typem vytvoří matcher se zadaným **názvem**. Matcher bude sledovat snímač typu **dataTypeValue**. Všimněte si, jak se vztahuje k prostoru s názvem *Focus Room A1*, který má uzel **zařízení,** který obsahuje několik senzorů. Chcete-li zřídit matcher, který bude sledovat jeden z těchto senzorů, ujistěte se, že jeho **dataTypeValue** odpovídá **dataType senzoru**. 

Pod existující matcher přidejte následující matcher. Zkontrolujte, zda jsou klávesy zarovnané a zda nejsou mezery nahrazeny kartami. Tyto řádky jsou také k dispozici v *provisionSample.yaml* souboru jako commented-out řádky. Můžete je odkomentovat `#` odebráním znaku před každým řádkem.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Tento matcher bude `SAMPLE_SENSOR_TEMPERATURE` sledovat senzor, který jste [přidali](tutorial-facilities-setup.md)v prvním kurzu . 

## <a name="create-a-user-defined-function"></a>Vytvoření uživatelem definované funkce

Pomocí uživatelem definovaných funkcí můžete přizpůsobit zpracování dat senzorů. Jsou to vlastní kód JavaScriptu, který se dá spustit v rámci instance Azure Digital Twins, když nastanou konkrétní podmínky popsané matchery. Pravidla shody a uživatelem definované funkce můžete vytvořit pro všechny senzory, které chcete monitorovat. Další informace naleznete [v článku Zpracování dat a uživatelem definované funkce](concepts-user-defined-functions.md). 

V ukázkovém souboru *provisionSample.yaml* vyhledejte oddíl, který začíná typem **userdefinedfunctions**. Tato část obsahuje uživatelem definovanou funkci s daným **názvem**. Tento udf působí na seznamu matchers pod **matcherNames**. Všimněte si, že pro uživatelem definovanou funkci můžete v elementu **script** zadat vlastní soubor JavaScriptu.

Všimněte si také části **roleassignments**. Přiřadí roli správce prostoru uživatelem definované funkci. Tato role umožňuje přístup k událostem, které pocházejí z některého z zřízených prostorů. 

1. Nakonfigurujte uživatelem definovanou funkci tak, aby zahrnovala pravidlo shody teploty. Provedete to přidáním následujícího řádku do uzlu `matcherNames` v souboru *provisionSample.yaml* (případně zrušením komentáře u tohoto řádku):

    ```yaml
            - Matcher Temperature
    ```

1. V editoru otevřete soubor **src\actions\userDefinedFunctions\availability.js**. Jedná se o soubor odkazovaný v elementu **skriptu** *provisionSample.yaml*. Uživatelem definovaná funkce v tomto souboru hledá podmínky, kdy v místnosti není detekován žádný pohyb a hladiny oxidu uhličitého jsou nižší než 1 000 ppm. 

   Upravte soubor JavaScriptu tak, aby monitoroval teplotu a další podmínky. Přidejte následující řádky kódu, abyste zjistili podmínky, kdy v místnosti není zjištěn žádný pohyb, hladiny oxidu uhličitého jsou nižší než 1 000 ppm a teplota je nižší než 78 stupňů Fahrenheita.

   >[!NOTE]
   > Tato část upravuje soubor *src\actions\userDefinedFunctions\availability.js,* takže se můžete podrobně naučit jeden způsob, jak napsat uživatelem definovanou funkci. Můžete však přímo použít soubor [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) v nastavení. Tento soubor obsahuje všechny potřebné změny pro tento kurz. Pokud místo toho použijete tento soubor, použijte správný název souboru pro klíč **skriptu** v [souboru src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    a. Na začátek souboru přidejte pod komentář `// Add your sensor type here` následující řádky pro senzor teploty:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Pod komentář `// Add your sensor variable here`přidejte následující řádky `var motionSensor`za příkaz, který definuje :

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Pod komentář `// Add your sensor latest value here`přidejte následující řádek `var carbonDioxideValue`za příkaz, který definuje :

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
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for poor air quality
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

1. Otevřete příkazové okno a přejděte na možnost **obsazení složky-quickstart\src**. Spuštěním následujícího příkazu zřídíte graf prostorové inteligence a uživatelem definovanou funkci:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   >[!IMPORTANT]
   > Chcete-li zabránit neoprávněnému přístupu k rozhraní API pro správu digitálních dvojčat, vyžaduje aplikace **pro rychlé spuštění obsazenosti,** abyste se přihlásili pomocí přihlašovacích údajů k účtu Azure. Uloží vaše přihlašovací údaje na krátkou dobu, takže se nemusíte přihlašovat při každém spuštění. Při prvním spuštění tohoto programu a po vypršení platnosti uložených přihlašovacích údajů vás aplikace přesměruje na přihlašovací stránku a poskytne kód specifický pro relaci, který chcete na této stránce zadat. Podle pokynů se přihlaste pomocí svého účtu Azure.

1. Po ověření účtu aplikace začne vytvářet ukázkový prostorový graf, jak je nakonfigurován v *provisionSample.yaml*. Počkejte, dokud dokončení zřizování. Bude to trvat několik minut. Poté sledujte zprávy v příkazovém okně a všimněte si, jak je vytvořen prostorový graf. Všimněte si, jak aplikace vytvoří centrum IoT `Venue`hub v kořenovém uzlu nebo .

1. Z výstupu v příkazovém okně `ConnectionString`zkopírujte `Devices` hodnotu položky , pod oddílem, do schránky. Tuto hodnotu budete potřebovat k simulaci připojení zařízení v další části.

    [![Ukázka zřizování](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

>[!TIP]
> Pokud se zobrazí chybová zpráva podobná "Operace vstupně-výstupních operací byla přerušena z důvodu ukončení podprocesu nebo požadavku na aplikaci" uprostřed zřizování, zkuste příkaz spustit znovu. K tomu může dojít, pokud došlo k časovému výpadku klienta HTTP z problému se sítí.

## <a name="simulate-sensor-data"></a>Simulace dat ze senzorů

V této části budete používat projekt s názvem *připojení zařízení* v ukázce. Budete simulovat data ze senzorů pro detekci pohybu, teploty a oxidu uhličitého. Tento projekt generuje náhodné hodnoty senzorů a s použitím připojovacího řetězce zařízení odesílá je do centra IoT.

1. V samostatném příkazovém okně přejděte na ukázku Azure Digital Twins a potom do složky **připojení zařízení.**

1. Spuštěním tohoto příkazu se ujistěte, že jsou správně závislosti projektu:

    ```cmd/sh
    dotnet restore
    ```

1. Otevřete soubor [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) v editoru a upravte následující hodnoty:

   a. **DeviceConnectionString:** Přiřaďte hodnotu `ConnectionString` z okna výstupu z předchozí části. Zkopírujte tento řetězec úplně v uvozovkách, takže simulátor můžete správně připojit s centrem IoT.

   b. **HardwareId** v rámci pole **Sensors:** Vzhledem k tomu, že simulujete události ze senzorů zřízených do instance Azure `sensors` Digital Twins, ID hardwaru a názvy senzorů v tomto souboru by měly odpovídat uzlu souboru *provisionSample.yaml.*

      Přidejte novou položku pro teplotní čidlo. Uzel **Senzory** v *souboru appsettings.json* by měl vypadat takto:

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

   >[!NOTE]
   > Vzhledem k tomu, že ukázka simulace nekomunikuje přímo s instancí Digital Twins, nevyžaduje ověření.

## <a name="get-results-of-the-user-defined-function"></a>Získání výsledků uživatelem definované funkce

Uživatelem definovaná funkce se spustí pokaždé, když vaše instance přijme data ze senzorů a zařízení. Tato část se dotazuje instance Azure Digital Twins, abyste získali výsledky uživatelem definované funkce. Budete upozorněni téměř v reálném čase, když je k dispozici místnost, že vzduch je čerstvý a teplota je správná. 

1. Otevřete příkazové okno, které jste použili k zřízení ukázky, nebo nové ho to to a přejděte znovu do složky **enekce-quickstart\src** ukázky.

1. Spusťte následující příkaz a po zobrazení výzvy se přihlaste:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

Výstupní okno ukazuje, jak uživatelem definovaná funkce spouští a zachycuje události ze simulace zařízení. 

   [![Výstup pro UDF](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png)](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png#lightbox)

Pokud je splněna sledovaná podmínka, uživatelem definovaná funkce nastaví hodnotu prostoru s příslušnou zprávou, jak jsme viděli [dříve](#create-a-user-defined-function). Funkce `GetAvailableAndFreshSpaces` vytiskne zprávu na konzoli.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete v tomto okamžiku přestat zkoumat Azure Digital Twins, můžete odstranit prostředky vytvořené v tomto kurzu:

1. V levé nabídce na [portálu Azure](https://portal.azure.com)vyberte **Všechny prostředky**, vyberte skupinu prostředků Digitální dvojčata a vyberte **Odstranit**.

    >[!TIP]
    > Pokud došlo k potížím s odstraněním instance Digitální dvojčata, byla s opravou zavedena aktualizace služby. Opakujte odstranění instance.

2. V případě potřeby odstraňte ukázkové aplikace v pracovním počítači.

## <a name="next-steps"></a>Další kroky

Teď, když jste zřídit své prostory a vytvořili rámec pro aktivaci vlastní oznámení, můžete přejít na některý z následujících kurzů:

> [!div class="nextstepaction"]
> [Kurz: Příjem oznámení z prostorů Azure Digital Twins pomocí Logic Apps](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Kurz: Vizualizace a analýza událostí z prostorů Azure Digital Twins s využitím služby Time Series Insights](tutorial-facilities-analyze.md)
