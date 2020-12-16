---
title: Sada SDK pro zařízení Azure IoT pro jazyk C | Microsoft Docs
description: Začněte se sadou SDK pro zařízení Azure IoT pro jazyk C a Naučte se vytvářet aplikace pro zařízení, které komunikují se službou IoT Hub.
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
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 17b31e365e311b97e322828927827f40733313a6
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588824"
---
# <a name="azure-iot-device-sdk-for-c"></a>Sada SDK pro zařízení Azure IoT pro jazyk C

Sada **SDK pro zařízení Azure IoT** je sada knihoven navržená tak, aby zjednodušila proces posílání zpráv a přijímání zpráv ze služby **Azure IoT Hub** . Existují různé varianty sady SDK, které cílí na konkrétní platformu, ale tento článek popisuje **sadu SDK pro zařízení Azure IoT pro jazyk C**.

> [!NOTE]
> Vložená sada C SDK je alternativou pro omezená zařízení, která podporují přístup k síti BYON (Přineste si vlastní síť). Vývojáři IoT mají volnost v navýšení klienta MQTT, TLS a zásuvky pro vytvoření řešení zařízení. [Přečtěte si další informace o vložené sadě C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Sada SDK pro zařízení Azure IoT pro jazyk C je napsaná ve standardu ANSI C (C99), aby se maximalizovala přenositelnost. Díky této funkci se knihovny dobře hodí pro provoz na různých platformách a zařízeních, zejména v případě, že je minimalizace nároky na disk a paměť.

Existuje široká škála platforem, na kterých byla sada SDK testována (podrobnosti naleznete v [katalogu zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/) ). I když tento článek obsahuje návody ukázkového kódu běžícího na platformě Windows, kód popsaný v tomto článku je identický v rámci řady podporovaných platforem.

Následující video představuje přehled sady Azure IoT SDK pro jazyk C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Tento článek vás seznámí s architekturou sady SDK pro zařízení Azure IoT pro jazyk C. Ukazuje, jak inicializovat knihovnu zařízení, odesílat data do IoT Hub a přijímat z nich zprávy. Informace v tomto článku by měly být dostačující k tomu, abyste mohli začít používat sadu SDK, ale také poskytují odkazy na Další informace o knihovnách.

## <a name="sdk-architecture"></a>Architektura sady SDK

Můžete najít [**sadu SDK pro zařízení Azure IoT pro**](https://github.com/Azure/azure-iot-sdk-c) úložiště GitHub c a zobrazit podrobnosti o rozhraní API v referenčních informacích k [rozhraní API jazyka c](/azure/iot-hub/iot-c-sdk-ref/).

Nejnovější verzi knihoven najdete v **Hlavní** větvi úložiště:

  ![Snímek obrazovky hlavní větve úložiště](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Základní implementace sady SDK je ve složce **\_ klienta iothub** , která obsahuje implementaci nejnižší vrstvy API v sadě SDK: knihovna **IoTHubClient** . Knihovna **IoTHubClient** obsahuje rozhraní API implementující nezpracované zasílání zpráv pro posílání zpráv IoT Hub a přijímání zpráv z IoT Hub. Při použití této knihovny zodpovídáte za implementaci serializace zprávy, ale budou zpracovávány další podrobnosti o komunikaci s IoT Hub.

* Složka **serializátoru** obsahuje pomocné funkce a ukázky, které ukazují, jak serializovat data před odesláním do Azure IoT Hub pomocí klientské knihovny. Použití serializátoru není povinné a je poskytované jako pohodlí. Chcete-li použít knihovnu **serializátorů** , definujete model, který určuje data, která mají být odeslána IoT Hub a zprávy, které od něj očekáváte přijmout. Po definování modelu sada SDK poskytuje plochu rozhraní API, která umožňuje snadnou práci se zprávami typu zařízení-Cloud a Cloud-zařízení, aniž byste se museli starat o informace o serializaci. Knihovna závisí na dalších open source knihovnách, které implementují přenos pomocí protokolů, jako jsou MQTT a AMQP.

* Knihovna **IoTHubClient** závisí na dalších open source knihovnách:

  * Knihovna [sdílených nástrojů Azure C](https://github.com/Azure/azure-c-shared-utility) , která poskytuje běžné funkce pro základní úlohy (například řetězce, manipulace se seznamem a vstupně-výstupní operace), které jsou potřeba pro různé sady SDK jazyka c související s Azure.

  * Knihovna [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) , která je implementací AMQP optimalizovaná pro omezená zařízení prostředků na straně klienta.

  * Knihovna [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) , což je obecná knihovna implementující protokol MQTT a optimalizovaná pro omezená zařízení prostředků.

Použití těchto knihoven je snazší pochopit, když si vyhledáte vzorový kód. Následující části vás provedou několika ukázkovými aplikacemi, které jsou součástí sady SDK. Tento návod by měl mít dobrý vliv na různé možnosti architektonických vrstev sady SDK a Úvod do fungování rozhraní API.

## <a name="before-you-run-the-samples"></a>Před spuštěním ukázek

Než budete moct spustit ukázky v sadě SDK pro zařízení Azure IoT pro jazyk C, musíte ve svém předplatném Azure [vytvořit instanci služby IoT Hub](iot-hub-create-through-portal.md) . Potom dokončete následující úlohy:

* Příprava vývojového prostředí
* Získejte přihlašovací údaje pro zařízení.

### <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Balíčky jsou k dispozici pro běžné platformy (například NuGet pro Windows nebo apt_get pro Debian a Ubuntu) a ukázky tyto balíčky používají, pokud jsou k dispozici. V některých případech je nutné sestavit sadu SDK pro nebo do svého zařízení. Pokud potřebujete sestavit sadu SDK, přečtěte si téma [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v úložišti GitHub.

Pokud chcete získat ukázkový kód aplikace, Stáhněte si z GitHubu kopii sady SDK. Získejte kopii zdroje z **Hlavní** větve [úložiště GitHubu](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Získání přihlašovacích údajů zařízení

Teď, když máte ukázkový zdrojový kód, je další věcí k tomu, abyste získali sadu přihlašovacích údajů pro zařízení. Aby zařízení mohlo přistupovat ke službě IoT Hub, musíte nejdřív přidat zařízení do registru IoT Hub identity. Po přidání zařízení získáte sadu přihlašovacích údajů zařízení, které potřebujete, aby se zařízení mohlo připojit ke službě IoT Hub. Ukázkové aplikace popsané v další části očekávají tyto přihlašovací údaje ve formě **připojovacího řetězce zařízení**.

K dispozici je několik open-source nástrojů, které vám pomůžou se správou služby IoT Hub.

* Aplikace pro Windows, která se nazývá [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer)

* Rozšíření Visual Studio Code pro různé platformy označované jako [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Rozhraní Python CLI pro různé platformy s názvem [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

V tomto kurzu se používá grafický nástroj *Průzkumník zařízení* . Pokud vyvíjíte v VS Code, můžete použít *nástroje Azure IoT pro vs Code* . Pokud dáváte přednost použití nástroje CLI, můžete použít také *rozšíření IoT pro nástroj Azure CLI 2,0* .

Nástroj Průzkumník zařízení používá ke zpracování různých funkcí v IoT Hub, včetně přidávání zařízení, knihovny služeb Azure IoT. Pokud k přidání zařízení použijete nástroj Průzkumník zařízení, zobrazí se pro vaše zařízení připojovací řetězec. Tento připojovací řetězec budete potřebovat ke spuštění ukázkových aplikací.

Pokud nejste obeznámeni s nástrojem Průzkumník zařízení, následující postup popisuje, jak ho použít k přidání zařízení a získání připojovacího řetězce zařízení.

1. Chcete-li nainstalovat Nástroj Průzkumník zařízení, přečtěte si téma [jak použít Device Explorer pro IoT Hub zařízení](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/).

1. Po spuštění programu se zobrazí toto rozhraní:

   ![Device Explorer s dvojitým snímekem](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Do prvního pole zadejte **připojovací řetězec IoT Hub** a klikněte na **aktualizovat**. Tento krok nakonfiguruje nástroj tak, aby mohl komunikovat s IoT Hub. 

**Připojovací řetězec** najdete v části IoT Hub nastavení **služby**  >    >  **zásady sdíleného přístupu**  >  **iothubowner**.

1. Po nakonfigurování připojovacího řetězce IoT Hub klikněte na kartu **Správa** :

   ![Snímek obrazovky s dvojitou Device Explorerí](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Na této kartě můžete spravovat zařízení zaregistrovaná ve službě IoT Hub.

1. Zařízení vytvoříte kliknutím na tlačítko **vytvořit** . Zobrazí se dialogové okno se sadou předem vyplněných klíčů (primární a sekundární). Zadejte **ID zařízení** a pak klikněte na **vytvořit**.

   ![Vytvořit snímek obrazovky zařízení](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Po vytvoření zařízení se v seznamu zařízení aktualizují všechna registrovaná zařízení, včetně toho, kterou jste právě vytvořili. Pokud kliknete pravým tlačítkem na nové zařízení, zobrazí se tato nabídka:

   ![Výsledek neDevice Explorerho kliknutí pravým tlačítkem myši](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Pokud zvolíte **Kopírovat připojovací řetězec pro vybrané zařízení**, je připojovací řetězec zařízení zkopírován do schránky. Ponechte kopii připojovacího řetězce zařízení. Budete ho potřebovat při spouštění ukázkových aplikací popsaných v následujících částech.

Až provedete výše uvedené kroky, jste připraveni začít spouštět nějaký kód. Většina ukázek obsahuje konstantu v horní části hlavního zdrojového souboru, která umožňuje zadat připojovací řetězec. Například odpovídající řádek z **iothub_client \_ samples \_ iothub_convenience_sample** aplikace se zobrazí takto.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Použití knihovny IoTHubClient

Ve složce **\_ klienta iothub** v úložišti [Azure-IoT-SDK-c](https://github.com/azure/azure-iot-sdk-c) se nachází složka s **ukázkami** , která obsahuje aplikaci nazvanou **iothub \_ Client \_ Sample \_ MQTT**.

Verze **iothub_client \_ ukázek \_ Iothub_convenience_sample** aplikace pro Windows zahrnuje následující řešení sady Visual Studio:

  ![Průzkumník řešení sady Visual Studio](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Pokud aplikace Visual Studio vyzve k přecílení projektu na nejnovější verzi, Přijměte výzvu.

Toto řešení obsahuje jeden projekt. V tomto řešení jsou nainstalované čtyři balíčky NuGet:

* Microsoft. Azure. C. SharedUtility
* Microsoft. Azure. IoTHub. MqttTransport
* Microsoft. Azure. IoTHub. IoTHubClient
* Microsoft. Azure. umqtt

Při práci se sadou SDK vždy potřebujete balíček **Microsoft. Azure. C. SharedUtility** . Tato ukázka používá protokol MQTT, proto musíte zahrnout balíčky **Microsoft. Azure. umqtt** a **Microsoft. Azure. IoTHub. MQTTTRANSPORT** (pro AMQP a HTTPS jsou k dispozici ekvivalentní balíčky). Vzhledem k tomu, že ukázka používá knihovnu **IoTHubClient** , musíte taky do svého řešení zahrnout balíček **Microsoft. Azure. IoTHub. IoTHubClient** .

Implementaci ukázkové aplikace najdete v části **iothub_client \_ samples \_ iothub_convenience_sample** zdrojového souboru.

Následující kroky používají tuto ukázkovou aplikaci k tomu, abyste vás provedli, co je potřeba k používání knihovny **IoTHubClient** .

### <a name="initialize-the-library"></a>Inicializovat knihovnu

> [!NOTE]
> Než začnete s knihovnami pracovat, možná budete muset provést nějakou inicializaci specifickou pro konkrétní platformu. Pokud například plánujete používat AMQP v systému Linux, je nutné inicializovat knihovnu OpenSSL. Ukázky v [úložišti GitHub](https://github.com/Azure/azure-iot-sdk-c) volejte funkci **\_ init Platform** Functions, když se klient spustí a před ukončením zavolá funkci **\_ deinit platformy** . Tyto funkce jsou deklarovány v souboru hlaviček Platform. h. Projděte si definice těchto funkcí pro cílovou platformu v [úložišti](https://github.com/Azure/azure-iot-sdk-c) , abyste zjistili, jestli ve svém klientovi potřebujete zahrnout inicializační kód specifický pro platformu.

Chcete-li začít pracovat s knihovnami, nejprve přidělte IoT Hub popisovač klienta:

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

Do této funkce předáte kopii připojovacího řetězce zařízení, který jste získali z nástroje Průzkumník zařízení. Určíte také komunikační protokol, který se má použít. Tento příklad používá MQTT, ale také možnosti AMQP a HTTPS.

Pokud máte platný **\_ \_ popisovač klienta IOTHUB**, můžete začít volat rozhraní API pro posílání a přijímání zpráv do a z IoT Hub.

### <a name="send-messages"></a>Odesílání zpráv

Ukázková aplikace nastaví smyčku pro posílání zpráv do služby IoT Hub. Následující fragment kódu:

- Vytvoří zprávu.
- Přidá vlastnost do zprávy.
- Odešle zprávu.

Nejdřív vytvořte zprávu:

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

Pokaždé, když odešlete zprávu, zadáte odkaz na funkci zpětného volání, která se vyvolá při odeslání dat. V tomto příkladu se funkce zpětného volání nazývá **SendConfirmationCallback**. Následující fragment kódu ukazuje tuto funkci zpětného volání:

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

Poznamenejte si volání funkce **\_ zničení IoTHubMessage** po skončení zprávy. Tato funkce uvolní prostředky přidělené při vytvoření zprávy.

### <a name="receive-messages"></a>Příjem zpráv

Přijímání zprávy je asynchronní operace. Nejprve Zaregistrujte zpětné volání, které se vyvolá, když zařízení obdrží zprávu:

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

Poslední parametr je ukazatel void bez ohledu na to, co chcete. V ukázce je to ukazatel na celé číslo, ale může to být ukazatel na komplexnější datovou strukturu. Tento parametr umožňuje funkci zpětného volání pracovat ve sdíleném stavu s volajícím této funkce.

Když zařízení obdrží zprávu, vyvolá se zaregistrovaná funkce zpětného volání. Tato funkce zpětného volání načte:

* ID zprávy a ID korelace ze zprávy.
* Obsah zprávy
* Jakékoli vlastní vlastnosti ze zprávy.

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

Pomocí funkce **IoTHubMessage \_ getbytearray** načtěte zprávu, která v tomto příkladu je řetězec.

### <a name="uninitialize-the-library"></a>Zrušení inicializace knihovny

Až budete hotovi s odesíláním událostí a přijímáním zpráv, můžete zrušit inicializaci knihovny IoT. Provedete to tak, že vydáte následující volání funkce:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Toto volání uvolní prostředky dříve přidělené funkcí **IoTHubClient \_ CreateFromConnectionString** .

Jak vidíte, můžete snadno odesílat a přijímat zprávy pomocí knihovny **IoTHubClient** . Knihovna zpracovává podrobnosti o komunikaci s IoT Hub, včetně toho, který protokol se má použít (z perspektivy vývojáře je to jednoduchá možnost konfigurace).

Knihovna **IoTHubClient** také poskytuje přesnou kontrolu nad tím, jak serializovat data, která zařízení odesílá IoT Hub. V některých případech je tato úroveň řízení výhodná, ale v jiných verzích se jedná o podrobnosti implementace, které nechcete mít obavy. V takovém případě můžete zvážit použití knihovny **serializátorů** , která je popsána v následující části.

## <a name="use-the-serializer-library"></a>Použití knihovny serializátorů

Knihovna **serializátorů** se nachází v koncepčním vrcholu knihovny **IoTHubClient** v sadě SDK. Používá knihovnu **IoTHubClient** pro podkladovou komunikaci s IoT Hub, ale přidává možnosti modelování, které z vývojářů odstraňují zatížení prostřednictvím serializace zprávy. Jak funguje tato knihovna, je nejlépe vyplývat příkladem.

Ve složce **serializátoru** v [úložišti Azure-IoT-SDK-c](https://github.com/Azure/azure-iot-sdk-c)je složka **ukázek** , která obsahuje aplikaci s názvem **SimpleSample \_ MQTT**. Verze této ukázky pro Windows zahrnuje následující řešení sady Visual Studio:

  ![Ukázka řešení Visual Studio pro MQTT](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Pokud aplikace Visual Studio vyzve k přecílení projektu na nejnovější verzi, Přijměte výzvu.

Stejně jako u předchozí ukázky obsahuje tato jedna z nich několik balíčků NuGet:

* Microsoft. Azure. C. SharedUtility
* Microsoft. Azure. IoTHub. MqttTransport
* Microsoft. Azure. IoTHub. IoTHubClient
* Microsoft. Azure. IoTHub. serializátor
* Microsoft. Azure. umqtt

V předchozí ukázce jste viděli většinu těchto balíčků, ale **Microsoft. Azure. IoTHub. serializátor** je nový. Tento balíček je vyžadován při použití knihovny **serializátoru** .

Implementaci ukázkové aplikace najdete v souboru **\_ \_ iothub_convenience_sample iothub_client Samples** .

Následující části vás seznámí s klíčovými částmi této ukázky.

### <a name="initialize-the-library"></a>Inicializovat knihovnu

Chcete-li začít pracovat s knihovnou **serializátoru** , zavolejte rozhraní API inicializace:

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

Volání funkce **\_ init serializátoru** je jednorázové volání a inicializuje se podkladová knihovna. Pak zavoláte funkci **IoTHubClient \_ ll \_ CreateFromConnectionString** , což je stejné rozhraní API jako v ukázce **IoTHubClient** . Toto volání nastaví připojovací řetězec zařízení (Toto volání je také tam, kde zvolíte protokol, který chcete použít). V této ukázce se jako přenos používá MQTT, ale může použít AMQP nebo HTTPS.

Nakonec zavolejte funkci **Create \_ model \_ instance** . **WeatherStation** je obor názvů modelu a **ContosoAnemometer** je název modelu. Po vytvoření instance modelu ji můžete použít k zahájení odesílání a přijímání zpráv. Je však důležité pochopit, co je model.

### <a name="define-the-model"></a>Definování modelu

Model v knihovně **serializátoru** definuje zprávy, které může vaše zařízení odeslat IoT Hub a zprávy označované jako *Akce* v jazyku modelování, které může získat. Definujete model pomocí sady maker jazyka C jako v ukázkové aplikaci **iothub_client \_ samples \_ iothub_convenience_sample** :

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

Makra **Begin \_ Namespace** a **End \_ Namespace** přebírají obor názvů modelu jako argument. Očekává se, že cokoli mezi těmito makry je definice modelu nebo modelů a struktury dat, které používají modely.

V tomto příkladu je k dispozici jeden model s názvem **ContosoAnemometer**. Tento model definuje dvě data, která může vaše zařízení odeslat IoT Hub: **DeviceID** a **WindSpeed**. Definuje taky tři akce (zprávy), které může vaše zařízení přijímat: **TurnFanOn**, **TurnFanOff** a **SetAirResistance**. Každý datový prvek má typ a každá akce má název (a volitelně také sadu parametrů).

Data a akce definované v modelu definují plochu rozhraní API, kterou můžete použít k posílání zpráv do IoT Hub a reakci na zprávy odeslané do zařízení. Použití tohoto modelu se nejlépe považuje za příklad.

### <a name="send-messages"></a>Odesílání zpráv

Model definuje data, která můžete odeslat IoT Hub. V tomto příkladu to znamená jednu ze dvou datových položek definovaných pomocí makra **WITH_DATA** . K odeslání hodnot **DeviceID** a **WindSpeed** do služby IoT Hub je potřeba několik kroků. První je nastavit data, která chcete odeslat:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Model, který jste definovali dříve, umožňuje nastavit hodnoty nastavením členů **struktury**. Dále proveďte serializaci zprávy, kterou chcete odeslat:

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

Tento kód zaserializace zařízení do cloudu do vyrovnávací paměti (na kterou odkazuje **cíl**). Kód poté vyvolá funkci **SendMessage** k odeslání zprávy do IoT Hub:

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

Druhý k poslednímu parametru **IoTHubClient \_ ll \_ SendEventAsync** je odkaz na funkci zpětného volání, která se volá, když se data úspěšně odešlou. Tady je funkce zpětného volání v ukázce:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Druhý parametr je ukazatel na kontext uživatele; stejný ukazatel předaný do **IoTHubClient \_ šechny \_ SendEventAsync**. V tomto případě je kontext jednoduchým čítačem, ale může to být cokoli, co potřebujete.

To je všechno, co posílá zprávy ze zařízení do cloudu. Ta, kterou zbývá k pokrytí, je postup příjmu zpráv.

### <a name="receive-messages"></a>Příjem zpráv

Příjem zprávy funguje podobně jako zprávy fungují v knihovně **IoTHubClient** . Nejprve zaregistrujete funkci zpětného volání zprávy:

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

Pak zapíšete funkci zpětného volání, která se vyvolá při přijetí zprávy:

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

Tento kód je často používaný – pro každé řešení je to stejné. Tato funkce přijme zprávu a postará ji o směrování do příslušné funkce prostřednictvím volání **\_ příkazu Spustit**. Funkce volaná v tomto okamžiku závisí na definici akcí v modelu.

Při definování akce v modelu je nutné implementovat funkci, která je volána, když vaše zařízení obdrží odpovídající zprávu. Například pokud váš model definuje tuto akci:

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

Všimněte si, jak název funkce odpovídá názvu akce v modelu a že parametry funkce odpovídají parametrům zadaným pro akci. První parametr je vždy vyžadován a obsahuje ukazatel na instanci vašeho modelu.

Když zařízení obdrží zprávu, která odpovídá tomuto podpisu, zavolá se odpovídající funkce. Z toho důvodu, že je nutné zahrnout často používaný kód z **IoTHubMessage**, je přijímání zpráv pouze záležitostí definování jednoduché funkce pro každou akci definovanou v modelu.

### <a name="uninitialize-the-library"></a>Zrušení inicializace knihovny

Až budete hotovi s odesíláním dat a přijímáním zpráv, můžete zrušit inicializaci knihovny IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Každá z těchto tří funkcí zarovnává se třemi inicializačními funkcemi popsanými dříve. Volání těchto rozhraní API zajistí, že jste uvolnili dříve přidělené prostředky.

## <a name="next-steps"></a>Další kroky

Tento článek se věnuje základům používání knihoven v **sadě SDK pro zařízení Azure IoT pro jazyk C**. Poskytuje vám dostatek informací, které vám pomohou pochopit, co je součástí sady SDK, její architektury a jak začít pracovat s ukázkami systému Windows. Další článek pokračuje v popisu sady SDK s vysvětlením [dalších informací o knihovně IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Další informace o vývoji pro IoT Hub najdete v tématu sady [SDK služby Azure IoT](iot-hub-devguide-sdks.md).

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/quickstart-linux.md)
