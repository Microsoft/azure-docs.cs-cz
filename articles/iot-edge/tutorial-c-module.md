---
title: Kurz vývoj modulu C pro Linux-Azure IoT Edge | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem jazyka C a jak ho nasadit na zařízení se systémem Linux se spuštěným IoT Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4c2505f210b1a2b52b64c25b4ffa0c26bb30d7ee
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044679"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Kurz: vývoj modulu C IoT Edge pro zařízení se systémem Linux

Pomocí Visual Studio Code můžete vyvíjet kód v jazyce C a nasazovat ho do zařízení se systémem Linux se spuštěným Azure IoT Edge.

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Použití editoru Visual Studio Code k vytvoření modulu IoT Edge v jazyce C
> * Použití editoru Visual Studio Code a Dockeru k vytvoření image Dockeru a jejímu publikování v registru kontejneru
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazit vygenerovaná data

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Obor řešení

Tento kurz ukazuje, jak vytvořit modul v jazyce **C** pomocí **Visual Studio Code**a jak ho nasadit na **zařízení se systémem Linux**. Pokud vyvíjíte moduly pro zařízení s Windows, použijte místo toho [vývoj modulu C IoT Edge pro zařízení s Windows](tutorial-c-module-windows.md) .

Následující tabulka vám pomůže pochopit možnosti vývoje a nasazení modulů jazyka C do systému Linux:

