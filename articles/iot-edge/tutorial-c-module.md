---
title: Výuka vývoje modulu C pro Linux – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Tento kurz ukazuje, jak vytvořit modul IoT Edge s kódem C a nasadit ho do linuxového zařízení se systémem IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 11/07/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4236b7ad3b15d1bb58a146f5905d226e0c3833d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760943"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Kurz: Vývoj modulu C IoT Edge pro linuxová zařízení

Pomocí kódu Visual Studia můžete vyvinout kód C a nasadit ho do linuxového zařízení se systémem Azure IoT Edge.

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Použití editoru Visual Studio Code k vytvoření modulu IoT Edge v jazyce C
> * Použití editoru Visual Studio Code a Dockeru k vytvoření image Dockeru a jejímu publikování v registru kontejneru
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Obor řešení

Tento kurz ukazuje, jak vyvinout modul v **Jazyce C** pomocí **kódu sady Visual Studio**code a jak jej nasadit do zařízení S **IP**. Pokud vyvíjíte moduly pro zařízení s Windows, přejděte na [vývoj modulu C IoT Edge pro zařízení s Windows.](tutorial-c-module-windows.md)

V následující tabulce můžete pochopit možnosti vývoje a nasazování modulů C do Linuxu:

| C | Visual Studio Code | Visual Studio |
| - | ------------------ | ------------- |
| **Linux AMD64** | ![Použití kódu VS pro moduly C v Linuxu AMD64](./media/tutorial-c-module/green-check.png) | ![Použití VS pro moduly C na Linuxu AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Použití kódu VS pro moduly C v Linuxu ARM32](./media/tutorial-c-module/green-check.png) | ![Použití VS pro moduly C na Linuxu ARM32](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste měli projít předchozí kurz nastavení vývojového prostředí pro vývoj kontejnerů Linux: [Vývoj modulů IoT Edge pro zařízení s Linuxem](tutorial-develop-for-linux.md). Dokončením tohoto kurzu byste měli mít následující předpoklady:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Linuxové zařízení se systémem Azure IoT Edge](quickstart-linux.md)
* Registr kontejnerů, jako je [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Kód](https://code.visualstudio.com/) nakonfigurovaný pomocí [nástrojů Azure IoT .](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
* [Docker CE](https://docs.docker.com/install/) nakonfigurován pro spouštění kontejnerů Linuxu.

Chcete-li vyvinout modul IoT Edge v C, nainstalujte do vývojového počítače následující další předpoklady:

* [Rozšíření C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) pro Visual Studio Code.

## <a name="create-a-module-project"></a>Vytvoření projektu modulu

Následující kroky vytvořit projekt modulu IoT Edge pro C pomocí Visual Studio Code a rozšíření Nástroje Azure IoT. Po vytvoření šablony projektu přidejte nový kód tak, aby modul odfiltrovat zprávy na základě jejich hlášené vlastnosti.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení v C, kterou můžete přizpůsobit pomocí vlastního kódu.

1. Vyberte **Zobrazit** > **paletu příkazů,** chcete-li otevřít paletu příkazů VS Code.

2. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud jste už přihlášení, můžete tento krok přeskočit.

3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název řešení nebo přijměte výchozí **edgesolution**. |
   | Vyberte šablonu modulu | Zvolte **modul C**. |
   | Zadejte název modulu | Pojmenujte modul **CModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněna z názvu, který jste zadali v posledním kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br> Konečné úložiště bitových \<obrazů\>vypadá jako název registru .azurecr.io/cmodule. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte si cílovou architekturu

V současné době visual studio kód můžete vyvíjet moduly C pro Linux AMD64 a Linux ARM32v7 zařízení. Musíte vybrat architekturu, na kterou cílíte s každým řešením, protože kontejner je sestaven a spuštěn jinak pro každý typ architektury. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro edge řešení**nebo vyberte ikonu zástupce v bočním panelu v dolní části okna.

2. V paletě příkazů vyberte cílovou architekturu ze seznamu možností. Pro účely tohoto kurzu používáme jako zařízení IoT Edge virtuální stroj Ubuntu, takže zachováme výchozí **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Výchozí kód modulu přijímá zprávy ve vstupní frontě a předává je prostřednictvím výstupní fronty. Přidáme nějaký další kód, aby modul zpracuje zprávy na hraničním okraji před jejich předáním do ioT hubu. Aktualizujte modul tak, aby analyzoval data o teplotě v každé zprávě a zprávu odešle do služby IoT Hub pouze v případě, že teplota překročí určitou prahovou hodnotu.

1. Data ze snímače v tomto scénáři přicházejí ve formátu JSON. Pokud chcete filtrovat zprávy ve formátu JSON, importujte knihovnu JSON pro jazyk C. V tomto kurzu se používá Parson.

   1. Stáhněte si [úložiště Parson GitHub](https://github.com/kgabis/parson). Soubory **parson.c** a **parson.h** zkopírujte do složky **CModule**.

   2. Otevřete **moduly** > **CModule** > **CMakeLists.txt**. Na začátku souboru importujte soubory Parson jako knihovnu **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Přidat `my_parson` do seznamu knihoven v **target_link_libraries** funkce CMakeLists.txt.

   4. Uložte soubor **CMakeLists.txt**.

   5. Otevřené **moduly** > **CModule** > **main.c**. V dolní části seznamu příkazů include přidejte nový, který chcete zahrnout `parson.h` pro podporu JSON:

      ```c
      #include "parson.h"
      ```

1. Do souboru **main.c** přidejte za část s příkazy include globální proměnnou `temperatureThreshold`. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Najít `CreateMessageInstance` funkci v main.c. Nahraďte příkaz inner if-else následujícím kódem, který přidá několik řádků funkcí:

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

   Nové řádky kódu v příkazu else přidají do zprávy novou vlastnost, která zprávu označí jako výstrahu. Tento kód označuje všechny zprávy jako výstrahy, protože přidáme funkce, které odesílají zprávy jenom do IoT Hubu, pokud hlásí vysoké teploty.

1. Celou funkci `InputQueue1Callback` nahraďte následujícím kódem. Tato funkce implementuje samotný filtr zasílání zpráv. Po přijetí zprávy zkontroluje, zda hlášená teplota překročí prahovou hodnotu. Pokud ano, pak předá zprávu prostřednictvím své výstupní fronty. Pokud ne, pak ignoruje zprávu.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
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
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
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

1. Najděte `SetupCallbacksForModule` funkci. Nahraďte funkci následujícím kódem, který přidá **příkaz else if** ke kontrole, zda byla aktualizována dvojče modulu.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
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

## <a name="build-and-push-your-module"></a>Sestavte a tlačte svůj modul

V předchozí části jste vytvořili řešení IoT Edge a přidali kód do modulu CModule, který odfiltruje zprávy, kde je hlášená teplota počítače v přijatelných mezích. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Otevřete terminál VS Code výběrem možnosti **Zobrazit** > **terminál**.

1. Přihlaste se k Dockeru zadáním následujícího příkazu do terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru z registru kontejnerů Azure. Tyto hodnoty můžete načíst z části **Přístupové klíče** v registru na webu Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití programu `--password-stdin`. Zatímco tento osvědčený postup se doporučuje pro produkční scénáře, je mimo rozsah tohoto kurzu. Další informace naleznete v odkazu na [přihlášení dockeru.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. V průzkumníku kódu VS klikněte pravým tlačítkem myši na soubor **deployment.template.json** a vyberte **možnost Sestavit a push řešení IoT Edge**.

   Příkaz sestavení a nabízení spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config,** která obsahuje úplný manifest nasazení, sestavený z informací v šabloně nasazení a dalších souborech řešení. Za druhé `docker build` spustí k vytvoření image kontejneru na základě příslušné dockerfile pro cílovou architekturu. Potom spustí `docker push` k nabízení úložiště bitové kopie do registru kontejneru.

## <a name="deploy-modules-to-device"></a>Nasazení modulů do zařízení

Pomocí průzkumníka kódu Visual Studio a rozšíření Nástroje Azure IoT k nasazení projektu modulu do zařízení IoT Edge. Již máte manifest nasazení připravený pro váš scénář, soubor **deployment.json** ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že vaše zařízení IoT Edge je v provozu.

1. V průzkumníku kódu Visual Studia rozbalte část **Zařízení služby Azure IoT Hub,** abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json.

4. Klikněte na tlačítko pro obnovení. Měli byste vidět nový **CModule** běží spolu s **modulu SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. V průzkumníku kódu Visual Studia klikněte pravým tlačítkem myši na název zařízení IoT Edge a vyberte **spustit sledování integrovaného koncového bodu událostí**.

2. Zobrazení zpráv přicházejících do vašeho IoT Hubu. Může chvíli trvat, než zprávy dorazí, protože zařízení IoT Edge musí přijímat nové nasazení a spustit všechny moduly. Změny, které jsme provedli v kódu CModule, pak před odesláním zpráv počkejte, dokud teplota stroje nedosáhne 25 stupňů. Přidá také typ zprávy **Výstraha** všechny zprávy, které dosáhnou této prahové hodnoty teploty.

   ![Zobrazení zpráv přicházejících do ioT hubu](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Úprava dvojčete modulu

Použili jsme dvojče modulu CModule v manifestu nasazení, abychom nastavili teplotní práh na 25 stupňů. Dvojče modulu můžete použít ke změně funkce bez nutnosti aktualizace kódu modulu.

1. V kódu Visual Studio rozbalte podrobnosti v zařízení IoT Edge a podívejte se na spuštěné moduly.

2. Klepněte pravým tlačítkem myši na **příkaz CModule** a vyberte **příkaz Upravit dvojče modulu**.

3. Najít **TemperatureThreshold** v požadovaných vlastnostech. Změňte jeho hodnotu na novou teplotu o 5 stupňů až 10 stupňů vyšší než poslední hlášená teplota.

4. Uložte soubor dvojčete modulu.

5. Klepněte pravým tlačítkem myši na libovolné místo v podokně úprav dvojčete modulu a vyberte **možnost Aktualizovat dvojče modulu**.

6. Sledujte příchozí zprávy mezi zařízeními a cloudy. Měli byste vidět zprávy zastavit, dokud není dosaženo nového prahu teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurace a prostředky Azure, které jste použili v tomto článku, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete připraveni vytvořit vlastní moduly, můžete se dozvědět více o [vývoji vlastních modulů IoT Edge](module-development.md) nebo o [vývoji modulů s visual studio code](how-to-vs-code-develop-module.md). Příklady modulů IoT Edge, včetně modulu simulované teploty, viz [vzorky modulů IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules) a [vzorky IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).

Můžete pokračovat na další kurzy se dozvíte, jak Azure IoT Edge vám může pomoci nasadit cloudové služby Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Vlastní Vision Service](tutorial-deploy-custom-vision.md)
