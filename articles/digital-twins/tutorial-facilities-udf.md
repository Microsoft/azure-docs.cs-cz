---
title: 'Kurz: sledování prostoru pomocí digitálních vláken Azure | Microsoft Docs'
description: Pomocí kroků v tomto kurzu se dozvíte, jak zřídit prostorové prostředky a monitorovat pracovní podmínky pomocí digitálních vláken Azure.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 74e3c46b2b1427c27923ed91846755797b8da690
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949087"
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
- Instance digitálních vláken je spuštěná. 
- Ukázky digitálních vláken se stáhly a extrahují na vašem pracovním počítači. [ C# ](https://github.com/Azure-Samples/digital-twins-samples-csharp) 
- [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) ve vývojovém počítači pro sestavení a spuštění ukázky. Spusťte `dotnet --version` a ověřte, zda je nainstalovaná správná verze. 
- [Visual Studio Code](https://code.visualstudio.com/) k prozkoumávání ukázkového kódu. 

> [!TIP]
> Pokud zřizujete novou instanci, použijte jedinečný název instance digitálního vlákna.

## <a name="define-conditions-to-monitor"></a>Definování podmínek, které se mají monitorovat

Můžete definovat sadu specifických podmínek, které se mají monitorovat v datech zařízení nebo senzoru, označovaných jako *shody*. Pak můžete definovat funkce označované jako *uživatelsky definované funkce*. Uživatelsky definované funkce spouštějí vlastní logiku pro data, která pocházejí z vašich prostorů a zařízení, když dojde k podmínkám zadaným pro tyto shody. Další informace najdete v tématu [zpracování dat a uživatelsky definované funkce](concepts-user-defined-functions.md). 

V ukázkovém projektu **obsazenost – rychlý Start** otevřete soubor **src\actions\provisionSample.yaml** v Visual Studio Code. Všimněte si oddílu, který začíná **porovnáváním**typů. Každá položka v rámci tohoto typu vytvoří koshodě se zadaným **názvem**. Koshodě bude monitorovat senzor typu **dataTypeValue**. Všimněte si, jak souvisí s prostorem s názvem *Room místnost a1*, který má uzel **zařízení** , který obsahuje několik senzorů. Aby se zajistilo, že se bude porovnávat jeden z těchto senzorů, ujistěte se, že jeho **dataTypeValue** odpovídá **datovému typu**senzoru. 

Přidejte následující shodu pod existující shody. Ujistěte se, že jsou klíče zarovnané a mezery nejsou nahrazené kartami. Tyto řádky jsou také k dispozici v souboru *provisionSample. yaml* jako řádky s komentářem. Můžete je odkomentovat odebráním znaku @no__t 0 před každým řádkem.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Tato shoda bude sledovat senzor `SAMPLE_SENSOR_TEMPERATURE`, který jste přidali v [prvním kurzu](tutorial-facilities-setup.md). 

## <a name="create-a-user-defined-function"></a>Vytvoření uživatelsky definované funkce

K přizpůsobení zpracování dat senzorů můžete použít uživatelsky definované funkce. Jedná se o vlastní kód JavaScriptu, který může běžet v instanci digitálních vláken Azure, když dojde k určitým podmínkám popsaným v odpovídajících sestavách. Pro každý senzor, který chcete monitorovat, můžete vytvořit odpovídající a uživatelsky definované funkce. Další informace najdete v tématu [zpracování dat a uživatelsky definované funkce](concepts-user-defined-functions.md). 

V ukázkovém souboru *provisionSample. yaml* vyhledejte oddíl, který začíná textem **UserDefinedFunctions**. Tato část zřídí uživatelsky definovanou funkci se zadaným **názvem**. Tato UDF funguje na seznamu shod v rámci **matcherNames**. Všimněte si, jak můžete jako **skript**zadat vlastní soubor JAVASCRIPTU pro UDF.

Také si poznamenejte část s názvem **RoleAssignments**. Přiřadí roli správce prostoru k uživatelsky definované funkci. Tato role umožňuje IT přístup k událostem, které pocházejí z kteréhokoliv zřízeného prostoru. 

1. Nakonfigurujte systém souborů UDF tak, aby zahrnoval teplotní teplotu přidáním nebo zrušením komentáře na následující řádek v uzlu `matcherNames` v souboru *provisionSample. yaml* :

    ```yaml
            - Matcher Temperature
    ```

1. Otevřete soubor **src\actions\userDefinedFunctions\availability.js** v editoru. Toto je soubor, na který se odkazuje v elementu **Script** *provisionSample. yaml*. Uživatelsky definovaná funkce v tomto souboru vyhledává podmínky, když se v místnosti nezjistí žádný pohyb a úrovně oxidu uhličitého jsou nižší než 1 000 ppm. 

   Upravte soubor JavaScriptu pro monitorování teploty a dalších podmínek. Přidejte následující řádky kódu, aby hledaly podmínky, když v místnosti není zjištěn žádný pohyb, úrovně oxidu uhličitého jsou nižší než 1 000 ppm a teplota je nižší než 78 stupňů Fahrenheita.

   > [!NOTE]
   > V této části se upraví soubor *src\actions\userDefinedFunctions\availability.js* , takže se můžete podrobněji dozvědět, jak napsat uživatelsky definovanou funkci. Můžete se ale rozhodnout přímo použít soubor [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) v instalačním programu. Tento soubor obsahuje všechny změny požadované pro tento kurz. Pokud tento soubor použijete místo toho, ujistěte se, že jste pro klíč **skriptu** v [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml)použili správný název souboru.

    a. V horní části souboru přidejte následující řádky za teplotu pod komentářem `// Add your sensor type here`:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Přidejte následující řádky za příkaz definující `var motionSensor` pod komentářem `// Add your sensor variable here`:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    r. Přidejte následující řádek za příkaz definující `var carbonDioxideValue` pod komentářem `// Add your sensor latest value here`:

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    trojrozměrné. Z komentáře `// Modify this line to monitor your sensor value` odeberte následující řádky kódu:

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

    cerebrální. Z komentáře `// Modify these lines as per your sensor` odeberte následující řádky kódu:

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    Nahraďte je následujícími řádky:

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    FJ. Odeberte následující blok kódu *if-else* za komentářem `// Modify this code block for your sensor`:

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

    A nahraďte ho následujícím blokem *if-else* :

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

    Upravená UDF bude hledat podmínku, kde bude místnost k dispozici a má oxid uhličitý a teplotu v přípustných mezích. Vygeneruje oznámení s příkazem `parentSpace.Notify(JSON.stringify(alert));`, pokud je tento stav splněn. Nastaví hodnotu monitorovaného prostoru bez ohledu na to, zda je podmínka splněna, spolu s odpovídající zprávou.

    věcn. Uložte soubor.

1. Otevřete příkazové okno a přejdete do složky **Occupancy-quickstart\src**. Spusťte následující příkaz, který zřídí graf prostorové logiky a uživatelsky definovanou funkci:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Aby se zabránilo neoprávněnému přístupu k rozhraní API pro správu digitálních vláken, **vyžaduje se přihlášení** pomocí přihlašovacích údajů k účtu Azure. Ukládá vaše přihlašovací údaje po krátkou dobu, takže se při každém spuštění nemusíte přihlašovat. Při prvním spuštění tohoto programu a po vypršení platnosti vašich uložených přihlašovacích údajů vám aplikace přesměruje na přihlašovací stránku a zadá na ni kód specifický pro relaci. Podle pokynů se přihlaste pomocí účtu Azure.

1. Po ověření účtu se v aplikaci spustí vytvoření ukázkového prostorového grafu, jak je nakonfigurováno v *provisionSample. yaml*. Počkejte, než se zřizování dokončí. Bude to trvat několik minut. Potom Sledujte zprávy v příkazovém okně a Všimněte si, jak se vytvořil prostorový graf. Všimněte si, jak aplikace vytvoří centrum IoT v kořenovém uzlu nebo `Venue`.

1. Z výstupu v příkazovém okně Zkopírujte hodnotu `ConnectionString` v části `Devices` do schránky. Tuto hodnotu budete potřebovat pro simulaci připojení zařízení v další části.

    [Ukázka @no__t – 1Provision](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

> [!TIP]
> Pokud se zobrazí chybová zpráva podobná operaci vstupně-výstupní operace byla přerušena z důvodu ukončení vlákna nebo žádosti o aplikaci (uprostřed zřizování), zkuste příkaz spustit znovu. K tomu může dojít, pokud u klienta HTTP vypršel časový limit problému se sítí.

## <a name="simulate-sensor-data"></a>Simulace dat senzorů

V této části použijete projekt s názvem *zařízení-připojení* v ukázce. Data snímače budete simulovat pro detekci pohybu, teploty a oxidu uhličitého. Tento projekt generuje náhodné hodnoty senzorů a odesílá je do služby IoT Hub pomocí připojovacího řetězce zařízení.

1. V samostatném příkazovém okně přejdete do ukázky digitálních vláken Azure a potom do složky pro **připojení zařízení** .

1. Spusťte tento příkaz, abyste se ujistili, že jsou závislosti pro projekt správné:

    ```cmd/sh
    dotnet restore
    ```

1. V editoru otevřete soubor [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) a upravte následující hodnoty:

   a. **DeviceConnectionString**: přiřaďte hodnotu `ConnectionString` v okně výstup z předchozí části. Tento řetězec zkopírujte v uvozovkách, aby se simulátor mohl správně připojit ke službě IoT Hub.

   b. **HardwareId** v rámci pole **senzory** : protože simulujete události ze senzorů zřízených vaší instancí digitálních vláken Azure, ID hardwaru a názvy senzorů v tomto souboru by měly odpovídat uzlu `sensors` v  *soubor provisionSample. yaml* .

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

1. Spusťte tento příkaz a začněte simulovat události zařízení pro teplotu, pohyb a oxid uhličitý:

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE]
   > Vzhledem k tomu, že ukázka simulace přímo nekomunikuje s instancí digitálního vlákna, nevyžaduje ověření.

## <a name="get-results-of-the-user-defined-function"></a>Získání výsledků uživatelsky definované funkce

Uživatelem definovaná funkce se spustí pokaždé, když vaše instance přijme data ze zařízení a snímačů. Tato část se dotazuje na instanci digitálního vlákna Azure, aby získala výsledky uživatelsky definované funkce. Zobrazí se téměř v reálném čase, pokud je k dispozici místnost, vzduchem je čerstvá a teplota je pravá. 

1. Otevřete okno příkazového řádku, které jste použili k zřízení ukázky, nebo nové příkazové okno a znovu se zajděte do složky **Occupancy-quickstart\src** v ukázce.

1. Spusťte následující příkaz a přihlaste se po zobrazení výzvy:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

Okno výstup ukazuje, jak uživatelsky definovaná funkce běží a zachycuje události z simulace zařízení. 

   [@no__t – 1Output pro systém souborů UDF](./media/tutorial-facilities-udf/udf-running.png)](./media/tutorial-facilities-udf/udf-running.png#lightbox)

Pokud je splněna monitorovaná podmínka, uživatelsky definovaná funkce nastaví hodnotu prostoru s příslušnou zprávou, jak jsme viděli [dříve](#create-a-user-defined-function). Funkce `GetAvailableAndFreshSpaces` vytiskne zprávu v konzole nástroje.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přestat zkoumat digitální vlákna Azure v tomto okamžiku, můžete odstranit prostředky vytvořené v tomto kurzu:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**, vyberte skupinu prostředků vaše digitální vlákna a vyberte **Odstranit**.

    > [!TIP]
    > Pokud jste narazili na potíže s odstraněním instance digitálního vlákna, aktualizovala se aktualizace služby s touto opravou. Zkuste prosím instanci znovu odstranit.

2. V případě potřeby odstraňte ukázkové aplikace v pracovním počítači.

## <a name="next-steps"></a>Další kroky

Teď, když jste zřídili své prostory a vytvořili jste rozhraní, abyste mohli aktivovat vlastní oznámení, můžete přejít do některého z následujících kurzů:

> [!div class="nextstepaction"]
> [Kurz: přijímání oznámení z digitálních vláken Azure pomocí Logic Apps](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Kurz: vizualizace a analýza událostí z digitálních vláken Azure pomocí Time Series Insights](tutorial-facilities-analyze.md)
