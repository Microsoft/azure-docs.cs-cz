---
title: Sada SDK pro zařízení Azure IoT pro C-serializátor | Microsoft Docs
description: Jak používat knihovnu serializátorů v sadě SDK pro zařízení Azure IoT pro C k vytváření aplikací pro zařízení, které komunikují se službou IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f52d1d1c5f264550076688d5e25e110de230eff4
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152229"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Sada SDK pro zařízení Azure IoT pro C – Další informace o serializátoru

První článek v této sérii představil [Úvod do sady SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). Další článek poskytuje podrobnější popis sady [SDK pro zařízení Azure IoT pro jazyk C--IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Tento článek dokončí pokrytí sady SDK poskytnutím podrobného popisu zbývající komponenty: knihovna **serializátorů** .

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Úvodní článek popisuje, jak použít knihovnu **serializátorů** k posílání událostí do a příjemi zpráv z IoT Hub. V tomto článku tuto diskuzi rozšiřujeme tak, že poskytujeme ucelenější vysvětlení způsobu modelování dat pomocí jazyka maker **serializátoru** . Článek také obsahuje další podrobnosti o tom, jak knihovna serializovat zprávy (a v některých případech, jak lze řídit chování serializace). Popíšeme také některé parametry, které můžete upravit, které určují velikost modelů, které vytvoříte.

Nakonec článek Přečtěte některá témata, která jsou popsaná v předchozích článcích, jako je zpracování zpráv a vlastností. Jak zjistíme, tyto funkce fungují stejným způsobem jako pomocí knihovny **serializátorů** , stejně jako s knihovnou **IoTHubClient** .

Vše popsané v tomto článku je založené na vzorcích sady SDK **serializátoru** . Pokud chcete postup sledovat, přečtěte si téma aplikace **SimpleSample \_ AMQP** a **SimpleSample \_ http** , které jsou součástí sady SDK pro zařízení Azure IoT pro jazyk C.

Můžete najít [**sadu SDK pro zařízení Azure IoT pro**](https://github.com/Azure/azure-iot-sdk-c) úložiště GitHub c a zobrazit podrobnosti o rozhraní API v referenčních informacích k [rozhraní API jazyka c](/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>Modelovací jazyk

Článek [sada SDK pro zařízení Azure IoT pro jazyk c](iot-hub-device-sdk-c-intro.md) v této sérii představila **sadu SDK pro zařízení Azure IoT pro jazyk c** Modeling v příkladu, který je k dispozici v aplikaci **SimpleSample \_ AMQP** :

```C
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Jak vidíte, modelovací jazyk je založen na makrech jazyka C. Vždy zahájíte definici pomocí ** \_ oboru názvů Begin** a vždy ukončete s **koncovým \_ oborem názvů**. Je běžné pojmenovat obor názvů vaší společnosti nebo, jako v tomto příkladu, projekt, na kterém pracujete.

Co se nachází uvnitř oboru názvů, jsou definice modelu. V tomto případě existuje jeden model pro anemometer. Model se pak může pojmenovat cokoli, ale obvykle je tento model pojmenován pro zařízení nebo typ dat, která chcete vyměňovat pomocí IoT Hub.  

Modely obsahují definici událostí, se kterými se můžete IoT Hub ( *data*) a také zprávy, které můžete přijímat z IoT Hub ( *Akce*). Jak vidíte v příkladu, události mají typ a název; akce mají název a volitelné parametry (každý s typem).

To, co se v této ukázce neukazuje, jsou další datové typy, které sada SDK podporuje. Budeme se zabývat dál.

> [!NOTE]
> IoT Hub odkazuje na data, která zařízení pošle jako *události*, zatímco Modelovací jazyk odkazuje na *data* (definované pomocí **WITH_DATA**). Podobně IoT Hub odkazuje na data, která odesíláte do zařízení jako *zprávy*, zatímco Modelovací jazyk odkazuje na něj jako *Akce* (definované pomocí **WITH_ACTION**). Počítejte s tím, že tyto výrazy mohou být v tomto článku zaměnitelné.
> 
> 

## <a name="supported-data-types"></a>Podporované datové typy

V modelech vytvořených pomocí knihovny **serializátoru** jsou podporovány následující datové typy:

| Typ | Popis |
| --- | --- |
| double |číslo s plovoucí desetinnou čárkou typu Double Precision |
| int |32-bitové celé číslo |
| float |číslo s plovoucí desetinnou čárkou s jednoduchou přesností |
| long |dlouhé celé číslo |
| int8 \_ t |8bitové celé číslo |
| Int16 \_ t |16bitové celé číslo |
| Int32 \_ t |32-bitové celé číslo |
| Int64 \_ t |64bitové celé číslo |
| bool |boolean |
| \_znak \_ PTR pro ASCII |Řetězec ASCII |
| \_posun data a \_ času \_ EDM |posun data a času |
| \_identifikátor GUID modelu EDM |Identifikátor GUID |
| \_binární soubor EDM |binární |
| DEKLAROVAT \_ strukturu |komplexní datový typ |

Pojďme začít s posledním datovým typem. **Deklarace \_ struct** umožňuje definovat komplexní datové typy, které jsou seskupení ostatních primitivních typů. Tato seskupení nám umožňují definovat model, který vypadá takto:

```C
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Náš model obsahuje jedinou událost dat typu **TestType**. **TestType** je komplexní typ, který obsahuje několik členů, které souhrnně znázorňují primitivní typy podporované modelovacím jazykem **serializátoru** .

S tímto modelem můžeme napsat kód pro posílání dat IoT Hub, který se zobrazí takto:

```C
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

V podstatě přiřazujeme každému členovi struktury **testu** hodnotu a potom zavoláme **SendAsync** , aby se odeslala událost **testovacích** dat do cloudu. **SendAsync** je pomocná funkce, která pošle jedinou datovou událost k IoT Hub:

```C
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Tato funkce serializovat danou událost dat a pošle ji do IoT Hub pomocí ** \_ SendEventAsyncu IoTHubClient**. To je stejný kód, který je popsán v předchozích článcích (**SendAsync** zapouzdřuje logiku do pohodlné funkce).

Jedna další pomocná funkce použitá v předchozím kódu je **getdatetimeoffset**. Tato funkce transformuje daný čas na hodnotu typu ** \_ Datum a \_ čas \_ posunu datového času EDM**:

```C
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Pokud spustíte tento kód, pošle se IoT Hub následující zpráva:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Všimněte si, že serializace je ve formátu JSON, což je formát generovaný knihovnou **serializátoru** . Všimněte si také, že každý člen serializovaného objektu JSON odpovídá členům **TestType** , které jsme definovali v našem modelu. Hodnoty se také přesně shodují s hodnotami použitými v kódu. Všimněte si ale, že binární data jsou zakódovaná ve formátu base64: "AQID" je kódování Base64 {0x01, 0x02, 0x03}.

Tento příklad ukazuje výhodu použití knihovny **serializátoru** – umožňuje nám odeslat JSON do cloudu bez nutnosti explicitně zabývat serializace v naší aplikaci. Všechno, co máme starosti, je nastavení hodnot událostí dat v našem modelu a pak volání jednoduchých rozhraní API k odeslání těchto událostí do cloudu.

S těmito informacemi můžeme definovat modely, které zahrnují rozsah podporovaných datových typů, včetně složitých typů (do jiných komplexních typů můžeme dokonce zahrnout komplexní typy). Serializovaný kód JSON generovaný výše uvedeným příkladem však přináší důležitý bod. *Způsob, jakým* pošleme data pomocí knihovny **serializátorů** , přesně určuje, jak se formát JSON vytvoří. V tomto konkrétním bodě se budeme zabývat dalšími.

## <a name="more-about-serialization"></a>Další informace o serializaci

Předchozí oddíl zvýrazní příklad výstupu vygenerovaného knihovnou **serializátoru** . V této části vyvysvětlíme, jak knihovna serializovat data a jak můžete řídit toto chování pomocí rozhraní API serializace.

Aby bylo možné pokračovat v diskusi k serializaci, budeme pracovat s novým modelem založeným na termostatu. Nejdřív Pojďme na scénář, který se snažíme adresovat, začít.

Chceme modelovat termostat, který měří teplotu a vlhkost. Každá část dat bude odesílána do IoT Hub odlišně. Ve výchozím nastavení termostat nařizuje událost teploty každé 2 minuty. událost vlhkosti se zaznamená každých 15 minut. Pokud dojde k příchozímu přenosu události, musí obsahovat časové razítko, které zobrazuje čas, kdy byla odpovídající teplota nebo vlhkost změřena.

V tomto scénáři vám ukážeme dva různé způsoby, jak modelovat data a my vysvětlujeme, jaký vliv má modelování na serializovaný výstup.

### <a name="model-1"></a>Model 1

Tady je první verze modelu, který podporuje předchozí scénář:

```C
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Všimněte si, že model obsahuje dvě události dat: **teplotu** a **vlhkost**. Na rozdíl od předchozích příkladů je typ každé události struktura definovaná pomocí příkazu **Declare \_ struct**. **TemperatureEvent** zahrnuje měření teploty a časové razítko; **HumidityEvent** obsahuje měření vlhkosti a časové razítko. Tento model nám dává přirozený způsob modelování dat pro výše popsaný scénář. Když pošleme událost do cloudu, pošleme buď jednu teplotu, časové razítko, nebo dvojici vlhkosti nebo časového razítka.

Do cloudu můžeme odeslat událost teploty pomocí kódu, jako je následující:

```C
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Budeme používat pevně zakódované hodnoty pro teplotu a vlhkost v ukázkovém kódu, ale Představme si, že ve skutečnosti načítáme tyto hodnoty pomocí vzorkování odpovídajících senzorů na termostatu.

Výše uvedený kód používá pomocníka **getdatetimeoffset** , který se dřív představil. Z důvodů, které se stanou jasnými později, tento kód explicitně odděluje úkol serializace a odeslání události. Předchozí kód serializaci událost teploty do vyrovnávací paměti. **Deklarace SendMessage** je pak pomocná funkce (obsažená v **SimpleSample \_ AMQP**), která odesílá událost do IoT Hub:

```C
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Tento kód je podmnožinou pomocné rutiny **SendAsync** popsané v předchozí části, takže se na něj nevrátíme znovu.

Když spustíte předchozí kód k odeslání události teploty, bude tato serializovaná forma události odeslána do IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Posíláme teplotu, která je typu **TemperatureEvent**a tato struktura obsahuje člen **teploty** a **času** . To se přímo odrazí v serializovaných datech.

Podobně můžeme odeslat událost vlhkosti s tímto kódem:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Serializovaná forma, která se pošle IoT Hub, se zobrazí takto:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Znovu, toto je očekáváno.

S tímto modelem si můžete představit, jak lze snadno přidat další události. Můžete definovat více struktur pomocí příkazu **Declare \_ struct**a zahrnout odpovídající událost do modelu pomocí **s \_ daty**.

Teď model upravíte tak, aby obsahoval stejná data, ale s jinou strukturou.

### <a name="model-2"></a>Model 2

Vezměte v úvahu tento alternativní model na předchozí:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

V tomto případě jsme vyloučili makra **Declare \_ struct** a jednoduše definujete položky dat z našeho scénáře pomocí jednoduchých typů z modelovacího jazyka.

Hned za chvíli ignorujte událost **čas** . V takovém případě zde je kód pro vstupní **teplotu**:

```C
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Tento kód pošle následující serializovanou událost do IoT Hub:

```C
{"Temperature":75}
```

A kód pro odeslání události vlhkosti se zobrazí takto:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Tento kód pošle IoT Hub:

```C
{"Humidity":45}
```

Dosud nejsou žádné překvapením. Teď se podíváme, jak používám makro SERIALIZACe.

Makro **serializace** může jako argumenty přijímat více datových událostí. Díky tomu můžeme společně serializovat **teplotu** a událost **vlhkosti** a poslat je IoT Hub při jednom volání:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Můžete odhadnout, že výsledkem tohoto kódu je, že se do IoT Hub odesílají dvě události dat:

[{"Teplota": 75}, {"vlhkost": 45}]

Jinými slovy, můžete očekávat, že tento kód je stejný jako při posílání **teploty** a **vlhkosti** zvlášť. Je to jen pohodlí k předání obou událostí do **serializace** ve stejném volání. Nejedná se však o případ. Místo toho kód výše pošle tuto jedinou událost dat do IoT Hub:

{"Teplota": 75, "vlhkost": 45}

To se může zdát neobvyklé, protože náš model definuje **teplotu** a **vlhkost** jako dvě *samostatné* události:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Do této chvíle jsme nevytvořili model těchto událostí, kde je **teplota** a **vlhkost** ve stejné struktuře:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Pokud jsme tento model používali, bylo by snazší pochopit, jak se ve stejné serializované zprávě budou odesílat **teploty** a **vlhkosti** . Nemusí ale být jasné, proč to funguje, když předáte obě události dat k **serializaci** pomocí modelu 2.

Toto chování je snazší pochopit, pokud znáte předpoklady, které vytváří knihovna **serializátorů** . Abychom to mohli udělat, vraťte se zpátky na náš model:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Tento model si můžete představit v objektově orientovaném smyslu. V tomto případě provádíme modelování fyzického zařízení (termostat) a toto zařízení obsahuje atributy, jako je **teplota** a **vlhkost**.

Celý stav našeho modelu můžeme odeslat pomocí kódu, jako je například následující:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Za předpokladu, že se nastaví hodnoty teploty, vlhkosti a času, uvidíme událost, jako kterou jste odeslali IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Někdy můžete chtít do cloudu poslat jenom *některé* vlastnosti modelu (to platí hlavně v případě, že váš model obsahuje velký počet datových událostí). Je užitečné odeslat jenom podmnožinu datových událostí, jako je například v našem předchozím příkladu:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Tím se vygeneruje přesně jedna serializovaná událost, jako kdyby jsme definovali **TemperatureEvent** s členem **teploty** a **času** , stejně jako u modelu 1. V tomto případě jsme dokázali vygenerovat přesně jednu serializovanou událost pomocí jiného modelu (model 2), protože se nazývá **serializace** jiným způsobem.

Důležité je, že pokud předáte více datových událostí k **serializaci,** předpokládá se, že každá událost je vlastnost v jednom objektu JSON.

Nejlepší přístup závisí na vás a na tom, jak si svůj model myslíte. Pokud odesíláte "události" do cloudu a každá událost obsahuje definovanou sadu vlastností, pak první přístup přináší spoustu smyslů. V takovém případě použijte **deklaraci \_ struct** k definování struktury každé události a poté ji zahrňte do svého modelu pomocí makra **s \_ daty** . Pak každou událost odešlete jako v prvním příkladu výše. V tomto přístupu byste k **serializátoru**předávali jenom jednu událost dat.

Pokud si myslíte o modelu v objektově orientovaném způsobem, druhý přístup vám může vyhovovat. V tomto případě prvky definované pomocí **s \_ daty** jsou vlastnosti objektu. Dodáte jakoukoli podmnožinu událostí k **serializaci** , kterou chcete, v závislosti na tom, kolik stavu objektu chcete odeslat do cloudu.

Přístup k Nether je správný nebo špatný. Stačí vědět, jak funguje knihovna **serializátorů** , a vyberte přístup k modelování, který nejlépe vyhovuje vašim potřebám.

## <a name="message-handling"></a>Zpracování zpráv

V tomto článku se zatím zabývá pouze odesíláním událostí do IoT Hub a nevyřešila se příjem zpráv. Důvodem je, že to, co potřebujeme znát o přijímání zpráv, se do značné míry týkalo v článku [sada SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). Odvolání z tohoto článku, který zpracovává zprávy pomocí registrace funkce zpětného volání zprávy:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Pak napíšete funkci zpětného volání, která se vyvolá při přijetí zprávy:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

Tato implementace **IoTHubMessage** volá konkrétní funkci pro každou akci v modelu. Například pokud váš model definuje tuto akci:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Je nutné definovat funkci s tímto podpisem:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** se pak zavolá, když se tato zpráva pošle na vaše zařízení.

Co jsme ještě nevysvětlili, to znamená, že serializovaná verze zprávy vypadá jako. Jinými slovy, pokud chcete do zařízení poslat zprávu **SetAirResistance** , co to vypadá?

Pokud posíláte zprávu do zařízení, provedete to přes sadu SDK služby Azure IoT. Ještě potřebujete znát, který řetězec se má poslat, aby se vyvolala konkrétní akce. Obecný formát pro odeslání zprávy se zobrazí takto:

```C
{"Name" : "", "Parameters" : "" }
```

Posíláte serializovaný objekt JSON se dvěma vlastnostmi: **název** je název akce (zpráva) a **parametry** , které obsahují parametry této akce.

Pokud například chcete vyvolat **SetAirResistance** , můžete poslat tuto zprávu do zařízení:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Název akce musí přesně odpovídat akci definované v modelu. Názvy parametrů se musí shodovat také. Všimněte si také citlivosti na velká a malá písmena. **Název** a **parametry** jsou vždycky velká písmena. Ujistěte se, že odpovídáte názvu a parametrům vaší akce v modelu. V tomto příkladu je název akce "SetAirResistance" a nikoli "SetAirResistance".

Dvě další akce **TurnFanOn** a **TurnFanOff** lze vyvolat odesláním těchto zpráv do zařízení:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Tato část popisuje všechno, co potřebujete znát při odesílání událostí a přijímání zpráv pomocí knihovny **serializátorů** . Než začnete pokračovat, podíváme se na některé parametry, které můžete nakonfigurovat tak, jak velký je váš model.

## <a name="macro-configuration"></a>Konfigurace maker

Pokud používáte knihovnu **serializátorů** , je důležitou součástí sady SDK, kterou je třeba znát, najdete v knihovně Azure-c-Shared-Utility.

Pokud jste naklonoval úložiště Azure-IoT-SDK-c z GitHubu a vystavili `git submodule update --init` příkaz, najdete tuto knihovnu sdílených nástrojů tady:

```C
.\\c-utility
```

Pokud jste knihovnu neklonovat, najdete ji [tady](https://github.com/Azure/azure-c-shared-utility).

V knihovně sdílených nástrojů se nachází následující složka:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Tato složka obsahuje řešení sady Visual Studio s názvem nástroje pro **makra \_ \_ h \_ generátor. sln**:

  ![Snímek obrazovky maco_utils_h_generator řešení sady Visual Studio](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Program v tomto řešení vygeneruje soubor ** \_ . h sady maker** . \_Sada SDK obsahuje výchozí soubor util. h. Toto řešení umožňuje upravit některé parametry a pak znovu vytvořit hlavičkový soubor založený na těchto parametrech.

Mezi dva klíčové parametry, které se mají zabývat, patří **nArithmetic** a **nMacroParameters**, které jsou definované na těchto dvou řádcích, které se našly v \_ utils.TTU makra:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Tyto hodnoty jsou výchozí parametry, které jsou součástí sady SDK. Každý parametr má následující význam:

* nMacroParameters – určuje, kolik parametrů můžete mít v jednom DEKLARaci \_ definice makra modelu.
* nArithmetic – řídí celkový počet členů povolených v modelu.

Důvodem je, že tyto parametry jsou důležité, protože určují, jak velký má váš model. Zvažte například tuto definici modelu:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Jak bylo zmíněno dříve, **deklarujete \_ model** pouze jako makro jazyka C. Názvy modelu a příkazu **with \_ data** (ještě jiné makro) jsou parametry **deklarovaného \_ modelu**. **nMacroParameters** definuje počet parametrů, které mohou být zahrnuty v **deklaraci \_ modelu**. To umožňuje definovat, kolik datových událostí a deklarací akcí můžete mít. V takovém případě s výchozím limitem 124 to znamená, že můžete definovat model s kombinací informací o akcích 60 a událostech dat. Pokud se pokusíte překročit tento limit, obdržíte chyby kompilátoru, které vypadají podobně jako toto:

  ![Snímek obrazovky s chybami kompilátoru parametrů makra](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

Parametr **nArithmetic** je více o interních pracích jazyka makra, než je vaše aplikace.  Řídí celkový počet členů, které můžete mít v modelu, včetně **DECLARE_STRUCT** maker. Pokud začnete zobrazovat chyby kompilátoru, například to, měli byste zkusit zvýšit **nArithmetic**:

   ![Snímek obrazovky s chybami aritmetického kompilátoru](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Chcete-li změnit tyto parametry, upravte hodnoty v \_ souboru utils.TT makra, znovu zkompilujte sady maker rutiny \_ \_ h \_ generátor. sln a spusťte kompilovaný program. Když to uděláte, vygeneruje se nový \_ soubor. h sady maker, který je umístěný v. \\ běžný \\ adresář Inc.

Aby bylo možné používat novou verzi sady maker \_ . h, odeberte balíček NuGet **serializátoru** z vašeho řešení a na jeho místě zahrnuje **serializátor** projektu sady Visual Studio. To umožňuje kódu kompilovat proti zdrojovému kódu knihovny serializátoru. To zahrnuje aktualizované sady maker \_ . h. Pokud to chcete provést pro **SimpleSample \_ AMQP**, začněte odebráním balíčku NuGet pro knihovnu serializátoru z řešení:

   ![Snímek obrazovky odebrání balíčku NuGet pro knihovnu serializátoru](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Pak tento projekt přidejte do řešení sady Visual Studio:

> .\\ serializátor jazyka c \\ \\ Build \\ Windows \\ serializátor. vcxproj
> 
> 

Až budete hotovi, vaše řešení by mělo vypadat takto:

   ![Snímek obrazovky simplesample_amqp řešení sady Visual Studio](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Nyní když zkompilujete své řešení, aktualizované sady maker \_ . h jsou zahrnuty do binárního souboru.

Všimněte si, že zvětšení těchto hodnot je dostatečně vysoké, může přesáhnout omezení kompilátoru. V tomto okamžiku je **nMacroParameters** hlavním parametrem, se kterým se má zabývat. Specifikace C99 určuje, že v definici makra je povoleno minimálně 127 parametrů. Kompilátor společnosti Microsoft se řídí přesně podle specifikace (a má limit 127), takže nebudete moci zvýšit **nMacroParameters** nad rámec výchozí hodnoty. Další kompilátory vám mohou umožnit (například kompilátor GNU podporuje vyšší limit).

Zatím jsme pokryli všechno, co potřebujete, abyste měli informace o tom, jak psát kód pomocí knihovny **serializátorů** . Než se pustíte do uzavírání, podíváme se na některá témata z předchozích článků, o kterých můžete zajímat.

## <a name="the-lower-level-apis"></a>Rozhraní API na nižší úrovni
Ukázková aplikace, na které se tento článek zaměřuje, je **SimpleSample \_ AMQP**. Tato ukázka používá rozhraní API vyšší úrovně (**nesouvisející s) k**odesílání událostí a přijímání zpráv. Pokud používáte tato rozhraní API, spouští se vlákno na pozadí, které postará o posílání událostí i přijímání zpráv. Rozhraní API nižší úrovně ale můžete použít k odstranění tohoto vlákna na pozadí a při posílání událostí nebo přijímání zpráv z cloudu přebírat explicitní kontrolu.

Jak je popsáno v [předchozím článku](iot-hub-device-sdk-c-iothubclient.md), existuje sada funkcí, které se skládají z rozhraní API vyšší úrovně:

* IoTHubClient \_ CreateFromConnectionString
* IoTHubClient \_ SendEventAsync
* IoTHubClient \_ SetMessageCallback
* IoTHubClient \_ zničit

Tato rozhraní API jsou znázorněná v **SimpleSample \_ AMQP**.

K dispozici je také podobná sada rozhraní API nižší úrovně.

* IoTHubClient \_ vše \_ CreateFromConnectionString
* IoTHubClient \_ vše \_ SendEventAsync
* IoTHubClient \_ vše \_ SetMessageCallback
* IoTHubClientt \_ vše \_ zničit

Všimněte si, že rozhraní API nižší úrovně fungují přesně stejným způsobem, jak je popsáno v předchozích článcích. První sadu rozhraní API můžete použít, pokud chcete, aby vlákno na pozadí zpracovával odesílající události a přijímalo zprávy. Druhou sadu rozhraní API použijete, pokud chcete při posílání a přijímání dat z IoT Hub explicitní kontrolu. Buď sada rozhraní API funguje stejně dobře s knihovnou **serializátoru** .

Příklad toho, jak se používají rozhraní API nižší úrovně s knihovnou **serializátoru** , najdete v tématu aplikace **SimpleSample \_ http** .

## <a name="additional-topics"></a>Další témata
Několik dalších témat, která se nahrazují, jsou zpracování vlastností, použití alternativních přihlašovacích údajů zařízení a možností konfigurace. Toto jsou všechna témata zahrnutá v [předchozím článku](iot-hub-device-sdk-c-iothubclient.md). Hlavním bodem je, že všechny tyto funkce fungují stejným způsobem s knihovnou **serializátoru** stejně jako s knihovnou **IoTHubClient** . Například pokud chcete připojit vlastnosti k události z modelu, použijte ** \_ vlastnosti IoTHubMessage** a **map** \_ **AddorUpdate**stejným způsobem, jak je popsáno dříve:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Bez ohledu na to, zda byla událost vygenerována z knihovny **serializátorů** nebo vytvořená ručně pomocí knihovny **IoTHubClient** .

V případě alternativních přihlašovacích údajů ke službě se pomocí **IoTHubClientu \_ \_ Create** funguje stejně jako **IoTHubClient \_ CreateFromConnectionString** pro přidělování ** \_ klientských \_ popisovačů IOTHUB**.

Nakonec, pokud používáte knihovnu **serializátorů** , můžete nastavit možnosti konfigurace pomocí IoTHubClient s metodou ** \_ \_ SetOption** stejným způsobem jako při použití knihovny **IoTHubClient** .

Funkce, která je jedinečná pro knihovnu **serializátorů** , jsou inicializační rozhraní API. Předtím, než můžete začít pracovat s knihovnou, je nutné **volat \_ inicializaci serializátoru**:

```C
serializer_init(NULL);
```

To se provádí těsně před voláním **IoTHubClient \_ CreateFromConnectionString**.

Podobně když jste hotovi s knihovnou, poslední volání, které uděláte, je ** \_ deinicializace serializátoru**:

```C
serializer_deinit();
```

V opačném případě všechny ostatní funkce uvedené výše fungují stejně v knihovně **serializátoru** stejně jako v knihovně **IoTHubClient** . Další informace o některém z těchto témat najdete v [předchozím článku](iot-hub-device-sdk-c-iothubclient.md) v této sérii.

## <a name="next-steps"></a>Další kroky

Tento článek podrobně popisuje jedinečné aspekty knihovny **serializátorů** obsažené v **sadě SDK pro zařízení Azure IoT pro jazyk C**. S poskytnutými informacemi byste měli mít dobrou představu o použití modelů k posílání událostí a přijímání zpráv z IoT Hub.

Tím se také uzavřou série tří částí, jak vyvíjet aplikace pomocí **sady SDK pro zařízení Azure IoT pro jazyk C**. Mělo by se jednat o dostatek informací, abyste mohli nejen začít, ale měli byste důkladně pochopit, jak rozhraní API fungují. Další informace najdete v tématu o několika ukázkách v sadě SDK, které zde nejsou popsané. V opačném případě je [dokumentace k sadě Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-c) dobrým prostředkem pro další informace.

Další informace o vývoji pro IoT Hub najdete v tématu sady [SDK služby Azure IoT](iot-hub-devguide-sdks.md).

Další zkoumání možností IoT Hub najdete v tématu [nasazení AI do hraničních zařízení pomocí Azure IoT Edge](../iot-edge/quickstart-linux.md).