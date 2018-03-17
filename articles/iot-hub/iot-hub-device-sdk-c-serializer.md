---
title: "Pro zařízení Azure IoT SDK pro jazyk C - serializátor | Microsoft Docs"
description: "Jak používat knihovnu serializátor v zařízení Azure IoT SDK pro jazyk C vytvoření aplikace pro zařízení, které komunikují pomocí služby IoT hub."
services: iot-hub
documentationcenter: 
author: yzhong94
manager: timlt
editor: 
ms.assetid: defbed34-de73-429c-8592-cd863a38e4dd
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2016
ms.author: yizhon
ms.openlocfilehash: da9a2dd8bad68c03bb75d1772cf9e5dc539892aa
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Pro zařízení Azure IoT SDK pro jazyk C – informace o serializátor
[Nejprve článek](iot-hub-device-sdk-c-intro.md) této série zavedená **zařízení Azure IoT SDK pro jazyk C**. Další článek poskytuje podrobnější popis [ **IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). Tento článek poskytuje podrobnější popis zbývající součásti dokončí pokrytí sady SDK: **serializátor** knihovny.

Úvodní článek popisuje postup použití **serializátor** knihovnu, která se události odesílat a přijímat zprávy ze služby IoT Hub. V tomto článku jsme rozšířit tento diskuzi o podrobnější vysvětlení způsobu modelu svá data pomocí **serializátor** makro jazyk. Článek také obsahuje další podrobnosti o tom, jak knihovny serializuje zprávy (a v některých případech, jak můžete řídit chování serializace). Popíšeme také některé parametry, které můžete upravit určující velikost modely, které vytvoříte.

Nakonec článek znovu navštíví některá témata popsaná v předchozích články například zprávu a vlastnosti zpracování. Až budete zjistěte těchto pracovních funkcí ve stejném způsobem pomocí **serializátor** knihovny stejně jako s **IoTHubClient** knihovny.

Všechno, co popsané v tomto článku je založena na **serializátor** ukázky SDK. Pokud chcete sledovat, najdete v článku **simplesample\_amqp** a **simplesample\_http** aplikace, které jsou součástí sady SDK zařízení Azure IoT pro C.

