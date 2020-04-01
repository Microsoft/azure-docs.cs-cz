---
title: Kurz vývoje modulu C pro Windows – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Tento kurz ukazuje, jak vytvořit modul IoT Edge s kódem C a nasadit ho do zařízení s Windows se systémem IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 09d039801107a44df4f3bf3745a1e074e6d708b8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760960"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Kurz: Vývoj modulu C IoT Edge pro zařízení se systémem Windows

Pomocí Visual Studia můžete vyvinout kód C a nasadit ho na zařízení s Windows se systémem Azure IoT Edge.

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Visual Studio slouží k vytvoření modulu IoT Edge, který je založen na sadě C SDK.
> * Pomocí Visual Studia a Dockeru vytvořte bitovou kopii Dockeru a publikujte ji do registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Obor řešení

Tento kurz ukazuje, jak vyvinout modul v **Jazyce C** pomocí **Visual Studia 2019** a nasadit ho do **zařízení s Windows**. Pokud vyvíjíte moduly pro linuxová zařízení, přejděte na [vývoj modulu C IoT Edge pro zařízení s Linuxem.](tutorial-c-module.md)

V následující tabulce můžete pochopit možnosti vývoje a nasazování modulů C do zařízení s Windows:

| C | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Vývoj modulů C pro WinAMD64 ve Visual Studiu](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste měli projít předchozí kurz nastavení vývojového prostředí pro vývoj kontejnerů systému Windows: [Vývoj modulů IoT Edge pro zařízení se systémem Windows](tutorial-develop-for-windows.md). Po dokončení tohoto výukového programu byste měli mít následující předpoklady na místě:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Zařízení s Windows se systémem Azure IoT Edge](quickstart.md).
* Registr kontejnerů, jako je [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) nakonfigurované s rozšířením [Azure IoT Edge Tools.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) nakonfigurovaný pro spouštění kontejnerů windows.
* Nainstalujte sadu Azure IoT C SDK pro Windows x64 až vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Pokud používáte Visual Studio 2017 (verze 15.7 nebo vyšší), stáhněte a nainstalujte [nástroje Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) pro VS 2017 z webu Visual Studio Marketplace.

## <a name="create-a-module-project"></a>Vytvoření projektu modulu

Následující kroky vytvoří projekt modulu IoT Edge, který je založen na sadě C SDK pomocí sady Visual Studio a rozšíření Azure IoT Edge Tools. Po vytvoření šablony projektu přidejte nový kód tak, aby modul odfiltrovat zprávy na základě jejich hlášené vlastnosti.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení v C, kterou můžete přizpůsobit pomocí vlastního kódu.

1. Spusťte Visual Studio 2019 a vyberte **Vytvořit nový projekt**.

