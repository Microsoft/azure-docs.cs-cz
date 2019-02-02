---
title: Kurz vytvoření vlastního modulu Javy – Azure IoT Edge | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Java a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 9abdbd232b7f346aae9ee5fbe93d23afa4aaf32c
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562364"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>Kurz: Vývoj modulu jazyka Java IoT Edge a nasazení simulovaného zařízení

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

* Zařízení IoT Edge můžete nastavit pomocí následujících kroků v rychlých startů pro [Linux](quickstart-linux.md) nebo [Windows](quickstart.md).
* IoT Edge na zařízeních s Windows verze 1.0.5 nepodporuje moduly v jazyce Java. Další informace najdete v tématu [poznámky k verzi 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Pokyny o tom, jak nainstalovat konkrétní verzi, najdete v článku [aktualizovat démon zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure. 

Prostředky pro vývoj:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Balíček rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) pro Visual Studio Code
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pro Visual Studio Code. 
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) s [nastavením proměnné prostředí `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby odkazovala na vaši instalaci JDK
* [Maven](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/).
   * Pokud vyvíjíte na zařízení s Windows, ujistěte se, že je Docker [nakonfigurovaný na používání kontejnerů Linuxu](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 


## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

V tomto kurzu se pomocí nástroje Azure IoT pro Visual Studio Code sestavíte modul a vytvořte **image kontejneru** ze souborů. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro uložení imagí kontejnerů, můžete použít jakýkoli registr kompatibilní s Dockerem. Jsou dvě oblíbené služby registrů Dockeru [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

Pokud ještě nemáte registr kontejnerů, postupujte podle těchto kroků a vytvořte nový v Azure:

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

2. Zadejte následující hodnoty pro vytvoření registru kontejneru:

   | Pole | Hodnota | 
   | ----- | ----- |
   | Název registru | Zadejte jedinečný název. |
   | Předplatné | V rozevíracím seznamu vyberte předplatné. |
   | Skupina prostředků | Pro jednodušší správu použijte stejnou skupinu prostředků pro všechny testovací prostředky, které jste vytvořili během hraničních zařízeních IoT rychlých startů a kurzů. Například **IoTEdgeResources**. |
   | Umístění | Zvolte umístění, které je blízko vás. |
   | Uživatel s rolí správce | Nastavte na **Povolit**. |
   | Skladová jednotka (SKU) | Vyberte **Basic**. | 

5. Vyberte **Vytvořit**.

6. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 

7. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v pozdější části kurzu a zajistit tak přístup do registru kontejneru. 

## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
Následujícím postupem se vytvoří projekt modul IoT Edge, který je založen na balíček šablon s Azure IoT Edge maven a sada SDK pro zařízení Azure IoT v Javě. Vytvoření projektu pomocí nástroje IoT Azure a Visual Studio Code.

### <a name="create-a-new-solution"></a>Vytvoření nového řešení

Vytvořte šablonu řešení v jazyce Java, kterou můžete přizpůsobit pomocí vlastního kódu. 

1. V nástroji Visual Studio Code zvolte **View** (Zobrazit)  > **Command Palette** (Paleta příkazů). Otevře se paleta příkazů VS Code. 

2. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení IoT Edge**. Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Zvolte **modul Java**. |
   | Zadejte hodnotu pro ID skupiny | Zadejte hodnotu ID skupiny nebo přijměte výchozí **com.edgemodule**. |
   | Zadejte název modulu | Název modulu **JavaModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Svou image kontejneru je předem z názvu, který jste zadali v předchozím kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>Finální bitové kopie úložiště bude vypadat jako \<název registru\>.azurecr.io/javamodule. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-java-module/repository.png)
   
Pokud modul Java vytváříte poprvé, může stažení balíčků maven trvat několik minut. Pak se v okně nástroje VS Code načte pracovní prostor řešení IoT Edge. Pracovní prostor řešení obsahuje pět komponent nejvyšší úrovně. **Moduly** složka obsahuje kód Java pro modul, stejně jako soubory Dockeru pro sestavování modulu jako image kontejneru. V souboru **\.env** jsou uložené přihlašovací údaje k vašemu registru kontejneru. Soubor **deployment.template.json** obsahuje informace, které modul runtime IoT Edge používá k nasazení modulů do zařízení. A **deployment.debug.template.json** souboru kontejnery ladicí verzi modulů. Složku **\.vscode** ani soubor **\.gitignore** v tomto kurzu upravovat nebudete. 

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

5. Přidejte následující definici do třídy **App**. Tato proměnná Určuje teploty prahovou hodnotu. Teplota měřená počítače nebudou hlášeny do služby IoT Hub, dokud překročí tuto hodnotu. 

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

8. Přidejte následující dvě statické vnitřní třídy do třídy **App**. Tyto třídy aktualizovat proměnnou tempThreshold potřebujete změny vlastnosti dvojčete modulu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

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

11. Soubor App.java uložte.

12. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge. Tento soubor říká agentovi, IoT Edge které moduly chcete nasadit, v tomto případě **tempSensor** a **JavaModule**a informuje Centrum IoT Edge, jak můžete směrovat zprávy mezi nimi. Rozšíření Visual Studio Code automaticky naplní většinu informací, že v šablonu nasazení, ale ověřte, že je vše přesné pro vaše řešení: 

   1. Výchozí platformu IoT Edge je nastavena **amd64** ve vaší VS Code stavového řádku, což znamená, že vaše **JavaModule** je nastavena na Linuxu amd64 verzi image. Změnit výchozí platforma ve stavovém řádku z **amd64** k **arm32v7** nebo **windows amd64** Pokud tomu tak architektuře zařízení IoT Edge. 

      ![Aktualizace modulu image platformy](./media/tutorial-java-module/image-platform.png)

   2. Zkontrolujte, jestli má šablona správný název modulu, ne výchozí název **SampleModule**, který jste změnili při vytváření řešení IoT Edge.

   3. **RegistryCredentials** části ukládá přihlašovací údaje registru Dockeru, tak, aby se agent IoT Edge můžete vyžádat bitové kopie modulu. Skutečná uživatelská jména a hesla se ukládají do souboru .env, který git ignoruje. Pokud jste to ještě neudělali, přidejte do souboru .env svoje přihlašovací údaje.  

   4. Pokud chcete získat další informace o manifesty nasazení, přečtěte si téma [zjistěte, jak nasadit moduly a vytvářet ve službě IoT Edge](module-composition.md).

13. Přidejte do manifestu nasazení dvojče modulu **JavaModule**. Vložte následující obsah JSON do dolní části oddílu **moduleContent** za dvojče modulu **$edgeHub**: 

   ```json
       "JavaModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Dvojče zařízení přidat do šablony nasazení](./media/tutorial-java-module/module-twin.png)

14. Uložte soubor deployment.template.json.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozí části jste vytvořili hraničních zařízeních IoT řešení a přidáním kódu **JavaModule** k filtrování zprávy kde teploty ohlášené počítače je nižší než limit přijatelné doby. Nyní sestavte řešení, jako image kontejneru a nasdílejte ji do vašeho registru kontejneru. 

1. Přihlaste se k Dockeru zadáním následujícího příkazu v terminálu Visual Studio Code. Potom můžete odeslat image modulu do služby Azure Container Registry.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Použijte uživatelské jméno, heslo a přihlašovací server zkopírované ze služby Azure Container Registry v první části. Tyto hodnoty můžete také načíst z oddílu **Přístupové klíče** v registru na webu Azure Portal.

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor deployment.template.json a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge). 

