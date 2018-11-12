---
title: Kurz k Azure IoT Edge Java | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Java a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: ed0605fbab4be0e0eb960b3b840e72f5fba2e8c8
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413770"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>Kurz: Vývoj modulu IoT Edge v jazyce Java a jeho nasazení na simulované zařízení

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech o nasazení Azure IoT Edge na simulované zařízení ve [Windows](quickstart.md) nebo [Linuxu](quickstart-linux.md). V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Použít Visual Studio Code k vytvoření modulu IoT Edge Java na základě balíčku šablon Azure IoT Edge maven a sady SDK pro zařízení Azure IoT Java
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Jako hraniční zařízení můžete použít svůj vývojový počítač nebo virtuální počítač podle postupu v rychlém startu pro [Linux](quickstart-linux.md).
* Moduly Java pro IoT Edge nepodporují zařízení s Windows.

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure. 

Prostředky pro vývoj:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Balíček rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) pro Visual Studio Code
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pro Visual Studio Code. 
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) s [nastavením proměnné prostředí `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby odkazovala na vaši instalaci JDK
* [Maven](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/).
   * Pokud vyvíjíte na zařízení s Windows, ujistěte se, že je Docker [nakonfigurovaný na používání kontejnerů Linuxu](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 


## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů
V tomto kurzu pomocí rozšíření Azure IoT Edge pro VS Code sestavíte modul a ze souborů vytvoříte **image kontejneru**. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro účely tohoto kurzu můžete použít jakýkoli registr kompatibilní s Dockerem. V cloudu jsou k dispozici dvě oblíbené služby registrů Dockeru – [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Azure Container Registry**.
2. Zadejte název registru, zvolte předplatné a skupinu prostředků a nastavte skladovou položku na **Basic**. 
3. Vyberte **Vytvořit**.
4. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 
5. Přepněte přepínač **Uživatel s rolí správce** na **Povolit**.
6. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v dalších částech kurzu k publikování image Dockeru do registru a k přidání přihlašovacích údajů registru do modulu runtime Azure IoT Edge. 

## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
V následujících krocích vytvoříte projekt modulu IoT Edge, který je založený na balíčku šablon Azure IoT Edge maven a sadě SDK zařízení Azure IoT Java. Použijete k tomu Visual Studio Code a rozšíření Azure IoT Edge.

### <a name="create-a-new-solution"></a>Vytvoření nového řešení

Vytvořte šablonu řešení v jazyce Java, kterou můžete přizpůsobit pomocí vlastního kódu. 

1. V nástroji Visual Studio Code zvolte **View** (Zobrazit)  > **Command Palette** (Paleta příkazů). Otevře se paleta příkazů VS Code. 

2. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení: 

   1. Vyberte složku, ve které chcete vytvořit řešení. 
   2. Zadejte název pro vaše řešení nebo přijměte výchozí název **EdgeSolution**.
   3. Jako šablonu modulu zvolte **Java Module**. 
   4. Zadejte hodnotu pro groupId nebo přijměte výchozí **com.edgemodule**.
   5. Nahraďte výchozí název modulu názvem **JavaModule**. 
   6. Jako úložiště imagí pro první modul určete registr kontejneru Azure, který jste vytvořili v předchozí části. Nahraďte **localhost:5000** hodnotou pro přihlašovací server, kterou jste zkopírovali. Konečný řetězec vypadá takto: \<název registru\>.azurecr.io/javamodule.


Pokud modul Java vytváříte poprvé, může stažení balíčků maven trvat několik minut. Pak se v okně nástroje VS Code načte pracovní prostor řešení IoT Edge. Pracovní prostor řešení obsahuje pět komponent nejvyšší úrovně. Složku **\.vscode** ani soubor **\.gitignore** v tomto kurzu upravovat nebudete. Složka **modules** obsahuje kód jazyka Java pro váš modul a také soubory Dockerfile pro sestavení modulu jako image kontejneru. V souboru **\.env** jsou uložené přihlašovací údaje k vašemu registru kontejneru. Soubor **deployment.template.json** obsahuje informace, které modul runtime IoT Edge používá k nasazení modulů do zařízení. 

Pokud jste při vytváření řešení nezadali registr kontejneru, ale přijali jste výchozí hodnotu localhost:5000, nebudete mít soubor \.env. 

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. 

1. V průzkumníku VS Code otevřete soubor .env. 
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure. 
3. Soubor uložte. 

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V průzkumníku VS Code otevřete **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

5. Na začátek souboru přidejte následující kód k importování nových odkazovaných tříd.

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

5. Přidejte následující definici do třídy **App**. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. Nahraďte metodu spuštění **MessageCallbackMqtt** následujícím kódem. Tato metoda se volá pokaždé, když modul dostane zprávu MQTT z IoT Edge Hubu. Odfiltruje zprávy, které hlásí nižší teploty, než je prahová hodnota nastavená ve dvojčeti modulu.

    ```java
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
    ```

8. Přidejte následující dvě statické vnitřní třídy do třídy **App**. Tyto třídy přijímají aktualizace požadovaných vlastností z dvojčete modulu a aktualizují proměnnou **tempThreshold** na stejnou hodnotu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

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

9. Přidejte následující řádky do metody **main** za **client.open()** k odběru aktualizací dvojčete modulu.

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

11. Soubor uložte.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozí části jste vytvořili řešení IoT a do modulu **JavaModule** jste přidali kód, který odfiltruje zprávy, ve kterých je hlášená teplota počítače nižší než přípustná mezní hodnota. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru. 

1. Zadáním následujícího příkazu v integrovaném terminálu editoru Visual Studio Code se přihlaste k Dockeru. Potom můžete odeslat image modulu do služby Azure Container Registry.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Použijte uživatelské jméno, heslo a přihlašovací server zkopírované ze služby Azure Container Registry v první části. Tyto hodnoty můžete také načíst z oddílu **Přístupové klíče** v registru na webu Azure Portal.

2. V průzkumníku VS Code otevřete soubor deployment.template.json v pracovním prostoru řešení IoT Edge. Tento soubor sděluje modulu **$edgeAgent**, že má nasadit dva moduly: **tempSensor** a **JavaModule**. Hodnota **JavaModule.image** je nastavená na verzi image Linux amd64. Změňte tuto verze image na **arm32v7**, pokud je to architektura vašeho zařízení IoT Edge. 

   Zkontrolujte, jestli má šablona správný název modulu, ne výchozí název **SampleModule**, který jste změnili při vytváření řešení IoT Edge.

   Další informace o manifestech nasazení najdete ve [vysvětlení, jak lze moduly IoT Edge používat, konfigurovat a opětovně používat](module-composition.md).

3. V souboru deployment.template.json se nachází oddíl **registryCredentials**, do kterého se ukládají přihlašovací údaje registru Dockeru. Skutečná uživatelská jména a hesla se ukládají do souboru .env, který git ignoruje.  

4. Přidejte do manifestu nasazení dvojče modulu **JavaModule**. Vložte následující obsah JSON do dolní části oddílu **moduleContent** za dvojče modulu **$edgeHub**: 
    ```json
        "JavaModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Soubor uložte.

5. V průzkumníku VS Code klikněte pravým tlačítkem na soubor deployment.template.json a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge). 

