---
title: Sada Azure IoT device SDK for C – serializer | Dokumenty společnosti Microsoft
description: Jak používat knihovnu Serializer v azure iot zařízení SDK pro C k vytvoření aplikací zařízení, které komunikují s centrem IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: d4916d651638f0d1dbb4f10e0e0732f5c330d300
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767025"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT zařízení SDK pro C – další informace o serializátoru

První článek v této řadě představil [úvod do sady Azure IoT zařízení SDK pro C](iot-hub-device-sdk-c-intro.md). Další článek poskytl podrobnější popis [sady Azure IoT device SDK pro C -- IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Tento článek dokončí pokrytí sady SDK poskytnutím podrobnější popis zbývající součásti: **serializátor** knihovny.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Úvodní článek popisuje, jak používat knihovnu **serializátorů** k odesílání událostí do a přijímání zpráv z ioT Hubu. V tomto článku rozšiřujeme tuto diskusi tím, že poskytuje úplnější vysvětlení, jak modelovat data s jazykem maker **serializátoru.** Článek také obsahuje další podrobnosti o tom, jak knihovna serializuje zprávy (a v některých případech, jak můžete řídit chování serializace). Popíšeme také některé parametry, které můžete upravit a které určují velikost modelů, které vytvoříte.

Nakonec článek znovu navštíví některá témata, na která se vztahuje v předchozích článcích, jako je například zpracování zpráv a vlastností. Jak zjistíme, tyto funkce fungují stejným způsobem pomocí **knihovny serializátoru** jako s knihovnou **IoTHubClient.**

Vše popsané v tomto článku je založena na **serializátoru** SDK vzorky. Pokud chcete sledovat spolu, najdete **simplesample\_amqp** a **simplesample\_http** aplikace zahrnuté v Azure IoT zařízení SDK pro C.

Můžete najít [**Azure IoT zařízení SDK pro C**](https://github.com/Azure/azure-iot-sdk-c) úložiště GitHub a zobrazit podrobnosti o rozhraní API v [odkazu rozhraní API C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>Modelovací jazyk

Azure [IoT zařízení SDK pro C](iot-hub-device-sdk-c-intro.md) článku v této řadě představil zařízení Azure **IoT SDK pro** jazyk modelování C prostřednictvím příkladu uvedeného v **simplesample\_amqp** aplikace:

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

Jak můžete vidět, jazyk modelování je založen na makrech C. Vždy začínáte definici **\_begin namespace** a vždy **končíte\_end namespace**. Je běžné pojmenovat obor názvů pro vaši společnost nebo, jako v tomto příkladu, projekt, na kterém pracujete.

Co jde uvnitř oboru názvů jsou definice modelu. V tomto případě existuje jediný model pro anemometr. Model může být opět pojmenován cokoli, ale obvykle je model pojmenován pro zařízení nebo typ dat, které chcete vyměňovat s ioT hubem.  

Modely obsahují definici událostí, které můžete příchozí do centra IoT Hub *(data)* a zprávy, které můžete přijímat z ioT hubu *(akce).* Jak můžete vidět z příkladu, události mají typ a název; akce mají název a volitelné parametry (každý s typem).

Co není prokázáno v této ukázce jsou další datové typy, které jsou podporovány sadou SDK. To si pokryjeme příště.

> [!NOTE]
> IoT Hub označuje data, která mu zařízení odesílá jako *události*, zatímco jazyk modelování je označuje jako *data* (definovaná pomocí **WITH_DATA**). Podobně IoT Hub označuje data, která odesíláte do zařízení jako *zprávy*, zatímco jazyk modelování odkazuje na to jako *akce* (definované pomocí **WITH_ACTION**). Uvědomte si, že tyto termíny mohou být použity zaměnitelně v tomto článku.
> 
> 

## <a name="supported-data-types"></a>Podporované datové typy

V modelech vytvořených pomocí knihovny **serializátorů** jsou podporovány následující datové typy:

| Typ | Popis |
| --- | --- |
| double |dvojité přesné číslo s plovoucí desetinnou táličnou |
| int |32bitové celé číslo |
| float |jednopřesné číslo s plovoucí desetinnou tísní |
| long |dlouhé celé číslo |
| int8\_t |Osmibitové celé číslo |
| int16\_t |16bitové celé číslo |
| int32\_t |32bitové celé číslo |
| int64\_t |64bitové celé číslo |
| bool |Boolean |
| ascii\_\_char ptr |Řetězec ASCII |
| POSUN\_ČASU\_\_EDM |posun data času |
| Identifikátor\_GUID EDM |GUID |
| EDM\_BINÁRNÍ |binární |
| DEKLAROVAT\_STRUKTURU |komplexní datový typ |

Začněme s posledním datovým typem. **\_DECLARE STRUCT** umožňuje definovat složité datové typy, které jsou seskupení jiných primitivních typů. Tato seskupení nám umožňují definovat model, který vypadá takto:

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

Náš model obsahuje jednu datovou událost typu **TestType**. **TestType** je komplexní typ, který zahrnuje několik členů, které společně ukazují primitivní typy podporované jazykem modelování **serializátoru.**

S modelem, jako je tento, můžeme napsat kód pro odesílání dat do ioT hubu, který se zobrazí takto:

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

V podstatě přiřazujeme hodnotu každému členovi **testovací** struktury a pak voláme **SendAsync** k odeslání testovací **datové** události do cloudu. **SendAsync** je pomocná funkce, která odesílá jednu datovou událost do služby IoT Hub:

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

Tato funkce serializuje danou datovou událost a odešle ji do služby IoT Hub pomocí **služby IoTHubClient\_SendEventAsync**. Jedná se o stejný kód popsaný v předchozích článcích **(SendAsync** zapouzdřuje logiku do pohodlné funkce).

Jednou další pomocnou funkcí použitou v předchozím kódu je **GetDateTimeOffset**. Tato funkce transformuje daný čas na hodnotu typu **POSUN DATA\_\_EDM\_:**

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

Pokud spustíte tento kód, zobrazí se do služby IoT Hub následující zpráva:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Všimněte si, že serializace je v JSON, což je formát generovaný knihovnou **serializátoru.** Všimněte si také, že každý člen serializované ho JSON objektodpovídá členy **TestType,** které jsme definovali v našem modelu. Hodnoty také přesně odpovídají těm, které jsou použity v kódu. Všimněte si však, že binární data jsou kódována base64: "AQID" je base64 kódování {0x01, 0x02, 0x03}.

Tento příklad ukazuje výhodu použití **serializační** knihovny – umožňuje nám odeslat JSON do cloudu, aniž by bylo nutné explicitně řešit serializace v naší aplikaci. Jediné, o co se musíme starat, je nastavení hodnot datových událostí v našem modelu a následné volání jednoduchých api pro odeslání těchto událostí do cloudu.

Pomocí těchto informací můžeme definovat modely, které zahrnují rozsah podporovaných datových typů, včetně komplexních typů (můžeme dokonce zahrnout komplexní typy v rámci jiných komplexních typů). Serializovaný JSON generovaný výše uvedeným příkladem však vyvolá důležitý bod. *Způsob* odesílání dat s knihovnou **serializátoru** přesně určuje, jak se vytvoří JSON. Tento konkrétní bod je to, co budeme pokrývat další.

## <a name="more-about-serialization"></a>Další informace o serializaci

Předchozí část zvýrazní příklad výstupu generovaného knihovnou **serializátoru.** V této části vám vysvětlíme, jak knihovna serializuje data a jak můžete řídit toto chování pomocí serializačních api.

Abychom pokročili v diskusi o serializaci, budeme pracovat s novým modelem založeným na termostatu. Nejprve poskytněme nějaké informace o scénáři, který se snažíme řešit.

Chceme modelovat termostat, který měří teplotu a vlhkost. Každá část dat bude odeslána do ioT hubu jinak. Ve výchozím nastavení termostat proniká teplotní událostí jednou za 2 minuty; událost vlhkosti je příchozí jednou za 15 minut. Pokud je buď událost příchozí, musí obsahovat časové razítko, které ukazuje čas, kdy byla naměřena odpovídající teplota nebo vlhkost.

Vzhledem k tomuto scénáři budeme demonstrovat dva různé způsoby modelování dat a vysvětlíme efekt, který má modelování na serializovaný výstup.

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

Všimněte si, že model obsahuje dvě datové události: **Teplota** a **vlhkost**. Na rozdíl od předchozích příkladů je typem každé události struktura definovaná pomocí **\_DECLARE STRUCT**. **TemperatureEvent** zahrnuje měření teploty a časové razítko; **HumidityEvent** obsahuje měření vlhkosti a časové razítko. Tento model nám poskytuje přirozený způsob, jak modelovat data pro scénář popsaný výše. Když pošleme událost do cloudu, pošleme buď teplotní/ časové razítko nebo dvojici vlhkosti a časového razítka.

Můžeme poslat teplotní událost do cloudu pomocí kódu, jako je následující:

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

Použijeme pevně zakódované hodnoty teploty a vlhkosti ve vzorovém kódu, ale představte si, že tyto hodnoty získáváme vzorkováním odpovídajících senzorů na termostatu.

Výše uvedený kód používá pomocnou stránku **GetDateTimeOffset,** která byla zavedena dříve. Z důvodů, které budou jasné později, tento kód explicitně odděluje úlohu serializace a odesílání události. Předchozí kód serializuje událost teploty do vyrovnávací paměti. Potom **sendMessage** je pomocná funkce (součástí **simplesample\_amqp)** který odešle událost do služby IoT Hub:

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

Tento kód je podmnožinou **pomocníka SendAsync** popsaného v předchozí části, takže si ho zde znovu neprojdeme.

Když spustíme předchozí kód pro odeslání události Teplota, tato serializovaná forma události je odeslána do ioT hubu:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Posíláme teplotu, která je typu **TemperatureEvent**, a tato struktura obsahuje temperature **a** **time** member. To se přímo odráží v serializovaných datech.

Podobně můžeme poslat událost vlhkosti s tímto kódem:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Serializovaný formulář odeslaný do ioT hubu se zobrazí takto:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Opět platí, že je to podle očekávání.

S tímto modelem si můžete představit, jak lze snadno přidat další události. Definujete více struktur pomocí **\_DECLARE STRUCT**a zahrnete odpovídající událost v modelu pomocí **funkce With\_DATA**.

Nyní upravíme model tak, aby zahrnoval stejná data, ale s jinou strukturou.

### <a name="model-2"></a>Model 2

Zvažte tento alternativní model k výše uvedenému:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

V tomto případě jsme odstranili **makro\_DECLARE STRUCT** a jednoduše definujeme datové položky z našeho scénáře pomocí jednoduchých typů z jazyka modelování.

Jen pro tuto chvíli, ignorovat **čas** událost. S tím stranou, tady je kód pro příchozí **teploty**:

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

Tento kód odešle do ioT hubu následující serializovanou událost:

```C
{"Temperature":75}
```

A kód pro odeslání události Vlhkost se zobrazí takto:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Tento kód to odešle do ioT hubu:

```C
{"Humidity":45}
```

Zatím stále nejsou žádná překvapení. Nyní změníme způsob použití makra SERIALIZE.

Makro **SERIALIZE** může trvat více datových událostí jako argumenty. To nám umožňuje serializovat událost **Teplota** a **vlhkost** společně a poslat je do centra IoT Hub v jednom hovoru:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Můžete hádat, že výsledkem tohoto kódu je, že dvě události dat jsou odesílány do ioT hubu:

[ {"Teplota":75}, {"Vlhkost":45}

Jinými slovy můžete očekávat, že tento kód je stejný jako odesílání **teploty** a **vlhkosti** samostatně. Je to jen pohodlí předat obě události **serializovat** ve stejném volání. Nicméně, to není tento případ. Místo toho výše uvedený kód odešle tuto událost jednoho datového kódu do centra IoT Hub:

{"Teplota":75, "Vlhkost":45}

To se může zdát divné, protože náš model definuje **teplotu** a **vlhkost** jako dvě *samostatné* události:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Více k věci, jsme neměli model těchto událostí, kde **teplota** a **vlhkost** jsou ve stejné struktuře:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Pokud bychom použili tento model, bylo by snazší pochopit, jak **by teplota** a vlhkost byly **odeslány** ve stejné serializované zprávě. Však nemusí být jasné, proč to funguje tímto způsobem při předání obou datových událostí **serializovat** pomocí modelu 2.

Toto chování je srozumitelnější, pokud znáte předpoklady, které provádí **knihovna serializátoru.** Aby to dávalo smysl, vraťme se k našemu modelu:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Myslete na tento model v objektově orientovaných termínech. V tomto případě modelujeme fyzické zařízení (termostat) a toto zařízení obsahuje atributy jako **Teplota** a **vlhkost**.

Můžeme poslat celý stav našeho modelu s kódem, jako je následující:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Za předpokladu, že jsou nastaveny hodnoty teploty, vlhkosti a času, uvidíme událost, jako je tato, odeslaná do ioT hubu:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Někdy můžete chtít odeslat pouze *některé* vlastnosti modelu do cloudu (to platí zejména v případě, že váš model obsahuje velký počet datových událostí). Je užitečné odeslat pouze podmnožinu událostí dat, například v našem předchozím příkladu:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

To generuje přesně stejnou serializovanou událost, jako kdybychom definovali **TemperatureEvent** s členem **Temperature** and **Time,** stejně jako jsme to udělali s modelem 1. V tomto případě jsme byli schopni generovat přesně stejnou serializovanou událost pomocí jiného modelu (model 2), protože jsme **nazvali SERIALIZE** jiným způsobem.

Důležité je, že pokud předáte více datových událostí **serializovat,** pak předpokládá, že každá událost je vlastnost v jednom objektu JSON.

Nejlepší přístup závisí na vás a jak si myslíte o vašem modelu. Pokud odesíláte "události" do cloudu a každá událost obsahuje definovanou sadu vlastností, pak první přístup dává velký smysl. V takovém případě byste **použili\_DECLARE STRUCT** k definování struktury každé události a pak je zahrnuli do modelu pomocí makra **\_WITH DATA.** Pak pošlete každou událost, jak jsme to udělali v prvním příkladu výše. V tomto přístupu by pouze předat jednu datovou událost **SERIALIZER**.

Pokud se nad svým modelem zamyslíte objektově orientovaným způsobem, může vám druhý přístup vyhovovat. V tomto případě prvky definované pomocí **WITH\_DATA** jsou "vlastnosti" objektu. Předáte jakoukoli podmnožinu událostí **serializovat,** které se vám líbí, v závislosti na tom, kolik stavu "objektu" chcete odeslat do cloudu.

Nether přístup je správné nebo špatné. Jen být vědomi toho, jak **serializer** knihovna funguje, a vybrat modelování přístup, který nejlépe vyhovuje vašim potřebám.

## <a name="message-handling"></a>Zpracování zpráv

Zatím tento článek pouze diskutovalo odesílání událostí do ioT hubu a neřeší přijímání zpráv. Důvodem je, že to, co potřebujeme vědět o přijímání zpráv byla z velké části zahrnuta v článku [Zařízení Azure IoT SDK pro C](iot-hub-device-sdk-c-intro.md). Odvolání z tohoto článku, který zpracováváte zprávy registrací funkce zpětného volání zprávy:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Potom napíšete funkci zpětného volání, která je vyvolána při přijetí zprávy:

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

Tato implementace **IoTHubMessage** volá konkrétní funkce pro každou akci ve vašem modelu. Pokud například model definuje tuto akci:

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

**SetAirResistance** je pak volána, když je tato zpráva odeslána do vašeho zařízení.

Co jsme zatím nevysvětlili, je to, jak vypadá serializovaná verze zprávy. Jinými slovy, pokud chcete odeslat zprávu **SetAirResistance** do vašeho zařízení, jak to vypadá?

Pokud odesíláte zprávu do zařízení, uděláte to prostřednictvím sady Azure IoT service SDK. Stále potřebujete vědět, jaký řetězec odeslat vyvolat konkrétní akci. Obecný formát pro odeslání zprávy se zobrazí takto:

```C
{"Name" : "", "Parameters" : "" }
```

Odesíláte serializovaný objekt JSON se dvěma vlastnostmi: **Název** je název akce (zpráva) a **Parametry** obsahuje parametry této akce.

Chcete-li například vyvolat **SetAirResistance,** můžete tuto zprávu odeslat do zařízení:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Název akce se musí přesně shodovat s akcí definovanou v modelu. Názvy parametrů se musí také shodovat. Všimněte si také rozlišování malých a velkých písmen. **Název** a **parametry** jsou vždy velká písmena. Ujistěte se, že odpovídá velikosti případu názvu akce a parametrů v modelu. V tomto příkladu je název akce "SetAirResistance" a ne "setairresistance".

Další dvě akce **TurnFanOn** a **TurnFanOff** lze vyvolat odesláním těchto zpráv do zařízení:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Tato část popisuje vše, co potřebujete vědět při odesílání událostí a přijímání zpráv s knihovnou **serializátoru.** Než budete pokračovat, podívejme se na některé parametry, které můžete nakonfigurovat, které řídí, jak velký je váš model.

## <a name="macro-configuration"></a>Konfigurace makra

Pokud používáte **serializační** knihovny důležitou součástí sady SDK, o které máte vědět, se nachází v knihovně azure-c-shared-utility.

Pokud jste naklonovali úložiště Azure-iot-sdk-c z GitHubu `git submodule update --init` a vydali příkaz, najdete tuto sdílenou knihovnu nástrojů zde:

```C
.\\c-utility
```

Pokud jste knihovnu nenaklonovali, najdete ji [zde](https://github.com/Azure/azure-c-shared-utility).

V knihovně sdílených nástrojů najdete následující složku:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Tato složka obsahuje řešení sady Visual Studio nazývané **makro\_utils\_h\_generator.sln**:

  ![Snímek obrazovky s maco_utils_h_generator řešení Visual Studio](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Program v tomto řešení generuje soubor **macro\_utils.h.** K sada SDK je přiložen výchozí soubor macro\_utils.h. Toto řešení umožňuje upravit některé parametry a potom znovu vytvořit soubor záhlaví na základě těchto parametrů.

Dva klíčové parametry, kterými se má zabývat, jsou **nArithmetic** a **nMacroParameters**, které jsou definovány v těchto dvou řádcích v makarech\_utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Tyto hodnoty jsou výchozí parametry zahrnuté do sady SDK. Každý parametr má následující význam:

* nMacroParameters – Určuje, kolik parametrů můžete\_mít v jedné definici makra DECLARE MODELU.
* nArithmetic – Řídí celkový počet členů povolených v modelu.

Důvod, proč jsou tyto parametry důležité, je, protože řídí, jak velký může být váš model. Zvažte například tuto definici modelu:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Jak již bylo zmíněno **dříve, DECLARE\_MODEL** je pouze makro C. Názvy modelu a **příkazu WITH\_DATA** (ještě jiné makro) jsou parametry DECLARE **\_MODEL**. **nMacroParameters** definuje, kolik parametrů může být zahrnuto do **\_declare modelu**. Efektivně to definuje, kolik dat událostí a deklarací akcí můžete mít. Jako takové s výchozí limit 124 to znamená, že můžete definovat model s kombinací asi 60 akcí a datových událostí. Pokud se pokusíte překročit tento limit, zobrazí se chyby kompilátoru, které vypadají podobně jako tento:

  ![Snímek obrazovky s chybami kompilátoru parametrů maker](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

**Parametr nArithmetic** je více o interním fungování jazyka maker než o aplikaci.  Řídí celkový počet členů, které můžete mít v modelu, včetně **DECLARE_STRUCT** maker. Pokud začnete vidět chyby kompilátoru, jako je tento, pak byste měli zkusit zvýšit **nArithmetic**:

   ![Snímek obrazovky s chybami aritmetické kompilátoru](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Chcete-li tyto parametry změnit, upravte\_hodnoty v souboru\_utils.tt\_makra, překompilujte makro utilit h roztok u zdroje informací.sln\_a spusťte zkompilovaný program. Pokud tak učiníte,\_nový soubor utils.h makro je generován a umístěn v . \\common\\inc adresáře.

Chcete-li použít novou verzi\_makra utils.h, odeberte **serializátor** NuGet balíček z vašeho řešení a na jeho místě patří **serializátor** Visual Studio projektu. To umožňuje kód kompilovat proti zdrojový kód serializační knihovny. To zahrnuje aktualizované\_makro utils.h. Pokud chcete provést pro **simplesample\_amqp**, začněte odebráním balíčku NuGet pro serializační knihovnu z řešení:

   ![Snímek obrazovky s odebráním balíčku NuGet pro knihovnu serializátorů](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Pak přidejte tento projekt do řešení sady Visual Studio:

> . \\c\\serializátor\\sestavení\\systému windows\\serializer.vcxproj
> 
> 

Až budete hotovi, vaše řešení by mělo vypadat takto:

   ![Snímek obrazovky s řešením simplesample_amqp Visual Studio](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Nyní při kompilaci řešení,\_aktualizované makro utils.h je součástí binárního souboru.

Všimněte si, že zvýšení těchto hodnot dostatečně vysoké může překročit limity kompilátoru. K tomuto bodu **nMacroParameters** je hlavním parametrem, se kterým se má týkat. Specifikace C99 určuje, že v definici makra je povoleno minimálně 127 parametrů. Kompilátor Microsoft uplatní spec přesně (a má limit 127), takže nebudete moci zvýšit **nMacroParameters** mimo výchozí. Ostatní kompilátory vám to mohou umožnit (například gnu kompilátor podporuje vyšší limit).

Zatím jsme se zabývali téměř vše, co potřebujete vědět o tom, jak psát kód s **serializační** knihovny. Před uzavřením, pojďme přehodnotit některá témata z předchozích článků, které vás možná zajímá.

## <a name="the-lower-level-apis"></a>Nižší úroveň API
Ukázková aplikace, na kterou se tento článek zaměřil, je **simplesample\_amqp**. Tato ukázka používá vyšší úroveň **(non-LL**) API pro odesílání událostí a přijímat zprávy. Pokud používáte tato rozhraní API, spustí se vlákno na pozadí, které se postará o odesílání událostí i přijímání zpráv. Můžete však použít rozhraní API nižší úrovně (LL) k odstranění tohoto vlákna na pozadí a převzít explicitní kontrolu nad při odesílání událostí nebo přijímat zprávy z cloudu.

Jak je popsáno v [předchozím článku](iot-hub-device-sdk-c-iothubclient.md), je sada funkcí, která se skládá z vyšší úrovně API:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* SetMessageCallback\_klienta IoTHub
* Zničení klienta\_IoTHub

Tato api jsou demonstrovány v **simplesample\_amqp**.

K dispozici je také analogické sadu nižší úrovně API.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL zničit

Všimněte si, že nižší úroveň API pracovat přesně stejným způsobem, jak je popsáno v předchozích článcích. První sadu rozhraní API můžete použít, pokud chcete, aby vlákno na pozadí zpracovávalo odesílání událostí a přijímání zpráv. Druhou sadu api použijte, pokud chcete explicitní kontrolu nad při odesílání a přijímání dat z ioT hubu. Obě sady api fungují stejně dobře s knihovnou **serializátoru.**

Příklad použití nízkoúrovňových api s knihovnou **serializátoru** naleznete v aplikaci **\_simplesample http.**

## <a name="additional-topics"></a>Další témata
Několik dalších témat, která stojí za zmínku znovu jsou zpracování vlastností, pomocí alternativní chod zařízení pověření a možnosti konfigurace. To jsou všechna témata, na která se vztahuje [v předchozím článku](iot-hub-device-sdk-c-iothubclient.md). Hlavním bodem je, že všechny tyto funkce fungují stejným způsobem s knihovnou **serializátoru** jako s knihovnou **IoTHubClient.** Například pokud chcete připojit vlastnosti k události z modelu, použijte **IoTHubMessage\_Vlastnosti** a **Map**\_**AddorUpdate**, stejným způsobem, jak je popsáno výše:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Jestli byla událost vygenerována z knihovny **serializátoru** nebo vytvořena ručně pomocí knihovny **IoTHubClient,** nezáleží.

Pro pověření alternativní zařízení pomocí **IoTHubClient\_LL\_Create** funguje stejně jako **IoTHubClient\_CreateFromConnectionString** pro přidělování **popisovač klienta\_\_IOTHUB**.

Nakonec pokud používáte knihovnu **serializátoru,** můžete nastavit možnosti konfigurace s **IoTHubClient\_LL\_SetOption** stejně jako při použití knihovny **IoTHubClient.**

Funkce, která je jedinečná pro knihovnu **serializátoru,** jsou inicializační api. Než začnete pracovat s knihovnou, musíte zavolat **\_serializátor init**:

```C
serializer_init(NULL);
```

To se provádí těsně před voláním **IoTHubClient\_CreateFromConnectionString**.

Podobně, když jste hotovi pracovat s knihovnou, poslední hovor budete dělat, je **serializer\_deinit**:

```C
serializer_deinit();
```

V opačném případě všechny ostatní funkce uvedené výše pracovat stejné v knihovně **serializátoru** jako v knihovně **IoTHubClient.** Další informace o těchto tématech naleznete v [předchozím článku](iot-hub-device-sdk-c-iothubclient.md) této řady.

## <a name="next-steps"></a>Další kroky

Tento článek podrobně popisuje jedinečné aspekty knihovny **serializátoru** obsažené v **sadě Azure IoT zařízení SDK pro C**. S poskytnuté informace byste měli mít dobré znalosti o tom, jak používat modely k odesílání událostí a přijímat zprávy z ioT hubu.

Tím také končí třídílné řady o tom, jak vyvíjet aplikace s **Azure IoT zařízení SDK pro C**. To by mělo být dostatek informací nejen začít, ale poskytnout vám důkladné pochopení toho, jak fungují api. Další informace, existuje několik vzorků v sdk není zahrnuta zde. V opačném případě [je dokumentace sady Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-c) dobrým zdrojem informací pro další informace.

Další informace o vývoji pro Službu IoT Hub najdete v tématu [sady Azure IoT SDK .](iot-hub-devguide-sdks.md)

Další informace o možnostech služby IoT Hub najdete v [tématu Nasazení ai na hraniční zařízení s Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).