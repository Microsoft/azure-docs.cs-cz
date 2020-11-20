---
title: Kurz vývoj modulu C pro Windows – Azure IoT Edge | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem jazyka C a jak ho nasadit na zařízení s Windows se systémem IoT Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d9cffcadcb95b6c8c61205d458610f402fa7286d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964588"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Kurz: vývoj modulu C IoT Edge pro zařízení s Windows

Pomocí sady Visual Studio můžete vyvíjet kód C a nasazovat ho do zařízení s Windows, na kterém běží Azure IoT Edge.

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Pomocí sady Visual Studio vytvořte modul IoT Edge založený na sadě C SDK.
> * Pomocí sady Visual Studio a Docker vytvoříte image Docker a publikujete ji do svého registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Tento kurz ukazuje, jak vytvořit modul v jazyce **C** pomocí sady **Visual Studio 2019** a jak ho nasadit na **zařízení s Windows**. Pokud vyvíjíte moduly pro zařízení se systémem Linux, použijte místo toho [vývoj modulu C IoT Edge pro zařízení se systémem Linux](tutorial-c-module.md) .

Následující tabulka vám pomůže pochopit možnosti vývoje a nasazení modulů C do zařízení s Windows:

| C | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Systém Windows AMD64** |  | ![Vývoj modulů C pro WinAMD64 v aplikaci Visual Studio](./media/tutorial-c-module/green-check.png) |

Před zahájením tohoto kurzu byste si měli projít předchozí kurz pro nastavení vývojového prostředí pro vývoj kontejnerů Windows: [vývoj IoT Edgech modulů pro zařízení s Windows](tutorial-develop-for-windows.md). Po dokončení tohoto kurzu byste měli mít následující požadavky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Zařízení se systémem Windows, na kterém běží Azure IoT Edge](quickstart.md).
* Registr kontejneru, například [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio) nakonfigurovaný s rozšířením [nástrojů Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) nakonfigurovaný pro spouštění kontejnerů Windows.
* Nainstalujte sadu Azure IoT C SDK pro Windows x64 prostřednictvím vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Pokud používáte Visual Studio 2017 (verze 15,7 nebo novější), Stáhněte si a nainstalujte [Azure IoT Edge nástroje](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) pro vs 2017 z webu Visual Studio Marketplace.

## <a name="create-a-module-project"></a>Vytvořit projekt modulu

Následující postup slouží k vytvoření projektu IoT Edge modulu, který je založen na sadě C SDK pomocí sady Visual Studio a rozšíření Azure IoT Edge Tools. Jakmile máte vytvořenou šablonu projektu, přidejte nový kód tak, aby modul vyfiltroval zprávy na základě jejich hlášených vlastností.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení v C, kterou můžete přizpůsobit pomocí vlastního kódu.

1. Spusťte Visual Studio 2019 a vyberte **vytvořit nový projekt**.