Když editoru Visual Studio Code sdělíte, že má sestavit vaše řešení, nejdříve se načtou informace ze šablony nasazení a v nové složce s názvem **config** se vygeneruje soubor deployment.json. Pak se v integrovaném terminálu spustí dva příkazy: `docker build` a `docker push`. Tyto dva příkazy sestaví kód, provedou kontejnerizaci aplikace Java a kód odešlou do kontejneru registru, který jste zadali při inicializaci řešení. 

Úplnou adresu image kontejneru se značkou můžete vidět v integrovaném terminálu VS Code. Adresa image je sestavená z informací v souboru module.json a má formát \<úložiště\>:\<verze\>-\<platforma\>. V tomto kurzu by měla vypadat takto: název_registru.azurecr.io/javamodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

V článku Rychlý start, pomocí kterého jste nastavili své zařízení IoT Edge, jste nasadili modul pomocí webu Azure Portal. Moduly můžete nasazovat také pomocí rozšíření Azure IoT Toolkit pro Visual Studio Code. Pro svůj scénář už máte připravený manifest nasazení – soubor **deployment.json**. Teď stačí jen vybrat zařízení, na které se nasazení provede.

1. Na paletě příkazů VS Code spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.

2. Na paletě příkazů VS Code spusťte **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Vybrat IoT Hub). 

3. Zvolte předplatné a centrum IoT obsahující zařízení IoT Edge, které chcete nakonfigurovat. 

4. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 

5. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení). 

   ![Vytvoření nasazení pro jedno zařízení](./media/tutorial-java-module/create-deployment.png)

6. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json. 

7. Klikněte na tlačítko pro obnovení. Měl by se zobrazit spuštěný nový modul **JavaModule** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**.  

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí. 

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů. 

Na samotném zařízení IoT Edge můžete stav modulů nasazení zobrazit pomocí příkazu `iotedge list`. Měly by se zobrazit čtyři moduly: dva moduly runtime IoT Edge, tempSensor a vlastní modul, který jste vytvořili v tomto kurzu. Spuštění všech modulů může několik minut trvat, proto příkaz spusťte znovu, pokud se zpočátku všechny nezobrazí. 

Pokud chcete zobrazit zprávy, které jednotlivé moduly generují, použijte příkaz `iotedge logs <module name>`. 

Zprávy přicházející do centra IoT můžete zobrazit pomocí Visual Studio Code. 

1. Když chcete monitorovat data, která přichází do služby IoT Hub, vyberte tři tečky (**...**) a potom vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
2. Pokud chcete monitorovat zprávy D2C pro konkrétní zařízení, klikněte pravým tlačítkem na příslušné zařízení v seznamu a vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
3. Když chcete monitorování dat zastavit, spusťte na paletě příkazů příkaz **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Zastavit monitorování zpráv D2C). 
4. Pokud chcete zobrazit nebo aktualizovat dvojče modulu, klikněte pravým tlačítkem na příslušný modul v seznamu a vyberte **Edit module twin** (Upravit dvojče modulu). Pokud chcete aktualizovat dvojče modulu, uložte soubor JSON dvojčete, klikněte pravým tlačítkem na oblast editoru a vyberte **Update Module Twin** (Aktualizovat dvojče modulu).
5. Pokud chcete zobrazit protokoly Dockeru, nainstalujte [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pro VS Code. Spuštěné moduly můžete vyhledat místně v průzkumníku Dockeru. Výběrem možnosti **Show Logs** (Zobrazit protokoly) v místní nabídce je zobrazíte protokoly v integrovaném terminálu.
 
## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Pokračujte dalšími kurzy, ve kterých se naučíte další způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Uložení dat na hraničních zařízeních s využitím databází SQL Serveru](tutorial-store-data-sql-server.md)

