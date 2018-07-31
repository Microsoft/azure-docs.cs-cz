---
title: Zařízení Azure IoT SDK pro jazyk C | Dokumentace Microsoftu
description: Začínáme s sady SDK pro zařízení Azure IoT pro C a zjistěte, jak vytvořit aplikace pro zařízení, které komunikují s centrem IoT.
author: yzhong94
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: yizhon
ms.openlocfilehash: 4f8ad67fafa20fd9adce62e8beb619999203ef62
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346657"
---
# <a name="azure-iot-device-sdk-for-c"></a>Pro zařízení Azure IoT SDK pro jazyk C

**Zařízení Azure IoT SDK** je sada knihoven, které jsou navržené pro zjednodušení procesu odesílání zpráv a příjem zpráv z **Azure IoT Hub** služby. Existují různé varianty sady SDK, každý cílení na konkrétní platformy, ale tento článek popisuje **zařízení Azure IoT SDK pro jazyk C**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Zařízení Azure IoT SDK pro jazyk C je napsán v C (C99) pro maximalizaci přenositelnost ANSI. Díky této funkci můžete knihoven skvěle se hodí hlavně v případě, že minimalizovat disku provozovat na více platformách a zařízeních, a nároky na paměť je prioritou.

Existuje široká škála platforem, na kterých byly testovány sady SDK (najdete v článku [programu Azure Certified for IoT zařízení katalogu](https://catalog.azureiotsuite.com/) podrobnosti). I když tento článek obsahuje postupy, ukázkový kód běží na platformě Windows, je kód popsaný v tomto článku identické škále podporovaných platforem.

Následující video představuje přehled sady SDK Azure IoT pro jazyk C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Tento článek vás seznámí s architekturou sady SDK pro zařízení Azure IoT pro C. Ukazuje, jak inicializovat knihovnu zařízení, odesílání dat do služby IoT Hub a příjem zpráv z něj. Informace v tomto článku by vám měly dostatečně, abyste mohli začít používat sadu SDK, ale přináší také odkazy na další informace o knihovnách.

## <a name="sdk-architecture"></a>Architektura sady SDK

Můžete najít [ **zařízení Azure IoT SDK pro jazyk C** ](https://github.com/Azure/azure-iot-sdk-c) Githubu úložišti a zobrazit podrobnosti o rozhraní API ve službě [reference k rozhraní API jazyka C](https://azure.github.io/azure-iot-sdk-c/index.html).

Nejnovější verzi knihovny najdete v **hlavní** větev úložiště:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

* Základní implementace sady SDK se **iothub\_klienta** složku, která obsahuje implementace nejnižší vrstvu rozhraní API v sadě SDK: **pro IoTHubClient** knihovny. **Pro IoTHubClient** knihovna obsahuje rozhraní API pro implementace nezpracovaná zasílání zpráv pro odeslání zprávy do služby IoT Hub a příjem zpráv ze služby IoT Hub. Při použití této knihovny, zodpovídáte za implementaci serializace zprávy, ale další podrobnosti o komunikaci se službou IoT Hub se postará služba za vás.
* **Serializátor** složka obsahuje pomocné funkce a ukázky, které ukazují, jak k serializaci dat před odesláním do služby Azure IoT Hub pomocí klientské knihovny. Použití serializátoru, který není povinná a je k dispozici v zájmu usnadnění práce. Použít **serializátor** knihovny, definujete model, který určuje data k odeslání do služby IoT Hub a zprávy, které by se měl zobrazit z něj. Jakmile model je definován, sada SDK vám poskytne povrch rozhraní API, která umožňuje jednoduchá práci s zprávy typu zařízení cloud a cloud zařízení bez starostí o podrobnosti o serializaci. Knihovny závisí na jiných open source knihoven, které implementují přenosu pomocí protokoly, například MQTT a protokolu AMQP.
* **Pro IoTHubClient** knihovny závisí na jiných open source knihoven:
  * [Azure C sdílený nástroj](https://github.com/Azure/azure-c-shared-utility) knihovny, která poskytuje běžné funkce pro základní úlohy (například řetězce, manipulace se seznamem a vstupně-výstupních operací) potřeby napříč několika souvisejících s Azure C sady SDK.
  * [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) knihovny, která je na straně klienta provádění AMQP optimalizovaný pro zařízení s omezením prostředků.
  * [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) knihovny, která je implementace protokolu MQTT knihovna pro obecné účely a optimalizovaný pro zařízení prostředků omezené.

Použijte tyto knihovny je lze snáze pochopit prohlédněte příklad kódu. Následující části vás provede několik ukázkových aplikací, které jsou zahrnuty v sadě SDK. Tento názorný postup by vám měl dát správné chování pro různé funkce vrstvy architektury sady SDK a úvod do fungování rozhraní API.

## <a name="before-you-run-the-samples"></a>Před spuštěním ukázky

Před spuštěním ukázky v zařízení Azure IoT SDK pro jazyk C, je nutné [vytvoření instance služby IoT Hub](iot-hub-create-through-portal.md) ve vašem předplatném Azure. Pak proveďte následující úkoly:

* Příprava vývojového prostředí
* Získáte přihlašovací údaje k zařízení.

### <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Balíčky jsou k dispozici pro běžné platformy (jako je NuGet pro Windows nebo apt_get pro Debian a Ubuntu) a ukázky použití těchto balíčků, pokud je k dispozici. V některých případech budete muset zkompilujte sadu SDK pro nebo na vašem zařízení. Pokud potřebujete zkompilujte sadu SDK, přečtěte si téma [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v úložišti GitHub.

Získat kód ukázkové aplikace, stáhněte si kopii sady SDK z Githubu. Získejte svoji kopii zdroje z **hlavní** větev [úložiště GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Získání přihlašovacích údajů pro zařízení

Teď, když máte zdrojový kód ukázkové, je dalším krokem je získat sadu přihlašovacích údajů zařízení. U zařízení bude mít přístup k službě IoT hub musíte nejprve přidat zařízení do registru identit služby IoT Hub. Když přidáte zařízení, získáte sadu přihlašovacích údajů zařízení, které potřebujete pro zařízení, které bude moct připojit ke službě IoT hub. Ukázkové aplikace popsané v další části předpokládají, že tyto přihlašovací údaje ve formě **připojovací řetězec zařízení**.

Existuje několik opensourcových nástrojů, které vám pomohou při správě služby IoT hub.

* Aplikace Windows s názvem [Průzkumník zařízení](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
* Volá se, Visual Studio Code příponou napříč platformami [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).
* Nástroje Python CLI napříč platformami volá [rozšíření IoT pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

Tento kurz používá grafickém *Průzkumník zařízení* nástroj. Můžete použít *rozšíření Azure IoT Toolkit pro VS Code* při vývoji v nástroji VS Code. Můžete také použít *rozšíření IoT pro Azure CLI 2.0* nástroj, pokud byste radši chtěli použít nástroj příkazového řádku.

Nástroj device explorer knihovny služby Azure IoT používá k provádění různých funkcí v IoT Hubu, včetně přidávání zařízení. Pokud používáte nástroj Průzkumník zařízení pro přidání zařízení, získání připojovacího řetězce pro vaše zařízení. Je nutné tento připojovací řetězec pro spuštění ukázkové aplikace.

Pokud nejste obeznámeni s nástroj device explorer, následující postup popisuje, jak ho použít k přidání zařízení a získat připojovací řetězec zařízení.

Chcete-li nainstalovat nástroj Průzkumník zařízení, najdete v článku [jak používat pro zařízení služby IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

Když program spustíte, zobrazí se toto rozhraní:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Zadejte vaše **připojovacího řetězce centra IoT** na první pole a klikněte na **aktualizace**. Tento krok nakonfiguruje nástroj, aby mohla komunikovat s centrem IoT. **Připojovací řetězec** najdete v části **služby Azure IoT Hub** > **nastavení** > **zásady sdíleného přístupu**  >  **iothubowner**.

Pokud je nakonfigurovaná připojovací řetězec služby IoT Hub, klikněte na tlačítko **správu** kartu:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Tato karta je, kde budete spravovat zařízení registrovaná ve službě IoT hub.

Vytvoření zařízení kliknutím **vytvořit** tlačítko. Dialogové okno zobrazí sadu předem naplněných klíče (primární i sekundární). Zadejte **ID zařízení** a potom klikněte na tlačítko **vytvořit**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Když se zařízení, zařízení seznam aktualizací se všechna registrovaná zařízení, včetně toho, který jste právě vytvořili. Pokud kliknete pravým tlačítkem nové zařízení, zobrazí tato nabídka:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Pokud se rozhodnete **zkopírujte připojovací řetězec pro vybrané zařízení**, připojovací řetězec zařízení je zkopírován do schránky. Zkopírujte připojovací řetězec zařízení. Když je potřebujete při spuštění ukázkové aplikace popsané v následujících částech.

Až provedete všechny výše uvedené kroky, jste připraveni začít spouštět nějaký kód. Většina ukázky obsahovat konstanty v horní části hlavní zdrojový soubor, který umožňuje zadat připojovací řetězec. Například odpovídající řádek z **iothub\_klienta\_ukázka\_mqtt** aplikace se zobrazí takto.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Použití knihovny pro IoTHubClient

V rámci **iothub\_klienta** složky [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) úložiště, je **ukázky** složky, která obsahuje aplikaci s názvem **iothub\_klienta\_ukázka\_mqtt**.

Verze Windows **iothub\_klienta\_ukázka\_mqtt** aplikace obsahuje následující řešení sady Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-mqtt.PNG)

> [!NOTE]
> Pokud tento projekt otevřít v sadě Visual Studio 2017, potvrďte jej překonfigurovat projekt na nejnovější verzi.

Toto řešení obsahuje jeden projekt. Existují čtyři balíčky NuGet, nainstalované v tomto řešení:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Budete vždy potřebovat **Microsoft.Azure.C.SharedUtility** balíček při práci se sadou SDK. Tato ukázka používá protokol MQTT, proto je nutné zahrnout **Microsoft.Azure.umqtt** a **Microsoft.Azure.IoTHub.MqttTransport** balíčky (je ekvivalentní balíčky AMQP a HTTPS). Protože příklad používá **pro IoTHubClient** knihovny, musíte taky zahrnout **Microsoft.Azure.IoTHub.IoTHubClient** balíček ve vašem řešení.

Můžete najít implementaci pro ukázkovou aplikaci v **iothub\_klienta\_ukázka\_mqtt.c** zdrojový soubor.

Následující kroky vás provedou co je potřeba použít pomocí této ukázkové aplikaci **pro IoTHubClient** knihovny.

### <a name="initialize-the-library"></a>Inicializovat knihovnu

> [!NOTE]
> Předtím, než se pustíte do práce knihovny, budete muset provést inicializaci specifické pro platformu. Například pokud plánujete protokol AMQP použít k Linuxu musí inicializovat knihovnu OpenSSL. Ukázky [úložiště GitHub](https://github.com/Azure/azure-iot-sdk-c) volání funkce nástroj **platformy\_init** při spuštění a volání klienta **platformy\_deinit**před ukončením funkce. Tyto funkce jsou deklarovány v souboru hlaviček platform.h. Prohlédněte si definice z těchto funkcí pro vaši cílovou platformu v [úložiště](https://github.com/Azure/azure-iot-sdk-c) k určení, jestli je potřeba zahrnout jakýkoli kód inicializace specifické pro platformu vašeho klienta.

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

Můžete předat kopii připojovací řetězec zařízení, které jste získali z nástroje Průzkumník zařízení pro tuto funkci. Můžete také určit komunikační protokol, který chcete použít. Tento příklad používá protokol MQTT, ale AMQP a protokol HTTPS jsou také možnosti.

Pokud máte platný **IOTHUB\_klienta\_zpracování**, můžete začít volání rozhraní API pro odesílání a příjem zpráv do a ze služby IoT Hub.

### <a name="send-messages"></a>Odesílání zpráv

Ukázková aplikace nastaví smyčku pro odesílání zpráv do služby IoT hub. Následující fragment kódu:

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

Pokaždé, když pošlete zprávu, zadáte odkaz na funkci zpětného volání, které je voláno, když se data odesílají. V tomto příkladu je volána funkce zpětného volání **SendConfirmationCallback**. Následující fragment kódu ukazuje tuto funkci zpětného volání:

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

Všimněte si volání **IoTHubMessage\_Destroy** fungovat až budete hotovi s touto zprávou. Tato funkce uvolní prostředky přidělené při vytváření zprávy.

### <a name="receive-messages"></a>Příjem zpráv

Přijetí zprávy je asynchronní operace. Nejprve zaregistrujte zpětné volání, které má být vyvolán při zařízení obdrží zprávu:

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

Poslední parametr není ukazatel void libovolně. V ukázce je ukazatel na celé číslo, ale může to být ukazatel na strukturu složitější data. Tento parametr povoluje funkce zpětného volání má provést výpočet sdílený stav s volající této funkce.

Pokud zařízení přijme zprávu, je vyvolána funkce registrovaného zpětného volání. Tato funkce zpětného volání načte:

* Id zprávy a id korelace ze zprávy.
* Obsah zprávy.
* Vlastní vlastnosti ze zprávy.

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

Použití **IoTHubMessage\_GetByteArray** funkce, která se načetla tuto zprávu, která v tomto příkladu je řetězec.

### <a name="uninitialize-the-library"></a>Uninitialize knihovny

Až to budete mít událostí odesílání a přijímání zpráv, můžete uninitialize knihovny IoT. Uděláte to tak, vydejte následující volání funkce:

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Toto volání uvolní prostředky dříve přidělený metodou **pro IoTHubClient\_CreateFromConnectionString** funkce.

Jak vidíte, je snadné odesílání a příjem zpráv s **pro IoTHubClient** knihovny. Knihovny zpracovává podrobnosti o komunikaci se službou IoT Hub, včetně který protokol se má použít (z pohledu vývojáře, jde o jednoduchou konfiguraci možnost).

**Pro IoTHubClient** knihovna také poskytuje přesnou kontrolu nad jak k serializaci dat, vaše zařízení odesílá do služby IoT Hub. Takovou míru kontroly v některých případech je výhodné, ale v jiných je podrobnosti implementace, které nechcete zabývat. Pokud je to tento případ, můžete zvážit použití **serializátor** knihovny, která je popsaná v další části.

## <a name="use-the-serializer-library"></a>Použít knihovnu serializátoru

Koncepčně **serializátor** knihovny se nachází nahoře **pro IoTHubClient** knihovny v sadě SDK. Používá **pro IoTHubClient** knihovny pro základní komunikaci se službou IoT Hub, ale přidá možnosti modelování odebrat si museli dělat starosti se zabývá serializace zprávy od vývojáře. Jak funguje tato knihovna je nejlepší prokázat příklad.

Uvnitř **serializátor** složky [úložiště azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c), je **ukázky** složky, která obsahuje aplikaci s názvem **simplesample\_mqtt**. Verze Windows této ukázky obsahuje následující řešení sady Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_mqtt.PNG)

> [!NOTE]
> Pokud tento projekt otevřít v sadě Visual Studio 2017, potvrďte jej překonfigurovat projekt na nejnovější verzi.

Stejně jako u předchozí ukázce tohohle zahrnuje několik balíčků NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Většina těchto balíčků v předchozí ukázce jste viděli, ale **Microsoft.Azure.IoTHub.Serializer** novinky. Tento balíček je povinný při použití **serializátor** knihovny.

Provádění ukázkovou aplikaci v můžete najít **simplesample\_mqtt.c** souboru.

Následující části vás provede procesem klíčových částí této ukázky.

### <a name="initialize-the-library"></a>Inicializovat knihovnu

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

Volání **serializátor\_init** je jednorázové volání funkce a inicializuje základní knihovny. Potom, voláte **pro IoTHubClient\_LL\_CreateFromConnectionString** funkce, která je stejného rozhraní API jako v **pro IoTHubClient** vzorku. Toto volání nastaví připojovací řetězec zařízení (Toto volání je také, kde můžete vybrat protokol chcete použít). Tato ukázka používá jako přenosového protokolu MQTT, ale použít připojení přes AMQP nebo HTTPS.

Nakonec proveďte volání **vytvořit\_modelu\_INSTANCE** funkce. **WeatherStation** je obor názvů modelu a **ContosoAnemometer** je název modelu. Jakmile je vytvořena instance modelu, můžete spustit odesílání a příjem zpráv. Ale je důležité pochopit, jaký je model.

### <a name="define-the-model"></a>Definovat model

V modelu **serializátor** knihovna definuje zprávy, které vaše zařízení může odesílat do služby IoT Hub a zprávy, označované jako *akce* v Modelovací jazyk, který může přijímat. Definovat model pomocí sady maker v jazyce C jako v **simplesample\_mqtt** ukázkovou aplikaci:

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

**Začít\_obor názvů** a **END\_obor názvů** makra obou trvat obor názvů tohoto modelu jako argument. Očekává se, že něco mezi tato makra je definice modelu nebo modely a datové struktury, které používají modely.

V tomto příkladu je volána jednoho modelu **ContosoAnemometer**. Tento model definuje dva druhy dat, která vaše zařízení může odesílat do služby IoT Hub: **DeviceId** a **rychlost větru**. Také definuje tři akce (zprávy), které vaše zařízení může získat: **TurnFanOn**, **TurnFanOff**, a **SetAirResistance**. Každý datový element má typ, a každou akci má název (a volitelně sadu parametrů).

Data a akce definované v modelu definovat povrch rozhraní API, můžete použít k odesílání zpráv do služby IoT Hub a reagovat na zprávy odeslané do zařízení. Použití tohoto modelu nejlépe odhalíte obsahuje příklad.

### <a name="send-messages"></a>Odesílání zpráv

Model definuje data odeslaná do služby IoT Hub. V tomto příkladu to znamená jeden dva datové položky definované pomocí **WITH_DATA** – makro. Existuje několik kroků, které jsou potřebné k odeslání **DeviceId** a **rychlost větru** hodnoty do služby IoT hub. První je nastavení data, která se mají posílat:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Model, který jste definovali dříve vám umožní nastavit hodnoty tak, že nastavíte členy **struktura**. V dalším kroku serializovat zprávu, kterou se mají posílat:

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

Tento kód serializuje typu zařízení cloud do vyrovnávací paměti (odkazovaná **cílové**). Kód poté vyvolá **sendMessage** funkce k odeslání zprávy do služby IoT Hub:

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


Druhou pro poslední parametr **pro IoTHubClient\_LL\_SendEventAsync** je odkaz na funkci zpětného volání, která se volá, když data úspěšně odesílají. Tady je funkce zpětného volání v ukázce:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Druhý parametr není ukazatel na uživatelský kontext; stejný ukazatel předaný **pro IoTHubClient\_LL\_SendEventAsync**. V takovém případě kontext je jednoduchý čítač, ale může být cokoliv, co chcete.

To je vše je k odesílání zpráv typu zařízení cloud. Zbývá jen k pokrytí je jak přijmout zprávy.

### <a name="receive-messages"></a>Příjem zpráv

Příjem zpráv funguje podobně jako zprávy způsob, jak pracovat **pro IoTHubClient** knihovny. Nejprve zaregistrujte funkce zpětného volání zpráv:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Potom napíšete funkce zpětného volání, která se vyvolá při doručení zprávy do:

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

Tento kód je často používaný text – je to stejné pro každé řešení. Tato funkce přijme zprávu a se postará o směrování pro příslušnou funkci prostřednictvím volání **EXECUTE\_příkaz**. Volaná funkce se v tuto chvíli závisí na definici akcí ve vašem modelu.

Když definujete akce v modelu, budete vyzváni k implementaci funkce, která je volána, když vaše zařízení pak získá odpovídající zprávu. Pokud například váš model definuje tato akce:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Definujte funkci s Tento podpis:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Všimněte si, jak název funkce odpovídá názvu akce v modelu a, parametry funkce odpovídají zadané parametry akce. První parametr je vždy vyžadován a obsahuje ukazatel na instance modelu.

Když zařízení obdrží zprávu, která odpovídající tomuto podpisu, se nazývá odpovídající funkce. Proto se kromě nutnosti zahrnout často používaný kód z **IoTHubMessage**, přijímání zpráv stačí jenom definice jednoduchou funkci pro každou akci v modelu definován.

### <a name="uninitialize-the-library"></a>Uninitialize knihovny

Jakmile budete hotovi, odesílání a přijímání zpráv, můžete uninitialize knihovny IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Každá z těchto tří funkcí v souladu s tři funkce inicializace, je popsáno výše. Volající tato rozhraní API zajišťuje uvolnění dříve přidělené prostředky.

## <a name="next-steps"></a>Další kroky

Tento článek popisuje základní informace o používání knihovny v **zařízení Azure IoT SDK pro jazyk C**. To vám poskytuje dostatek informací k pochopení, co je součástí sady SDK, jeho architektuře a jak začít pracovat s ukázkami Windows. Další článek pokračuje popis sady SDK a popsat [Další informace o knihovně pro IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Další informace o vývoji pro službu IoT Hub, najdete v článku [sad SDK Azure IoT][lnk-sdks].

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