Když editoru Visual Studio Code sdělíte, že má sestavit vaše řešení, nejdříve se načtou informace ze šablony nasazení a v nové složce s názvem **config** se vygeneruje soubor deployment.json. Pak se v integrovaném terminálu spustí dva příkazy: `docker build` a `docker push`. Tyto dva příkazy sestaví kód, provedou kontejnerizaci aplikace Java a kód odešlou do kontejneru registru, který jste zadali při inicializaci řešení. 

Úplnou adresu image kontejneru se značkou můžete vidět v integrovaném terminálu VS Code. Adresa image je sestavená z informací v souboru module.json a má formát \<úložiště\>:\<verze\>-\<platforma\>. V tomto kurzu by měla vypadat takto: název_registru.azurecr.io/javamodule:0.0.1-amd64.

>[!TIP]
>Pokud obdržíte chybu při vytváření a nasdílení změn modul, proveďte následující kontroly:
>* Přihlásili jste k Dockeru ve Visual Studio Code pomocí přihlašovacích údajů ze služby container registry? Tyto přihlašovací údaje se liší od těch, které používáte k přihlášení k webu Azure portal.
>* Správnost vašeho kontejneru úložiště? Otevřít **moduly** > **cmodule** > **module.json** a najít **úložiště** pole. Úložiště imagí by měl vypadat jako  **\<registryname\>.azurecr.io/javamodule**. 
>* Sestavujete stejného typu kontejnerů, které běží v počítači pro vývoj? Visual Studio Code výchozí kontejnery Linuxu amd64. Pokud vývojovém počítači běží kontejnery Windows nebo Linuxem arm32v7 kontejnery, aktualizujte platformy na modrý stavového řádku v dolní části okna VS Code tak, aby odpovídaly vaši kontejnerovou platformu.

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

