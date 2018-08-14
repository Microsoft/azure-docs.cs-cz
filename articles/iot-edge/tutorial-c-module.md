---
title: Kurz k Azure IoT Edge C | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce C a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 07/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 31560cbd4d8b4572ce930db7ffb8753f3e4a4bc0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425914"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Kurz: Vývoj modulu IoT Edge v jazyce C a jeho nasazení na simulované zařízení

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Použití editoru Visual Studio Code k vytvoření modulu IoT Edge v jazyce C
> * Použití editoru Visual Studio Code a Dockeru k vytvoření image Dockeru a jejímu publikování v registru kontejneru 
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Jako hraniční zařízení můžete použít svůj vývojový počítač nebo virtuální počítač podle postupu v rychlém startu pro zařízení s [Linuxem](quickstart-linux.md) nebo [Windows](quickstart.md).

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Standard v Azure. 

Prostředky pro vývoj:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Rozšíření C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) pro Visual Studio Code.
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pro Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů
V tomto kurzu pomocí rozšíření Azure IoT Edge pro VS Code sestavíte modul a ze souborů vytvoříte **image kontejneru**. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro účely tohoto kurzu můžete použít jakýkoli registr kompatibilní s Dockerem. V cloudu jsou k dispozici dvě oblíbené služby registrů Dockeru – [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

Následující příkaz Azure CLI vytvoří registr ve skupině prostředků **IoTEdgeResources**. Nahraďte **{acr_name}** jedinečným názvem vašeho registru. 

   ```azurecli-interactive
   az acr create --resource-group IoTEdgeResources --name {acr_name} --sku Basic --admin-enabled true
   ```

Načtěte přihlašovací údaje pro váš registr. 

   ```azurecli-interactive
   az acr credential show --name {acr_name}
   ```

Zkopírujte hodnoty pro **Uživatelské jméno** a jedno z hesel. Tyto hodnoty použijete v dalších částech kurzu k publikování image Dockeru do registru a k přidání přihlašovacích údajů registru do modulu runtime Edge. 

## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
V následujících krocích si ukážeme, jak vytvořit projekt modulu IoT Edge, který je založený na rozhraní .NET Core 2.0. Budeme používat Visual Studio Code a rozšíření Azure IoT Edge.
1. V nástroji Visual Studio Code vyberte, že chcete **zobrazit** > **integrovaný terminál**, aby se otevřel integrovaný terminál VS Code.
2. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code. 
3. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud jste už přihlášení, můžete tento krok přeskočit.
4. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení: 
   1. Vyberte složku, ve které chcete vytvořit řešení. 
   2. Zadejte název pro vaše řešení nebo přijměte výchozí název **EdgeSolution**.
   3. Jako šablonu modulu zvolte **C Module**. 
   4. Pojmenujte modul **CModule**. 
   5. Jako úložiště imagí pro první modul určete registr kontejneru Azure, který jste vytvořili v předchozí části. Nahraďte **localhost:5000** hodnotou pro přihlašovací server, kterou jste zkopírovali. Konečný řetězec vypadá takto: **\<název_registru\>.azurecr.io/cmodule**.
 
4. V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge. Obsahuje složku **modules**, složku **.vscode**, soubor šablony manifestu nasazení a soubor **.env**. Výchozí kód modulu se implementuje jako modul kanálu. 

5. Pokud chcete filtrovat zprávy ve formátu JSON, je potřeba importovat knihovnu JSON pro jazyk C. K parsování formátu JSON v modulu C můžete zvolit jakoukoli knihovnu JSON nebo si můžete napsat vlastní. V následujících krocích se jako příklad používá knihovna [Parson](https://github.com/kgabis/parson).
   1. Z [úložiště Parson na GitHubu](https://github.com/kgabis/parson) si stáhněte soubory **parson.c** a **parson.h**. Tyto dva soubory zkopírujte a vložte do složky **CModule**.
   2. Otevřete soubor **modules** > **CModule** > **CMakeLists.txt**. Přidejte následující řádky pro import knihovny parson jako my_parson.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Do části `target_link_libraries` v souboru **CMakeLists.txt** přidejte `my_parson`.
   4. Otevřete soubor **modules** > **CModule** > **main.c**. Na konec části s vkládáním přidejte následující kód pro zahrnutí souboru `parson.h`, který zajišťuje podporu JSON:

      ```c
      #include "parson.h"
      ```

6. Za část s vkládáním přidejte globální proměnnou `temperatureThreshold`. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu. 

    ```c
    static double temperatureThreshold = 25;
    ```

7. Celou funkci `CreateMessageInstance` nahraďte následujícím kódem. Tato funkce přidělí kontext pro zpětné volání. 

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

8. Celou funkci `InputQueue1Callback` nahraďte následujícím kódem. Tato funkce implementuje samotný filtr zasílání zpráv. 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n", 
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

9. Přidejte funkci `moduleTwinCallback`. Tato metoda přijímá aktualizace požadovaných vlastností dvojčete modulu a aktualizuje proměnnou **temperatureThreshold** na stejnou hodnotu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n", 
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

10. Do funkce `SetupCallbacksForModule` přidejte nové zpětné volání pro `moduleTwinCallback`. Celou funkci `SetupCallbacksForModule` nahraďte následujícím kódem.

    ```c
    static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
    {
        int ret;

        if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else
        {
            ret = 0;
        }

        return ret;
    }
    ```

11. Soubor uložte.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozí části jste vytvořili řešení IoT Edge a do modulu CModule jste přidali kód, který odfiltruje zprávy, ve kterých je hlášená teplota počítače nižší než přípustná mezní hodnota. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru. 

1. Přihlaste se k Dockeru tak, že do integrovaného terminálu Visual Studio Code zadáte následující příkaz, aby bylo možné odeslat image modulu do ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Použijte uživatelské jméno, heslo a přihlašovací server, který jste zkopírovali z registru kontejneru Azure v první části. Můžete ho také načíst znovu z oddílu **Přístupové klíče** ve vašem registru na webu Azure Portal.

2. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge. Tento soubor sděluje modulu `$edgeAgent`, že se mají nasadit dva moduly: **tempSensor** a **CModule**. Hodnota `CModule.image` je nastavená na verzi image Linux amd64. Další informace o manifestech nasazení najdete ve [vysvětlení, jak lze moduly IoT Edge používat, konfigurovat a opětovně používat](module-composition.md).

3. V souboru **deployment.template.json** se nachází oddíl **registryCredentials**, do kterého se ukládají přihlašovací údaje registru Dockeru. Skutečná uživatelská jména a hesla se ukládají do souboru .env, který git ignoruje.

4. Přidejte do manifestu nasazení dvojče modulu CModule. Vložte následující obsah JSON do dolní části oddílu `moduleContent`, za dvojče modulu `$edgeHub`: 
    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Soubor uložte.
5. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build IoT Edge solution** (Vytvořit řešení IoT Edge). 

Když editoru Visual Studio Code sdělíte, že má sestavit vaše řešení, nejdříve se načtou informace ze šablony nasazení a v nové složce **config** se vygeneruje soubor `deployment.json`. Pak se v integrovaném terminálu spustí dva příkazy: `docker build` a `docker push`. Tyto dva příkazy sestaví kód, provedou kontejnerizaci knihovny `CModule.dll` a odešlou ji do kontejneru registru, který jste zadali při inicializaci řešení. 

Úplnou adresu image kontejneru se značkou můžete vidět v integrovaném terminálu VS Code. Adresa image je sestavená z informací v souboru `module.json` a má formát **\<úložiště\>:\<verze\>-\<platforma\>**. V tomto kurzu by měla vypadat takto: **název_registru.azurecr.io/cmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

1. Nakonfigurujte rozšíření Azure IoT Toolkit s použitím připojovacího řetězce pro vaše centrum IoT: 
    1. Otevřete průzkumník VS Code tím, že vyberete **Zobrazení** > **Průzkumník**. 
    2. V průzkumníku klikněte na **AZURE IOT HUB DEVICES** (ZAŘÍZENÍ AZURE IOT HUB) a pak klikněte na **...**. Klikněte na **Select IoT Hub** (Vybrat IoT Hub). Postupem podle pokynů se přihlaste k účtu Azure a zvolte IoT Hub. 
       Upozorňujeme, že nastavení můžete provést také kliknutím na **Set IoT Hub Connection String** (Nastavit připojovací řetězec pro IoT Hub). V místním okně zadejte připojovací řetězec pro IoT Hub, ke kterému se vaše zařízení IoT Edge připojí.

2. V průzkumníku zařízení Azure IoT Hub klikněte pravým tlačítkem na vaše zařízení IoT Edge a pak klikněte na **Create Deployment for IoT Edge device** (Vytvořit nasazení pro zařízení IoT Edge). Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge).

3. Klikněte na tlačítko pro obnovení. Měl by se zobrazit spuštěný nový modul **CModule** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

1. Když chcete monitorovat data, která přichází do služby IoT Hub, klikněte na **...** a vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
2. Pokud chcete monitorovat zprávy D2C pro konkrétní zařízení, klikněte pravým tlačítkem na příslušné zařízení v seznamu a vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
3. Pokud chcete monitorování dat zastavit, spusťte na paletě příkazů příkaz **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Zastavit monitorování zpráv D2C). 
4. Pokud chcete zobrazit nebo aktualizovat dvojče modulu, klikněte pravým tlačítkem na příslušný modul v seznamu a vyberte **Edit module twin** (Upravit dvojče modulu). Pokud chcete aktualizovat dvojče modulu, uložte soubor JSON dvojčete, klikněte pravým tlačítkem na oblast editoru a vyberte **Update Module Twin** (Aktualizovat dvojče modulu).
5. Pokud chcete zobrazit protokoly Dockeru, můžete nainstalovat [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pro VS Code a vyhledat spuštěné moduly místně v průzkumníku Dockeru. Kliknutím na **Show Logs** (Zobrazit protokoly) v místní nabídce je zobrazíte v integrovaném terminálu.
 
## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud budete pokračovat k dalšímu doporučenému článku, můžete už vytvořené prostředky a konfigurace zachovat a znovu je použít.

Pokud nebudete pokračovat, můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupin prostředků Azure je nevratná akce. V případě odstranění se skupina prostředků i všechny prostředky, které obsahuje, trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT Hub.
>

Pokud chcete odstranit jenom IoT Hub, spusťte následující příkaz, ve kterém se použije název vaší služby Hub a název skupiny prostředků:

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


Odstranění celé skupiny prostředků podle názvu:

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete chtít vytvářet vlastní moduly, získáte další informace na stránce o [vývoji modulu C pomocí Azure IoT Edge pro Visual Studio Code](how-to-develop-c-module.md). Pokračujte dalšími kurzy, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Uložení dat na hraničních zařízeních s využitím databází SQL Serveru](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
