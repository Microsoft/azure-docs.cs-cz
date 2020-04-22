---
title: Sada SDK zařízení Azure IoT pro C | Dokumenty společnosti Microsoft
description: Začínáme s sadou Azure IoT device SDK for C a zjistěte, jak vytvářet aplikace pro zařízení, které komunikují s centrem IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b9b27bb142cb729536a3b7a561ed8b8ff5e0ccf5
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731305"
---
# <a name="azure-iot-device-sdk-for-c"></a>Azure IoT device SDK for C

Sada **SDK zařízení Azure IoT** je sada knihoven navržených tak, aby zjednodušily proces odesílání zpráv do a přijímání zpráv ze služby **Azure IoT Hub.** Existují různé varianty sady SDK, z nichž každá cílí na konkrétní platformu, ale tento článek popisuje **sadku SDK zařízení Azure IoT pro C**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Sada Azure IoT device SDK for C je napsaná v ANSI C (C99) pro maximalizaci přenositelnosti. Díky této funkci jsou knihovny vhodné pro provoz na více platformách a zařízeních, zejména tam, kde je prioritou minimalizace disků a nároků na paměť.

Existuje široká škála platforem, na kterých byla sada SDK testována (podrobnosti najdete v [katalogu zařízení Azure Certified for IoT).](https://catalog.azureiotsolutions.com/) Přestože tento článek obsahuje návody ukázkový kód spuštěný na platformě Windows, kód popsaný v tomto článku je shodný v rozsahu podporovaných platforem.

Následující video představuje přehled sady Azure IoT SDK pro C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Tento článek vás seznámí s architekturou sady Azure IoT zařízení SDK pro C. Ukazuje, jak inicializovat knihovnu zařízení, odesílat data do ioT hubu a přijímat zprávy z něj. Informace v tomto článku by měly stačit k zahájení používání sady SDK, ale také poskytuje odkazy na další informace o knihovnách.

## <a name="sdk-architecture"></a>Architektura sady SDK

Můžete najít [**Azure IoT zařízení SDK pro C**](https://github.com/Azure/azure-iot-sdk-c) úložiště GitHub a zobrazit podrobnosti o rozhraní API v [odkazu rozhraní API C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

Nejnovější verzi knihoven naleznete v **hlavní** větvi repozitáře:

  ![Snímek obrazovky hlavní větve úložiště](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Základní implementace sady SDK je ve složce **klienta iothub,\_** která obsahuje implementaci nejnižší vrstvy rozhraní API v sdk: knihovna **IoTHubClient.** Knihovna **IoTHubClient** obsahuje api implementující nezpracovaná zasílání zpráv pro odesílání zpráv do služby IoT Hub a příjem zpráv z ioT hubu. Při použití této knihovny jste zodpovědní za implementaci serializace zpráv, ale další podrobnosti o komunikaci s ioT Hub jsou zpracovány za vás.

* Složka **serializer** obsahuje pomocné funkce a ukázky, které ukazují, jak serializovat data před odesláním do služby Azure IoT Hub pomocí klientské knihovny. Použití serializátoru není povinné a je poskytováno jako pohodlí. Chcete-li použít knihovnu **serializátorů,** definujte model, který určuje data, která mají být odeslána do centra IoT Hub, a zprávy, které z něj očekáváte. Jakmile je model definován, sada SDK poskytuje povrch rozhraní API, který umožňuje snadno pracovat se zprávami typu zařízení cloud a cloud zařízení bez obav o podrobnosti serializace. Knihovna závisí na jiných knihovnách s otevřeným zdrojovým kódem, které implementují přenos pomocí protokolů, jako jsou MQTT a AMQP.

* Knihovna **IoTHubClient** závisí na jiných knihovnách s otevřeným zdrojovým kódem:

  * Knihovna [sdílených nástrojů Azure C,](https://github.com/Azure/azure-c-shared-utility) která poskytuje běžné funkce pro základní úkoly (například řetězce, manipulace se seznamy a vi) potřebné v několika sadách C SDK související s Azure.

  * Knihovna [Azure uAMQP,](https://github.com/Azure/azure-uamqp-c) což je implementace AMQP na straně klienta optimalizovaná pro zařízení s omezenými prostředky.

  * Knihovna [Azure uMQTT,](https://github.com/Azure/azure-umqtt-c) což je univerzální knihovna implementující protokol MQTT a optimalizovaná pro zařízení s omezenými prostředky.

Použití těchto knihoven je srozumitelnější při pohledu na ukázkový kód. Následující části vás provedou několika ukázkovými aplikacemi, které jsou součástí sady SDK. Tento návod by vám měl poskytnout dobrý pocit z různých možností architektonických vrstev sady SDK a úvod do fungování řešení API.

## <a name="before-you-run-the-samples"></a>Před spuštěním vzorků

Než spustíte ukázky v sadě Azure IoT zařízení SDK pro C, musíte [vytvořit instanci služby IoT Hub](iot-hub-create-through-portal.md) ve vašem předplatném Azure. Poté proveďte následující úkoly:

* Příprava vývojového prostředí
* Získejte pověření zařízení.

### <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Balíčky jsou k dispozici pro běžné platformy (například NuGet pro Windows nebo apt_get pro Debian a Ubuntu) a ukázky používají tyto balíčky, pokud jsou k dispozici. V některých případech je třeba zkompilovat sadu SDK pro zařízení nebo na jeho zařízení. Pokud potřebujete zkompilovat sdk, najdete [v tématu Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v úložišti GitHub.

Chcete-li získat ukázkový kód aplikace, stáhněte si kopii sady SDK z GitHubu. Získejte kopii zdroje z **hlavní** větve [úložiště GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Získání přihlašovacích údajů zařízení

Nyní, když máte ukázkový zdrojový kód, další věc, kterou musíte udělat, je získat sadu přihlašovacích údajů zařízení. Aby zařízení bylo možné získat přístup k centru IoT hub, musíte nejprve přidat zařízení do registru identit služby IoT Hub. Když přidáte zařízení, získáte sadu přihlašovacích údajů zařízení, které potřebujete, aby se zařízení bylo možné připojit k centru IoT Hub. Ukázkové aplikace popsané v další části očekávají tato pověření ve formě **připojovacího řetězce zařízení**.

Existuje několik open source nástrojů, které vám pomůžou spravovat vaše centrum IoT hub.

* Aplikace pro Windows s názvem [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer).

* Rozšíření kódu Visual Studia napříč platformami s názvem [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Multiplatformní python CLI s názvem [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

Tento kurz používá grafický nástroj *průzkumníkzařízení.* Pokud vyvíjíte v kódu VS, můžete použít *nástroje Azure IoT pro kód VS.* *Rozšíření IoT pro nástroj Azure CLI 2.0* můžete použít také, pokud dáváte přednost použití nástroje příkazového příkazového příkazu.

Nástroj průzkumník zařízení používá knihovny služeb Azure IoT k provádění různých funkcí v centru IoT Hub, včetně přidávání zařízení. Pokud k přidání zařízení použijete nástroj Průzkumník zařízení, získáte pro zařízení připojovací řetězec. Tento připojovací řetězec potřebujete ke spuštění ukázkových aplikací.

Pokud nejste obeznámeni s nástrojem průzkumníkzařízení, následující postup popisuje, jak jej použít k přidání zařízení a získat připojovací řetězec zařízení.

1. Pokud chcete nainstalovat nástroj Průzkumník zařízení, přečtěte si informace [o použití Průzkumníka zařízení pro zařízení služby IoT Hub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

1. Při spuštění programu se zobrazí toto rozhraní:

   ![Snímek obrazovky s dvojčetem Průzkumníka zařízení](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Do prvního pole zadejte **připojovací řetězec služby IoT Hub** a klepněte na tlačítko **Aktualizovat**. Tento krok nakonfiguruje nástroj tak, aby mohl komunikovat s ioT hubem. 

**Připojovací řetězec** najdete v části > **Nastavení** >  **služby IoT****Hub, který je vlastníkem iothubowner .** > **iothubowner**

1. Když je připojovací řetězec ioT Hub nakonfigurován, klikněte na kartu **Správa:**

   ![Snímek obrazovky S kaši i pro aplikaci Device Explorer Twin / Management](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Na této kartě můžete spravovat zařízení zaregistrovaná ve vašem centru IoT hub.

1. Zařízení vytvoříte kliknutím na tlačítko **Vytvořit.** Zobrazí se dialogové okno se sadou předem vyplněných klíčů (primárních a sekundárních). Zadejte **ID zařízení** a klepněte na **tlačítko Vytvořit**.

   ![Vytvořit snímek obrazovky zařízení](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Po vytvoření zařízení se seznam Zařízení aktualizuje se všemi registrovanými zařízeními, včetně toho, které jste právě vytvořili. Pokud kliknete pravým tlačítkem myši na nové zařízení, zobrazí se tato nabídka:

   ![Výsledek klepnutí pravým tlačítkem myši na Průzkumníka zařízení](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Pokud zvolíte **Kopírovat připojovací řetězec pro vybrané zařízení**, připojovací řetězec zařízení se zkopíruje do schránky. Uchovávejte kopii připojovacího řetězce zařízení. Potřebujete při spuštění ukázkové aplikace popsané v následujících částech.

Po dokončení výše uvedených kroků můžete začít spouštět nějaký kód. Většina vzorků má konstantu v horní části hlavního zdrojového souboru, který umožňuje zadat připojovací řetězec. Například odpovídající řádek z **\_iothub_client\_vzorků iothub_convenience_sample** aplikace se zobrazí následujícím způsobem.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Použití knihovny IoTHubClient

V rámci složky **\_klienta iothub** v úložišti [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) je **ukázková** složka, která obsahuje aplikaci nazvanou **iothub\_client\_sample\_mqtt**.

Verze **iothub_client ukázky\_\_iothub_convenience_sample** aplikace pro Windows obsahuje následující řešení sady Visual Studio:

  ![Průzkumník řešení visual studia](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Pokud vás Visual Studio požádá o přesměrování projektu na nejnovější verzi, přijměte výzvu.

Toto řešení obsahuje jeden projekt. V tomto řešení jsou nainstalovány čtyři balíčky NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Klient Microsoft.Azure.IoTHub.IoTHub
* Microsoft.Azure.umqtt

Vždy potřebujete balíček **Microsoft.Azure.C.SharedUtility** při práci s sadou SDK. Tato ukázka používá protokol MQTT, proto musíte zahrnout balíčky **Microsoft.Azure.umqtt** a **Microsoft.Azure.IoTHub.MqttTransport** (existují ekvivalentní balíčky pro AMQP a HTTPS). Vzhledem k tomu, že ukázka používá knihovnu **IoTHubClient,** musíte také zahrnout balíček **Microsoft.Azure.IoTHub.IoTHubClient** ve vašem řešení.

Implementaci ukázkové aplikace najdete v **\_ukázkových iothub_client\_ukázky iothub_convenience_sample** zdrojový soubor.

Následující kroky používají tuto ukázkovou aplikaci, která vás provede tím, co je nutné k použití knihovny **IoTHubClient.**

### <a name="initialize-the-library"></a>Inicializovat knihovnu

> [!NOTE]
> Než začnete pracovat s knihovnami, budete muset provést některé inicializace specifické pro platformu. Pokud například plánujete používat AMQP v Linuxu, musíte inicializovat knihovnu OpenSSL. Ukázky v [úložišti GitHub](https://github.com/Azure/azure-iot-sdk-c) volání **platformy\_** funkce nástroje init při spuštění klienta a volání **\_deinit platformy** funkce před ukončením. Tyto funkce jsou deklarovány v souboru hlavičky platform.h. Zkontrolujte definice těchto funkcí pro cílovou platformu v [úložišti](https://github.com/Azure/azure-iot-sdk-c) a zjistěte, zda je třeba do klienta zahrnout kód inicializace specifické pro platformu.

Chcete-li začít pracovat s knihovnami, nejprve přidělit klientský popisovač služby IoT Hub:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Předáte kopii připojovacího řetězce zařízení, který jste získali z nástroje průzkumníkzařízení, do této funkce. Můžete také určit komunikační protokol, který má být používán. Tento příklad používá MQTT, ale AMQP a HTTPS jsou také možnosti.

Pokud máte platný **popisovač\_klienta\_IOTHUB**, můžete začít volat api pro odesílání a přijímání zpráv do a z IoT Hub.

### <a name="send-messages"></a>Odesílání zpráv

Ukázková aplikace nastaví smyčku pro odesílání zpráv do centra IoT. Následující úryvek:

- Vytvoří zprávu.
- Přidá vlastnost zprávy.
- Odešle zprávu.

Nejprve vytvořte zprávu:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Pokaždé, když odešlete zprávu, zadáte odkaz na funkci zpětného volání, která je vyvolána při odeslání dat. V tomto příkladu se funkce zpětného volání nazývá **SendConfirmationCallback**. Následující úryvek zobrazuje tuto funkci zpětného volání:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Poznámka: volání **funkce Zničení zprávy\_IoTHubMessage,** když jste hotovi se zprávou. Tato funkce uvolní prostředky přidělené při vytváření zprávy.

### <a name="receive-messages"></a>Příjem zpráv

Příjem zprávy je asynchronní operace. Nejprve zaregistrujete zpětné volání vyvolat, když zařízení obdrží zprávu:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

Poslední parametr je void ukazatel na co chcete. V ukázce je ukazatel na celé číslo, ale může to být ukazatel na složitější strukturu dat. Tento parametr umožňuje funkci zpětného volání pracovat ve sdíleném stavu s volajícím této funkce.

Když zařízení obdrží zprávu, je vyvolána registrovaná funkce zpětného volání. Tato funkce zpětného volání načte:

* ID zprávy a ID korelace ze zprávy.
* Obsah zprávy.
* Všechny vlastní vlastnosti ze zprávy.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Pomocí funkce **IoTHubMessage\_GetByteArray** načtěte zprávu, která v tomto příkladu je řetězec.

### <a name="uninitialize-the-library"></a>Zrušení inicializace knihovny

Po dokončení odesílání událostí a přijímání zpráv můžete zrušit inicializaci knihovny IoT. Chcete-li tak učinit, vyzaňte následující volání funkce:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Toto volání uvolní prostředky dříve přidělené **ioTHubClient\_CreateFromConnectionString** funkce.

Jak můžete vidět, je snadné odesílat a přijímat zprávy s knihovnou **IoTHubClient.** Knihovna zpracovává podrobnosti komunikace s IoT Hub, včetně toho, který protokol použít (z pohledu vývojáře se jedná o jednoduchou možnost konfigurace).

Knihovna **IoTHubClient** také poskytuje přesnou kontrolu nad tím, jak serializovat data, která vaše zařízení odesílá do služby IoT Hub. V některých případech je tato úroveň kontroly výhodou, ale v jiných je to detail implementace, který nechcete být znepokojeni. V takovém případě můžete zvážit použití knihovny **serializátoru,** která je popsána v další části.

## <a name="use-the-serializer-library"></a>Použití knihovny serializátorů

Koncepčně **serializační** knihovna je na vrcholu knihovny **IoTHubClient** v sdk. Používá knihovnu **IoTHubClient** pro základní komunikaci s IoT Hub, ale přidá možnosti modelování, které odstraňují zátěž z řešení serializace zpráv od vývojáře. Jak tato knihovna funguje, nejlépe dokládá příklad.

Uvnitř **serializátoru** složky v [úložišti azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c), je **ukázkové** složky, která obsahuje aplikaci s názvem **simplesample\_mqtt**. Verze tohoto ukázně pro Windows obsahuje následující řešení sady Visual Studio:

  ![Řešení Visual Studio pro ukázku mqtt](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Pokud vás Visual Studio požádá o přesměrování projektu na nejnovější verzi, přijměte výzvu.

Stejně jako u předchozí ukázky obsahuje tento balíček NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Klient Microsoft.Azure.IoTHub.IoTHub
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Většinu těchto balíčků jste viděli v předchozí ukázce, ale **Microsoft.Azure.IoTHub.Serializer** je nový. Tento balíček je vyžadován při použití knihovny **serializátoru.**

Implementaci ukázkové aplikace najdete v **\_iothub_client\_ukázky iothub_convenience_sample** souboru.

Následující části vás provedou klíčovými částmi této ukázky.

### <a name="initialize-the-library"></a>Inicializovat knihovnu

Chcete-li začít pracovat s knihovnou **serializačního zařízení,** zavolejte inicializační api:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

Volání funkce **init\_serializátoru** je jednorázové volání a inicializuje základní knihovnu. Potom zavoláte **ioTHubClient\_\_LL CreateFromConnectionString** funkce, která je stejné rozhraní API jako v ukázce **IoTHubClient.** Toto volání nastaví připojovací řetězec zařízení (toto volání je také místo, kde zvolíte protokol, který chcete použít). Tato ukázka používá MQTT jako přenos, ale může použít AMQP nebo HTTPS.

Nakonec zavolejte funkci **CREATE\_MODEL\_INSTANCE.** **WeatherStation** je obor názvů modelu a **ContosoAnemometer** je název modelu. Po vytvoření instance modelu ji můžete použít k zahájení odesílání a přijímání zpráv. Je však důležité pochopit, co je model.

### <a name="define-the-model"></a>Definování modelu

Model v knihovně **serializátorů** definuje zprávy, které může vaše zařízení odesílat do centra IoT Hub, a zprávy nazývané *akce* v jazyce modelování, které může přijímat. Model definujete pomocí sady maker C jako v **iothub_client\_ukázky\_iothub_convenience_sample** ukázkové aplikace:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

**Makra\_BEGIN NAMESPACE** i END **\_NAMESPACE** berou obor názvů modelu jako argument. Očekává se, že cokoli mezi těmito makry je definice modelu nebo modelů a datových struktur, které modely používají.

V tomto příkladu je jeden model s názvem **ContosoAnemometer**. Tento model definuje dvě části dat, které může vaše zařízení odesílat do ioT hubu: **DeviceId** a **WindSpeed**. Definuje také tři akce (zprávy), které vaše zařízení může přijímat: **TurnFanOn**, **TurnFanOff**a **SetAirResistance**. Každý datový prvek má typ a každá akce má název (a volitelně sadu parametrů).

Data a akce definované v modelu definují povrch rozhraní API, který můžete použít k odesílání zpráv do služby IoT Hub a reagovat na zprávy odeslané do zařízení. Použití tohoto modelu je nejlépe pochopit prostřednictvím příkladu.

### <a name="send-messages"></a>Odesílání zpráv

Model definuje data, která můžete odeslat do ioT hubu. V tomto příkladu to znamená jednu ze dvou datových položek definovaných pomocí **WITH_DATA** makra. K odeslání hodnot **DeviceId** a **WindSpeed** do centra IoT je potřeba několik kroků. Prvním z nás je nastavení dat, která chcete odeslat:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Model, který jste definovali dříve, umožňuje nastavit hodnoty nastavením členů **struktury**. Dále serializujte zprávu, kterou chcete odeslat:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Tento kód serializuje zařízení-cloud do vyrovnávací paměti (odkazuje podle **cíle).** Kód pak vyvolá funkci **sendMessage** k odeslání zprávy do služby IoT Hub:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

Předposlední parametr **IoTHubClient\_LL\_SendEventAsync** je odkaz na funkci zpětného volání, která je volána při úspěšném odeslání dat. Zde je funkce zpětného volání v ukázce:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Druhý parametr je ukazatel na kontext uživatele; stejný ukazatel předaný **ioTHubClient\_LL\_SendEventAsync**. V tomto případě je kontext jednoduchý čítač, ale může to být cokoliv chcete.

To je vše, co je k odesílání zpráv zařízení cloud. Jediné, co zbývá pokrýt, je, jak přijímat zprávy.

### <a name="receive-messages"></a>Příjem zpráv

Příjem zprávy funguje podobně jako způsob, jakým zprávy fungují v knihovně **IoTHubClient.** Nejprve zaregistrujete funkci zpětného volání zprávy:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Potom napíšete funkci zpětného volání, která je vyvolána při přijetí zprávy:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Tento kód je standardní -- je to stejné pro každé řešení. Tato funkce obdrží zprávu a postará se o směrování do příslušné funkce prostřednictvím volání **EXECUTE\_COMMAND**. Funkce volaná v tomto okamžiku závisí na definici akcí v modelu.

Když definujete akci v modelu, budete muset implementovat funkci, která se nazývá, když vaše zařízení obdrží odpovídající zprávu. Pokud například model definuje tuto akci:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Definujte funkci s tímto podpisem:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Všimněte si, jak název funkce odpovídá názvu akce v modelu a že parametry funkce odpovídají parametrům určeným pro akci. První parametr je vždy povinný a obsahuje ukazatel na instanci modelu.

Když zařízení obdrží zprávu, která odpovídá tomuto podpisu, je volána odpovídající funkce. Proto kromě nutnosti zahrnout často používaný kód z **IoTHubMessage**, příjem zpráv je pouze otázkou definování jednoduché funkce pro každou akci definovanou v modelu.

### <a name="uninitialize-the-library"></a>Zrušení inicializace knihovny

Po dokončení odesílání dat a přijímání zpráv můžete zrušit inicializaci knihovny IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Každá z těchto tří funkcí je zarovnána se třemi dříve popsanými inicializačními funkcemi. Volání těchto api zajistí, že uvolníte dříve přidělené prostředky.

## <a name="next-steps"></a>Další kroky

Tento článek se zabýval základy používání knihoven v **sadě Azure IoT device SDK for C**. Poskytla vám dostatek informací, abyste pochopili, co je součástí sady SDK, její architekturu a jak začít pracovat s ukázkami systému Windows. Další článek pokračuje v popisu sady SDK vysvětlením [další informace o knihovně IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Další informace o vývoji pro Službu IoT Hub najdete v tématu [sady Azure IoT SDK .](iot-hub-devguide-sdks.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