V článku Rychlý start, pomocí kterého jste nastavili své zařízení IoT Edge, jste nasadili modul pomocí webu Azure Portal. Můžete také nasadit moduly pomocí rozšíření Azure IoT Hub Toolkit (dříve rozšíření Azure IoT Toolkit) pro Visual Studio Code. Pro svůj scénář už máte připravený manifest nasazení – soubor **deployment.json**. Teď stačí jen vybrat zařízení, na které se nasazení provede.

1. Paleta příkazů VS Code, spusťte příkaz **Azure: Přihlaste se** a postupujte podle pokynů k přihlášení účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.

2. V nástroji VS Code paletu příkazů, spusťte **Azure IoT Hub: Vyberte službu IoT Hub**. 

3. Zvolte předplatné a centrum IoT obsahující zařízení IoT Edge, které chcete nakonfigurovat. 

4. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 

5. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení). 

   ![Vytvoření nasazení pro jedno zařízení](./media/tutorial-java-module/create-deployment.png)

6. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json. 

7. Klikněte na tlačítko pro obnovení. Měl by se zobrazit spuštěný nový modul **JavaModule** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**.  

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí. 

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů. 

Na zařízení IoT Edge se zobrazí stav modulů nasazení pomocí příkazu `iotedge list`. Měly by se zobrazit čtyři moduly: dva moduly runtime IoT Edge, tempSensor a vlastní modul, který jste vytvořili v tomto kurzu. Spuštění všech modulů může několik minut trvat, proto příkaz spusťte znovu, pokud se zpočátku všechny nezobrazí. 

Pokud chcete zobrazit zprávy, které jednotlivé moduly generují, použijte příkaz `iotedge logs <module name>`. 

Zprávy přicházející do centra IoT můžete zobrazit pomocí Visual Studio Code. 

1. Když chcete monitorovat data, která přichází do služby IoT Hub, vyberte tři tečky (**...**) a potom vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
2. Pokud chcete monitorovat zprávy D2C pro konkrétní zařízení, klikněte pravým tlačítkem na příslušné zařízení v seznamu a vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
3. Pokud chcete monitorování dat zastavit, spusťte příkaz **Azure IoT Hub: Zastavit monitorování zpráv D2C** v paletu příkazů. 
4. Pokud chcete zobrazit nebo aktualizovat dvojče modulu, klikněte pravým tlačítkem na příslušný modul v seznamu a vyberte **Edit module twin** (Upravit dvojče modulu). Pokud chcete aktualizovat dvojče modulu, uložte soubor JSON dvojčete, klikněte pravým tlačítkem na oblast editoru a vyberte **Update Module Twin** (Aktualizovat dvojče modulu).
5. Pokud chcete zobrazit protokoly Dockeru, nainstalujte [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pro VS Code. Spuštěné moduly můžete vyhledat místně v průzkumníku Dockeru. Výběrem možnosti **Show Logs** (Zobrazit protokoly) v místní nabídce je zobrazíte protokoly v integrovaném terminálu.
 
## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Pokračujte dalšími kurzy, ve kterých se naučíte další způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Uložení dat na hraničních zařízeních s využitím databází SQL Serveru](tutorial-store-data-sql-server.md)

