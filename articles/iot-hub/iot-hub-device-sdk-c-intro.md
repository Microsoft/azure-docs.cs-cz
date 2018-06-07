---
title: Zařízení Azure IoT SDK pro jazyk C | Microsoft Docs
description: Začínáme s Azure IoT zařízení SDK pro jazyk C a informace o vytváření aplikací pro zařízení, které komunikují pomocí služby IoT hub.
author: yzhong94
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: yizhon
ms.openlocfilehash: 78cd8b2d6afe98e34c33ed3c841c8023d9dab764
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635266"
---
# <a name="azure-iot-device-sdk-for-c"></a>Pro zařízení Azure IoT SDK pro jazyk C

**Zařízení Azure IoT SDK** je sada knihoven navržený tak, aby zjednodušit proces odesílání zpráv a příjem zpráv z **Azure IoT Hub** služby. Existují různé varianty sady SDK, každý cílení na konkrétní platformu, ale tento článek popisuje **zařízení Azure IoT SDK pro jazyk C**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Zařízení Azure IoT SDK pro jazyk C je napsán v ANSI C (C99) a maximalizovat tak přenositelnost. Tato funkce zpřístupňuje v knihovnách vhodné pracovat na několika platformách a zařízeních, zejména v případě, že minimalizovat disku a spotřeba paměti je prioritu.

