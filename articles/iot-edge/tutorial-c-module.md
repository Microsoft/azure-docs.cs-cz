---
title: Kurz vytvoření vlastního modulu C - Azure IoT Edge | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce C a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 798cf405c222a443dbbd3a316d20c482daf4429f
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563248"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Kurz: Vývoj modulu jazyka C IoT Edge a nasazení simulovaného zařízení

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
* Moduly C pro Azure IoT Edge nepodporují kontejnery Windows. Pokud vaše zařízení IoT Edge je počítač s Windows, ujistěte se, že je nakonfigurován na použití kontejnery Linuxu. Informace o instalaci rozdíly mezi kontejnery Windows a Linux najdete v tématu [nainstalovat modul runtime IoT Edge ve Windows](how-to-install-iot-edge-windows.md).

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.

Prostředky pro vývoj:

* [Visual Studio Code](https://code.visualstudio.com/).
* [Rozšíření C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) pro Visual Studio Code.
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/).

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
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |
   | Umístění | Zvolte umístění, které je blízko vás. |
   | Uživatel s rolí správce | Nastavte na **Povolit**. |
   | Skladová jednotka (SKU) | Vyberte **Basic**. |

5. Vyberte **Vytvořit**.

6. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**.

7. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v pozdější části kurzu a zajistit tak přístup do registru kontejneru.

## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
Zobrazit následující kroky, je postup pro vytvoření projektu modul IoT Edge založené na rozhraní .NET core 2.0 pomocí Visual Studio Code a nástroje Azure IoT.

### <a name="create-a-new-solution"></a>Vytvoření nového řešení

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

V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge. Pracovní prostor řešení obsahuje pět komponent nejvyšší úrovně. Složka **modules** obsahuje kód C pro váš modul a také soubory Dockerfile pro sestavení modulu jako image kontejneru. V souboru **\.env** jsou uložené přihlašovací údaje k vašemu registru kontejneru. Soubor **deployment.template.json** obsahuje informace, které modul runtime IoT Edge používá k nasazení modulů do zařízení. A **deployment.debug.template.json** souboru kontejnery ladicí verzi modulů. Složku **\.vscode** ani soubor **\.gitignore** v tomto kurzu upravovat nebudete.

Pokud jste při vytváření řešení nezadali registr kontejneru, ale přijali jste výchozí hodnotu localhost:5000, nebudete mít soubor \.env.

<!--
   ![C solution workspace](./media/tutorial-c-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Přidejte do modulu C kód, který mu umožní načítat data ze snímače, kontrolovat, jestli hlášená teplota počítače nepřesáhla bezpečnou prahovou hodnotu, a předávat tyto informace do služby IoT Hub.

5. Data ze snímače v tomto scénáři přicházejí ve formátu JSON. Pokud chcete filtrovat zprávy ve formátu JSON, importujte knihovnu JSON pro jazyk C. V tomto kurzu se používá Parson.

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

   5. Otevřete soubor **modules** > **CModule** > **main.c**. Na konec seznamu příkazů include přidejte nový příkaz pro zahrnutí souboru `parson.h`, který zajistí podporu JSON:

      ```c
      #include "parson.h"
      ```

6. Do souboru **main.c** přidejte za část s příkazy include globální proměnnou `temperatureThreshold`. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu.

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

10. Funkci `SetupCallbacksForModule` nahraďte následujícím kódem.

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

11. Uložte soubor main.c.

12. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge. Tento soubor říká agentovi, IoT Edge které moduly chcete nasadit, v tomto případě **tempSensor** a **CModule**a informuje Centrum IoT Edge, jak můžete směrovat zprávy mezi nimi. Rozšíření Visual Studio Code automaticky naplní většinu informací, že v šablonu nasazení, ale ověřte, že je vše přesné pro vaše řešení: 

   1. Výchozí platformu IoT Edge je nastavena **amd64** ve vaší VS Code stavového řádku, což znamená, že vaše **CModule** nastavený na verzi image Linuxu amd64. Změnit výchozí platforma ve stavovém řádku z **amd64** k **arm32v7** Pokud tomu tak architektuře zařízení IoT Edge. 

      ![Aktualizace modulu image platformy](./media/tutorial-c-module/image-platform.png)

   2. Zkontrolujte, jestli má šablona správný název modulu, ne výchozí název **SampleModule**, který jste změnili při vytváření řešení IoT Edge.

   3. **RegistryCredentials** části ukládá přihlašovací údaje registru Dockeru, tak, aby se agent IoT Edge můžete vyžádat bitové kopie modulu. Skutečná uživatelská jména a hesla se ukládají do souboru .env, který git ignoruje. Pokud jste to ještě neudělali, přidejte do souboru .env svoje přihlašovací údaje.  

   4. Pokud chcete získat další informace o manifesty nasazení, přečtěte si téma [zjistěte, jak nasadit moduly a vytvářet ve službě IoT Edge](module-composition.md).

13. Přidejte do manifestu nasazení dvojče modulu CModule. Vložte následující obsah JSON do dolní části oddílu `moduleContent`, za dvojče modulu `$edgeHub`:

    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

   ![Přidání dvojčete modulu CModule do šablony nasazení](./media/tutorial-c-module/module-twin.png)

14. Uložte soubor **deployment.template.json**.

## <a name="build-and-push-your-solution"></a>Vytváření a nasdílení změn vašeho řešení

V předchozí části jste vytvořili řešení IoT Edge a do modulu CModule jste přidali kód, který odfiltruje zprávy, ve kterých je hlášená teplota počítače v přípustných mezích. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Výběrem **View** (Zobrazit) > **Integrated Terminal** (Integrovaný terminál) otevřete integrovaný terminál VS Code.

1. Zadáním následujícího příkazu v integrovaném terminálu editoru Visual Studio Code se přihlaste k Dockeru. Musíte se přihlásit pomocí svých přihlašovacích údajů ke službě Azure Container Registry, abyste do registru mohli odeslat image modulu.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Použijte uživatelské jméno, heslo a přihlašovací server, který jste zkopírovali z registru kontejneru Azure v první části. Můžete ho také načíst znovu z oddílu **Přístupové klíče** ve vašem registru na webu Azure Portal.

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge).

