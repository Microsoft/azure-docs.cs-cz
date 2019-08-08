---
title: Kurz k vlastnímu modulu Java – Azure IoT Edge | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Java a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 1aebe97b27902d37587ec6ac7009a5076b732f0f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840129"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Kurz: Vývoj modulu Java IoT Edge pro zařízení se systémem Linux

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Použijete simulované IoT Edge zařízení, které jste vytvořili v nasazení Azure IoT Edge na simulovaném zařízení v rychlém startu pro [Linux](quickstart-linux.md) . V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Použít Visual Studio Code k vytvoření modulu IoT Edge Java na základě balíčku šablon Azure IoT Edge maven a sady SDK pro zařízení Azure IoT Java
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Obor řešení

Tento kurz ukazuje, jak vytvořit modul v **jazyce Java** pomocí **Visual Studio Code**a jak ho nasadit na **zařízení se systémem Linux**. IoT Edge nepodporuje moduly Java pro zařízení s Windows. 

Následující tabulka vám pomůže pochopit možnosti pro vývoj a nasazování modulů Java: 

| Java | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Použití VS Code pro moduly Java v systému Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Použití VS Code pro moduly Java v systému Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste si měli projít předchozí kurz pro nastavení vývojového prostředí pro vývoj kontejnerů pro Linux: [Vývoj IoT Edgech modulů pro zařízení se systémem Linux](tutorial-develop-for-linux.md). Po dokončení některého z těchto kurzů byste měli mít následující požadavky: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Zařízení se systémem Linux se spuštěným Azure IoT Edge](quickstart-linux.md)
* Registr kontejneru, například [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovaných pomocí [nástrojů Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurovaný pro spouštění kontejnerů Linux.

Pokud chcete vytvořit modul IoT Edge v jazyce Java, nainstalujte do vývojového počítače následující další požadavky: 

* [Balíček rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) pro Visual Studio Code
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) s [nastavením proměnné prostředí `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby odkazovala na vaši instalaci JDK
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Vytvořit projekt modulu
Následující kroky vytvoří projekt IoT Edge modulu, který je založen na balíčku šablon Azure IoT Edge Maven a sadě SDK pro zařízení Java Azure IoT. Projekt se vytváří pomocí Visual Studio Code a nástrojů Azure IoT.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení v jazyce Java, kterou můžete přizpůsobit pomocí vlastního kódu. 

1. V nástroji Visual Studio Code zvolte **View** (Zobrazit)  > **Command Palette** (Paleta příkazů). Otevře se paleta příkazů VS Code. 

2. V paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení**IoT Edge. Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Zvolte **modul Java**. |
   | Zadejte hodnotu pro ID skupiny | Zadejte hodnotu ID skupiny nebo přijměte výchozí **com.edgemodule**. |
   | Zadejte název modulu | Název modulu **JavaModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Vaše image kontejneru se předem vyplní názvem, který jste zadali v posledním kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>Finální úložiště imagí vypadá jako \<název\>registru. azurecr.IO/javamodule. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-java-module/repository.png)
   
Pokud modul Java vytváříte poprvé, může stažení balíčků maven trvat několik minut. Pak se v okně nástroje VS Code načte pracovní prostor řešení IoT Edge. Pracovní prostor řešení obsahuje pět komponent nejvyšší úrovně. Složka **modulů** obsahuje kód Java pro váš modul a také soubory Docker pro sestavení modulu jako image kontejneru. V souboru **\.env** jsou uložené přihlašovací údaje k vašemu registru kontejneru. Soubor **deployment.template.json** obsahuje informace, které modul runtime IoT Edge používá k nasazení modulů do zařízení. A **deployment.debug.template.json** souboru kontejnery ladicí verzi modulů. Složku **\.vscode** ani soubor **\.gitignore** v tomto kurzu upravovat nebudete. 

Pokud jste při vytváření řešení nezadali registr kontejneru, ale přijali jste výchozí hodnotu localhost:5000, nebudete mít soubor \.env. 

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. 

1. V průzkumníku VS Code otevřete soubor .env. 
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure. 
3. Soubor uložte. 

### <a name="select-your-target-architecture"></a>Vyberte cílovou architekturu.

V současné době Visual Studio Code může vyvíjet moduly Java pro zařízení se systémem Linux AMD64 a Linux ARM32v7. Musíte vybrat architekturu, kterou cílíte na každé řešení, protože kontejner je sestavený a pro každý typ architektury funguje jinak. Výchozí hodnota je Linux AMD64. 

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro řešení**Edge nebo na bočním panelu v dolní části okna vyberte ikonu zástupce. 

2. V paletě příkazů vyberte v seznamu možností cílovou architekturu. Pro tento kurz používáme virtuální počítač s Ubuntu jako zařízení IoT Edge, takže se zachová výchozí hodnota **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V průzkumníku VS Code otevřete **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

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

   ![Dvojče zařízení přidat do šablony nasazení](./media/tutorial-java-module/module-twin.png)

10. Uložte soubor Deployment. template. JSON.

## <a name="build-and-push-your-module"></a>Sestavení a vložení modulu

V předchozí části jste vytvořili řešení IoT Edge a Přidali jste do **JavaModule** kód pro odfiltrování zpráv, u kterých se teplota hlášeného počítače nachází pod přípustným limitem. Nyní Sestavte řešení jako image kontejneru a nahrajte ho do registru kontejneru. 

1. Výběrem **View** (Zobrazit) > **Terminal** (Terminál) otevřete integrovaný terminál VS Code.

1. Přihlaste se k Docker zadáním následujícího příkazu v terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru ze služby Azure Container Registry. Tyto hodnoty můžete načíst z oddílu **přístupové klíče** v registru v Azure Portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití `--password-stdin`nástroje. I když se tento osvědčený postup doporučuje u produkčních scénářů, je mimo rozsah tohoto kurzu. Další informace najdete v tématu [přihlašovací](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) Reference k Docker.

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge).

   Příkaz Build a push spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config** , která obsahuje úplný manifest nasazení, a vyplní informace v šabloně nasazení a dalších souborech řešení. Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. Pak se spustí a `docker push` nahraje úložiště imagí do registru kontejneru.

## <a name="deploy-modules-to-device"></a>Nasadit moduly do zařízení

K nasazení projektu modulu do zařízení IoT Edge použijte Průzkumníka Visual Studio Code a rozšíření Azure IoT Tools. Již máte připravený manifest nasazení pro váš scénář, soubor **Deployment. JSON** ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že je zařízení IoT Edge spuštěné.

1. V Průzkumníkovi Visual Studio Code rozbalte část **zařízení Azure IoT Hub** a podívejte se na seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json.

4. Klikněte na tlačítko pro obnovení. Měl by se zobrazit nový **JavaModule** spuštěný spolu s modulem **SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.  

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem myši na název vašeho zařízení IoT Edge a vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

2. Zobrazení zpráv přicházejících do IoT Hub. Doručení zpráv může chvíli trvat, protože IoT Edge zařízení musí přijmout nové nasazení a spustit všechny moduly. Změny, které jsme provedli v kódu JavaModule, čekají, dokud teplota počítače nedosáhne 25 stupňů před odesláním zpráv. Přidá také výstrahu typu zpráva pro všechny zprávy, které dosáhnou prahové hodnoty teploty. 

## <a name="edit-the-module-twin"></a>Upravit nevlákenný modul

V manifestu nasazení jsme použili vláken Module JavaModule, které nastaví prahovou hodnotu teploty na 25 stupních. Chcete-li změnit funkčnost, aniž byste museli aktualizovat kód modulu, můžete použít vlákna modulu.

1. V Visual Studio Code rozbalte podrobnosti pod zařízením IoT Edge a podívejte se na běžící moduly. 

2. Klikněte pravým tlačítkem na **JavaModule** a vyberte **Upravit modul**s dvojitou čárkou. 

3. V požadovaných vlastnostech vyhledejte **TemperatureThreshold** . Změňte jeho hodnotu na novou teplotu 5 stupňů na 10 stupňů vyšší než při nejnovější hlášené teplotě. 

4. Uložte modul s dvojitým pracovním souborem.

5. Klikněte pravým tlačítkem na libovolné místo v podoknì s dvojitou úpravou modulu a vyberte **aktualizovat modul vlákna**. 

6. Umožňuje monitorovat příchozí zprávy ze zařízení do cloudu. Měli byste vidět, že se zprávy zastavily, dokud se nedosáhne nové prahové hodnoty teploty. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete připraveni vytvořit vlastní moduly, můžete získat další informace o [vývoji vlastních modulů IoT Edge](module-development.md) nebo o [vývoji modulů pomocí Visual Studio Code](how-to-vs-code-develop-module.md). V dalších kurzech můžete pokračovat a zjistit, jak vám Azure IoT Edge může pomáhat s nasazením cloudových služeb Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)[](tutorial-deploy-custom-vision.md) [Machine Learning](tutorial-deploy-machine-learning.md)Custom Vision Service
> 
> 