Existují širokou škálu platformy, na kterých SDK je testovaná (najdete v článku [Azure certifikované pro katalog zařízení IoT](https://catalog.azureiotsuite.com/) podrobnosti). I když tento článek obsahuje návody ukázkový kód spuštěný na platformě Windows, napříč celou škálu podporované platformy je stejný jako kód popsané v tomto článku.

Následující video obsahuje přehled sady SDK Azure IoT pro C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Tento článek vás seznámí s architektuře zařízení Azure IoT SDK pro C. Ukazuje, jak provést inicializaci knihovny zařízení odesílat data do služby IoT Hub a příjem zpráv z něj. Informace v tomto článku by vám měly dostatečně začít používat sadu SDK, ale také poskytuje ukazatele na další informace o knihovnách.

## <a name="sdk-architecture"></a>Architektura sady SDK

Můžete najít [ **zařízení Azure IoT SDK pro jazyk C** ](https://github.com/Azure/azure-iot-sdk-c) Githubu úložiště a zobrazte podrobnosti o rozhraní API v [referenční dokumentace rozhraní API jazyka C](https://azure.github.io/azure-iot-sdk-c/index.html).

Nejnovější verzi knihovny najdete v **hlavní** větev úložiště:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

* Implementace základní sady SDK je v **iothub\_klienta** složku, která obsahuje implementaci nejnižší vrstvu rozhraní API v sadě SDK: **IoTHubClient** knihovny. **IoTHubClient** knihovna obsahuje rozhraní API pro implementace nezpracovaná zasílání zpráv pro odesílání zpráv do služby IoT Hub a příjem zpráv ze služby IoT Hub. Při použití této knihovny, je zodpovědná za implementace zpráva serializaci, ale další podrobnosti o komunikaci se službou IoT Hub jsou zpracovávány pro vás.
* **Serializátor** složka obsahuje podpůrné funkce a ukázky, které ukazují, jak k serializaci dat před odesláním do služby Azure IoT Hub pomocí klientské knihovny. Použití serializátoru, který není povinné a zajišťuje pro potřeby. Použít **serializátor** knihovny, můžete definovat model, který určuje data k odeslání do služby IoT Hub a zprávy, které chcete dostávat. Jakmile je definována modelu, sadu SDK vám poskytne plochy rozhraní API, která umožňuje snadno pracovat s zprávy typu zařízení cloud a z cloudu do zařízení bez obav o podrobnostech serializace. Knihovny závisí na jiné opensourcové knihovny, které implementují přenosu pomocí protokolů, například MQTT a AMQP.
* **IoTHubClient** knihovny závisí na jiné opensourcové knihovny:
  * [Azure C sdílené nástroj](https://github.com/Azure/azure-c-shared-utility) knihovny, která poskytuje běžné funkce pro základní úlohy (například řetězce, manipulace se seznamem a vstupně-výstupní operace) potřeby napříč několika souvisejících s Azure C sady SDK.
  * [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) knihovny, která je na straně klienta implementace AMQP optimalizované pro zařízení prostředků omezené.
  * [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) knihovny, která je pro obecné účely knihovna implementaci protokolu MQTT a optimalizovaný pro zařízení prostředků omezené.

Použijte tyto knihovny je srozumitelnější prohlížením ukázkový kód. Následující části vás provede procesem několik ukázkových aplikací, které jsou zahrnuté v sadě SDK. Tento názorný postup by měl poskytují dobrý chování pro různé funkce architektury vrstvy sady SDK a úvod do fungování rozhraní API.

## <a name="before-you-run-the-samples"></a>Před spuštěním ukázky

Před spuštěním ukázky v zařízení Azure IoT SDK pro jazyk C, je nutné [vytvoření instance služby IoT Hub](iot-hub-create-through-portal.md) ve vašem předplatném Azure. Dokončete následující úlohy:

* Příprava vývojového prostředí
* Získejte přihlašovací údaje zařízení.

### <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Balíčky jsou k dispozici pro běžné platformy (například NuGet pro systém Windows nebo apt_get Debian a Ubuntu) a ukázky použití těchto balíčků, pokud je k dispozici. V některých případech budete muset zkompilujte sadu SDK pro nebo na zařízení. Pokud potřebujete zkompilujte sadu SDK, přečtěte si téma [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v úložišti GitHub.

Ukázkový kód aplikace si stáhněte kopii sady SDK z Githubu. Požádejte kopii zdroj z **hlavní** větev [úložiště GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Získat přihlašovací údaje zařízení

Teď, když máte ukázka zdrojový kód, dalším krokem je získat sadu pověření zařízení. Pro zařízení a budou moci služby IoT hub je nejprve nutno přidat zařízení do registru identit služby IoT Hub. Když přidáte zařízení, můžete získat sadu pověření zařízení, které potřebujete pro zařízení, které bude moct připojit ke službě IoT hub. Ukázkové aplikace popsané v následující části předpokládají, že tyto přihlašovací údaje ve formě **zařízení připojovací řetězec**.

Je několik nástrojů s otevřeným zdrojem, které vám pomohou spravovat služby IoT hub.

* Aplikace systému Windows s názvem [explorer zařízení](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
* Nástroj příkazového řádku Python napříč platformami názvem [IoT rozšíření pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

Tento kurz používá grafickém *explorer zařízení* nástroj. Můžete také *IoT rozšíření pro Azure CLI 2.0* Pokud byste radši chtěli použít nástroj příkazového řádku.

Nástroj Průzkumník zařízení používá k provádění různých funkcí na IoT Hub, včetně přidávání zařízení knihovny služby Azure IoT. Pokud používáte nástroj Průzkumník zařízení pro přidání zařízení, můžete získat připojovací řetězec pro vaše zařízení. Je nutné tento připojovací řetězec ke spuštění ukázkové aplikace.

Pokud si nejste obeznámeni s nástroj Průzkumník zařízení, následující postup popisuje, jak ho použít k přidání zařízení a získat připojovací řetězec zařízení.

Chcete-li nainstalovat nástroj Průzkumník zařízení, přečtěte si téma [použití Průzkumníka zařízení pro zařízení IoT Hub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

Když spustíte program, zobrazí se toto rozhraní:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Zadejte vaše **IoT Hub připojovací řetězec** první pole a klikněte na **aktualizace**. Tento krok nakonfiguruje nástroj tak, aby může komunikovat se službou IoT Hub.

Pokud je nakonfigurovaná připojovací řetězec služby IoT Hub, klikněte na tlačítko **správy** karty:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Tato karta je, kde budete spravovat zařízení zaregistrovaná ve službě IoT hub.

Kliknutím na vytvořit zařízení **vytvořit** tlačítko. Zobrazí se dialogové okno zobrazí sadu předem vyplněná klíče (primární i sekundární). Zadejte **ID zařízení** a pak klikněte na **vytvořit**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Při vytvoření zařízení zařízení seznam aktualizací s všechna registrovaná zařízení, včetně je ta, kterou jste právě vytvořili. Pokud kliknete pravým tlačítkem na nové zařízení, zobrazí se v této nabídce:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Pokud se rozhodnete **zkopírujte připojovací řetězec pro vybrané zařízení**, zařízení připojovací řetězec je zkopírován do schránky. Ponechat si kopii připojovací řetězec zařízení. Budete ho potřebovat při spuštění ukázkové aplikace popsané v následujících částech.

Po dokončení kroků výše, jste připravení začít spouštět nějaký kód. Obě ukázky mít konstanta v horní části hlavní zdrojový soubor, který umožňuje zadat připojovací řetězec. Například odpovídající řádek z **iothub\_klienta\_ukázka\_mqtt** aplikace se zobrazí následujícím způsobem.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Použití knihovny IoTHubClient

V rámci **iothub\_klienta** složky v [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) úložiště, je **ukázky** složky, která obsahuje aplikaci s názvem **iothub\_klienta\_ukázka\_mqtt**.

Verze Windows **iothub\_klienta\_ukázka\_mqtt** aplikace zahrnuje následující řešení sady Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-mqtt.PNG)

> [!NOTE]
> Pokud tento projekt otevřít v aplikaci Visual Studio 2017, přijetí výzvy přesměrovat projektu na nejnovější verzi.

Toto řešení obsahuje jedné aplikace. Existují čtyři balíčky NuGet, které jsou nainstalované v tomto řešení:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Vždy musíte **Microsoft.Azure.C.SharedUtility** balíček při práci s SDK. Tato ukázka používá protokol MQTT, proto je nutné zahrnout **Microsoft.Azure.umqtt** a **Microsoft.Azure.IoTHub.MqttTransport** balíčků (existují zde ekvivalentní balíčky pro AMQP a HTTPS). Vzhledem k tomu, že ukázce se používá **IoTHubClient** knihovny, musíte taky zahrnout **Microsoft.Azure.IoTHub.IoTHubClient** balíček ve vašem řešení.

Můžete najít implementaci pro ukázkovou aplikaci v **iothub\_klienta\_ukázka\_mqtt.c** zdrojový soubor.

Následující kroky vás provedou co je potřeba použít pomocí této ukázkové aplikaci **IoTHubClient** knihovny.

### <a name="initialize-the-library"></a>Inicializace knihovny

> [!NOTE]
> Před zahájením práce s knihovny, musíte provést inicializaci některé specifické pro platformu. Například pokud máte v plánu používat AMQP v systému Linux je nutné inicializovat knihovny OpenSSL. Ukázky [úložiště GitHub](https://github.com/Azure/azure-iot-sdk-c) volání funkce nástroj **platformy\_init** při spuštění a volání klienta **platformy\_deinit**funkce před ukončením. Tyto funkce jsou deklarované v záhlaví souboru platform.h. Prohlédněte si definice tyto funkce pro svou cílovou platformu v [úložiště](https://github.com/Azure/azure-iot-sdk-c) k určení, jestli je potřeba zahrnout žádné specifické pro platformu inicializace kódu do vašeho klienta.

Pokud chcete začít pracovat s knihovny, nejprve přidělte popisovač klienta služby IoT Hub:

```c
if ((iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Předáte kopii zařízení připojovací řetězec, který jste obdrželi z nástroj Průzkumník zařízení této funkce. Můžete také určit komunikační protokol používat. Tento příklad používá MQTT, ale AMQP a HTTPS jsou také možnosti.

Pokud máte platný **IOTHUB\_klienta\_zpracování**, můžete spustit volání rozhraní API odesílat a přijímat zprávy do a ze služby IoT Hub.

### <a name="send-messages"></a>Poslat zprávy

Ukázkovou aplikaci nastaví smyčku k odesílání zpráv do služby IoT hub. Následující fragment kódu:

- Vytvoří zprávu.
- Přidá vlastnost ke zprávě.
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

Pokaždé, když odešlete zprávu, zadáte odkaz na funkci zpětného volání, která je volána, když data se odesílají. V tomto příkladu je volána funkce zpětného volání **SendConfirmationCallback**. Následující fragment kódu ukazuje tuto funkci zpětného volání:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Všimněte si volání **IoTHubMessage\_Destroy** fungovat po dokončení se zprávou. Tato funkce uvolní prostředky přidělené při vytvoření zprávy.

### <a name="receive-messages"></a>Přijmout zprávy

Přijímání zprávy je asynchronní operace. Nejprve zaregistrovat zpětného volání, který má být vyvolán při zařízení obdrží zprávu:

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

Poslední parametr je neplatný ukazatel na libovolně. V ukázce je ukazatel na celé číslo, ale může to být ukazatel na složitější datová struktura. Tento parametr umožňuje funkce zpětného volání k provozu na sdílení stavu s volající tuto funkci.

Pokud zařízení obdrží zprávu, je vyvolána registrovaná funkce zpětného volání. Tato funkce zpětného volání načte:

* Id zprávy a id korelace zprávy.
* Obsah zprávy.
* Vlastní vlastnosti zprávy.

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

Použití **IoTHubMessage\_GetByteArray** funkce pro načtení zprávy, která v tomto příkladu je řetězec.

### <a name="uninitialize-the-library"></a>Uninitialize – knihovny

Po dokončení události odesílání a příjmu zprávy, můžete Uninitialize – knihovna IoT. Uděláte to tak, vydejte následující volání funkce:

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Toto volání uvolní prostředky přidělené dříve **IoTHubClient\_CreateFromConnectionString** funkce.

Jak vidíte, se snadno odesílat a přijímat zprávy pomocí **IoTHubClient** knihovny. Knihovny zpracovává podrobnosti o komunikaci se službou IoT Hub, včetně používaný protokol (z pohledu vývojáře, je tato možnost jednoduché konfigurace).

**IoTHubClient** knihovna také poskytuje přesnou kontrolu nad postupy k serializaci dat vaše zařízení odesílá do služby IoT Hub. V některých případech tato úroveň řízení je několik výhod, ale v jiné je podrobností implementace, které nechcete, aby se to týká s. Pokud je to tento případ, můžete zvážit použití **serializátor** knihovny, která je popsaná v další části.

## <a name="use-the-serializer-library"></a>Použití knihovny serializátor

Koncepčně **serializátor** knihovny umístěné na **IoTHubClient** knihovny v sadě SDK. Použije **IoTHubClient** knihovny pro základní komunikaci se službou IoT Hub, ale přidá modelování možnosti, které odebrat zatížení práci s serializace zprávu od vývojáře. Jak funguje tato knihovna je nejvhodnější ukázán na příkladu.

Uvnitř **serializátor** složku [úložiště azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c), je **ukázky** složky, která obsahuje aplikaci s názvem **simplesample\_mqtt**. Verze systému Windows Tato ukázka obsahuje následující řešení sady Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_mqtt.PNG)

> [!NOTE]
> Pokud tento projekt otevřít v aplikaci Visual Studio 2017, přijetí výzvy přesměrovat projektu na nejnovější verzi.

Stejně jako u předchozí ukázce tato zahrnuje několik balíčků NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Seznámili jste se většina těchto balíčků ve v předchozím příkladu, ale **Microsoft.Azure.IoTHub.Serializer** je nová. Tento balíček je požadovaná při použití **serializátor** knihovny.

Implementace ukázkovou aplikaci v můžete najít **simplesample\_mqtt.c** souboru.

Následující části vás provede procesem klíčovými částmi této ukázky.

### <a name="initialize-the-library"></a>Inicializace knihovny

Pokud chcete začít pracovat s **serializátor** knihovny, volání inicializace rozhraní API:

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

Volání **serializátor\_init** funkce je jednorázové volání a inicializuje základní knihovny. Potom zavolejte **IoTHubClient\_UDOU\_CreateFromConnectionString** funkci, což je rozhraní API stejné jako v **IoTHubClient** ukázka. Toto volání nastaví připojovací řetězec zařízení (Toto volání se také kde zvolíte protokol chcete použít). Tato ukázka používá MQTT jako přenos, ale může použít protokol AMQP nebo HTTPS.

Nakonec zavolejte **vytvořit\_modelu\_INSTANCE** funkce. **WeatherStation** je obor názvů modelu a **ContosoAnemometer** je název modelu. Po vytvoření instance modelu, můžete spustit odesílání a přijímání zpráv. Je ale důležité si uvědomit, jaký je model.

### <a name="define-the-model"></a>Definování modelu

Model v **serializátor** knihovny definuje zprávy, které zařízení může odesílat do služby IoT Hub a zprávy, označované jako *akce* v jazyce modelování, který může přijímat. Definovat model pomocí sady maker v jazyce C jako v **simplesample\_mqtt** ukázkovou aplikaci:

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

**Začít\_obor názvů** a **END\_obor názvů** makra i obor názvů modelu jako argument. Očekává se, že nic mezi tyto makra je definice modelu nebo modely a datové struktury, které používají modely.

V tomto příkladu je jednotný model názvem **ContosoAnemometer**. Tento model definuje dva kusy dat, který zařízení může odesílat do služby IoT Hub: **DeviceId** a **větru**. Definuje také tři akce (zpráv), které vaše zařízení může získat: **TurnFanOn**, **TurnFanOff**, a **SetAirResistance**. Každý datový prvek má typ, přičemž každá akce má název (a volitelně sadu parametrů).

Data a akce definované v modelu definovat plochy rozhraní API, které můžete použít k odesílání zpráv do služby IoT Hub a reakce na zprávy odeslané do zařízení. Použití tohoto modelu odhalíte nejlépe v příkladu.

### <a name="send-messages"></a>Poslat zprávy

Model definuje data, která můžete odeslat do služby IoT Hub. V tomto příkladu to znamená, jeden dvě datové položky definované pomocí **WITH_DATA** makro. Existuje několik kroků, které jsou potřebné k odeslání **DeviceId** a **větru** hodnoty do služby IoT hub. První je nastavit data, která se mají posílat:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Model definovaného dříve umožňuje nastavit hodnoty nastavením členy **struktura**. V dalším kroku serializovat zprávu, kterou chcete odeslat:

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

Tento kód serializuje zařízení cloud do vyrovnávací paměti (odkazuje **cílové**). Kód poté vyvolá **sendMessage** funkce k odeslání zprávy do služby IoT Hub:

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


Druhou pro poslední parametr **IoTHubClient\_UDOU\_SendEventAsync** je odkaz na funkci zpětného volání, která je volána, když data úspěšně odeslána. Tady je funkce zpětného volání ve vzorku:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Druhý parametr je ukazatel na kontext uživatele; Předaný stejné ukazatele **IoTHubClient\_UDOU\_SendEventAsync**. V takovém případě kontextu je jednoduchý čítač, ale může být, vše, co chcete.

To je všechno je zasílání zpráv typu zařízení cloud. Jediné, co ponecháno tak, aby pokrývalo je jak přijmout zprávy.

### <a name="receive-messages"></a>Přijmout zprávy

Příjem zpráv funguje podobně jako způsob zprávy fungovat **IoTHubClient** knihovny. Nejprve zaregistrovat funkci zpětného volání zpráva:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Potom napíšete funkce zpětného volání, která je volána, když je obdržena zprávu:

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

Tento kód je často používaný – je to stejný pro všechny řešení. Tato funkce přijímá zprávy a má na starosti směrování na odpovídající funkce prostřednictvím volání **EXECUTE\_příkaz**. Volaná funkce v tomto okamžiku závisí na definici akcí v daném modelu.

Když definujete akce v modelu, vyžádání implementovat funkci, která je volána, když zařízení obdrží odpovídající zprávu. Například pokud váš model definuje tato akce:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Definice funkce s Tento podpis:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Všimněte si, jak název funkce odpovídá názvu akce v modelu a že parametry funkce odpovídaly parametry zadané pro tuto akci. První parametr bude vždy požadován a obsahuje ukazatel na instanci modelu.

Pokud zařízení obdrží zprávu, která odpovídá tento podpis, odpovídající funkce je volána. Proto kromě zajištění dostatečného nutné zahrnovat často používaný kód z **IoTHubMessage**, přijímání zpráv stačí definice jednoduché funkce pro jednotlivé akce definované v modelu.

### <a name="uninitialize-the-library"></a>Uninitialize – knihovny

Po dokončení odesílání dat a příjmu zpráv, můžete Uninitialize – knihovna IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Každá z těchto tří funkcí zarovnaná s tři Inicializace funkce popsané. Volání tato rozhraní API zajistí, že můžete uvolnit dříve přidělené prostředky.

## <a name="next-steps"></a>Další kroky

Základní informace o používání knihoven v zahrnuté v tomto článku **zařízení Azure IoT SDK pro jazyk C**. Je k dispozici dostatek informací zjistit, co je součástí sady SDK, jeho architektura a jak začít pracovat s ukázky Windows. Další článek pokračuje popis sady SDK a popsat, [Další informace o knihovně IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Další informace o vývoji pro IoT Hub, najdete v tématu [SDK služby Azure IoT][lnk-sdks].

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