Když editoru Visual Studio Code sdělíte, že má sestavit vaše řešení, nejprve vygeneruje soubor `deployment.json` v nové složce **config**. Informace pro soubor deployment.json se shromáždí ze souboru šablony, kterou jste aktualizovali, souboru .env, který jste použili k uložení přihlašovacích údajů k vašemu registru kontejneru, a souboru module.json ve složce CModule.

Pak se v integrovaném terminálu editoru Visual Studio Code spustí dva příkazy: `docker build` a `docker push`. Tyto dva příkazy sestaví kód, provedou kontejnerizaci knihovny `CModule.dll` a odešlou ji do kontejneru registru, který jste zadali při inicializaci řešení.

Úplnou adresu image kontejneru se značkou můžete vidět v integrovaném terminálu VS Code. Adresa image je sestavená z informací v souboru `module.json` a má formát **\<úložiště\>:\<verze\>-\<platforma\>**. V tomto kurzu by měla vypadat takto: **myregistry.azurecr.io/cmodule:0.0.1-amd64**.

>[!TIP]
>Pokud obdržíte chybu při vytváření a nasdílení změn modul, proveďte následující kontroly:
>* Přihlásili jste k Dockeru ve Visual Studio Code pomocí přihlašovacích údajů ze služby container registry? Tyto přihlašovací údaje se liší od těch, které používáte k přihlášení k webu Azure portal.
>* Správnost vašeho kontejneru úložiště? Otevřít **moduly** > **cmodule** > **module.json** a najít **úložiště** pole. Úložiště imagí by měl vypadat jako  **\<registryname\>.azurecr.io/cmodule**. 
>* Sestavujete stejného typu kontejnerů, které běží v počítači pro vývoj? Visual Studio Code výchozí kontejnery Linuxu amd64. Pokud vývojovém počítači běží arm32v7 kontejnery Linuxu, aktualizujte platformy na modrý stavového řádku v dolní části okna VS Code tak, aby odpovídaly vaši kontejnerovou platformu. Moduly C nemůže být sestaven jako kontejnery Windows. 

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

V článku Rychlý start, pomocí kterého jste nastavili své zařízení IoT Edge, jste nasadili modul pomocí webu Azure Portal. Můžete také nasadit moduly pomocí rozšíření Azure IoT Hub Toolkit (dříve rozšíření Azure IoT Toolkit) pro Visual Studio Code. Pro svůj scénář už máte připravený manifest nasazení – soubor **deployment.json**. Teď stačí jen vybrat zařízení, na které se nasazení provede.

1. V nástroji VS Code paletu příkazů, spusťte **Azure IoT Hub: Vyberte službu IoT Hub**.

2. Zvolte předplatné a centrum IoT obsahující zařízení IoT Edge, které chcete nakonfigurovat.

3. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub).

4. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

   ![Vytvoření nasazení pro jedno zařízení](./media/tutorial-c-module/create-deployment.png)

5. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json.

6. Klikněte na tlačítko pro obnovení. Měl by se zobrazit spuštěný nový modul **CModule** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

Na samotném zařízení IoT Edge můžete stav modulů nasazení zobrazit pomocí příkazu `iotedge list`. Měly by se zobrazit čtyři moduly: dva moduly runtime IoT Edge, tempSensor a vlastní modul, který jste vytvořili v tomto kurzu. Spuštění všech modulů může několik minut trvat, proto příkaz spusťte znovu, pokud se zpočátku všechny nezobrazí.

Pokud chcete zobrazit zprávy, které jednotlivé moduly generují, použijte příkaz `iotedge logs <module name>`.

Zprávy přicházející do centra IoT můžete zobrazit pomocí Visual Studio Code.

1. Když chcete monitorovat data, která přichází do služby IoT Hub, klikněte na **...** a vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
2. Pokud chcete monitorovat zprávy D2C pro konkrétní zařízení, klikněte pravým tlačítkem na příslušné zařízení v seznamu a vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
3. Pokud chcete monitorování dat zastavit, spusťte příkaz **Azure IoT Hub: Zastavit monitorování zpráv D2C** v paletě příkazů.
4. Pokud chcete zobrazit nebo aktualizovat dvojče modulu, klikněte pravým tlačítkem na příslušný modul v seznamu a vyberte **Edit module twin** (Upravit dvojče modulu). Pokud chcete aktualizovat dvojče modulu, uložte soubor JSON dvojčete, klikněte pravým tlačítkem na oblast editoru a vyberte **Update Module Twin** (Aktualizovat dvojče modulu).
5. Pokud chcete zobrazit protokoly Dockeru, můžete nainstalovat [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pro VS Code a vyhledat spuštěné moduly místně v průzkumníku Dockeru. Kliknutím na **Show Logs** (Zobrazit protokoly) v místní nabídce je zobrazíte v integrovaném terminálu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete chtít vytvářet vlastní moduly, získáte další informace na stránce o [vývoji modulu C pomocí Azure IoT Edge pro Visual Studio Code](how-to-develop-c-module.md). Pokračujte dalšími kurzy, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Uložení dat na hraničních zařízeních s využitím databází SQL Serveru](tutorial-store-data-sql-server.md)