| C | Visual Studio Code | Visual Studio |
| - | ------------------ | ------------- |
| **Linux AMD64** | ![Použití VS Code pro moduly jazyka C v systému Linux AMD64](./media/tutorial-c-module/green-check.png) | ![Použití VS pro moduly C v systému Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Použití VS Code pro moduly jazyka C v systému Linux ARM32](./media/tutorial-c-module/green-check.png) | ![Použití VS pro moduly C v ARM32 pro Linux](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Požadované součásti

Před zahájením tohoto kurzu byste si měli projít předchozí kurz nastavení vývojového prostředí pro vývoj kontejnerů pro Linux: [vývoj IoT Edgech modulů pro zařízení se systémem Linux](tutorial-develop-for-linux.md). Po dokončení tohoto kurzu byste měli mít následující požadavky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Zařízení se systémem Linux se spuštěným Azure IoT Edge](quickstart-linux.md)
* Registr kontejneru, například [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovaných pomocí [nástrojů Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurovaný pro spouštění kontejnerů Linux.

Chcete-li vytvořit modul IoT Edge v jazyce C, nainstalujte na svém vývojovém počítači následující další požadavky:

* [Rozšíření C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) pro Visual Studio Code.

Instalace sady Azure IoT C SDK není pro tento kurz nutná, ale může poskytovat užitečné funkce, jako je IntelliSense a čtení definic programu. Informace o instalaci najdete v tématu sady [SDK a knihovny Azure IoT C](https://github.com/Azure/azure-iot-sdk-c).

## <a name="create-a-module-project"></a>Vytvořit projekt modulu

Následující postup slouží k vytvoření projektu IoT Edge modulu pro jazyk C pomocí Visual Studio Code a rozšíření Azure IoT Tools. Jakmile máte vytvořenou šablonu projektu, přidejte nový kód tak, aby modul vyfiltroval zprávy na základě jejich hlášených vlastností.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení v C, kterou můžete přizpůsobit pomocí vlastního kódu.

1. Výběrem **Zobrazit**  >  **paleta příkazů** otevřete paletu příkazů vs Code.

2. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud jste už přihlášení, můžete tento krok přeskočit.

3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vybrat složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název vašeho řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Vyberte **modul C**. |
   | Zadejte název modulu | Pojmenujte modul **CModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Vaše image kontejneru se předem vyplní názvem, který jste zadali v posledním kroku. Položku **localhost: 5000** nahraďte hodnotou **přihlašovacího serveru** z služby Azure Container Registry. Přihlašovací server můžete načíst ze stránky přehled v registru kontejneru v Azure Portal. <br><br> Konečné úložiště imagí vypadá jako \<registry name\> . azurecr.IO/cmodule. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

Rozšíření IoT Edge se pokusí načíst přihlašovací údaje registru kontejneru z Azure a naplnit je do souboru prostředí. Zkontrolujte, jestli jsou vaše přihlašovací údaje už zahrnuté. Pokud ne, přidejte je nyní:

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte cílovou architekturu.

V současné době Visual Studio Code může vyvíjet moduly C pro zařízení se systémem Linux AMD64 a Linux ARM32v7. Musíte vybrat architekturu, kterou cílíte na každé řešení, protože kontejner je sestavený a pro každý typ architektury funguje jinak. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro řešení Edge**nebo vyberte ikonu zástupce na bočním panelu v dolní části okna.

2. V paletě příkazů vyberte v seznamu možností cílovou architekturu. Pro tento kurz používáme virtuální počítač s Ubuntu jako zařízení IoT Edge, takže se zachová výchozí hodnota **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Výchozí kód modulu přijímá zprávy ve vstupní frontě a předává je spolu s výstupní frontou. Pojďme přidat nějaký další kód, aby modul zpracoval zprávy na hranici před jejich přesměrováním na IoT Hub. Aktualizujte modul tak, aby analyzoval data o teplotě v každé zprávě, a pošle zprávu jenom IoT Hub, pokud teplota překročí určitou prahovou hodnotu.

1. Data ze snímače v tomto scénáři přicházejí ve formátu JSON. Pokud chcete filtrovat zprávy ve formátu JSON, importujte knihovnu JSON pro jazyk C. V tomto kurzu se používá Parson.

   1. Stáhněte si [úložiště GitHub Parson](https://github.com/kgabis/parson). Soubory **parson.c** a **parson.h** zkopírujte do složky **CModule**.

   2. Otevřete **moduly**  >  **CModule**  >  **CMakeLists.txt**. Na začátku souboru importujte soubory Parson jako knihovnu **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Přidejte `my_parson` do seznamu knihoven ve funkci **target_link_libraries** CMakeLists.txt.

   4. Uložte soubor **CMakeLists.txt**.

   5. Otevřete **modul**  >  **CModule**  >  **Main. c**. V dolní části seznamu příkazů include přidejte nové, které chcete zahrnout do `parson.h` podpory JSON:

      ```c
      #include "parson.h"
      ```

1. Do souboru **main.c** přidejte za část s příkazy include globální proměnnou `temperatureThreshold`. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Vyhledejte `CreateMessageInstance` funkci v Main. c. Nahraďte příkaz Inner if-else následujícím kódem, který přidá několik řádků funkčnosti:

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

1. Celou funkci `InputQueue1Callback` nahraďte následujícím kódem. Tato funkce implementuje samotný filtr zasílání zpráv. Při přijetí zprávy kontroluje, zda velikost hlášené teploty překročí prahovou hodnotu. Pokud ano, přepošle zprávu přes výstupní frontu. Pokud ne, bude zpráva ignorována.

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

1. Vyhledejte `SetupCallbacksForModule` funkci. Nahraďte funkci následujícím kódem, který přidá příkaz **else if** pro kontrolu, zda byl modul nefunkční.

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

## <a name="build-and-push-your-module"></a>Sestavení a vložení modulu

V předchozí části jste vytvořili řešení IoT Edge a Přidali jste kód do CModule, který odfiltruje zprávy, kde je hlášená teplota počítače v přípustných mezích. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Kliknutím na **Zobrazit**  >  **terminál**otevřete vs Code terminálu.

2. Přihlaste se k Docker zadáním následujícího příkazu v terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru ze služby Azure Container Registry. Tyto hodnoty můžete načíst z oddílu **přístupové klíče** v registru v Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití nástroje `--password-stdin` . I když se tento osvědčený postup doporučuje u produkčních scénářů, je mimo rozsah tohoto kurzu. Další informace najdete v tématu přihlašovací Reference k [Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

3. V Průzkumníku VS Code klikněte pravým tlačítkem na **deployment.template.jsna** soubor a vyberte **sestavení a nabízené IoT Edge řešení**.

   Příkaz Build a push spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config** , která obsahuje úplný manifest nasazení, a vyplní informace v šabloně nasazení a dalších souborech řešení. Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. Pak se spustí a nahraje `docker push` úložiště imagí do registru kontejneru.

   Tento proces může trvat několik minut poprvé, ale při příštím spuštění příkazů je rychlejší.

## <a name="deploy-modules-to-device"></a>Nasadit moduly do zařízení

K nasazení projektu modulu do zařízení IoT Edge použijte Průzkumníka Visual Studio Code a rozšíření Azure IoT Tools. Už máte připravený manifest nasazení pro váš scénář, **deployment.amd64.js** v souboru ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že je zařízení IoT Edge spuštěné.

1. V Průzkumníkovi Visual Studio Code v části **Azure IoT Hub** rozbalte **zařízení** , abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte **deployment.amd64.js** v souboru ve složce **config** a pak klikněte na **Vybrat manifest nasazení Edge**. Nepoužívejte soubor deployment.template.json.

4. V části zařízení rozbalte **moduly** a zobrazte seznam nasazených a spuštěných modulů. Klikněte na tlačítko pro obnovení. Měl by se zobrazit nový **CModule** spuštěný spolu s modulem **SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.

    Spuštění modulů může trvat několik minut. Modul runtime IoT Edge musí přijmout nový manifest nasazení, Stáhnout image modulu z modulu runtime kontejneru a pak začít každý nový modul.

## <a name="view-generated-data"></a>Zobrazit vygenerovaná data

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem myši na název vašeho zařízení IoT Edge a vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

2. Zobrazení zpráv přicházejících do IoT Hub. Doručení zpráv může chvíli trvat, protože IoT Edge zařízení musí přijmout nové nasazení a spustit všechny moduly. Změny, které jsme provedli v kódu CModule, čekají, dokud teplota počítače nedosáhne 25 stupňů před odesláním zpráv. Přidá také **výstrahu** typu zpráva pro všechny zprávy, které dosáhnou prahové hodnoty teploty.

   ![Zobrazení zpráv přicházejících na IoT Hub](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Upravit nevlákenný modul

V manifestu nasazení jsme použili vláken Module CModule, které nastaví prahovou hodnotu teploty na 25 stupních. Chcete-li změnit funkčnost, aniž byste museli aktualizovat kód modulu, můžete použít vlákna modulu.

1. V Visual Studio Code rozbalte podrobnosti pod zařízením IoT Edge a podívejte se na běžící moduly.

2. Klikněte pravým tlačítkem na **CModule** a vyberte **Upravit modul s dvojitou**čárkou.

3. V požadovaných vlastnostech vyhledejte **TemperatureThreshold** . Změňte jeho hodnotu na novou teplotu 5 stupňů na 10 stupňů vyšší než při nejnovější hlášené teplotě.

4. Uložte modul s dvojitým pracovním souborem.

5. Klikněte pravým tlačítkem na libovolné místo v podoknì s dvojitou úpravou modulu a vyberte **aktualizovat modul vlákna**.

6. Umožňuje monitorovat příchozí zprávy ze zařízení do cloudu. Měli byste vidět, že se zprávy zastavily, dokud se nedosáhne nové prahové hodnoty teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurace a prostředky Azure, které jste použili v tomto článku, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge.

V dalších kurzech můžete pokračovat a zjistit, jak vám Azure IoT Edge může pomáhat s nasazením cloudových služeb Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)