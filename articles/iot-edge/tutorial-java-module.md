---
title: Kurz – vlastní kurz k modulům Java pomocí Azure IoT Edge
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Java a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- mqtt
- devx-track-java
ms.openlocfilehash: 3f24f38db7704557894d866b789890763f9e1316
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463251"
---
# <a name="tutorial-develop-a-java-iot-edge-module-using-linux-containers"></a>Kurz: vývoj modulu Java IoT Edge pomocí kontejnerů Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Použijete simulované IoT Edge zařízení, které jste vytvořili v Azure IoT Edge nasazení na simulovaném zařízení v článcích rychlý Start. V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Použít Visual Studio Code k vytvoření modulu IoT Edge Java na základě balíčku šablon Azure IoT Edge maven a sady SDK pro zařízení Azure IoT Java
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Tento kurz ukazuje, jak vytvořit modul v **jazyce Java** pomocí **Visual Studio Code** a jak ho nasadit do IoT Edgeho zařízení. IoT Edge nepodporuje moduly Java sestavené jako kontejnery Windows.

Následující tabulka vám pomůže pochopit možnosti pro vývoj a nasazování modulů Java:

| Java | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Použití VS Code pro moduly Java v systému Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Použití VS Code pro moduly Java v systému Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

Před zahájením tohoto kurzu byste si měli projít předchozí kurz nastavení vývojového prostředí pro vývoj kontejnerů pro Linux: [vývoj IoT Edgech modulů pro zařízení se systémem Linux](tutorial-develop-for-linux.md). Po dokončení některého z těchto kurzů byste měli mít následující požadavky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* Zařízení se systémem Azure IoT Edge. Pomocí rychlých startů můžete nastavit zařízení se systémem [Linux](quickstart-linux.md) nebo [zařízení s Windows](quickstart.md).
* Registr kontejneru, například [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovaných pomocí [nástrojů Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurovaný pro spouštění kontejnerů Linux.

Pokud chcete vytvořit modul IoT Edge v jazyce Java, nainstalujte do vývojového počítače následující další požadavky:

* [Balíček rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) pro Visual Studio Code
* [Java se Development Kit 11](/azure/developer/java/fundamentals/java-jdk-long-term-support)a [nastavte `JAVA_HOME` proměnnou prostředí](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby odkazovala na instalaci JDK.
* [Maven](https://maven.apache.org/)

   >[!TIP]
   >Instalační procesy Java a Maven přidávají do systému proměnné prostředí. Po dokončení instalace restartujte všechny instance otevřeného Visual Studio Code terminálu, PowerShellu nebo příkazového řádku. Tento krok zajistí, že tyto nástroje můžou rozpoznávat příkazy Java a Maven v popředí.

## <a name="create-a-module-project"></a>Vytvořit projekt modulu

Následující kroky vytvoří projekt IoT Edge modulu, který je založen na balíčku šablon Azure IoT Edge Maven a sadě SDK pro zařízení Java Azure IoT. Projekt se vytváří pomocí Visual Studio Code a nástrojů Azure IoT.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení v jazyce Java, kterou můžete přizpůsobit pomocí vlastního kódu.

1. V Visual Studio Code výběrem **Zobrazit**  >  **paleta příkazů** otevřete paletu příkazů vs Code.

2. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vybrat složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název vašeho řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Vyberte **modul Java**. |
   | Zadejte název modulu | Pojmenujte modul **JavaModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Vaše image kontejneru se předem vyplní názvem, který jste zadali v posledním kroku. Položku **localhost: 5000** nahraďte hodnotou **přihlašovacího serveru** z služby Azure Container Registry. Přihlašovací server můžete načíst ze stránky přehled v registru kontejneru v Azure Portal. <br><br>Konečné úložiště imagí vypadá jako \<registry name\> . azurecr.IO/javamodule. |
   | Zadejte hodnotu pro ID skupiny. | Zadejte hodnotu ID skupiny nebo přijměte výchozí **com. edgemodule**. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-java-module/repository.png)

Pokud vytváříte modul Java poprvé, může trvat několik minut, než se stáhnou balíčky Maven. Až bude řešení připravené, okno VS Code načte pracovní prostor řešení IoT Edge. Pracovní prostor řešení obsahuje pět součástí nejvyšší úrovně:

* Složka **moduly** obsahuje kód Java pro váš modul a soubory Docker pro sestavení modulu jako image kontejneru.
* Soubor **\. ENV** obsahuje přihlašovací údaje registru kontejneru.
* Soubor **deployment.template.json** obsahuje informace, které modul runtime IoT Edge používá k nasazení modulů do zařízení.
* **deployment.debug.template.jsv** kontejneru souboru je ladicí verze modulů.
* V tomto kurzu nebudete upravovat složku **\. VSCode** ani soubor **\. gitignore** .

Pokud jste při vytváření řešení nezadali registr kontejneru, ale přijali jste výchozí hodnotu localhost:5000, nebudete mít soubor \.env.

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

Rozšíření IoT Edge se pokusí načíst přihlašovací údaje registru kontejneru z Azure a naplnit je do souboru prostředí. Zkontrolujte, jestli jsou vaše přihlašovací údaje už zahrnuté. Pokud ne, přidejte je nyní:

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte cílovou architekturu.

V současné době Visual Studio Code může vyvíjet moduly Java pro zařízení se systémem Linux AMD64 a Linux ARM32v7. Musíte vybrat architekturu, kterou cílíte na každé řešení, protože kontejner je sestavený a pro každý typ architektury funguje jinak. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro řešení Edge** nebo vyberte ikonu zástupce na bočním panelu v dolní části okna.

2. V paletě příkazů vyberte v seznamu možností cílovou architekturu. Pro tento kurz používáme virtuální počítač s Ubuntu jako zařízení IoT Edge, takže se zachová výchozí hodnota **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V Průzkumníku vs Code otevřete **moduly**  >  **JavaModule**  >  **Src**  >  **Main**  >  **Java**  >    >  **edgemodule**  >  **App. Java**.

2. Na začátek souboru přidejte následující kód k importování nových odkazovaných tříd.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;

    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;

    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. Přidejte následující definici do třídy **App**. Tato proměnná nastaví prahovou hodnotu teploty. Měřená teplota počítače se nebude hlásit IoT Hub až do chvíle, než se překročí tato hodnota.

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. Nahraďte metodu spuštění **MessageCallbackMqtt** následujícím kódem. Tato metoda se volá pokaždé, když modul dostane zprávu MQTT z IoT Edge Hubu. Odfiltruje zprávy, které hlásí nižší teploty, než je prahová hodnota nastavená ve dvojčeti modulu.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;

            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. Přidejte následující dvě statické vnitřní třídy do třídy **App**. Tyto třídy aktualizují proměnnou tempThreshold v případě, že se změní požadovaná vlastnost modulu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }

    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. Přidejte následující řádky do metody **main** za **client.open()** k odběru aktualizací dvojčete modulu.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. Uložte soubor App. Java.

8. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge.

9. Přidejte do manifestu nasazení dvojče modulu **JavaModule**. Vložte následující obsah JSON do dolní části oddílu **moduleContent** za dvojče modulu **$edgeHub**:

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Přidat nevlákenný modul do šablony nasazení](./media/tutorial-java-module/module-twin.png)

10. Uložte soubor deployment.template.json.

## <a name="build-and-push-your-module"></a>Sestavení a vložení modulu

V předchozí části jste vytvořili řešení IoT Edge a Přidali jste do **JavaModule** kód pro odfiltrování zpráv, u kterých se teplota hlášeného počítače nachází pod přípustným limitem. Nyní Sestavte řešení jako image kontejneru a nahrajte ho do registru kontejneru.

1. Otevřete vs Code Integrated Terminal výběrem možnosti **Zobrazit**  >  **terminál**.

2. Přihlaste se k Docker zadáním následujícího příkazu v terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru ze služby Azure Container Registry. Tyto hodnoty můžete načíst z oddílu **přístupové klíče** v registru v Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití nástroje `--password-stdin` . I když se tento osvědčený postup doporučuje u produkčních scénářů, je mimo rozsah tohoto kurzu. Další informace najdete v tématu přihlašovací Reference k [Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

3. V Průzkumníku VS Code klikněte pravým tlačítkem na **deployment.template.jsna** soubor a vyberte **sestavení a nabízené IoT Edge řešení**.

   Příkaz Build a push spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config** , která obsahuje úplný manifest nasazení, který vychází z informací v šabloně nasazení a dalších souborů řešení. Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. Pak se spustí a nahraje `docker push` úložiště imagí do registru kontejneru.

   Tento proces může trvat několik minut poprvé, ale při příštím spuštění příkazů je rychlejší.

## <a name="deploy-modules-to-device"></a>Nasadit moduly do zařízení

K nasazení projektu modulu do zařízení IoT Edge použijte Průzkumníka Visual Studio Code a rozšíření Azure IoT Tools. Už máte připravený manifest nasazení pro váš scénář, **deployment.amd64.js** v souboru ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že je zařízení IoT Edge spuštěné.

1. V Průzkumníkovi Visual Studio Code v části **Azure IoT Hub** rozbalte **zařízení** , abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte **deployment.amd64.js** v souboru ve složce **config** a pak klikněte na **Vybrat manifest nasazení Edge**. Nepoužívejte soubor deployment.template.json.

4. V části zařízení rozbalte **moduly** a zobrazte seznam nasazených a spuštěných modulů. Klikněte na tlačítko pro obnovení. Měl by se zobrazit nový **JavaModule** spuštěný spolu s modulem **SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.  

    Spuštění modulů může trvat několik minut. Modul runtime IoT Edge musí přijmout nový manifest nasazení, Stáhnout image modulu z modulu runtime kontejneru a pak začít každý nový modul.

## <a name="view-the-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem myši na název vašeho zařízení IoT Edge a vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

2. Zobrazení zpráv přicházejících do IoT Hub. Doručení zpráv může chvíli trvat. Zařízení IoT Edge musí přijmout nové nasazení a spustit všechny moduly. Změny, které jsme provedli v kódu JavaModule, čekají, dokud teplota počítače nedosáhne 25 stupňů před odesláním zpráv. Přidá také **výstrahu** typu zpráva pro všechny zprávy, které dosáhnou prahové hodnoty teploty.

## <a name="edit-the-module-twin"></a>Upravit nevlákenný modul

V manifestu nasazení jsme použili vláken Module JavaModule, které nastaví prahovou hodnotu teploty na 25 stupních. Chcete-li změnit funkčnost, aniž byste museli aktualizovat kód modulu, můžete použít vlákna modulu.

1. V Visual Studio Code rozbalte podrobnosti pod zařízením IoT Edge a podívejte se na běžící moduly.

2. Klikněte pravým tlačítkem na **JavaModule** a vyberte **Upravit modul s dvojitou** čárkou.

3. V požadovaných vlastnostech vyhledejte **TemperatureThreshold** . Změňte jeho hodnotu na novou teplotu 5 stupňů na 10 stupňů vyšší než při nejnovější hlášené teplotě.

4. Uložte modul s dvojitým pracovním souborem.

5. Klikněte pravým tlačítkem na libovolné místo v podoknì s dvojitou úpravou modulu a vyberte **aktualizovat modul vlákna**.

6. Umožňuje monitorovat příchozí zprávy ze zařízení do cloudu. Měli byste vidět, že se zprávy zastavily, dokud se nedosáhne nové prahové hodnoty teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge, který filtruje nezpracovaná data generovaná zařízením IoT Edge.

V dalších kurzech se dozvíte, jak Azure IoT Edge pomáhá nasadit Azure Cloud Services pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)