Můžete najít [ **zařízení Azure IoT SDK pro jazyk C** ](https://github.com/Azure/azure-iot-sdk-c) Githubu úložiště a zobrazte podrobnosti o rozhraní API v [referenční dokumentace rozhraní API jazyka C](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-modeling-language"></a>Představuje Modelovací jazyk
[Úvodní článek](iot-hub-device-sdk-c-intro.md) této série zavedená **zařízení Azure IoT SDK pro jazyk C** Modelovací jazyk prostřednictvím v příkladu v **simplesample\_amqp** aplikace:

```
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

Jak vidíte, který představuje Modelovací jazyk je založena na maker v jazyce C. Je třeba nejprve vaší definice s **BEGIN\_obor názvů** a vždy končit **END\_obor názvů**. Je běžné název oboru názvů pro vaši společnost nebo, jako v následujícím příkladu, projekt, který právě pracujete.

Co v oboru názvů jsou definice modelu. V takovém případě je jeden model anemometer. Ještě jednou může být název modelu, nic, ale obvykle to jmenuje pro zařízení nebo typ dat, kterou chcete exchange službou IoT Hub.  

Modely obsahují definice událostí můžete příjem příchozích dat do služby IoT Hub ( *data*) a také zprávy může přijímat ze služby IoT Hub ( *akce*). Jak je vidět z příkladu události mít typ a název; název a volitelné parametry (každý s typem) mají akce.

Co není ukázáno v této ukázce jsou další datové typy, které jsou podporované sadou SDK. Jsme zaměříme tento další.

> [!NOTE]
> IoT Hub odkazuje na data, která zařízení odesílá do ní jako *události*, zatímco jazyk modelování odkazuje na jej jako *data* (definovat pomocí **WITH_DATA**). Podobně IoT Hub odkazuje na data, která odešlete na zařízení jako *zprávy*, zatímco jazyk modelování odkazuje na jej jako *akce* (definovat pomocí **WITH_ACTION**). Upozorňujeme, že tyto podmínky mohou být použity zcela zaměnitelným významem v tomto článku.
> 
> 

### <a name="supported-data-types"></a>Podporované datové typy
Jsou podporovány následující typy dat v modelů vytvořených pomocí **serializátor** knihovny:

| Typ | Popis |
| --- | --- |
| Double |Dvojitá přesnost číslo s plovoucí desetinnou |
| celá čísla |32bitové celé číslo |
| Plovoucí desetinná čárka |číslo s plovoucí desetinnou jednoduchou přesností |
| dlouhá |dlouhé celé číslo |
| int8\_t |8bitové celé číslo |
| Int16\_t |16bitové celé číslo |
| int32\_t |32bitové celé číslo |
| int64\_t |64bitové celé číslo |
| BOOL |Boolean |
| ascii\_char\_ptr |Řetězec ASCII |
| EDM\_DATUM\_ČAS\_POSUNUTÍ |Datum čas posunutí |
| EDM\_GUID |IDENTIFIKÁTOR GUID |
| EDM\_BINÁRNÍ |Binární |
| DEKLAROVAT\_– STRUKTURA |Komplexní datový typ |

Začněme s posledním datovým typem. **DECLARE\_struktura** můžete zadat rozšířené datové typy, které jsou seskupení primitivní typy. Tato seskupení povolit nám definovat model, který vypadá takto:

```
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

Naše model obsahuje událostí jednoho datového typu **TestType**. **TestType** jedná o komplexní typ, který zahrnuje několik členů, které se souhrnně ukazují primitivní typy podporované systémem **serializátor** Modelovací jazyk.

S takového modelu jsme můžete napsat kód pro odesílání dat do služby IoT Hub, který se zobrazí následujícím způsobem:

```
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

V podstatě, jsme se přiřazení hodnoty k každý člen **testovací** strukturu a pak volání **SendAsync** k odeslání **testovací** dat událostí do cloudu. **SendAsync** je pomocné funkce, která odesílá události jednoho datového do služby IoT Hub:

```
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

Tato funkce serializuje daná data události a odesílá do služby IoT Hub pomocí **IoTHubClient\_SendEventAsync**. Toto je stejný kód popsané v předchozí články (**SendAsync** zapouzdří logiku do vhodného funkce).

Jeden další pomocné funkce použitá v předchozí kód je **GetDateTimeOffset**. Tato funkce transformuje daném čase na hodnotu typu **EDM\_datum\_čas\_posun**:

```
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

Pokud spustíte tento kód, následující zpráva odeslána do služby IoT Hub:

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Upozorňujeme, že serializaci JSON, který je ve formátu vygenerované pomocí **serializátor** knihovny. Všimněte si, že každý člen serializovaný objekt JSON odpovídá členů **TestType** , definovaného v našem modelu. Hodnoty také přesně shodovat s hodnotami používá v kódu. Ale Všimněte si, že binární data s kódováním base64, pomocí: "AQID" je Base64, pomocí kódování {0x01, 0x02, 0x03}.

Tento příklad ukazuje výhodou použití **serializátor** knihovna – umožňuje poslat JSON do cloudu, bez nutnosti explicitně serializace v naší aplikaci. Všechny, které máme si dělat starosti se nastaví hodnoty dat události v našem modelu a poté volání jednoduché rozhraní API k odeslání události do cloudu.

Pomocí těchto informací jsme můžete definovat modely, které zahrnují řadu podporované datové typy, včetně komplexní typy (může i zahrnuta komplexních typů v rámci jiné komplexní typy). Však mohl serializovat JSON vygenerovaných v předchozím příkladu se vyvolá bod důležité. *Jak* odešleme dat pomocí **serializátor** knihovny určuje přesně jak je formátu JSON. Zda je konkrétní bodu, co jsme zaměříme Další.

## <a name="more-about-serialization"></a>Další informace o serializaci
V předchozí části jsou zdůrazněné příklad výstupu generované **serializátor** knihovny. V této části vám objasníme, jak knihovny serializuje data a jak můžete řídit tohoto chování pomocí serializace rozhraní API.

Chcete-li zálohy diskuse o serializaci, jsme budete pracovat se nový model podle termostatu. Nejprve umožňuje zadat základní na scénáři Pokoušíme se adresa.

Chceme modelu termostatu, které měří teploty a vlhkosti. Každá položka dat je přejdete k odeslání do služby IoT Hub jinak. Ve výchozím nastavení ingresses termostatu teploty událostí jednou za 2 minut; událost vlhkosti je ingressed jednou za 15 minut. Po ingressed buď událostí musí obsahovat časové razítko, které zobrazuje čas, že se měří odpovídající teploty a vlhkosti.

Tento scénář, vám ukážeme dva různé způsoby, jak model dat, a vysvětlíme účinek, modelování měl na serializovaných výstup.

### <a name="model-1"></a>Model 1
Tady je první verze součásti model, který podporuje předchozí situaci:

```
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

Všimněte si, že tento model zahrnuje dvě data události: **teploty** a **vlhkosti**. Na rozdíl od předchozích příkladech typ jednotlivých událostí je struktura definovat pomocí **DECLARE\_struktura**. **TemperatureEvent** zahrnuje měření teploty a časové razítko; **HumidityEvent** obsahuje měření vlhkosti a časové razítko. Tento model nám poskytuje přirozené způsob, jak model data pro scénář popsaný výše. Když jsme odeslání události do cloudu, buď pošleme teploty/časové razítko nebo pár vlhkosti/časové razítko.

Vám můžeme poslat teploty událostí v cloudu pomocí kódu, například následující:

```
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

Jsme budete používat pevně definovaných hodnot pro teploty a vlhkosti v ukázkovém kódu, ale Představte si, že jsme se ve skutečnosti načítání tyto hodnoty vzorkováním odpovídající snímače na termostatu.

Kód výše používá **GetDateTimeOffset** pomocné rutiny, která byla zavedená dříve. Z důvodů, které se stanou zrušte novější se tento kód explicitně odděluje úlohu serializaci a odeslání události. Předchozí kód serializuje teploty událostí do vyrovnávací paměti. Potom **sendMessage** je pomocné funkce (součástí **simplesample\_amqp**), odešle událost do služby IoT Hub:

```
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

Tento kód je podmnožinou **SendAsync** pomocné rutiny popsané v předchozí části, takže jsme nebude projít ho znovu sem.

Když jsme spustí předchozí kódu pro odeslání události teploty, tento formulář serializovaných události je odeslána do služby IoT Hub:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Byla odeslána teploty, které je typu **TemperatureEvent** a že struktura obsahuje **teploty** a **čas** člen. To se projeví přímo v serializovaných datech.

Podobně vám můžeme poslat vlhkosti událostí s tímto kódem:

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Serializovaná formulář, který je odeslán do služby IoT Hub vypadá takto:

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

To je opět podle očekávání.

V tomto modelu, který chcete mít jak další události lze snadno přidat. Můžete definovat další struktur pomocí **DECLARE\_struktura**a zahrnovat odpovídající události v modelu pomocí **WITH\_DATA**.

Nyní Pojďme upravit modelu tak, že obsahují stejná data, ale s jinou strukturu.

### <a name="model-2"></a>Model 2
Vezměte v úvahu tento alternativní model tomu výše:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

V takovém případě jsme vyloučili **DECLARE\_struktura** makra a jsou jednoduše definování datové položky z našich scénář pomocí jednoduché typy z představuje Modelovací jazyk.

Jenom pro tuto chvíli umožňuje ignorovat **čas** událostí. S vyhraďte, zde je kód pro příjem příchozích dat **teploty**:

```
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

Tento kód následující událostí serializovaný odešle do služby IoT Hub:

```
{"Temperature":75}
```

A kód pro odesílání událostí vlhkosti vypadat takto:

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Tento kód to odešle do služby IoT Hub:

```
{"Humidity":45}
```

Pokud existují ještě žádné výskyt nečekaných událostí. Nyní změníme jak používáme makro SERIALIZACE.

**SERIALIZACE** makro může trvat několik dat události jako argumenty. To umožňuje nám k serializaci **teploty** a **vlhkosti** událostí společně a odešlete je do služby IoT Hub v jednom volání:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Může uhodnout, že výsledkem tohoto kódu je, že se dvěma datovými události posílají do služby IoT Hub:

[

{"Teploty": 75},

{"Vlhkosti": 45}

]

Jinými slovy, můžou očekávat, že tento kód je stejný jako odesílání **teploty** a **vlhkosti** samostatně. Je právě v zájmu usnadnění předat obou události **SERIALIZACE** ve stejném volání. Nicméně, který tak není. Místo toho výše uvedený kód odešle tato událost jednoho datového do služby IoT Hub:

{"Teploty": 75, "vlhkosti": 45}

To může zdát neobvyklé, protože naše model definuje **teploty** a **vlhkosti** jako dvě *samostatné* události:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Více bodu jsme nebyl model tyto události kde **teploty** a **vlhkosti** jsou ve stejné struktuře:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Pokud jsme použili tento model, bude možné snadněji pochopit, jak **teploty** a **vlhkosti** se využije stejné serializovaná zpráva. Ale nemusí být zřejmé, proč funguje tímto způsobem při předání obě data události **SERIALIZACE** pomocí modelu 2.

Toto chování je jednodušší zjistit, pokud víte, předpokladů že **serializátor** je zajistit knihovny. Chcete-li mít smysl to přejděte zpět do našeho modelu:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Tento model si můžete představit v objektově orientované podmínky. V takovém případě jsme se modelování fyzického zařízení (termostatu) a zařízení zahrnuje atributů, například **teploty** a **vlhkosti**.

Vám můžeme poslat celý stav našeho modelu s kódem například následující:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Za předpokladu, že hodnoty z teploty, nastavení vlhkosti a času, vidíme by událost takto odeslané do služby IoT Hub:

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Někdy může jenom chcete odeslat *některé* vlastnosti modelu do cloudu (to je obzvláště hodnota true, pokud model obsahuje velké množství dat událostí). Je užitečné k odeslání pouze podmnožinu dat události, například v našem příkladu starší:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Tím se vygeneruje přesně stejnou událostí serializovaný jako kdyby měl definovaného **TemperatureEvent** s **teploty** a **čas** člena, stejně jako jsme s modelu 1. V takovém případě jsme byli schopni generování přesně stejnou událostí serializovaný pomocí jiného modelu (model 2), protože volali jsme **SERIALIZACE** jiným způsobem.

Důležité je, že pokud předáte více dat událostí do **SERIALIZACE,** pak předpokládá, že každá událost je vlastnost v jeden objekt JSON.

Nejlepší metodou závisí na a jak si myslíte o modelu. Pokud odesíláte "události" do cloudu a každé události, obsahuje sada vlastností, prvním přístupem je velké množství smysl. V takovém případě byste použili **DECLARE\_struktura** definovat strukturu každé události a zahrnout je do modelu pomocí **WITH\_DATA** makro. Pak je odeslat všechny události, jako jsme to udělali v prvním příkladu výše. V tomto přístupu jenom předáte jednoho datového událost pro **SERIALIZÁTOR**.

Pokud si myslíte o modelu způsobem objektově orientované, může vyhovovat druhý postup je. V takovém případě elementy definovat pomocí **WITH\_DATA** jsou "vlastnosti" objektu. Předat ať podmnožinu události **SERIALIZACE** , se vám líbí, podle toho, kolik z vaší "" stav objektu se mají posílat do cloudu.

Nether přístup je pravé nebo nesprávné. Právě zajímat, jak **serializátor** funguje knihovny a vybrat přístup modelování, který nejlépe vyhovuje vašim potřebám.

## <a name="message-handling"></a>Zpracování zpráv
Tento článek, pokud má pouze popsané odesílání událostí do služby IoT Hub a nebyl řešit přijímání zpráv. Z důvodu pro to, co je potřeba vědět o přijímání zpráv má z velké části popsaná v [dříve článek](iot-hub-device-sdk-c-intro.md). Z tohoto článku odvolat, zpracování zpráv tak, že zaregistrujete funkci zpětného volání zpráva:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Funkce zpětného volání, která je volána, když je obdržena zprávu zapište:

```
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

Tato implementace **IoTHubMessage** volá konkrétní funkci pro každou akci do modelu. Například pokud váš model definuje tato akce:

```
WITH_ACTION(SetAirResistance, int, Position)
```

Je nutné definovat funkce s Tento podpis:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** pak je volána při odeslání zprávy do vašeho zařízení.

Co jsme nebyly vysvětlené ještě je serializovaná verze zprávy, která bude vypadat takto. Jinými slovy Pokud chcete odeslat **SetAirResistance** zpráva do zařízení, co typu?

Pokud odesíláte zprávy do zařízení, krok lze provést pomocí sady SDK služby Azure IoT. Dál potřebujete vědět, jaké řetězec k odeslání do volání určité akce. Obecný formát pro odesílání zprávy vypadá takto:

```
{"Name" : "", "Parameters" : "" }
```

Odesíláte serializovaný objekt JSON s dvě vlastnosti: **název** je název akce (zprávy) a **parametry** obsahuje parametry této akce.

Například pro vyvolání **SetAirResistance** tuto zprávu můžete odeslat do zařízení:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Název akce musí přesně shodovat akce definované v modelu. Názvy parametrů musí odpovídat také. Všimněte si také malá a velká písmena. **Název** a **parametry** jsou vždy velká písmena. Ujistěte se, že malá a velká písmena názvu akce a parametrů do modelu. V tomto příkladu je název akce "SetAirResistance" a není "setairresistance".

Další dvě akce **TurnFanOn** a **TurnFanOff** nelze vyvolat odesláním těchto zpráv do zařízení:

```
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Tato část popisuje všechno, co potřebujete vědět, kdy události odesílání a příjem zpráv s **serializátor** knihovny. Než budete pokračovat, můžeme zahrnují některé parametry, které můžete nakonfigurovat, které řídí, jak velké je váš model.

## <a name="macro-configuration"></a>Konfigurace – makro
Pokud používáte **serializátor** knihovny důležitou součástí sady SDK zajímat se nachází v knihovně azure-c sdílené – nástroj.
Při naklonování úložišti Azure-iot-sdk-c z Githubu pomocí možnosti--rekurzivní zjistíte této sdílené nástroj knihovny:

```
.\\c-utility
```

Pokud nebyly klonovat knihovny, můžete ji najít [zde](https://github.com/Azure/azure-c-shared-utility).

V knihovně Sdílené nástroj najdete následující složku:

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Tato složka obsahuje řešení sady Visual Studio názvem **makro\_utils\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

Generuje program v tomto řešení **makro\_utils.h** souboru. Je výchozí makro\_utils.h soubor je součástí sady SDK. Toto řešení umožňuje změnit některé parametry a pak znovu vytvořte soubor hlaviček na základě těchto parametrů.

Jsou dva klíče parametry se to týká s **nArithmetic** a **nMacroParameters** které jsou definované v těchto dvou řádcích najít v makro\_utils.tt:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Tyto hodnoty jsou výchozí parametry součástí sady SDK. Jednotlivé parametry mají následující význam:

* nMacroParameters – Určuje, kolik parametry může mít v jedné DECLARE\_definici makra modelu.
* nArithmetic – ovládací prvky celkový počet členů v modelu povolené.

Z důvodu, který tyto parametry jsou důležité je, protože tím i určovat, jak velká může být váš model. Představte si třeba tuto definici modelu:

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Jak je uvedeno nahoře, **DECLARE\_modelu** je právě makro C. Názvy modelu a **WITH\_DATA** – příkaz (ještě jiné makro) jsou parametry **DECLARE\_modelu**. **nMacroParameters** definuje, kolik parametry můžou být součástí **DECLARE\_modelu**. Definuje efektivně, kolik dat událostí a akce deklarace může mít. Jako takový výchozí limit 124 to znamená, že můžete definovat model pomocí kombinace o 60 akce a data události. Pokud se pokusíte překročí toto omezení, dostanete chyby kompilátoru, které vypadají podobně jako tento:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

**NArithmetic** parametr se o vnitřní strukturu a funkci jazyka – makro víc než vaše aplikace.  Jimi řídí celkový počet členů, může mít v modelu, včetně **DECLARE_STRUCT** makra. Pokud spustíte zobrazuje chyby kompilátoru, jako je tato, pak doporučujeme zvýšit **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Pokud chcete změnit tyto parametry, upravte hodnoty v makro\_utils.tt souboru, znovu zkompiluje makro\_utils\_h\_generator.sln řešení a spusťte zkompilovaný program. Když uděláte, nové makro\_utils.h soubor je vygenerována a uložena v umístění.\\ běžné\\inc adresáře.

Chcete-li použít novou verzi – makro\_utils.h, odebrat **serializátor** balíček NuGet, řešení a v jeho místní zahrnovat **serializátor** projektu sady Visual Studio. To umožňuje váš kód mohl zkompilovat proti zdrojový kód knihovny serializátor. To zahrnuje aktualizované makro\_utils.h. Pokud chcete to udělat pro **simplesample\_amqp**, začněte tím, že balíček NuGet pro knihovnu serializátor odebráním řešení:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Pak přidejte tento projekt do řešení sady Visual Studio:

> .\\c\\serializer\\build\\windows\\serializer.vcxproj
> 
> 

Když jste hotovi, řešení by měl vypadat přibližně takto:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Nyní když zkompilujete řešení aktualizované makro\_utils.h je součástí vaší binární.

Všimněte si, že zvýšení tyto hodnoty dostatečně vysoký může překročit omezení kompilátoru. K tomuto bodu **nMacroParameters** je hlavní parametr, pomocí kterého se to týká. Specifikace C99 Určuje, že minimálně 127 parametry jsou povoleny v definici makra. Kompilátor Microsoft přesně odpovídá o specifikace (a může obsahovat maximálně 127 znaků), takže nebude možné zvýšit **nMacroParameters** nad výchozí. Další kompilátory může umožňují Uděláte to tak (například kompilátoru GNU podporuje vyšší limit).

Dosavadní téměř všechno, co potřebujete vědět o tom, jak psát kód, který představuje jste zahrnutých jsme **serializátor** knihovny. Před uzavřením, můžeme pokroku některá témata z předchozí články, které asi vás zajímá o.

## <a name="the-lower-level-apis"></a>Rozhraní API nižší úrovně
Ukázkovou aplikaci, na kterém se tento článek zaměřuje je **simplesample\_amqp**. Této ukázce se používá vyšší úrovni (jiný-"UDOU") rozhraní API pro události odesílat a přijímat zprávy. Používáte-li tato rozhraní API, spustí vlákna na pozadí, který se stará o odesílání událostí i přijímání zpráv. Můžete však použít rozhraní API nižší úrovni (vše) Pokud chcete odstranit tento vlákna na pozadí a proveďte explicitní kontrolu nad při odesílání událostí nebo příjem zpráv z cloudu.

Jak je popsáno v [předchozí článek](iot-hub-device-sdk-c-iothubclient.md), je sada funkcí, které se skládá z vyšší úrovně rozhraní API:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_Destroy

Tato rozhraní API je ukázán v **simplesample\_amqp**.

K dispozici je také podobá sadu rozhraní API nižší úrovně.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_UDOU\_Destroy

Některé nižší úrovně rozhraní API funkční stejným způsobem, jak je popsáno v předchozí článcích. První sadu rozhraní API můžete použít, pokud chcete, aby vlákna na pozadí pro zpracování událostí odesílání a příjmu zprávy. Druhá sada rozhraní API použijte, pokud chcete, aby explicitní ovládat, kdy odesílat a přijímat data ze služby IoT Hub. Buď sadu rozhraní API pracovní stejnou měrou i s **serializátor** knihovny.

Příklad použití rozhraní API nižší úrovně s **serializátor** knihovny, najdete v článku **simplesample\_http** aplikace.

## <a name="additional-topics"></a>Další témata
Několik dalších tématech důležité zmínit, znovu se vlastnost zpracování, pomocí přihlašovacích údajů jiného zařízení a možnosti konfigurace. Toto jsou všechna témata popsaná v [předchozí článek](iot-hub-device-sdk-c-iothubclient.md). Hlavní bod je, že všechny tyto funkce fungovat stejně jako s **serializátor** knihovny stejně jako s **IoTHubClient** knihovny. Například pokud chcete přiřadit vlastnosti události z modelu, použijete **IoTHubMessage\_vlastnosti** a **mapy**\_**AddorUpdate**, stejným způsobem, jak je popsáno dříve:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Zda událost byla vygenerována z **serializátor** knihovny nebo vytvořit ručně pomocí **IoTHubClient** knihovny nezáleží.

Pro přihlašovací údaje jiného zařízení, pomocí **IoTHubClient\_UDOU\_vytvořit** funguje stejně dobře jako **IoTHubClient\_CreateFromConnectionString** pro přidělování **IOTHUB\_klienta\_zpracování**.

Nakonec pokud používáte **serializátor** knihovny, můžete nastavit možnosti konfigurace s **IoTHubClient\_UDOU\_SetOption** stejně jako jste to udělali při použití **IoTHubClient** knihovny.

Funkce, které jsou jedinečné pro **serializátor** knihovny se inicializace rozhraní API. Před zahájením práce s knihovny, musí volat **serializátor\_init**:

```
serializer_init(NULL);
```

To se provádí těsně před voláním **IoTHubClient\_CreateFromConnectionString**.

Podobně po dokončení práce s knihovnou, posledním volání budete provedete je **serializátor\_deinit**:

```
serializer_deinit();
```

Jinak, všechny ostatní funkce výše uvedených fungovat stejně **serializátor** knihovny nebudou **IoTHubClient** knihovny. Další informace o kterékoli z těchto témat, najdete v článku [předchozí článek](iot-hub-device-sdk-c-iothubclient.md) této série.

## <a name="next-steps"></a>Další postup
Tento článek podrobně popisuje jedinečné aspekty **serializátor** knihovny, které jsou součástí **zařízení Azure IoT SDK pro jazyk C**. S informacemi, pokud že byste měli mít dostatečné povědomí o tom, jak používat modely odesílat události a přijímat zprávy ze služby IoT Hub.

To se taky ukončí řady třemi částmi o tom, jak vyvíjet aplikace s **zařízení Azure IoT SDK pro jazyk C**. To by měl být dostatek informací pro jenom vám pomůžou začít ale získáte důkladné znalosti o fungování rozhraní API. Další informace nejsou několik ukázky v sadě SDK, které nejsou zahrnuté v tomto poli. Jinak [dokumentaci k sadě SDK](https://github.com/Azure/azure-iot-sdk-c) je dobré prostředku pro další informace.

Další informace o vývoji pro IoT Hub, najdete v tématu [SDK služby Azure IoT][lnk-sdks].

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
