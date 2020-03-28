---
title: Kurz – kurz vlastního modulu Java pomocí Azure IoT Edge
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Java a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 228e50160e5c13b2d24a504b02c4bb7e3a420a46
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76772916"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Kurz: Vývoj modulu Java IoT Edge pro linuxová zařízení

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v nasazení Azure IoT Edge na simulovaném zařízení v rychlém startu [Linuxu.](quickstart-linux.md) V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Použít Visual Studio Code k vytvoření modulu IoT Edge Java na základě balíčku šablon Azure IoT Edge maven a sady SDK pro zařízení Azure IoT Java
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Obor řešení

Tento kurz ukazuje, jak vyvíjet modul v **jazyce Java** pomocí **kódu Sady Visual Studio**a jak jej nasadit do zařízení S **IP**. IoT Edge nepodporuje java moduly pro zařízení se systémem Windows.

V následující tabulce můžete pochopit možnosti vývoje a nasazování modulů Java:

| Java | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Použití kódu VS pro moduly Java v Linuxu AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Použití VS Code pro moduly Java na Linuxu ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste měli projít předchozí kurz nastavení vývojového prostředí pro vývoj kontejnerů Linux: [Vývoj modulů IoT Edge pro zařízení s Linuxem](tutorial-develop-for-linux.md). Dokončením některého z těchto výukových programů byste měli mít následující předpoklady:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Linuxové zařízení se systémem Azure IoT Edge](quickstart-linux.md)
* Registr kontejnerů, jako je [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Kód](https://code.visualstudio.com/) nakonfigurovaný pomocí [nástrojů Azure IoT .](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
* [Docker CE](https://docs.docker.com/install/) nakonfigurován pro spouštění kontejnerů Linuxu.

Chcete-li vyvinout modul IoT Edge v jazyce Java, nainstalujte do vývojového počítače následující další předpoklady: 

* [Balíček rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) pro Visual Studio Code
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) s [nastavením proměnné prostředí `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby odkazovala na vaši instalaci JDK
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Vytvoření projektu modulu

Následující kroky vytvoří projekt modulu IoT Edge, který je založený na balíčku šablony Azure IoT Edge a sadě Azure IoT Java device SDK. Projekt vytvoříte pomocí kódu Visual Studio a nástrojů Azure IoT.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení v jazyce Java, kterou můžete přizpůsobit pomocí vlastního kódu.

1. V kódu Visual Studia vyberte **Zobrazit** > **paletu příkazů** a otevřete paletu příkazů VS Code.

2. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název řešení nebo přijměte výchozí **edgesolution**. |
   | Vyberte šablonu modulu | Zvolte **Java Modul**. |
   | Poskytnout hodnotu pro groupId | Zadejte hodnotu ID skupiny nebo přijměte výchozí **modul com.edgemodule**. |
   | Zadejte název modulu | Pojmenujte svůj modul **JavaModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněna z názvu, který jste zadali v posledním kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>Konečné úložiště bitových \<obrazů\>vypadá jako název registru .azurecr.io/javamodule. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-java-module/repository.png)

Pokud jste poprvé, co vytváříte java modul, může trvat několik minut, než si stáhnete balíčky maven. Když je řešení připravené, okno VS Code načte pracovní prostor řešení IoT Edge. Pracovní prostor řešení obsahuje pět komponent nejvyšší úrovně:

* Složka **modulů** obsahuje kód Java pro váš modul a soubory Dockeru pro vytvoření modulu jako image kontejneru.
* Soubor ** \.env** ukládá pověření registru kontejneru.
* Soubor **deployment.template.json** obsahuje informace, které modul runtime IoT Edge používá k nasazení modulů do zařízení.
* **Deployment.debug.template.json kontejnery** ladicí verze modulů.
* V tomto kurzu nebudete upravovat složku ** \.vscode** ani ** \.soubor gitignore.**

Pokud jste při vytváření řešení nezadali registr kontejneru, ale přijali jste výchozí hodnotu localhost:5000, nebudete mít soubor \.env.

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte si cílovou architekturu

V současné době visual studio kód může vyvíjet java moduly pro Linux AMD64 a Linux ARM32v7 zařízení. Musíte vybrat architekturu, na kterou cílíte s každým řešením, protože kontejner je sestaven a spuštěn jinak pro každý typ architektury. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro edge řešení**nebo vyberte ikonu zástupce v bočním panelu v dolní části okna.

2. V paletě příkazů vyberte cílovou architekturu ze seznamu možností. Pro účely tohoto kurzu používáme jako zařízení IoT Edge virtuální stroj Ubuntu, takže zachováme výchozí **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V Průzkumníku kódu VS, otevřené **moduly** > **JavaModule** > **src** > **hlavní** > **java** > **com** > **edgemodule** > **App.java**.

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

3. Přidejte následující definici do třídy **App**. Tato proměnná nastavuje teplotní práh. Naměřená teplota stroje nebude hlášena do služby IoT Hub, dokud nepřejde nad tuto hodnotu.

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

5. Přidejte následující dvě statické vnitřní třídy do třídy **App**. Tyto třídy aktualizovat tempThreshold proměnnou při změně požadované vlastnosti dvojčete modulu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

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

7. Uložte soubor App.java.

8. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge.

9. Přidejte do manifestu nasazení dvojče modulu **JavaModule**. Vložte následující obsah JSON do dolní části oddílu **moduleContent** za dvojče modulu **$edgeHub**:

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Přidání dvojčete modulu do šablony nasazení](./media/tutorial-java-module/module-twin.png)

10. Uložte soubor deployment.template.json.

## <a name="build-and-push-your-module"></a>Sestavte a tlačte svůj modul

V předchozí části jste vytvořili řešení IoT Edge a přidali kód do **Modulu JavaModul,** abyste odfiltrovat zprávy, kde je hlášená teplota počítače pod přijatelný limit. Nyní vytvořte řešení jako image kontejneru a převejít te do registru kontejnerů.

1. Otevřete integrovaný terminál VS Code výběrem **možnosti Zobrazit** > **terminál**.

1. Přihlaste se k Dockeru zadáním následujícího příkazu do terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru z registru kontejnerů Azure. Tyto hodnoty můžete načíst z části **Přístupové klíče** v registru na webu Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití programu `--password-stdin`. Zatímco tento osvědčený postup se doporučuje pro produkční scénáře, je mimo rozsah tohoto kurzu. Další informace naleznete v odkazu na [přihlášení dockeru.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. V průzkumníku kódu VS klikněte pravým tlačítkem myši na soubor **deployment.template.json** a vyberte **možnost Sestavit a push řešení IoT Edge**.

   Příkaz sestavení a nabízení spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config,** která obsahuje úplný manifest nasazení, který je sestaven z informací v šabloně nasazení a dalších souborech řešení. Za druhé `docker build` spustí k vytvoření image kontejneru na základě příslušné dockerfile pro cílovou architekturu. Potom spustí `docker push` k nabízení úložiště bitové kopie do registru kontejneru.

## <a name="deploy-modules-to-device"></a>Nasazení modulů do zařízení

Pomocí průzkumníka kódu Visual Studio a rozšíření Nástroje Azure IoT k nasazení projektu modulu do zařízení IoT Edge. Již máte manifest nasazení připravený pro váš scénář, soubor **deployment.json** ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že vaše zařízení IoT Edge je v provozu.

1. V průzkumníku kódu Visual Studia rozbalte část **Zařízení služby Azure IoT Hub,** abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json.

4. Klikněte na tlačítko pro obnovení. Měli byste vidět nový **JavaModule** běží spolu s **Modul SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.  

## <a name="view-the-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. V průzkumníku kódu Visual Studia klikněte pravým tlačítkem myši na název zařízení IoT Edge a vyberte **spustit sledování integrovaného koncového bodu událostí**.

2. Zobrazení zpráv přicházejících do vašeho IoT Hubu. Může chvíli trvat, než zprávy dorazí. Zařízení IoT Edge musí přijmout nové nasazení a spustit všechny moduly. Změny, které jsme provedli v kódu JavaModule, pak počkejte, až teplota stroje dosáhne 25 stupňů před odesláním zpráv. Přidá také typ zprávy **Výstraha** všechny zprávy, které dosáhnou této prahové hodnoty teploty.

## <a name="edit-the-module-twin"></a>Úprava dvojčete modulu

Použili jsme dvojče modulu JavaModule v manifestu nasazení, abychom nastavili teplotní práh na 25 stupňů. Dvojče modulu můžete použít ke změně funkce bez nutnosti aktualizace kódu modulu.

1. V kódu Visual Studio rozbalte podrobnosti v zařízení IoT Edge a podívejte se na spuštěné moduly.

2. Klepněte pravým tlačítkem myši na **položku JavaModule** a vyberte příkaz **Upravit dvojče modulu**.

3. Najít **TemperatureThreshold** v požadovaných vlastnostech. Změňte jeho hodnotu na novou teplotu o 5 stupňů až 10 stupňů vyšší než poslední hlášená teplota.

4. Uložte soubor dvojčete modulu.

5. Klepněte pravým tlačítkem myši na libovolné místo v podokně úprav dvojčete modulu a vyberte **možnost Aktualizovat dvojče modulu**.

6. Sledujte příchozí zprávy mezi zařízeními a cloudy. Měli byste vidět zprávy zastavit, dokud není dosaženo nového prahu teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge, který filtruje nezpracovaná data generovaná vaším zařízením IoT Edge. Až budete připraveni vytvářet vlastní moduly, můžete se dozvědět více o [vývoji modulů IoT Edge](module-development.md) nebo o [vývoji modulů pomocí kódu Visual Studio](how-to-vs-code-develop-module.md). Příklady kódu, včetně modulu simulované teploty, najdete [v tématu ukázky modulu IoT Edge.](https://github.com/Azure/iotedge/tree/master/edge-modules)

Pokračujte k dalším kurzům a zjistěte, jak vám Azure IoT Edge pomáhá nasadit cloudové služby Azure ke zpracování a analýze dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Vlastní Vision Service](tutorial-deploy-custom-vision.md)
