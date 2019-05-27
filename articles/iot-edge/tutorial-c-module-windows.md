---
title: Kurz vývoj modulu jazyka C pro Windows – Azure IoT Edge | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem jazyka C a nasaďte ji do zařízení s Windows s IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: ee64e5a49bf2825c83c74167d7eb75aa3dc59387
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239820"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Kurz: Vývoj modulu jazyka C IoT Edge pro zařízení s Windows

Pomocí sady Visual Studio k vývoji kódu jazyka C a nasaďte ji do zařízení s Windows s Azure IoT Edge. 

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Vytvoření modulu IoT Edge, která je založena na sadě SDK .NET Core 2.1 pomocí sady Visual Studio.
> * Pomocí sady Visual Studio a Dockeru k vytvoření image Dockeru a její publikování do registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Řešení rozsahu

Tento kurz ukazuje postupy při vývoji modulu v **C** pomocí **Visual Studio 2017**a jak ji nasadit **zařízení Windows**. Pokud vyvíjíte moduly pro Linux zařízení, přejděte na [vývoj modulu jazyka C IoT Edge pro zařízení s Linuxem](tutorial-c-module.md) místo. 

Popis možností pro vývoj a nasazení modulů C na zařízení s Windows pomocí následující tabulky: 

| C | Visual Studio Code | Visual Studio 2017 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Vývoj modulů jazyka C pro WinAMD64 v sadě Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, by měl prošli předchozího kurzu věnovaného nastavení vývojového prostředí pro vývoj kontejnerů Windows: [Vývoj modulů IoT Edge pro zařízení s Windows](tutorial-develop-for-windows.md). Po dokončení tohoto kurzu, byste měli mít splněné následující požadavky: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [zařízení Windows s Azure IoT Edge](quickstart.md).
* Registr kontejnerů, třeba [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017), verze 15.7 nebo novější, nakonfigurují [nástroje Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) rozšíření.
* [Docker CE](https://docs.docker.com/install/) nakonfigurován pro spouštění kontejnerů Windows.
* Sadu SDK Azure IoT pro C. 

## <a name="create-a-module-project"></a>Vytvoření modulu projektu

Následujícím postupem se vytvoří projekt modul IoT Edge, založené na .NET Core 2.0 SDK s použitím sady Visual Studio a rozšíření Azure IoT Edge Tools. Jakmile budete mít vytvořenou šablonu projektu, přidejte nový kód, aby modul odfiltruje zprávy podle jejich ohlášené vlastnosti. 

### <a name="create-a-new-project"></a>Vytvořit nový projekt

Vytvořte šablonu řešení v C, kterou můžete přizpůsobit pomocí vlastního kódu.

1. Spuštění sady Visual Studio jako správce.

2. Vyberte **Soubor** > **Nový** > **Projekt**. 

3. V okně Nový projekt, vyberte **Azure IoT** typ projektu a zvolte **Azure IoT Edge** projektu. Přejmenování projektu a řešení na něco popisného jako **CTutorialApp**. Vyberte **OK** pro vytvoření projektu. 

   ![Vytvořte nový projekt Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

4. V okně aplikace IoT Edge a modul konfiguraci projektu s použitím následujících hodnot: 

   | Pole | Hodnota |
   | ----- | ----- |
   | Aplikační platforma | Zrušte zaškrtnutí políčka **Linux Amd64**a zkontrolujte **WindowsAmd64**. |
   | Vybrat šablonu | Vyberte **modulu C**. | 
   | Název modulu projektu | Pojmenujte modul **CModule**. | 
   | Úložiště imagí dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Svou image kontejneru je předem z hodnoty názvu modulu projektu. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br> Finální bitové kopie úložiště bude vypadat jako \<název registru\>.azurecr.io/cmodule. |

   ![Konfigurace projektu pro cílové zařízení, typ modulu a container registry](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Vyberte **OK** změny. 

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

Manifest nasazení sdílí přihlašovací údaje pro svůj registr kontejneru se modul runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. Použijte přihlašovací údaje z **přístupové klíče** část svůj registr kontejneru Azure. 

1. V Průzkumníku řešení sady Visual Studio, otevřete **deployment.template.json** souboru. 

2. Najít **registryCredentials** vlastnost $edgeAgent požadované vlastnosti. 

3. Aktualizujte vlastnost pomocí svých přihlašovacích údajů, za tento formát: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Update the module with custom code

The default module code receives messages on an input queue and passes them along through an output queue. Let's add some additional code so that the module processes the messages at the edge before forwarding them to IoT Hub. Update the module so that it analyzes the temperature data in each message, and only sends the message to IoT Hub if the temperature exceeds a certain threshold. 


1. The data from the sensor in this scenario comes in JSON format. To filter messages in JSON format, import a JSON library for C. This tutorial uses Parson.

   1. Download the [Parson GitHub repository](https://github.com/kgabis/parson). Copy the **parson.c** and **parson.h** files into the **CModule** project.

   2. In Visual Studio, open the **CMakeLists.txt** file from the CModule project folder. At the top of the file, import the Parson files as a library called **my_parson**.

      ```
      add_library (my_parson parson.c parson.h)
      ```

   3. Add **my_parson** to the list of libraries in the **target_link_libraries** section of the CMakeLists.txt file.

   4. Save the **CMakeLists.txt** file.

   5. Open **CModule** > **main.c**. At the bottom of the list of include statements, add a new one to include `parson.h` for JSON support:

      ```c
      #include "parson.h"
      ```

2.  V **main.c** přidejte globální proměnnou s názvem `temperatureThreshold` vedle messagesReceivedByInput1Queue proměnné. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Najít `CreateMessageInstance` funkce v main.c. Nahraďte následující kód, který přidá několik řádků funkce vnitřní if-else – příkaz: 

   ```c
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
   ```

   Nové řádky kódu ve else – příkaz Přidat nové vlastnosti do zprávy, která označí zprávu jako oznámení. Tento kód označuje všechny zprávy jako výstrahy, protože přidáme funkce, které vykazují vysokou teploty-li pouze odesílá zprávy do služby IoT Hub. 

4. Najít `InputQueue1Callback` funkce a celý funkce nahraďte následujícím kódem. Tato funkce implementuje samotný filtr zasílání zpráv. Při doručení zprávy do zkontroluje, zda ohlášené teplota překročí prahovou hodnotu. Pokud ano, pak předá zprávu prostřednictvím jeho výstupní fronty. Pokud ne, pak ignoruje zprávy. 

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

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Přidejte funkci `moduleTwinCallback`. Tato metoda přijímá aktualizace požadovaných vlastností dvojčete modulu a aktualizuje proměnnou **temperatureThreshold** na stejnou hodnotu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

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

6. Najít `SetupCallbacksForModule` funkce. Nahraďte následující kód, který přidá funkce **else if** příkazu ke kontrole, jestli má aktualizované dvojčete modulu. 

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

7. Uložte soubor main.c.

8. Otevřít **deployment.template.json** souboru. 

9. Přidejte do manifestu nasazení dvojče modulu CModule. Vložte následující obsah JSON do dolní části oddílu `moduleContent`, za dvojče modulu `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Přidání dvojčete modulu CModule do šablony nasazení](./media/tutorial-c-module-windows/module-twin.png)

1. Uložte soubor **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Vytváření a nasdílení změn modulu

V předchozí části jste vytvořili hraničních zařízeních IoT řešení a přidáním kódu **CModule** k filtrování zprávy kde teploty ohlášené počítače je nižší než přípustnou mezní hodnotu. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru. 

1. Použijte následující příkaz pro přihlášení k Docker na svém vývojovém počítači. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru ze služby Azure container registry. Takové hodnoty můžete načíst **přístupové klíče** části vašeho registru na webu Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití `--password-stdin`. Tento osvědčený postup doporučuje pro produkční scénáře, je mimo rámec tohoto návodu. Další informace najdete v tématu [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odkaz.

2. V Průzkumníku řešení sady Visual Studio klikněte pravým tlačítkem na název projektu, který má být sestaveno. Výchozí název je **AzureIotEdgeApp1** a protože vytváříte modulu Windows, by měl být rozšíření **Windows.Amd64**. 

3. Vyberte **vytváření a nasdílení změn moduly IoT Edge**. 

   Sestavení a odeslání příkaz spustí tři operace. Nejprve vytvoří novou složku řešení, nazývá **config** , který obsahuje soubory manifestu, sestavení si informace při nasazení šablony a dalších řešení úplné nasazení. Za druhé, poběží `docker build` k sestavení image kontejneru, který je založen na příslušný soubor dockerfile pro cílové architektury. Pak spustí `docker push` tak, aby nabízel úložiště imagí do vašeho registru kontejneru. 

## <a name="deploy-modules-to-device"></a>Do zařízení nasadit moduly

Pomocí Průzkumníka cloudu sady Visual Studio a rozšíření Azure IoT Edge nástroje pro nasazení projektu modulu do zařízení IoT Edge. Už máte manifest nasazení připravili pro váš scénář **deployment.json** souboru ve složce Konfigurace. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že zařízení IoT Edge je zprovozněný. 

1. V Průzkumníku cloudu sady Visual Studio rozšíření prostředků k seznamu zařízení IoT. 

2. Klikněte pravým tlačítkem na název zařízení IoT Edge, které chcete dostávat nasazení. 

3. Vyberte **vytvořit nasazení**.

4. V Průzkumníku souborů vyberte **deployment.windows amd64** souboru ve složce Konfigurace vašeho řešení. 

5. Aktualizujte Průzkumníka cloudu zobrazíte nasazené moduly, které jsou uvedeny ve vašem zařízení. 


## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí. 

Chcete-li zobrazit zprávy při jejich doručení na službě IoT Hub můžete použít rozšíření nástroje IoT Edge. 

1. V Průzkumníku cloudu sady Visual Studio vyberte název vašeho zařízení IoT Edge. 

2. V **akce** seznamu vyberte **spustit monitorování integrovaných událostí koncový bod**. 

3. Zobrazení zpráv přicházejících u služby IoT Hub. Může trvat nějakou dobu zprávy dorazí, protože zařízení IoT Edge se zobrazí její nové nasazení a spuštění všech modulů. Pak změny, které jsme provedli CModule kód Počkejte, dokud počítač teploty dosáhne 25 stupňů před odesláním zprávy. Také přidá typ zprávy **výstrah** pro všechny zprávy, které dosažení této prahové hodnoty teploty. 

   ![Zobrazení zpráv přicházejících u služby IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Upravit dvojče modulu

Dvojče modulu CModule jsme použili k nastavení prahové hodnoty teploty ve stupních 25. Chcete-li změnit funkci bez nutnosti aktualizovat kód modulu můžete použít dvojče modulu.

1. V sadě Visual Studio, otevřete **deployment.windows amd64.json** souboru. (Ne deployment.template soubor. Pokud se nasazení manifestu v konfiguračním souboru v Průzkumníku řešení, vyberte **zobrazit všechny soubory** ikonu na panelu nástrojů Průzkumník.)

2. Najít dvojče CModule a změňte hodnotu **temperatureThreshold** parametr nové teploty 5 až 10 stupňů vyšší než nejnovější ohlášené teploty. 

3. Uložit **deployment.windows amd64.json** souboru.

4. Postupujte podle kroků nasazení znovu a použijte aktualizovaného manifestu nasazení do vašeho zařízení. 

5. Monitorování příchozích zpráv typu zařízení cloud. Měli byste vidět zprávy zastavit, dokud nebude dosaženo novou teploty prahovou hodnotu. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

V opačném případě můžete odstranit místní konfigurací a použité v tomto článku se vyhnout poplatkům za prostředky Azure. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Jakmile budete připraveni k sestavení vlastních modulů, další informace o [vyvíjet vlastní moduly IoT Edge](module-development.md) nebo jak [vývoj modulů pomocí sady Visual Studio](how-to-visual-studio-develop-module.md). Budete moct pokračovat do další kurzy se dozvíte, jak Azure IoT Edge můžete nasadit cloudové služby Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [službu Custom Vision Service](tutorial-deploy-custom-vision.md)
