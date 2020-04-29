---
title: 'Kurz: monitorování prostoru zařízení IoT – digitální vlákna Azure | Microsoft Docs'
description: Pomocí kroků v tomto kurzu se dozvíte, jak zřídit prostorové prostředky a monitorovat pracovní podmínky pomocí digitálních vláken Azure.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 6cf6a8f7de181a81d60028e33ba2631815c8ca04
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75895375"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Kurz: zřízení a sledování pracovních podmínek pomocí Azure Digital Workers Preview

V tomto kurzu se dozvíte, jak pomocí služby Azure Digital resirees Preview monitorovat vaše prostory pro požadované teplotní podmínky a úroveň pohodlí. Po [konfiguraci sestavování vzorového sestavení](tutorial-facilities-setup.md)můžete pomocí kroků v tomto kurzu zřídit vlastní funkce pro vytváření a spouštění vašich dat ze senzorů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Definujte podmínky, které se mají monitorovat.
> * Vytvořte uživatelsky definovanou funkci (UDF).
> * Simulace dat senzorů.
> * Získání výsledků uživatelsky definované funkce.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [dokončili nastavení digitálních vláken Azure](tutorial-facilities-setup.md). Než budete pokračovat, ujistěte se, že máte následující:

- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Spuštěná instance služby Digital Twins. 
- [Ukázky digitálních vláken v jazyce C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) stažených a extrahovaných v pracovním počítači. 
- [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) ve vývojovém počítači pro sestavení a spuštění ukázky. Spusťte `dotnet --version` , chcete-li ověřit, zda je nainstalovaná správná verze. 
- [Visual Studio Code](https://code.visualstudio.com/) pro zkoumání vzorového kódu. 

>[!TIP]
> Pokud zřizujete novou instanci, použijte jedinečný název instance digitálního vlákna.

## <a name="define-conditions-to-monitor"></a>Definice podmínek, které se mají monitorovat

Můžete definovat sadu specifických podmínek, které se mají monitorovat v datech zařízení nebo senzoru, označovaných jako *shody*. Pak můžete definovat funkce označované jako *uživatelsky definované funkce*. Uživatelsky definované funkce spouštějí vlastní logiku pro data, která pocházejí z vašich prostorů a zařízení, když dojde k podmínkám zadaným pro tyto shody. Další informace najdete v tématu [zpracování dat a uživatelsky definované funkce](concepts-user-defined-functions.md). 

Ve Visual Studio Code otevřete soubor **src\actions\provisionSample.yaml** z ukázkového projektu **occupancy-quickstart**. Všimněte si části, která začíná typem **matchers** (Pravidla shody). Každá položka v rámci tohoto typu vytvoří koshodě se zadaným **názvem**. Koshodě bude monitorovat senzor typu **dataTypeValue**. Všimněte si, jak souvisí s prostorem s názvem *Room místnost a1*, který má uzel **zařízení** , který obsahuje několik senzorů. Aby se zajistilo, že se bude porovnávat jeden z těchto senzorů, ujistěte se, že jeho **dataTypeValue** odpovídá **datovému typu**senzoru. 

Přidejte následující shodu pod existující shody. Ujistěte se, že jsou klíče zarovnané a mezery nejsou nahrazené kartami. Tyto řádky jsou také k dispozici v souboru *provisionSample. yaml* jako řádky s komentářem. Můžete je odkomentovat odebráním `#` znaku před každým řádkem.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Tato shoda bude sledovat `SAMPLE_SENSOR_TEMPERATURE` senzor, který jste přidali v [prvním kurzu](tutorial-facilities-setup.md). 

## <a name="create-a-user-defined-function"></a>Vytvoření uživatelem definované funkce

K přizpůsobení zpracování dat senzorů můžete použít uživatelsky definované funkce. Jedná se o vlastní kód JavaScriptu, který může běžet v instanci digitálních vláken Azure, když dojde k určitým podmínkám popsaným v odpovídajících sestavách. Pravidla shody a uživatelem definované funkce můžete vytvořit pro všechny senzory, které chcete monitorovat. Další informace najdete v tématu [zpracování dat a uživatelsky definované funkce](concepts-user-defined-functions.md). 

V ukázkovém souboru *provisionSample. yaml* vyhledejte oddíl, který začíná textem **UserDefinedFunctions**. Tato část zřídí uživatelsky definovanou funkci se zadaným **názvem**. Tato UDF funguje na seznamu shod v rámci **matcherNames**. Všimněte si, že pro uživatelem definovanou funkci můžete v elementu **script** zadat vlastní soubor JavaScriptu.

Všimněte si také části **roleassignments**. Přiřadí roli správce prostoru k uživatelsky definované funkci. Tato role umožňuje IT přístup k událostem, které pocházejí z kteréhokoliv zřízeného prostoru. 

1. Nakonfigurujte uživatelem definovanou funkci tak, aby zahrnovala pravidlo shody teploty. Provedete to přidáním následujícího řádku do uzlu `matcherNames` v souboru *provisionSample.yaml* (případně zrušením komentáře u tohoto řádku):

    ```yaml
            - Matcher Temperature
    ```

1. V editoru otevřete soubor **src\actions\userDefinedFunctions\availability.js**. Toto je soubor, na který se odkazuje v elementu **Script** *provisionSample. yaml*. Uživatelsky definovaná funkce v tomto souboru vyhledává podmínky, když se v místnosti nezjistí žádný pohyb a úrovně oxidu uhličitého jsou nižší než 1 000 ppm. 

   Upravte soubor JavaScriptu pro monitorování teploty a dalších podmínek. Přidejte následující řádky kódu, aby hledaly podmínky, když v místnosti není zjištěn žádný pohyb, úrovně oxidu uhličitého jsou nižší než 1 000 ppm a teplota je nižší než 78 stupňů Fahrenheita.

   >[!NOTE]
   > V této části se upraví soubor *src\actions\userDefinedFunctions\availability.js* , takže se můžete podrobněji dozvědět, jak napsat uživatelsky definovanou funkci. Můžete se ale rozhodnout přímo použít soubor [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) v instalačním programu. Tento soubor obsahuje všechny potřebné změny pro tento kurz. Pokud tento soubor použijete místo toho, ujistěte se, že jste pro klíč **skriptu** v [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml)použili správný název souboru.

    a. Na začátek souboru přidejte pod komentář `// Add your sensor type here` následující řádky pro senzor teploty:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Přidejte následující řádky za příkaz, který definuje `var motionSensor`, pod komentářem: `// Add your sensor variable here`

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Přidejte následující řádek za příkaz, který definuje `var carbonDioxideValue`, pod komentářem: `// Add your sensor latest value here`

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

1. Otevřete příkazové okno a přejdete do složky **Occupancy-quickstart\src**. Spusťte následující příkaz, který zřídí graf prostorové logiky a uživatelsky definovanou funkci:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   >[!IMPORTANT]
   > Aby se zabránilo neoprávněnému přístupu k rozhraní API pro správu digitálních vláken, **vyžaduje se přihlášení** pomocí přihlašovacích údajů k účtu Azure. Ukládá vaše přihlašovací údaje po krátkou dobu, takže se při každém spuštění nemusíte přihlašovat. Při prvním spuštění tohoto programu a po vypršení platnosti vašich uložených přihlašovacích údajů vám aplikace přesměruje na přihlašovací stránku a zadá na ni kód specifický pro relaci. Podle pokynů se přihlaste pomocí svého účtu Azure.

1. Po ověření účtu se v aplikaci spustí vytvoření ukázkového prostorového grafu, jak je nakonfigurováno v *provisionSample. yaml*. Počkejte, než se zřizování dokončí. Bude to trvat několik minut. Potom Sledujte zprávy v příkazovém okně a Všimněte si, jak se vytvořil prostorový graf. Všimněte si, jak aplikace vytvoří centrum IoT v kořenovém uzlu nebo v `Venue`.

1. Z výstupu v příkazovém okně Zkopírujte hodnotu `ConnectionString`v `Devices` části do schránky. Tuto hodnotu budete potřebovat pro simulaci připojení zařízení v další části.

    [![Zřídit ukázku](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

>[!TIP]
> Pokud se zobrazí chybová zpráva podobná operaci vstupně-výstupní operace byla přerušena z důvodu ukončení vlákna nebo žádosti o aplikaci (uprostřed zřizování), zkuste příkaz spustit znovu. K tomu může dojít, pokud u klienta HTTP vypršel časový limit problému se sítí.

## <a name="simulate-sensor-data"></a>Simulace dat ze senzorů

V této části použijete projekt s názvem *zařízení-připojení* v ukázce. Data snímače budete simulovat pro detekci pohybu, teploty a oxidu uhličitého. Tento projekt generuje náhodné hodnoty senzorů a s použitím připojovacího řetězce zařízení odesílá je do centra IoT.

1. V samostatném příkazovém okně přejdete do ukázky digitálních vláken Azure a potom do složky pro **připojení zařízení** .

1. Spuštěním tohoto příkazu se ujistěte, že jsou správně závislosti projektu:

    ```cmd/sh
    dotnet restore
    ```

1. V editoru otevřete soubor [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) a upravte následující hodnoty:

   a. **DeviceConnectionString:** Přiřaďte hodnotu `ConnectionString` z okna výstupu z předchozí části. Tento řetězec zkopírujte v uvozovkách, aby se simulátor mohl správně připojit ke službě IoT Hub.

   b. **HardwareId** v rámci pole **senzory** : protože simulujete události ze senzorů zřízených vaší instancí digitálních vláken Azure, ID hardwaru a názvy senzorů v tomto souboru by se měly shodovat s `sensors` uzlem souboru *provisionSample. yaml* .

      Přidejte novou položku pro senzor teploty. Uzel **senzory** v souboru *appSettings. JSON* by měl vypadat takto:

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
   > Vzhledem k tomu, že ukázka simulace přímo nekomunikuje s instancí digitálního vlákna, nevyžaduje ověření.

## <a name="get-results-of-the-user-defined-function"></a>Získání výsledků uživatelsky definované funkce

Uživatelem definovaná funkce se spustí pokaždé, když vaše instance přijme data ze senzorů a zařízení. Tato část se dotazuje na instanci digitálního vlákna Azure, aby získala výsledky uživatelsky definované funkce. Až bude dostupná místnost, bude se vám zobrazovat oznámení téměř v reálném čase, a to až do okamžiku, kdy je vzduch v čerstvém stavu a teplota je pravá. 

1. Otevřete okno příkazového řádku, které jste použili k zřízení ukázky, nebo nové příkazové okno a znovu se zajděte do složky **Occupancy-quickstart\src** v ukázce.

1. Spusťte následující příkaz a po zobrazení výzvy se přihlaste:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

Okno výstup ukazuje, jak uživatelsky definovaná funkce běží a zachycuje události z simulace zařízení. 

   [![Výstup pro systém souborů UDF](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png)](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png#lightbox)

Pokud je splněna monitorovaná podmínka, uživatelsky definovaná funkce nastaví hodnotu prostoru s příslušnou zprávou, jak jsme viděli [dříve](#create-a-user-defined-function). `GetAvailableAndFreshSpaces` Funkce vytiskne zprávu v konzole nástroje.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přestat zkoumat digitální vlákna Azure v tomto okamžiku, můžete odstranit prostředky vytvořené v tomto kurzu:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**, vyberte skupinu prostředků vaše digitální vlákna a vyberte **Odstranit**.

    >[!TIP]
    > Pokud jste narazili na potíže s odstraněním instance digitálního vlákna, aktualizovala se aktualizace služby s touto opravou. Zkuste prosím instanci znovu odstranit.

2. V případě potřeby odstraňte ukázkové aplikace v pracovním počítači.

## <a name="next-steps"></a>Další kroky

Teď, když jste zřídili své prostory a vytvořili jste rozhraní, abyste mohli aktivovat vlastní oznámení, můžete přejít do některého z následujících kurzů:

> [!div class="nextstepaction"]
> [Kurz: Příjem oznámení z prostorů Azure Digital Twins pomocí Logic Apps](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Kurz: Vizualizace a analýza událostí z prostorů Azure Digital Twins s využitím služby Time Series Insights](tutorial-facilities-analyze.md)
