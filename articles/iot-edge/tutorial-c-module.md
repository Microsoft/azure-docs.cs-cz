---
title: Kurz vývoj modulu jazyka C pro Linux – Azure IoT Edge | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem jazyka C a nasaďte ji do zařízení s Linuxem s IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 63169423e757f3e1e73a95a1523d74c8fc59b2b2
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835126"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Kurz: Vývoj modulu jazyka C IoT Edge pro zařízení s Linuxem

Vývoj kódu jazyka C a nasadit ho k Linuxovému zařízení s Azure IoT Edge pomocí Visual Studio Code. 

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použití editoru Visual Studio Code k vytvoření modulu IoT Edge v jazyce C
> * Použití editoru Visual Studio Code a Dockeru k vytvoření image Dockeru a jejímu publikování v registru kontejneru
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Řešení rozsahu

Tento kurz ukazuje postupy při vývoji modulu v **C** pomocí **Visual Studio Code**a jak ji nasadit **Linuxovému zařízení**. Pokud vyvíjíte moduly pro zařízení s Windows, přejděte na [vývoj modulu jazyka C IoT Edge pro zařízení s Windows](tutorial-c-module-windows.md) místo.

V následující tabulce použijte k pochopení možností pro vývoj a nasazení modulů jazyka C pro Linux: 

