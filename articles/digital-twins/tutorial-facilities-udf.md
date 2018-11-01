---
title: Monitorování prostoru pomocí služby Azure Digital Twins | Microsoft Docs
description: Zjistěte, jak pomocí kroků v tomto kurzu zřídit prostorové prostředky a monitorovat pracovní podmínky s využitím služby Azure Digital Twins.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/26/2018
ms.author: dkshir
ms.openlocfilehash: cf45cb8de0e40dfe5f5772dcb1a0be2aa7585fd6
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156676"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>Kurz: Zřízení budovy a monitorování pracovních podmínek pomocí služby Azure Digital Twins

Tento kurz ukazuje, jak pomocí služby Azure Digital Twins monitorovat požadované teplotní podmínky a úroveň komfortu v prostorech. Jakmile budete mít [nakonfigurovanou ukázkovou budovu](tutorial-facilities-setup.md), můžete pomocí kroků v tomto kurzu budovu zřídit a spouštět vlastní funkce pracující s daty ze senzorů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Definice podmínek, které se mají monitorovat
> * Vytvoření uživatelem definované funkce
> * Simulace dat ze senzorů
> * Získání výsledků uživatelem definované funkce

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [nakonfigurovali vlastní systém Azure Digital Twins](tutorial-facilities-setup.md). Než budete pokračovat, ujistěte se, že máte následující:
- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Spuštěná instance služby Digital Twins. 
- Pracovní počítač se staženými a extrahovanými [ukázkami služby Digital Twins v jazyce C#](https://github.com/Azure-Samples/digital-twins-samples-csharp). 
- Počítač pro vývoj se sadou [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) pro sestavení a spuštění ukázky. Spuštěním příkazu `dotnet --version` ověřte, že máte nainstalovanou správnou verzi. 
- [Visual Studio Code](https://code.visualstudio.com/) pro zkoumání vzorového kódu. 

## <a name="define-conditions-to-monitor"></a>Definice podmínek, které se mají monitorovat
Můžete definovat sadu konkrétních podmínek, které se mají monitorovat v datech ze senzorů nebo zařízení. Tyto podmínky se označují jako **pravidla shody**. Dále můžete definovat funkce označované jako *uživatelem definované funkce*, které spouští vlastní logiku pro data přicházející z vašich prostorů a zařízení, když dojde ke splnění podmínek uvedených v pravidlech shody. Další informace najdete v tématu [Zpracování dat a uživatelem definované funkce](concepts-user-defined-functions.md). 

Ve Visual Studio Code otevřete soubor **_src\actions\provisionSample.yaml_** z ukázkového projektu **_occupancy-quickstart_**. Všimněte si části, která začíná typem **matchers** (Pravidla shody). Každá položka v rámci tohoto typu vytvoří pravidlo shody se zadaným názvem (**Name**), které bude monitorovat senzor typu **dataTypeValue**. Všimněte si, jak to souvisí s prostorem *Focus Room A1*, který má uzel **devices** (zařízení) obsahující několik senzorů (**sensors**). Pokud chcete zřídit pravidlo shody, které bude sledovat některý z těchto senzorů, hodnota **dataTypeValue** pravidla musí odpovídat hodnotě **dataType** senzoru. 

Pod stávající pravidla shody přidejte následující pravidlo shody a ujistěte se, že jsou klíče zarovnané a že se mezery nenahradí za tabulátory:

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Toto pravidlo bude sledovat senzor *SAMPLE_SENSOR_TEMPERATURE*, který jste přidali v [prvním kurzu](tutorial-facilities-setup.md). Všimněte si, že tyto řádky jsou i v souboru *provisionSample.yaml* označené jako komentář. Komentář můžete jednoduše zrušit odebráním znaku # na začátku každého řádku. 

<a id="udf" />

## <a name="create-a-user-defined-function"></a>Vytvoření uživatelem definované funkce
Uživatelem definované funkce umožňují přizpůsobit zpracování dat ze senzorů. Jedná se o vlastní kód JavaScriptu, který se může spouštět v rámci vaší instance služby Digital Twins, když dojde ke splnění konkrétních podmínek popsaných v pravidlech shody. *Pravidla shody* a *uživatelem definované funkce* můžete vytvořit pro všechny senzory, které chcete monitorovat. Podrobnější informace najdete v tématu [Zpracování dat a uživatelem definované funkce](concepts-user-defined-functions.md). 

V ukázkovém souboru *provisionSample.yaml* vyhledejte část začínající typem **userdefinedfunctions** (Uživatelem definované funkce). Tato část zřídí uživatelem definovanou funkci s daným názvem (**Name**), která reaguje na seznam pravidel shody v části **matcherNames**. Všimněte si, že pro uživatelem definovanou funkci můžete v elementu **script** zadat vlastní soubor JavaScriptu. Všimněte si také části **roleassignments**. Tato část uživatelem definované funkci přiřadí roli *Správce prostorů*, která jí umožní přístup k událostem přicházejícím z jakéhokoli zřízeného prostoru. 

1. Nakonfigurujte uživatelem definovanou funkci tak, aby zahrnovala pravidlo shody teploty. Provedete to přidáním následujícího řádku do uzlu `matcherNames` v souboru *provisionSample.yaml* (případně zrušením komentáře u tohoto řádku):

    ```yaml
            - Matcher Temperature
    ```

1. V editoru otevřete soubor **_src\actions\userDefinedFunctions\availability.js_**. Na tento soubor odkazuje element **script** v souboru *provisionSample.yaml*. Uživatelem definovaná funkce v tomto souboru hledá tyto podmínky: v místnosti se nezjistí žádný pohyb a úroveň oxidu uhličitého je nižší než 1 000 ppm. Upravte soubor JavaScriptu tak, aby se společně s dalšími podmínkami monitorovala také teplota. Přidejte následující řádky kódu pro hledání těchto podmínek: v místnosti se nezjistí žádný pohyb, úroveň oxidu uhličitého je nižší než 1 000 ppm a teplota je nižší než 78 stupňů Fahrenheita.

   > [!NOTE]
   > V této části se upravuje soubor *src\actions\userDefinedFunctions\availability.js*, abyste se mohli podrobně seznámit s jedním ze způsobů psaní uživatelem definovaných funkcí. Můžete se však rozhodnout použít ve svém nastavení přímo soubor [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js). Tento soubor obsahuje všechny potřebné změny pro tento kurz. Pokud použijete tento soubor, nezapomeňte použít správný název souboru pro klíč **_script_** v souboru [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    1. Na začátek souboru přidejte pod komentář `// Add your sensor type here` následující řádky pro senzor teploty:

        ```JavaScript
            var temperatureType = "Temperature";
            var temperatureThreshold = 78;
        ```
   
    1. Pod příkaz definující `var motionSensor` a komentář `// Add your sensor variable here` přidejte následující řádky:

        ```JavaScript
            var temperatureSensor = otherSensors.find(function(element) {
                return element.DataType === temperatureType;
            });
        ```
    
    1. Pod příkaz definující `var carbonDioxideValue` a komentář `// Add your sensor latest value here` přidejte následující řádek:

        ```JavaScript
            var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
        ```
    
    1. Odeberte následující řádky kódu pod komentářem `// Modify this line to monitor your sensor value`: 

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
    
    1. Odeberte následující řádky kódu pod komentářem `// Modify these lines as per your sensor`:

        ```JavaScript
            var availableFresh = "Room is available and air is fresh";
            var noAvailableOrFresh = "Room is not available or air quality is poor";
        ```

       Nahraďte je následujícími řádky:

        ```JavaScript
            var alert = "Room with fresh air and comfortable temperature is available.";
            var noAlert = "Either room is occupied, or working conditions are not right.";
        ```
    
    1. Odeberte následující blok kódu *if-else* pod komentářem `// Modify this code block for your sensor`:

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
    
    1. Uložte soubor. 
    
1. Otevřete příkazové okno a přejděte do složky **_occupancy-quickstart\src_**. Spuštěním následujícího příkazu zřiďte graf prostorové inteligence a uživatelem definovanou funkci. 

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Aby se zabránilo neautorizovanému přístupu k vašemu rozhraní API pro správu služby Digital Twins, vyžaduje aplikace **_occupancy-quickstart_**, abyste se přihlásili pomocí přihlašovacích údajů svého účtu Azure. Po krátkou dobu vaše přihlašovací údaje uloží, takže se možná nebudete muset přihlašovat při každém spuštění. Při prvním spuštění tohoto programu, stejně jako v případě vypršení platnosti uložených přihlašovacích údajů, budete přesměrováni na přihlašovací stránku a obdržíte kód specifický pro relaci, který na této stránce zadáte. Podle pokynů se přihlaste pomocí svého účtu Azure.


1. Po ověření vašeho účtu aplikace zahájí vytváření ukázkového prostorového grafu podle konfigurace v souboru *provisionSample.yaml*. Počkejte na dokončení zřizování, které bude trvat několik minut. Po dokončení sledujte zprávy v příkazovém okně a všimněte si, jak se vytváří váš prostorový graf. Všimněte si vytvoření centra IoT v kořenovém uzlu `Venue`. 

1. Z výstupu v příkazovém okně si do schránky zkopírujte hodnotu `ConnectionString` v části `Devices`. Tuto hodnotu budete potřebovat k simulování připojení zařízení v následující části.

    ![Ukázka zřizování](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Pokud se vám během zřizování zobrazí podobná chybová zpráva jako „The I/O operation has been aborted because of either a thread exit or an application request“ (Vstupně-výstupní operace byla přerušena z důvodu ukončení vlákna nebo požadavku aplikace), zkuste příkaz spustit znovu. Důvodem může být vypršení časového limitu klienta HTTP kvůli problému se sítí.

<a id="simulate" />

## <a name="simulate-sensor-data"></a>Simulace dat ze senzorů
V této části budete pomocí projektu *device-connectivity* v ukázce simulovat data ze senzorů pro detekci pohybu, teploty a úrovně oxidu uhličitého. Tento projekt generuje náhodné hodnoty senzorů a s použitím připojovacího řetězce zařízení odesílá je do centra IoT.

1. V samostatném příkazovém okně přejděte k ukázce služby Digital Twins a pak do složky **_device-connectivity_**.

1. Spuštěním tohoto příkazu se ujistěte, že jsou správně závislosti projektu:

    ```cmd/sh
    dotnet restore
    ```

1. V editoru otevřete soubor *appSettings.json* a upravte následující hodnoty:
    1. *DeviceConnectionString:* Přiřaďte hodnotu `ConnectionString` z okna výstupu z předchozí části. Nezapomeňte zkopírovat celý řetězec v uvozovkách, jinak se simulátor nebude moct správně připojit k centru IoT.

    1. *HardwareId* v poli *Sensors*: Vzhledem k tomu, že simulujete události ze senzorů zřízených pro vaši instanci služby Digital Twins, ID hardwaru a názvy senzorů v tomto souboru musí odpovídat uzlu `sensors` v souboru *provisionSample.yaml*. Přidejte novou položku pro senzor teploty. Uzel **Sensors** v souboru *appSettings.json* by měl vypadat nějak takto:

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
   > Vzhledem k tomu, že ukázka simulace nekomunikuje přímo s instancí služby Digital Twins, nevyžaduje ověření.

## <a name="get-results-of-user-defined-function"></a>Získání výsledků uživatelem definované funkce
Uživatelem definovaná funkce se spustí pokaždé, když vaše instance přijme data ze senzorů a zařízení. Tato část se dotazuje vaší instance služby Digital Twins za účelem získání výsledků uživatelem definované funkce. Téměř v reálném čase zjistíte, když bude dostupná místnost s čerstvým vzduchem a správnou teplotou. 

1. Otevřete příkazové okno, které jste použili ke zřízení ukázky, nebo nové příkazové okno a opět přejděte do složky **_occupancy-quickstart\src_** ukázky. 

1. Spusťte následující příkaz a po zobrazení výzvy se přihlaste:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

V okně výstupu se zobrazí spuštění uživatelem definované funkce, která zachycuje události ze simulace zařízení. 

   ![Spuštění uživatelem definované funkce](./media/tutorial-facilities-udf/udf-running.png)

V závislosti na tom, jestli je splněná monitorovaná podmínka, nastaví uživatelem definovaná funkce hodnotu prostoru s odpovídající zprávou (jak jsme viděli [ve výše uvedené části](#udf)), kterou funkce `GetAvailableAndFreshSpaces` vypíše do konzoly. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už službu Azure Digital Twins nechcete dál zkoumat, můžete prostředky vytvořené v rámci tohoto kurzu odstranit:

1. V levé nabídce na webu [Azure Portal](http://portal.azure.com) klikněte na **Všechny prostředky**, vyberte svou skupinu prostředků Digital Twins a **odstraňte** ji.
2. V případě potřeby můžete pokračovat odstraněním ukázkových aplikací i na pracovním počítači. 


## <a name="next-steps"></a>Další kroky

Teď, když jste zřídili prostory a vytvořili architekturu pro aktivaci vlastních oznámení, můžete pokračovat některým z následujících kurzů. 

> [!div class="nextstepaction"]
> [Kurz: Příjem oznámení z prostorů Azure Digital Twins pomocí Logic Apps](tutorial-facilities-events.md)

Nebo:

> [!div class="nextstepaction"]
> [Kurz: Vizualizace a analýza událostí z prostorů Azure Digital Twins s využitím služby Time Series Insights](tutorial-facilities-analyze.md)
