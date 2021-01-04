---
title: Kurz – vývoj modulů C pro Windows pomocí Azure IoT Edge
description: V tomto kurzu se dozvíte, jak vytvořit moduly IoT Edge pomocí kódu jazyka C a jak je nasadit na zařízení s Windows, na kterých běží IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1f346e1b737075fa79dc1146152125a6c5a3ec1a
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704673"
---
# <a name="tutorial-develop-c-iot-edge-modules-for-windows-devices"></a>Kurz: vývoj modulů C IoT Edge pro zařízení s Windows

V tomto článku se dozvíte, jak pomocí sady Visual Studio vyvíjet kód C a nasadit ho do zařízení s Windows, na kterém běží Azure IoT Edge.

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje vaši obchodní logiku přímo v zařízeních IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Pomocí sady Visual Studio vytvořte modul IoT Edge založený na sadě C SDK.
> * Pomocí sady Visual Studio a Docker vytvoříte image Docker a publikujete ji do svého registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Modul odesílá zprávy pouze v případě, že teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Tento kurz ukazuje, jak vytvořit modul v jazyce C pomocí sady Visual Studio 2019 a jak ho nasadit na zařízení s Windows. Pokud vyvíjíte moduly pro zařízení se systémem Linux, místo toho použijte k [vývoji modulů C IoT Edge pro Linux](tutorial-csharp-module.md) .

Informace o možnostech vývoje a nasazení modulů jazyka C v zařízeních s Windows najdete v následující tabulce:

| C | Visual &nbsp; Studio &nbsp; Code | Visual Studio 2017 &nbsp; a &nbsp; 2019 |
| -- | ------------------ | :------------------: |
| Systém Windows AMD64 |  | ![Vývoj modulů C pro WinAMD64 v aplikaci Visual Studio](./media/tutorial-c-module/green-check.png) |

Před zahájením tohoto kurzu nastavte vývojové prostředí podle pokynů v kurzu [vývoj IoT Edge modulů pro zařízení s Windows](tutorial-develop-for-windows.md) . Po dokončení bude vaše prostředí obsahovat následující požadavky:

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) na bezplatné nebo standardní úrovni v Azure.
* [Zařízení s Windows, na kterém běží Azure IoT Edge](quickstart.md).
* Registr kontejnerů, například [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio)nakonfigurovaný s rozšířením [nástrojů Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)nakonfigurovaný pro spouštění kontejnerů Windows.

Pomocí následujících příkazů nainstalujte sadu Azure IoT C SDK pro Windows x64 prostřednictvím vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Pokud používáte Visual Studio 2017 (verze 15,7 nebo novější), Stáhněte a nainstalujte Azure IoT Edge nástroje pro Visual Studio 2017 z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Vytvořit projekt modulu

V této části vytvoříte projekt modulu IoT Edge, který je založen na sadě C SDK pomocí sady Visual Studio a rozšíření Azure IoT Edge Tools. Po vytvoření šablony projektu přidáte nový kód tak, aby modul vyfiltroval zprávy na základě jejich hlášených vlastností.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení v C, kterou můžete přizpůsobit pomocí vlastního kódu.

1. Otevřete Visual Studio 2019 a pak vyberte **vytvořit nový projekt**.

1. V podokně **vytvořit nový projekt** vyhledejte **IoT Edge** a potom v seznamu výsledků vyberte projekt **Azure IoT Edge (Windows amd64)** .

   ![Snímek obrazovky s podoknem IoT Edge vytvořit nový projekt](./media/tutorial-c-module-windows/new-project.png)

1. Vyberte **Další**.

    Otevře se podokno **Konfigurovat nový projekt** .

   ![Snímek obrazovky s podoknem konfigurovat nový projekt](./media/tutorial-c-module-windows/configure-project.png)

1. V podokně **Konfigurovat nový projekt** přejmenujte projekt a řešení na výstižnější, například **CTutorialApp**. 