2. Vyhledejte **IoT Edge** a vyberte projekt **Azure IoT Edge (Windows amd64)** . Klikněte na **Next** (Další).

   ![Vytvoření nového projektu Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

3. Přejmenujte projekt a řešení na něco popisného, jako je **CTutorialApp**. Kliknutím na **vytvořit** vytvořte projekt.

   ![Konfigurace nového projektu Azure IoT Edge](./media/tutorial-c-module-windows/configure-project.png)

4. Nakonfigurujte projekt s následujícími hodnotami:

   | Pole | Hodnota |
   | ----- | ----- |
   | Vybrat šablonu | Vyberte **modul C**. |
   | Název projektu modulu | Pojmenujte modul **CModule**. |
   | Úložiště imagí Docker | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Vaše image kontejneru je předem vyplněná z hodnoty název projektu modulu. Položku **localhost: 5000** nahraďte hodnotou **přihlašovacího serveru** z služby Azure Container Registry. Přihlašovací server můžete načíst ze stránky přehled v registru kontejneru v Azure Portal. <br><br> Konečné úložiště imagí vypadá jako \<registry name\> . azurecr.IO/cmodule. |

   ![Konfigurace projektu pro cílové zařízení, typ modulu a registr kontejnerů](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Vyberte **Přidat** a vytvořte projekt.

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

Manifest nasazení sdílí přihlašovací údaje pro váš registr kontejneru s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. Použijte přihlašovací údaje z oddílu **přístupové klíče** ve službě Azure Container Registry.

1. V Průzkumníku řešení sady Visual Studio otevřete **deployment.template.jsv** souboru.

2. V $edgeAgent požadovaných vlastnostech Najděte vlastnost **registryCredentials** . Měla by mít vaše adresa registru automatické vyplňování z informací, které jste zadali při vytváření projektu. Pole uživatelského jména a hesla by měla obsahovat názvy proměnných. Například:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Otevřete soubor **. env** v řešení modulu. (Ve výchozím nastavení je to ve Průzkumník řešení skryté, takže možná budete muset vybrat tlačítko **Zobrazit všechny soubory** a zobrazit ho.) Soubor. env by měl obsahovat stejné proměnné uživatelského jména a hesla, které jste viděli v deployment.template.jssouboru.

4. Přidejte hodnoty **uživatelského jména** a **hesla** z Azure Container Registry.

5. Uložte změny do souboru. env.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Výchozí kód modulu přijímá zprávy ve vstupní frontě a předává je spolu s výstupní frontou. Pojďme přidat nějaký další kód, aby modul zpracoval zprávy na hranici před jejich přesměrováním na IoT Hub. Aktualizujte modul tak, aby analyzoval data o teplotě v každé zprávě, a pošle zprávu jenom IoT Hub, pokud teplota překročí určitou prahovou hodnotu.

1. Data ze snímače v tomto scénáři přicházejí ve formátu JSON. Pokud chcete filtrovat zprávy ve formátu JSON, importujte knihovnu JSON pro jazyk C. V tomto kurzu se používá Parson.

   1. Stáhněte si [úložiště GitHub Parson](https://github.com/kgabis/parson). Zkopírujte soubory **Parson. c** a **Parson. h** do projektu **CModule** .

   2. V aplikaci Visual Studio otevřete **CMakeLists.txt** soubor ze složky projektu CModule. Na začátku souboru importujte soubory Parson jako knihovnu **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Přidejte `my_parson` do seznamu knihoven v části **target_link_libraries** souboru CMakeLists.txt.

   4. Uložte soubor **CMakeLists.txt**.

   5. Otevřete **CModule**  >  **Main. c**. V dolní části seznamu příkazů include přidejte nové, které chcete zahrnout do `parson.h` podpory JSON:

      ```c
      #include "parson.h"
      ```

2. V **hlavním souboru. c** přidejte globální proměnnou s názvem `temperatureThreshold` vedle proměnné messagesReceivedByInput1Queue. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Vyhledejte `CreateMessageInstance` funkci v Main. c. Nahraďte příkaz Inner if-else následujícím kódem, který přidá několik řádků funkčnosti:

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

   Nové řádky kódu v příkazu else přidají do zprávy novou vlastnost, která označí zprávu jako výstrahu. Tento kód označí všechny zprávy jako upozornění, protože přidáme funkce, které pošle zprávy pouze do IoT Hub, pokud budou sestavy vysoké teploty.

4. Vyhledejte `InputQueue1Callback` funkci a nahraďte celou funkci následujícím kódem. Tato funkce implementuje samotný filtr zasílání zpráv. Při přijetí zprávy kontroluje, zda velikost hlášené teploty překročí prahovou hodnotu. Pokud ano, přepošle zprávu přes výstupní frontu. Pokud ne, bude zpráva ignorována.

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

6. Vyhledejte `SetupCallbacksForModule` funkci. Nahraďte funkci následujícím kódem, který přidá příkaz **else if** pro kontrolu, zda byl modul nefunkční.

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

8. Otevřete **deployment.template.jsv** souboru.

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

## <a name="build-and-push-your-module"></a>Sestavení a vložení modulu

V předchozí části jste vytvořili řešení IoT Edge a Přidali jste do **CModule** kód pro odfiltrování zpráv, u kterých se teplota hlášeného počítače nachází pod přijatelnou prahovou hodnotou. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

### <a name="sign-in-to-docker"></a>Přihlásit se k Docker

Poskytněte přihlašovací údaje registru kontejneru do Docker na svém vývojovém počítači, aby bylo možné vložit image kontejneru do registru.

1. Otevřete PowerShell nebo příkazový řádek.

2. Přihlaste se k Docker pomocí přihlašovacích údajů služby Azure Container Registry, které jste uložili po vytvoření registru.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití nástroje `--password-stdin` . I když se tento osvědčený postup doporučuje u produkčních scénářů, je mimo rozsah tohoto kurzu. Další informace najdete v tématu přihlašovací Reference k [Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

### <a name="build-and-push"></a>Sestavení a vložení

Váš vývojový počítač má teď přístup k vašemu registru kontejnerů a vaše IoT Edgeá zařízení budou taky. Je čas převést kód projektu na Image kontejneru.

1. V Průzkumníku řešení sady Visual Studio klikněte pravým tlačítkem myši na název projektu, který chcete sestavit. Výchozí název je **AzureIotEdgeApp1**. Pro tento kurz jste zvolili název **CTutorialApp** . Vzhledem k tomu, že vytváříte modul Windows, musí být přípona **Windows. amd64**.

2. Vyberte **sestavení a moduly nabízených IoT Edge**.

   Příkaz Build a push spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config** , která obsahuje úplný manifest nasazení, vyplní informace v šabloně nasazení a další soubory řešení. Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. Pak se spustí a nahraje `docker push` úložiště imagí do registru kontejneru.

   Tento proces může trvat několik minut poprvé, ale při příštím spuštění příkazů je rychlejší.

## <a name="deploy-modules-to-device"></a>Nasadit moduly do zařízení

K nasazení projektu modulu do zařízení IoT Edge použijte Visual Studio Cloud Explorer a rozšíření Azure IoT Edge Tools. Už máte připravený manifest nasazení pro váš scénář, **deployment.windows-amd64.js** v souboru ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.


Ujistěte se, že je zařízení IoT Edge spuštěné.

1. V Průzkumníku cloudu sady Visual Studio rozbalte prostředky, abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem myši na název zařízení IoT Edge, pro které chcete nasazení přijmout.

3. Vyberte **vytvořit nasazení**.

4. V Průzkumníku souborů vyberte soubor **nasazení. Windows-amd64** v konfigurační složce vašeho řešení.

5. Aktualizujte Průzkumníka cloudu, aby se zobrazily nasazené moduly uvedené v rámci vašeho zařízení.

## <a name="view-generated-data"></a>Zobrazit vygenerovaná data

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

K zobrazení zpráv při jejich doručování do IoT Hub můžete použít rozšíření IoT Edge Tools.

1. V Průzkumníku cloudu sady Visual Studio vyberte název zařízení IoT Edge.

2. V seznamu **Akce** vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

3. Zobrazení zpráv přicházejících do IoT Hub. Doručení zpráv může chvíli trvat, protože IoT Edge zařízení musí přijmout nové nasazení a spustit všechny moduly. Změny, které jsme provedli v kódu CModule, čekají, dokud teplota počítače nedosáhne 25 stupňů před odesláním zpráv. Přidá také **výstrahu** typu zpráva pro všechny zprávy, které dosáhnou prahové hodnoty teploty.

   ![Zobrazení zpráv přicházejících na IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Upravit nevlákenný modul

Pro nastavení prahové hodnoty teploty v 25 stupních jsme použili dvojitou CModule modul. Chcete-li změnit funkčnost, aniž byste museli aktualizovat kód modulu, můžete použít vlákna modulu.

1. V aplikaci Visual Studio otevřete **deployment.windows-amd64.jsv** souboru. (Ne soubor Deployment. template. Pokud se v konfiguračním souboru v Průzkumníkovi řešení nezobrazuje manifest nasazení, vyberte na panelu nástrojů Průzkumníka ikonu **Zobrazit všechny soubory** .)

2. Najděte CModule dvojitou hodnotu a změňte hodnotu parametru **temperatureThreshold** na novou teplotu 5 stupňů na 10 stupňů vyšší než Poslední hlášená teplota.

3. Uložte **deployment.windows-amd64.jsdo** souboru.

4. Podle kroků nasazení znovu použijte aktualizovaný manifest nasazení na vaše zařízení.

5. Umožňuje monitorovat příchozí zprávy ze zařízení do cloudu. Měli byste vidět, že se zprávy zastavily, dokud se nedosáhne nové prahové hodnoty teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurace a prostředky Azure, které jste použili v tomto článku, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge.

V dalších kurzech můžete pokračovat a zjistit, jak vám Azure IoT Edge může pomáhat s nasazením cloudových služeb Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)