| C | Visual Studio Code | Visual Studio | 
| - | ------------------ | ------------- |
| **Linux AMD64** | ![Použití VS Code pro moduly C v Linuxu AMD64](./media/tutorial-c-module/green-check.png) | ![Použití VS pro C moduly na Linuxu AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Použití VS Code pro moduly C v Linuxu ARM32](./media/tutorial-c-module/green-check.png) | ![Použití VS pro C moduly na Linux ARM32](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, by měl prošli předchozího kurzu věnovaného nastavení vývojového prostředí pro vývoj kontejnerů Linux: [Vývoj modulů IoT Edge pro zařízení s Linuxem](tutorial-develop-for-linux.md). Po dokončení tohoto kurzu, byste měli mít splněné následující požadavky: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [Linuxovému zařízení s Azure IoT Edge](quickstart-linux.md)
* Registr kontejnerů, třeba [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovanou [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurované ke spuštění kontejnerů Linuxu.

K vývoji modul IoT Edge v jazyce C, nainstalujte na svém vývojovém počítači následující další požadavky: 

* [Rozšíření C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) pro Visual Studio Code.

## <a name="create-a-module-project"></a>Vytvoření modulu projektu

Následující kroky vytvořit projekt modul IoT Edge pro jazyk C pomocí Visual Studio Code a rozšíření nástroje Azure IoT. Jakmile budete mít vytvořenou šablonu projektu, přidejte nový kód, aby modul odfiltruje zprávy podle jejich ohlášené vlastnosti. 

### <a name="create-a-new-project"></a>Vytvořit nový projekt

Vytvořte šablonu řešení v C, kterou můžete přizpůsobit pomocí vlastního kódu.

1. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code.

2. Do palety příkazů zadejte a spusťte příkaz **Azure: Přihlaste se** a postupujte podle pokynů k přihlášení účtu Azure. Pokud jste už přihlášení, můžete tento krok přeskočit.

3. Do palety příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení IoT Edge**. Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Zvolte **modulu C**. |
   | Zadejte název modulu | Pojmenujte modul **CModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Svou image kontejneru je předem z názvu, který jste zadali v předchozím kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br> Finální bitové kopie úložiště bude vypadat jako \<název registru\>.azurecr.io/cmodule. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte Cílová architektura

Visual Studio Code v současné době můžete vyvíjet C moduly pro Linux AMD64 a Linux ARM32v7 zařízení. Budete muset vybrat, jakou architekturu vývoji cílíte jednotlivých řešení, protože kontejneru je sestaven a jinak spusťte pro každý typ architektury. Výchozí hodnota je Linux AMD64. 

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavit výchozí Cílová platforma pro řešení**, nebo vyberte ikonu zástupce v bočním panelu v dolní části okna. 

2. Vyberte Cílová architektura v paletu příkazů v seznamu možností. Pro účely tohoto kurzu používáme virtuálního počítače s Ubuntu jako zařízení IoT Edge, budou mít výchozí **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Výchozí modul kód přijímá zprávy ve vstupní frontě a předává je do výstupní fronty. Přidáme další kód tak, aby modul zpracovávat zprávy na hraničních zařízeních před předáním do služby IoT Hub. Aktualizace modulu tak, aby analyzuje data o teplotě v každé zprávě a pouze odešle zprávu do služby IoT Hub, pokud teplota překročí určitou prahovou hodnotu. 

1. Data ze snímače v tomto scénáři přicházejí ve formátu JSON. Pokud chcete filtrovat zprávy ve formátu JSON, importujte knihovnu JSON pro jazyk C. V tomto kurzu se používá Parson.

   1. Stáhněte si [úložiště Parson GitHub](https://github.com/kgabis/parson). Soubory **parson.c** a **parson.h** zkopírujte do složky **CModule**.

   2. Otevřete soubor **modules** > **CModule** > **CMakeLists.txt**. Na začátku souboru importujte soubory Parson jako knihovnu **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Přidejte **my_parson** do seznamu knihoven ve funkci **target_link_libraries** v souboru CMakeLists.txt.

   4. Uložte soubor **CMakeLists.txt**.

   5. Otevřete soubor **modules** > **CModule** > **main.c**. V dolní části seznamu #include, zahrnují přidání nového `parson.h` pro podporu JSON:

      ```c
      #include "parson.h"
      ```

1. Do souboru **main.c** přidejte za část s příkazy include globální proměnnou `temperatureThreshold`. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Najít `CreateMessageInstance` funkce v main.c. Nahraďte následující kód, který přidá několik řádků funkce vnitřní if-else – příkaz: 

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

1. Celou funkci `InputQueue1Callback` nahraďte následujícím kódem. Tato funkce implementuje samotný filtr zasílání zpráv. Při doručení zprávy do zkontroluje, zda ohlášené teplota překročí prahovou hodnotu. Pokud ano, pak předá zprávu prostřednictvím jeho výstupní fronty. Pokud ne, pak ignoruje zprávy. 

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

1. Přidejte funkci `moduleTwinCallback`. Tato metoda přijímá aktualizace požadovaných vlastností dvojčete modulu a aktualizuje proměnnou **temperatureThreshold** na stejnou hodnotu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

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

1. Najít `SetupCallbacksForModule` funkce. Nahraďte následující kód, který přidá funkce **else if** příkazu ke kontrole, jestli má aktualizované dvojčete modulu.

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

1. Uložte soubor main.c.

1. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge. 

1. Přidejte do manifestu nasazení dvojče modulu CModule. Vložte následující obsah JSON do dolní části oddílu `moduleContent`, za dvojče modulu `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Přidání dvojčete modulu CModule do šablony nasazení](./media/tutorial-c-module/module-twin.png)

1. Uložte soubor **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Vytváření a nasdílení změn modulu

V předchozí části jste vytvořili hraničních zařízeních IoT řešení a přidat kód do CModule, který odfiltruje zprávy kde teploty ohlášené počítač je v přijatelných mezích. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Otevřete terminál VS Code tak, že vyberete **zobrazení** > **terminálu**.

1. Přihlaste se k Dockeru zadáním následujícího příkazu v terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru ze služby Azure container registry. Takové hodnoty můžete načíst **přístupové klíče** části vašeho registru na webu Azure Portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití `--password-stdin`. Tento osvědčený postup doporučuje pro produkční scénáře, je mimo rámec tohoto návodu. Další informace najdete v tématu [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odkaz.

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge).

   Sestavení a odeslání příkaz spustí tři operace. Nejprve vytvoří novou složku řešení, nazývá **config** , který obsahuje soubory manifestu, sestavení si informace při nasazení šablony a dalších řešení úplné nasazení. Za druhé, poběží `docker build` k sestavení image kontejneru, který je založen na příslušný soubor dockerfile pro cílové architektury. Pak spustí `docker push` tak, aby nabízel úložiště imagí do vašeho registru kontejneru.

## <a name="deploy-modules-to-device"></a>Do zařízení nasadit moduly

Pomocí Průzkumníka Visual Studio Code a rozšíření IoT nástroje Azure k nasazení projektu modulu do zařízení IoT Edge. Už máte manifest nasazení připravili pro váš scénář **deployment.json** souboru ve složce Konfigurace. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že zařízení IoT Edge je zprovozněný. 

1. V Průzkumníku Visual Studio Code, rozbalte **zařízení Azure IoT Hub** část a zobrazit seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json.

4. Klikněte na tlačítko pro obnovení. Měl by se zobrazit spuštěný nový modul **CModule** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem myši na název vašeho zařízení IoT Edge a vyberte **spustit monitorování zpráv D2C**.

2. Zobrazení zpráv přicházejících u služby IoT Hub. Může trvat nějakou dobu zprávy dorazí, protože zařízení IoT Edge se zobrazí její nové nasazení a spuštění všech modulů. Pak změny, které jsme provedli CModule kód Počkejte, dokud počítač teploty dosáhne 25 stupňů před odesláním zprávy. Také přidá typ zprávy **výstrah** pro všechny zprávy, které dosažení této prahové hodnoty teploty. 

   ![Zobrazení zpráv přicházejících u služby IoT Hub](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Upravit dvojče modulu

Dvojče modulu CModule jsme použili v manifestu nasazení pro nastavení prahové hodnoty teploty ve stupních 25. Chcete-li změnit funkci bez nutnosti aktualizovat kód modulu můžete použít dvojče modulu.

1. Ve Visual Studio Code rozbalte podrobnosti v části zařízení IoT Edge zobrazíte spuštěné moduly. 

2. Klikněte pravým tlačítkem na **CModule** a vyberte **upravit dvojče zařízení**. 

3. Najít **TemperatureThreshold** v požadované vlastnosti. Změňte její hodnotu na nové teploty 5 až 10 stupňů vyšší než nejnovější ohlášené teploty. 

4. Uložte soubor dvojčete modulu.

5. Klikněte pravým tlačítkem na libovolné místo ve dvojčeti modulu úpravy podokně a vyberte **aktualizace dvojčete modulu**. 

5. Monitorování příchozích zpráv typu zařízení cloud. Měli byste vidět zprávy zastavit, dokud nebude dosaženo novou teploty prahovou hodnotu. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurací a použité v tomto článku se vyhnout poplatkům za prostředky Azure.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Jakmile budete připraveni k sestavení vlastních modulů, další informace o [vyvíjet vlastní moduly IoT Edge](module-development.md) nebo jak [vývoj modulů s Visual Studio Code](how-to-vs-code-develop-module.md). Budete moct pokračovat do další kurzy se dozvíte, jak Azure IoT Edge můžete nasadit cloudové služby Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [službu Custom Vision Service](tutorial-deploy-custom-vision.md)