1. Vyberte **Vytvořit** a vytvořte projekt.

   Otevře se podokno **Přidat modul** .

   ![Snímek obrazovky s podoknem přidat modul pro konfiguraci projektu](./media/tutorial-c-module-windows/add-application-and-module.png)

1. Na stránce **Konfigurovat nový projekt** proveďte následující:

   a. V levém podokně vyberte šablonu **modulu C** .  
   b. Do pole **název modulu** zadejte **CModule**.  
   c. V poli **Adresa URL úložiště** nahraďte **localhost: 5000** hodnotou **přihlašovacího serveru** z služby Azure Container Registry, a to v následujícím formátu: `<registry name>.azurecr.io/cmodule`

    > [!NOTE]
    > Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Vaše image kontejneru je předem vyplněná z modulu hodnota název projektu.  Přihlašovací server můžete načíst ze stránky přehled v registru kontejneru v Azure Portal.

1. Vyberte **Přidat** a vytvořte projekt.

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

Manifest nasazení sdílí přihlašovací údaje pro váš registr kontejneru s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. Použijte přihlašovací údaje z oddílu **přístupové klíče** ve službě Azure Container Registry.

1. V aplikaci Visual Studio Průzkumník řešení otevřete *deployment.template.jsv* souboru.

1. V $edgeAgent požadovaných vlastnostech vyhledejte vlastnost **registryCredentials** . Adresa registru vlastnosti by měla být vyplněna informacemi, které jste zadali při vytváření projektu. Pole uživatelského jména a hesla by měla obsahovat názvy proměnných. Například:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Otevřete soubor prostředí (ENV) ve vašem řešení modulu. Ve výchozím nastavení je soubor v Průzkumník řešení skrytý, takže možná budete muset vybrat tlačítko **Zobrazit všechny soubory** a zobrazit ho. Soubor ENV by měl obsahovat stejné proměnné uživatelského jména a hesla, které jste viděli v *deployment.template.js* souboru.

1. Přidejte hodnoty **uživatelského jména** a **hesla** z Azure Container Registry.

1. Uložte změny do souboru ENV.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Výchozí kód modulu přijímá zprávy ve vstupní frontě a předává je spolu s výstupní frontou. Pojďme přidat nějaký další kód, aby modul zpracoval zprávy na hraničních zařízeních před jejich přesměrováním do služby IoT Hub. Aktualizujte modul tak, aby analyzoval data o teplotě v každé zprávě a odeslal zprávu do centra IoT pouze v případě, že teplota překročí určitou prahovou hodnotu.