2. Vyhledejte **IoT Edge** a zvolte projekt **Azure IoT Edge (Windows amd64).** Klikněte na **Další**.

   ![Vytvoření nového projektu Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

3. Přejmenujte projekt a řešení na něco popisného, jako je **CTutorialApp**. Chcete-li vytvořit projekt, klepněte na **tlačítko Vytvořit.**

   ![Konfigurace nového projektu Azure IoT Edge](./media/tutorial-c-module-windows/configure-project.png)

4. Nakonfigurujte projekt s následujícími hodnotami:

   | Pole | Hodnota |
   | ----- | ----- |
   | Výběr šablony | Vyberte **modul C**. |
   | Název projektu modulu | Pojmenujte modul **CModule**. |
   | Úložiště bitových obrázků dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněna z hodnoty názvu projektu modulu. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br> Konečné úložiště bitových \<obrazů\>vypadá jako název registru .azurecr.io/cmodule. |

   ![Konfigurace projektu pro cílové zařízení, typ modulu a registr kontejneru](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Chcete-li vytvořit projekt, vyberte **přidat.**

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

Manifest nasazení sdílí pověření pro registr kontejnerů s runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. Použijte přihlašovací údaje z části **Přístupové klíče** v registru kontejnerů Azure.

1. V průzkumníku řešení Visual Studio otevřete soubor **deployment.template.json.**

2. Najděte vlastnost **registryCredentials** v $edgeAgent požadované vlastnosti. Adresa registru by měla být automaticky vyplněna z informací, které jste zadali při vytváření projektu. Pole uživatelského jména a hesla by měla obsahovat názvy proměnných. Například:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Otevřete soubor **ENV** v modulovém řešení. (Ve výchozím nastavení je v Průzkumníku řešení skrytá, takže možná budete muset vybrat tlačítko **Zobrazit všechny soubory,** abyste ho zobrazili.) Soubor .env by měl obsahovat stejné proměnné uživatelského jména a hesla, které jste viděli v souboru deployment.template.json.

4. Přidejte hodnoty **Uživatelské jméno** a **heslo** z registru kontejnerů Azure.

5. Uložte změny do souboru ENV.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Výchozí kód modulu přijímá zprávy ve vstupní frontě a předává je prostřednictvím výstupní fronty. Přidáme nějaký další kód, aby modul zpracuje zprávy na hraničním okraji před jejich předáním do ioT hubu. Aktualizujte modul tak, aby analyzoval data o teplotě v každé zprávě a zprávu odešle do služby IoT Hub pouze v případě, že teplota překročí určitou prahovou hodnotu.

1. Data ze snímače v tomto scénáři přicházejí ve formátu JSON. Pokud chcete filtrovat zprávy ve formátu JSON, importujte knihovnu JSON pro jazyk C. V tomto kurzu se používá Parson.

   1. Stáhněte si [úložiště Parson GitHub](https://github.com/kgabis/parson). Zkopírujte soubory **parson.c** a **parson.h** do projektu **CModule.**

   2. V sadě Visual Studio otevřete soubor **CMakeLists.txt** ze složky projektu CModule. Na začátku souboru importujte soubory Parson jako knihovnu **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Přidejte `my_parson` do seznamu knihoven v **části target_link_libraries** souboru CMakeLists.txt.

   4. Uložte soubor **CMakeLists.txt**.

   5. Otevřete **CModule** > **main.c**. V dolní části seznamu příkazů include přidejte nový, který chcete zahrnout `parson.h` pro podporu JSON:

      ```c
      #include "parson.h"
      ```

2. V souboru **main.c** přidejte `temperatureThreshold` globální proměnnou volanou vedle proměnné messagesReceivedByInput1Queue. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Najít `CreateMessageInstance` funkci v main.c. Nahraďte příkaz inner if-else následujícím kódem, který přidá několik řádků funkcí:

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

4. Najděte `InputQueue1Callback` funkci a nahraďte celou funkci následujícím kódem. Tato funkce implementuje samotný filtr zasílání zpráv. Po přijetí zprávy zkontroluje, zda hlášená teplota překročí prahovou hodnotu. Pokud ano, pak předá zprávu prostřednictvím své výstupní fronty. Pokud ne, pak ignoruje zprávu.

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

6. Najděte `SetupCallbacksForModule` funkci. Nahraďte funkci následujícím kódem, který přidá **příkaz else if** ke kontrole, zda byla aktualizována dvojče modulu.

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

7. Uložte soubor main.c.

8. Otevřete soubor **deployment.template.json.**

9. Přidejte do manifestu nasazení dvojče modulu CModule. Vložte následující obsah JSON do dolní části oddílu `moduleContent`, za dvojče modulu `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Přidání dvojčete modulu CModule do šablony nasazení](./media/tutorial-c-module-windows/module-twin.png)

10. Uložte soubor **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Sestavte a tlačte svůj modul

V předchozí části jste vytvořili řešení IoT Edge a přidali kód do **modulu CModule** pro odfiltrování zpráv, kde je hlášená teplota počítače pod přijatelnou prahovou hodnotou. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Pomocí následujícího příkazu se přihlaste k Dockeru ve vývojovém počítači. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru z registru kontejnerů Azure. Tyto hodnoty můžete načíst z části **Přístupové klíče** v registru na webu Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití programu `--password-stdin`. Zatímco tento osvědčený postup se doporučuje pro produkční scénáře, je mimo rozsah tohoto kurzu. Další informace naleznete v odkazu na [přihlášení dockeru.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

2. V průzkumníku řešení Visual Studio klikněte pravým tlačítkem myši na název projektu, který chcete sestavit. Výchozí název je **AzureIotEdgeApp1** a vzhledem k tomu, že vytváříte modul Windows, rozšíření by mělo být **Windows.Amd64**.

3. Vyberte **sestavení a nabízení modulů IoT Edge**.

   Příkaz sestavení a nabízení spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config,** která obsahuje úplný manifest nasazení, sestavený z informací v šabloně nasazení a dalších souborech řešení. Za druhé `docker build` spustí k vytvoření image kontejneru na základě příslušné dockerfile pro cílovou architekturu. Potom spustí `docker push` k nabízení úložiště bitové kopie do registru kontejneru.

## <a name="deploy-modules-to-device"></a>Nasazení modulů do zařízení

Pomocí průzkumníka cloudu Visual Studio a rozšíření Azure IoT Edge Tools nasadit projekt modulu do zařízení IoT Edge. Již máte manifest nasazení připravený pro váš scénář, soubor **deployment.json** ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že vaše zařízení IoT Edge je v provozu.

1. V průzkumníku cloudu Visual Studia rozbalte prostředky a podívejte se na seznam zařízení IoT.

2. Klikněte pravým tlačítkem myši na název zařízení IoT Edge, které chcete přijmout nasazení.

3. Vyberte **možnost Vytvořit nasazení**.

4. V průzkumníku souborů vyberte soubor **deployment.windows-amd64** v konfigurační složce vašeho řešení.

5. Aktualizujte průzkumníka cloudu, abyste viděli nasazené moduly uvedené pod vaším zařízením.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Pomocí rozšíření Nástroje pro edge IoT můžete zobrazit zprávy při jejich doručení do centra IoT Hub.

1. V průzkumníku cloudu Visual Studia vyberte název zařízení IoT Edge.

2. V seznamu **Akce** vyberte **Spustit sledování předdefinovaného koncového bodu událostí**.

3. Zobrazení zpráv přicházejících do vašeho IoT Hubu. Může chvíli trvat, než zprávy dorazí, protože zařízení IoT Edge musí přijímat nové nasazení a spustit všechny moduly. Změny, které jsme provedli v kódu CModule, pak před odesláním zpráv počkejte, dokud teplota stroje nedosáhne 25 stupňů. Přidá také typ zprávy **Výstraha** všechny zprávy, které dosáhnou této prahové hodnoty teploty.

   ![Zobrazení zpráv přicházejících do ioT hubu](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Úprava dvojčete modulu

Použili jsme dvojče modulu CModule, abychom nastavili teplotní práh na 25 stupňů. Dvojče modulu můžete použít ke změně funkce bez nutnosti aktualizace kódu modulu.

1. V sadě Visual Studio otevřete soubor **deployment.windows-amd64.json.** (Není soubor deployment.template. Pokud manifest nasazení v konfiguračním souboru v průzkumníku řešení nevidíte, vyberte na panelu nástrojů průzkumníka ikonu **Zobrazit všechny soubory.)**

2. Najděte dvojče Modulu CModul a změňte hodnotu parametru **temperatureThreshold** na novou teplotu o 5 až 10 stupňů vyšší, než je poslední hlášená teplota.

3. Uložte soubor **deployment.windows-amd64.json.**

4. Podle pokynů pro nasazení znovu použít aktualizovaný manifest nasazení na vašem zařízení.

5. Sledujte příchozí zprávy mezi zařízeními a cloudy. Měli byste vidět zprávy zastavit, dokud není dosaženo nového prahu teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurace a prostředky Azure, které jste použili v tomto článku, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete připraveni vytvořit vlastní moduly, můžete se dozvědět více o [vývoji vlastních modulů IoT Edge](module-development.md) nebo o [vývoji modulů v sadě Visual Studio](how-to-visual-studio-develop-module.md). Příklady modulů IoT Edge, včetně modulu simulované teploty, viz [vzorky modulů IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules) a [vzorky IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).

Můžete pokračovat na další kurzy se dozvíte, jak Azure IoT Edge vám může pomoci nasadit cloudové služby Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Vlastní Vision Service](tutorial-deploy-custom-vision.md)