1. Data ze snímače v tomto scénáři přicházejí ve formátu JSON. Pokud chcete filtrovat zprávy ve formátu JSON, importujte knihovnu JSON pro jazyk C. V tomto kurzu se používá Parson.

   a. Stáhněte si [úložiště GitHub Parson](https://github.com/kgabis/parson).  
   b. Zkopírujte soubory *Parson. c* a *Parson. h* do projektu CModule.  
   c. V aplikaci Visual Studio otevřete *CMakeLists.txt* soubor ze složky projektu CModule.  
   d. Na začátku souboru importujte soubory Parson jako knihovnu **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. Přidejte `my_parson` do seznamu knihoven v části "target_link_libraries" souboru *CMakeLists.txt* .  
   f. Uložte soubor *CMakeLists.txt*.  
   například Vyberte **CModule**  >  **Main. c**. V dolní části seznamu příkazů include přidejte nový příkaz, který se má zahrnout do `parson.h` podpory JSON:

      ```c
      #include "parson.h"
      ```

1. V *hlavním souboru. c* přidejte globální proměnnou s názvem `temperatureThreshold` vedle `messagesReceivedByInput1Queue` proměnné. Tato proměnná nastaví hodnotu, kterou musí měřená teplota překročit, aby se data odesílala do služby IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Vyhledejte `CreateMessageInstance` funkci v *Main. c*. Nahraďte příkaz Inner *if-else* následujícím kódem, který přidá několik řádků funkčnosti:

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

   Nové řádky kódu v příkazu *Else* přidají do zprávy novou vlastnost, která označí zprávu jako výstrahu. Tento kód označí všechny zprávy jako upozornění, protože přidáme funkce, které odesílají zprávy do centra IoT pouze v případě, že budou vykazovat vysoké teploty.

1. Vyhledejte `InputQueue1Callback` funkci a nahraďte celou funkci následujícím kódem. Tato funkce implementuje samotný filtr zasílání zpráv. Při přijetí zprávy kontroluje, zda velikost hlášené teploty překročí prahovou hodnotu. Pokud teplota překročí prahovou hodnotu, funkce přepošle zprávu přes výstupní frontu. Pokud nepřekročí prahovou hodnotu, funkce ignoruje zprávu.

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

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
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
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Přidejte funkci `moduleTwinCallback`. Tato metoda přijímá aktualizace požadovaných vlastností dvojčete modulu a aktualizuje proměnnou **temperatureThreshold** na stejnou hodnotu. Všechny moduly mají vlastní modul s dvojitou funkční, což umožňuje konfigurovat kód, který běží uvnitř modulu přímo z cloudu.

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

1. Vyhledejte `SetupCallbacksForModule` funkci. Nahraďte funkci následujícím kódem, který přidá příkaz *else-if* ke kontrole, zda byl modul nefunkční.

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

1. Uložte soubor *main.c*.

1. Otevřete *deployment.template.jsv* souboru.

1. Do manifestu nasazení přidejte **CModule** modul s dvojitou vlákenou. Vložte následující obsah JSON do dolní části oddílu `moduleContent`, za dvojče modulu `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Snímek obrazovky znázorňující, že se modul přidávají do šablony nasazení.](./media/tutorial-c-module-windows/module-twin.png)

1. Uložte soubor *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Sestavení a vložení modulu

V předchozí části jste vytvořili řešení IoT Edge a Přidali jste kód pro odfiltrování zpráv **, u kterých** se teplota hlášeného počítače nachází pod přijatelnou prahovou hodnotou. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

### <a name="sign-in-to-docker"></a>Přihlásit se k Docker

Poskytněte přihlašovací údaje registru kontejneru do Docker na svém vývojovém počítači, aby bylo možné vložit image kontejneru do registru.

1. Otevřete PowerShell nebo okno příkazového řádku.

1. Přihlaste se k Docker pomocí přihlašovacích údajů služby Azure Container Registry, které jste uložili po vytvoření registru.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití nástroje `--password-stdin` . I když doporučujeme tento postup jako osvědčený postup pro produkční scénáře, je mimo rozsah tohoto kurzu. Další informace najdete v tématu [přihlašovací Reference k Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Sestavení a vložení

Váš vývojový počítač má teď přístup k vašemu registru kontejnerů a vaše IoT Edgeá zařízení budou taky. Je čas převést kód projektu na Image kontejneru.

1. V aplikaci Visual Studio Průzkumník řešení klikněte pravým tlačítkem myši na název projektu, který chcete sestavit. Výchozí název je **AzureIotEdgeApp1**. V tomto kurzu jsme zvolili název **CTutorialApp** a, protože vytváříte modul Windows, musí být přípona **Windows. amd64**.

1. Vyberte **sestavení a moduly nabízených IoT Edge**.

   Příkaz Build a push spustí tři operace:
   * Nejprve vytvoří novou složku v řešení s názvem *config*, která obsahuje úplný manifest nasazení. Je sestaven z informací v šabloně nasazení a v dalších souborech řešení. 
   * Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. 
   * Nakonec se spustí a nahraje `docker push` úložiště imagí do registru kontejneru.

   Tento proces může trvat několik minut poprvé, ale při příštím spuštění příkazů bude rychlejší.

## <a name="deploy-modules-to-the-device"></a>Nasazení modulů do zařízení

K nasazení projektu modulu do zařízení IoT Edge použijte Visual Studio Cloud Explorer a rozšíření Azure IoT Edge Tools. Již jste připravili manifest nasazení pro váš scénář, *deployment.windows-amd64.js* v souboru ve složce *config* . Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že je zařízení IoT Edge spuštěné.

1. V Průzkumníku cloudu sady Visual Studio rozbalte prostředky, abyste si zobrazili seznam zařízení IoT.

1. Klikněte pravým tlačítkem myši na název zařízení IoT Edge, pro které chcete nasazení přijmout.

1. Vyberte **vytvořit nasazení**.

1. V Průzkumníku souborů sady Visual Studio vyberte *deployment.windows-amd64.js* v souboru ve složce *config* vašeho řešení.

1. Aktualizujte Průzkumníka cloudu, aby se zobrazily nasazené moduly, které jsou uvedené v rámci vašeho zařízení.

## <a name="view-generated-data"></a>Zobrazit vygenerovaná data

Po použití manifestu nasazení na zařízení IoT Edge bude modul runtime IoT Edge na zařízení shromažďovat nové informace o nasazení a spustí se na něm. Všechny moduly, které jsou spuštěny v zařízení, ale nejsou součástí manifestu nasazení, jsou zastaveny. Všechny moduly, které v zařízení chybí, jsou spuštěné.

Pomocí rozšíření IoT Edge Tools můžete zobrazovat zprávy při jejich doručování do služby IoT Hub.

1. V Průzkumníku cloudu sady Visual Studio vyberte název zařízení IoT Edge.

1. V seznamu **Akce** vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

1. Podívejte se na zprávy, které přicházejí do služby IoT Hub. Doručení zpráv může chvíli trvat, protože IoT Edge zařízení musí přijmout nové nasazení a spustit všechny moduly. Změny kódu CModule musí počkat, dokud teplota počítače nedosáhne 25 stupňů předtím, než bude možné zprávy odeslat. Kód také přidá **výstrahu** typu zpráva pro všechny zprávy, které dosáhnou prahové hodnoty teploty.

   ![Snímek obrazovky okna výstup zobrazující zprávy, které přicházejí do centra IoT Hub.](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Upravit nevlákenný modul

Pro nastavení prahové hodnoty teploty na 25 stupňů jste použili modul CModule s dvojitou hodnotou. Chcete-li změnit funkčnost, aniž byste museli aktualizovat kód modulu, můžete použít vlákna modulu.

1. V aplikaci Visual Studio otevřete *deployment.windows-amd64.jsv* souboru. 

   Neotevírejte *soubor* *Deployment. template* . Pokud se v *konfiguračním* souboru v Průzkumník řešení nezobrazuje manifest nasazení, vyberte na panelu nástrojů Průzkumník řešení ikonu **Zobrazit všechny soubory** .

1. Vyhledejte CModule s dvojitou hodnotou a změňte hodnotu parametru **temperatureThreshold** na novou teplotu, která je 5 až 10 stupňů vyšší než Poslední hlášená teplota.

1. Uložte *deployment.windows-amd64.jsdo* souboru.

1. Podle pokynů pro nasazení znovu použijte aktualizovaný manifest nasazení na vaše zařízení.

1. Umožňuje monitorovat příchozí zprávy ze zařízení do cloudu. Zprávy by měly skončit až do dosažení nové prahové hodnoty teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat k dalšímu doporučenému článku, můžete zachovat a znovu použít prostředky a konfigurace, které jste vytvořili v tomto kurzu. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete k tomu, abyste se vyhnuli poplatkům, mohli odstranit místní konfigurace a prostředky Azure, které jste zde použili.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge.

Další informace o tom, jak vám Azure IoT Edge může pomáhat při nasazení cloudových služeb Azure pro zpracování a analýzu dat na hraničních zařízeních, najdete v dalších kurzech.

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md) 
>  [Azure Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Azure Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Služba Custom Vision](tutorial-deploy-custom-vision.md)
