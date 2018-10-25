---
title: Pro zařízení Azure IoT SDK pro jazyk C - serializátor | Dokumentace Microsoftu
description: Jak použít knihovnu serializátor v zařízení Azure IoT SDK pro jazyk C k vytvoření aplikace pro zařízení, které komunikují s centrem IoT.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: yizhon
ms.openlocfilehash: 0a7e30be374ae5095e206ce0e519e51bb58f1f00
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024854"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Pro zařízení Azure IoT SDK pro jazyk C – Další informace pro serializátor

První článek v této sérii zavedené [Úvod do zařízení Azure IoT SDK pro jazyk C](iot-hub-device-sdk-c-intro.md). Další článek poskytuje podrobnější popis toho [zařízení Azure IoT SDK pro jazyk C – pro IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Tento článek uzavírá pokrytí sady SDK tím, že poskytuje podrobnější popis zbývající součásti: **serializátor** knihovny.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Úvodní článek popisuje způsob použití **serializátor** knihovny k odesílání událostí a příjem zpráv ze služby IoT Hub. V tomto článku rozšíříme tím, že poskytuje podrobnější vysvětlení postupu modelování dat pomocí této diskuse **serializátor** makra jazyka. Článek taky obsahuje další podrobnosti o jak knihovny serializuje zprávy (a v některých případech, jak můžete řídit chování serializace). Popíšeme si také některé parametry, které můžete upravit, které určují velikost modelů, které vytvoříte.

Nakonec článku znovu navštíví některá témata popsané v předchozí články, jako jsou zprávy a vlastnosti zpracování. Jako jsme budete přečtěte si tyto pracovní funkce ve stejném způsob použití **serializátor** knihovny stejně jako s **pro IoTHubClient** knihovny.

Všechno, co je popsáno v tomto článku je založen na **serializátor** ukázky SDK. Pokud chcete postup sledovat, najdete v článku **simplesample\_amqp** a **simplesample\_http** aplikací, které jsou součástí sady SDK pro zařízení Azure IoT pro C.

Můžete najít [ **zařízení Azure IoT SDK pro jazyk C** ](https://github.com/Azure/azure-iot-sdk-c) Githubu úložišti a zobrazit podrobnosti o rozhraní API ve službě [reference k rozhraní API jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>Modelovací jazyk

[Zařízení Azure IoT SDK pro jazyk C](iot-hub-device-sdk-c-intro.md) článek v této sérii zavedené **zařízení Azure IoT SDK pro jazyk C** modelování jazyka v příkladu v **simplesample\_ amqp** aplikace:

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

Jak je vidět, Modelovací jazyk podle maker v jazyce C. Vždy začínají vaší definice s **BEGIN\_obor názvů** a vždy končit **END\_obor názvů**. Je běžné názvu oboru názvů pro vaši společnost, nebo jako v následujícím příkladu projektu, který právě pracujete.

Co dostane uvnitř oboru názvů jsou definice modelu. V takovém případě je jeden model pro anemometer. Ještě jednou může mít název modelu, cokoli, ale obvykle pojmenován model zařízení nebo typ data, která chcete k výměně službou IoT Hub.  

Modely obsahovat definici událostí příchozího přenosu dat do služby IoT Hub můžete ( *data*) a také zprávy dostanete ze služby IoT Hub ( *akce*). Jak je vidět z příkladu, události mají typ a název. akce mají název a volitelné parametry (každý s typem).

Co není ukázáno v tomto příkladu jsou další datové typy, které jsou podporované prostřednictvím sady SDK. Probereme to teď.

> [!NOTE]
> IoT Hub odkazuje na data, která zařízení odesílá do ní jako *události*, zatímco Modelovací jazyk odkazuje na jako *data* (definované pomocí **WITH_DATA**). Obdobně služby IoT Hub odkazuje na data, která odešlete do zařízení jako *zprávy*, zatímco Modelovací jazyk odkazuje na jako *akce* (definované pomocí **WITH_ACTION**). Mějte na paměti, že tyto podmínky můžou používat Zaměnitelně v tomto článku.
> 
> 

## <a name="supported-data-types"></a>Podporované datové typy

Jsou podporovány následující typy dat v modelů vytvořených pomocí **serializátor** knihovny:

| Typ | Popis |
| --- | --- |
| double |dvojité přesnosti s plovoucí desetinnou čárkou číslo s desetinnou čárkou |
| int |32bitové celé číslo |
| float |číslo s plovoucí desetinnou čárkou desetinnou čárkou a jednoduchou přesností |
| zem. šířka |dlouhé celé číslo |
| int8\_t |8bitové celé číslo |
| Int16\_t |16bitové celé číslo |
| datový typ Int32\_t |32bitové celé číslo |
| Int64\_t |64bitové celé číslo |
| BOOL |Boolean |
| ascii\_char\_ptr |Řetězec ASCII |
| EDM\_DATUM\_ČAS\_POSUN |posun času datum |
| EDM\_GUID |GUID |
| EDM\_BINÁRNÍ |Binární |
| DEKLAROVAT\_– STRUKTURA |Komplexní datový typ. |

Začněme s posledním datovým typem. **DECLARE\_struktura** umožňuje definovat komplexních datových typů, které jsou seskupeními primitivní typy. Tyto skupiny umožňuje definovat model, který vypadá takto:

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

Náš model obsahuje událost jednoho datového typu **TestType**. **TestType** je komplexní typ, který zahrnuje několik členů, které společně ukazují primitivních typů podporovaných **serializátor** Modelovací jazyk.

S modelem tímto způsobem se nám můžete napsat kód k odesílání dat do služby IoT Hub, který se zobrazí takto:

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

V podstatě jsme už přiřazení hodnoty každého člena **testovací** strukturu a následným voláním **SendAsync** k odeslání **Test** dat událostí do cloudu. **SendAsync** je pomocná funkce, která odesílá jeden dat událostí do služby IoT Hub:

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

Tato funkce serializuje danou data událostí a odesílá je do služby IoT Hub pomocí **pro IoTHubClient\_SendEventAsync**. Toto je stejný kód popsané v předchozí článků (**SendAsync** zapouzdří logiku do praktické funkce).

Jedna funkce pomocné rutiny použitá v předchozím kódu je **GetDateTimeOffset**. Tato funkce transformuje daného času na hodnotu typu **EDM\_datum\_čas\_posun**:

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

Pokud tento kód spustit, je následující zpráva odeslána do služby IoT Hub:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Všimněte si, že serializace je ve formátu JSON, což je formát generovaných **serializátor** knihovny. Všimněte si také, že každý člen serializovaný objekt JSON odpovídá členů **TestType** , který jsme definovali v náš model. Tyto hodnoty také přesně shodují, se používají v kódu. Mějte však na paměti, že binární data jsou s kódováním base64: "AQID" je base64 kódování {0x01, 0x02, 0x03}.

Tento příklad ukazuje výhodou použití **serializátor** knihovny – umožňuje poslat JSON do cloudu, bez nutnosti explicitně serializace v naší aplikaci. Všechno, co musíme starat o je nastavit hodnoty dat událostí do náš model a následným voláním jednoduchá rozhraní API k odeslání události do cloudu.

Pomocí těchto informací můžete definujeme modelů, které obsahují řadu podporované datové typy, včetně složitých typů (může dokonce zahrnujeme komplexní typy v rámci jiné komplexní typy). Serializovaná JSON generovaných výše uvedený příklad, ale zobrazí důležitý bod. *Jak* pošleme dat pomocí **serializátor** knihovny určuje přesně jak je vytvořen ve formátu JSON. Daný okamžik je co probereme dále.

## <a name="more-about-serialization"></a>Další informace o serializaci

Předchozí část ukazuje příklad výstupu generovaných **serializátor** knihovny. V této části vysvětlíme, jak knihovna serializuje data a jak můžete řídit chování pomocí serializace rozhraní API.

Aby bylo možné předem diskuse o serializaci, budete spolupracujeme se nový model založený na termostat. Nejprve můžeme poskytují základní informace o scénář, ale My se snažíme adresu.

Chceme, aby k modelování termostat, která měří teploty a vlhkosti. Každá část dat bude odeslán do služby IoT Hub odlišně. Ve výchozím nastavení, ingresses termostat událostí teplotní jednou za 2 minut; událost vlhkosti je ingressed každých 15 minut. Když je buď událost ingressed, musí zahrnovat časové razítko, které ukazuje, že bylo měřeno odpovídající teploty a vlhkosti.

Tento scénář, ukážeme vám, dvěma různými způsoby pro modelování dat a vysvětlíme efekt, modelování má serializovaná výstupu.

### <a name="model-1"></a>Model 1

Zde je první verze modelu, který podporuje předchozího scénáře:

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

Všimněte si, že model zahrnuje dvě data události: **teploty** a **vlhkosti**. Na rozdíl od předchozích případech typ každé události je struktura definovány pomocí **DECLARE\_struktura**. **TemperatureEvent** zahrnuje měření teploty a časové razítko; **HumidityEvent** obsahuje měření vlhkosti a časové razítko. Tento model získáváme přirozený způsob modelování dat pro výše popsaného scénáře. Když budeme události odesílat do cloudu, budeme vám posílat buď teploty/časové razítko nebo pár vlhkosti/časové razítko.

Událostí teplotní nám můžete odeslat do cloudu pomocí kódu, jako jsou následující:

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

Jsme budete používat pevně definovaných hodnot pro teploty a vlhkosti v ukázkovém kódu, ale Představte si, že jsme se tyto hodnoty ve skutečnosti načítání vzorkováním odpovídající senzory na termostat.

Kód výše používá **GetDateTimeOffset** pomocné rutiny, které se zavedly dříve. Z důvodů, které se stanou vymazat novější Tento kód explicitně odděluje úkolu serializace a odesílá události. Předchozí kód serializuje událostí teplotní do vyrovnávací paměti. Potom **sendMessage** je pomocná funkce (součástí **simplesample\_amqp**), který odesílá události do služby IoT Hub:

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

Tento kód je podmnožinou **SendAsync** pomocné rutiny popsané v předchozí části, tak nebude koná nad ním tady.

Po spuštění předchozího kódu pro odesílání událostí teplotní jsme tento serializovanou formu událost je odeslána do služby IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Posíláme teploty, které je typu **TemperatureEvent** a obsahuje tuto strukturu **teploty** a **čas** člena. Toto je zohledněno přímo v serializovaných datech.

Podobně abychom vám mohli poslat vlhkosti událostí s tímto kódem:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Serializované podoby, která je odeslána do služby IoT Hub se zobrazí takto:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Důvodem jsou opět podle očekávání.

V tomto modelu, si dokážete představit, jak další události je možné snadno přidat. Můžete definovat další struktur pomocí **DECLARE\_struktura**a zahrnují příslušné události v modelu **WITH\_DATA**.

Teď Pojďme upravit model tak, že obsahují stejná data, ale s odlišnou strukturu.

### <a name="model-2"></a>Model 2

Vezměte v úvahu tato alternativní model ten výše:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

V tomto případě jsme odstranili **DECLARE\_struktura** makra a jednoduše definujete datové položky v našem scénáři použití jednoduchých typů z jazyka modelování.

Právě pro tuto chvíli přeskočit **čas** událostí. S doplňováním, tady je kód pro příchozí přenos dat **teploty**:

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

Tento kód odesílá následující serializovaná událostí do služby IoT Hub:

```C
{"Temperature":75}
```

A kód pro odesílání událostí vlhkosti, zobrazí se takto:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Tento kód tyto odešle do služby IoT Hub:

```C
{"Humidity":45}
```

Zatím nejsou stále žádná překvapení. Teď Změníme, jak použít makra SERIALIZACE.

**SERIALIZACE** – makro může trvat více dat události jako argumenty. Díky tomu můžeme serializovat **teploty** a **vlhkosti** události společně a odešlete je do služby IoT Hub v jednom volání:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Odhad může být, že výsledek tohoto kódu je, že do dvou datových událostí se odesílají do služby IoT Hub:

[{"Teploty": 75}, {"Vlhkosti": 45}]

Jinými slovy, by se dalo očekávat, že tento kód je stejný jako odesílání **teploty** a **vlhkosti** samostatně. Je právě usnadnění a předat do obou událostí **SERIALIZACE** ve stejném volání. Ale to není případ. Místo toho výše uvedený kód odešle tato událost jednoho datového do služby IoT Hub:

{"Teploty": 75, "vlhkosti": 45}

To může zdát neobvyklé, protože definuje náš model **teploty** a **vlhkosti** jako dvě *samostatné* události:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Více do bodu, jsme neměli model tyto události kde **teploty** a **vlhkosti** jsou ve stejné struktuře:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Použili tento model, bylo by lépe pochopit, jak **teploty** a **vlhkosti** by se odeslaly v stejná serializovaná zpráva. Ale nemusí být zřejmé, proč to funguje tímto způsobem při předání obou data událostí do **SERIALIZACE** pomocí modelu 2.

Toto chování je lze snáze pochopit, pokud víte, předpoklady, které **serializátor** dosahuje knihovny. Dávat smysl to Vraťme se k naší modelu:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Představte si tento model v objektově orientované podmínky. V tomto případě jsme už modelujete fyzické zařízení (termostat) a toto zařízení obsahuje atributy, jako je **teploty** a **vlhkosti**.

Abychom vám mohli poslat celý náš model se například následující kód stavu:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Za předpokladu, že hodnoty teploty, vlhkosti a čas je nastaveno, uvidíme nějakou událost, třeba to odeslaných do služby IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Někdy může pouze chcete odeslat *některé* vlastnosti modelu do cloudu (to je obzvláště hodnotu true, pokud váš model obsahuje velké množství dat událostí). Je užitečné odeslat pouze podmnožinu dat události, jako například v našem příkladu starší:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Tím se vygeneruje přesně stejnou událost serializovaná jakoby jsme definovali **TemperatureEvent** s **teploty** a **čas** člen, podobně jako jsme s model 1. V tomto případě jsme byli schopni generovat přesně stejnou událost serializované pomocí jiný model (model 2), protože jsme volat **SERIALIZACE** jiným způsobem.

Důležité je, že Pokud předáváte více dat událostí do **SERIALIZACE,** pak předpokládá, že každá událost představuje vlastnost v jeden objekt JSON.

Nejlepším řešením, závisí na vás a jak uvažovat o váš model. Pokud odesíláte "události" do cloudu a definovaná sada vlastností obsahuje každou událost, prvním přístupem je velmi výhodné. V takovém případě byste použili **DECLARE\_struktura** definují strukturu každé události a zahrnout je do modelu pomocí **WITH\_DATA** – makro. Pak odešlete každé události jako jsme to udělali v prvním příkladu výše. V rámci tohoto přístupu pouze předáte na jednoho datového událost **SERIALIZÁTOR**.

Pokud si myslíte o váš model podporuje objektově orientované, může vyhovovat druhým přístupem je. V takovém případě elementy definované pomocí **WITH\_DATA** jsou "vlastnosti" objektu. Předejte libovolné podmnožinu událostí do **SERIALIZACE** , který vás zajímá, podle toho, jak velká část vašich "" stav objektu chcete odeslat do cloudu.

Nether přístup je chybný nebo vpravo. Právě Uvědomte si, jak **serializátor** knihovny funguje a vybrat, přístup k modelování, který nejlépe vyhovuje vašim potřebám.

## <a name="message-handling"></a>Zpracování zpráv

Zatím Tento článek obsahuje pouze popsané odesílání událostí do služby IoT Hub a nebyla určena příjem zpráv. Důvod pro to je, že potřebujeme vědět o přijímání zpráv má z velké části popsaná v článku [zařízení Azure IoT SDK pro jazyk C](iot-hub-device-sdk-c-intro.md). Z tohoto článku navrácení, zpracování zpráv tak, že zaregistrujete funkce zpětného volání zpráv:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Potom napíšete funkce zpětného volání, která se vyvolá při doručení zprávy do:

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

Tato implementace **IoTHubMessage** volá funkci specifické pro každou akci v modelu. Pokud například váš model definuje tato akce:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Je nutné definovat funkci s Tento podpis:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** potom je volána při odeslání zprávy do vašeho zařízení.

Co jsme ještě vysvětlení ještě vypadá serializovaná verze zprávy. Jinými slovy Pokud chcete odeslat **SetAirResistance** zprávu do vašeho zařízení, co tento vzhled, jako jsou?

Pokud odesíláte zprávy do zařízení, bude to provést prostřednictvím sady SDK služby Azure IoT. Dál potřebujete vědět, jaké řetězec k odeslání na volání určité akce. Obecný formát pro odeslání zprávy, se zobrazí takto:

```C
{"Name" : "", "Parameters" : "" }
```

E-mail posíláte serializovaný objekt JSON s dvě vlastnosti: **název** je název akce (zpráva) a **parametry** obsahuje parametry akce.

Pro příklad, který má být vyvolán **SetAirResistance** tuto zprávu můžete odeslat do zařízení:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Název akce musí přesně odpovídat akci v modelu definován. Názvy parametrů musí odpovídat také. Všimněte si také rozlišování velikosti písmen. **Název** a **parametry** jsou vždy velká písmena. Ujistěte se, že rozlišovat velikost písmen názvu akce a parametry v modelu. V tomto příkladu je název akce "SetAirResistance" a nikoli "setairresistance".

Další dvě akce **TurnFanOn** a **TurnFanOff** lze vyvolat odesláním tyto zprávy na zařízení:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Tato část popisuje vše, co potřebujete znát při odesílání událostí a příjem zpráv s **serializátor** knihovny. Než budete pokračovat, Pojďme zahrnují některé parametry, které můžete nakonfigurovat, které řídí, jak velké je váš model.

## <a name="macro-configuration"></a>Konfigurace – makro

Pokud používáte **serializátor** knihovny je důležitou součástí sady SDK je potřeba vědět najdete v knihovně azure-c sdílené – nástroj.

Pokud jste naklonovali z Githubu pomocí možnosti--rekurzivní úložiště Azure-iot-sdk-c, zjistíte této nástroj sdílené knihovny:

```C
.\\c-utility
```

Pokud ještě jste naklonovali knihovny, najdete ho [tady](https://github.com/Azure/azure-c-shared-utility).

V rámci knihovny sdílené nástroje se nalézt následující složku:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Tato složka obsahuje řešení sady Visual Studio volá **– makro\_utils\_h\_generator.sln**:

  ![Snímek obrazovky maco_utils_h_generator řešení sady Visual Studio](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Vygeneruje program v tomto řešení **– makro\_utils.h** souboru. Je výchozí makro\_utils.h soubor součástí sady SDK. Toto řešení umožňuje změnit některé parametry a pak znovu vytvořte soubor hlaviček na základě těchto parametrů.

Jsou dvě klíčové parametry zabývat **nArithmetic** a **nMacroParameters** které jsou definovány v těchto dvou řádcích nalezen v makru\_utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Tyto hodnoty jsou součástí sady SDK výchozí parametry. Každý parametr má následující význam:

* nMacroParameters – Určuje, jak velký počet parametrů může mít v jedné DECLARE\_definici makra modelu.
* nArithmetic – Určuje celkový počet členů v modelu povolené.

Z důvodů, proč tyto parametry jsou důležité je vzhledem k tomu, jakým se řídí, jak velká může být váš model. Zvažte například tuto definici modelu:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Jak už bylo zmíněno dříve, **DECLARE\_modelu** je právě C – makro. Název modelu a **WITH\_DATA** – příkaz (ale jiné – makro) jsou parametry **DECLARE\_modelu**. **nMacroParameters** definuje, jak velký počet parametrů. mohou být součástí **DECLARE\_modelu**. Definuje efektivně, kolik data události a akce deklarace může mít. V důsledku toho se výchozí omezení 124 to znamená, že můžete definovat model pomocí kombinace přibližně 60 akce a data události. Pokud se pokusíte překročit tento limit, dostanete chyb kompilátoru, které vypadají podobně jako tato:

  ![Snímek obrazovky chyby kompilátoru parametry – makro](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

**NArithmetic** parametr je další informace o vnitřní činnost jazyk – makro než vaší aplikace.  Určuje celkový počet členů může mít v modelu, včetně **DECLARE_STRUCT** makra. Pokud jste začnou zobrazovat chyby kompilátoru například to, pak byste měli zvyšuje **nArithmetic**:

   ![Snímek obrazovky chyby aritmetického kompilátoru](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Pokud chcete tyto parametry měnit, upravte hodnoty v makru\_utils.tt soubor, znovu zkompilovat makro\_utils\_h\_generator.sln řešení a spuštění zkompilovaného programu. Pokud tak učiníte, nové makro\_utils.h souboru je generovány a umístěny do.\\ běžné\\celkové adresáře.

Chcete-li používat novou verzi – makro\_utils.h, odeberte **serializátor** balíček NuGet z vašeho řešení a místo něj zahrnovat **serializátor** projektu sady Visual Studio. Díky tomu váš kód mohl zkompilovat pro zdrojový kód knihovny serializátor. Jedná se o aktualizovanou – makro\_utils.h. Pokud chcete, aby to udělal za **simplesample\_amqp**, začněte tím, že odebrání balíčku NuGet pro knihovny serializátor z řešení:

   ![Snímek obrazovky se odebírá se balíček NuGet knihovny serializátoru](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Pak přidejte tento projekt do řešení sady Visual Studio:

> .\\c\\serializer\\build\\windows\\serializer.vcxproj
> 
> 

Jakmile budete hotovi, vaše řešení by měl vypadat nějak takto:

   ![Snímek obrazovky simplesample_amqp řešení sady Visual Studio](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Nyní při kompilaci vašeho řešení, aktualizované – makro\_utils.h je součástí binární soubor.

Všimněte si, že zvýšení tyto hodnoty dostatečně vysoká, může překročit omezení kompilátoru. Do této chvíle **nMacroParameters** je hlavní parametr, pomocí kterého se to týká. Specifikace C99 Určuje, že minimálně 127 parametry jsou povoleny v definici makra. Kompilátor společnosti Microsoft následuje specifikace přesně (a má maximálně 127), tak nebude možné zvýšit **nMacroParameters** nad výchozí. Jiné kompilátory vás k tomu může povolit (třeba GNU kompilátor podporuje vyšší limit).

Zatím věnovali jsme téměř vše, co potřebujete vědět o tom, jak psát kód s **serializátor** knihovny. Před uzavřením, Vraťme se k některá témata z předchozí článků, které vás může zajímat o.

## <a name="the-lower-level-apis"></a>Rozhraní API služby nižší úrovně
Ukázková aplikace, na kterém se tento článek zaměřuje je **simplesample\_amqp**. Tento příklad používá vyšší úrovni (non -**LL**) rozhraní API pro odesílání událostí a příjem zpráv. Pokud vás pomocí těchto rozhraní API, vlákna na pozadí se spustí, který postará o odesílání událostí a příjem zpráv. Ale můžete použít rozhraní API nižší úrovni (vše) k odstranění tohoto vlákna na pozadí a provést explicitní kontrolu nad při odesílání událostí a příjem zpráv z cloudu.

Jak je popsáno v [předchozím článku](iot-hub-device-sdk-c-iothubclient.md), je sada funkcí, která se skládá z vyšší úrovně rozhraní API:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* Pro IoTHubClient\_SetMessageCallback
* Pro IoTHubClient\_zničit

Tato rozhraní API je ukázán v **simplesample\_amqp**.

K dispozici je také obdobná sadu rozhraní API nižší úrovně.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* Pro IoTHubClient\_LL\_SetMessageCallback
* Pro IoTHubClient\_LL\_zničit

Všimněte si, že nižší úrovně rozhraní API fungovat stejným způsobem, jak je popsáno v předchozí články. První sada rozhraní API můžete použít, pokud chcete, aby vlákno na pozadí pro zpracování událostí odesílání a přijímání zpráv. Druhá sada rozhraní API použijte, pokud chcete mít explicitní kontrolu nad při odesílání a příjem dat ze služby IoT Hub. Buď sadu rozhraní API pro práci stejně dobře s **serializátor** knihovny.

Příklad použití nižší úrovně rozhraní API se **serializátor** knihovny, najdete v článku **simplesample\_http** aplikace.

## <a name="additional-topics"></a>Další témata
Několik dalších témat, která stojí za zmínku, znovu se vlastnost zpracování, pomocí zařízení s alternativní přihlašovací údaje a možnosti konfigurace. Toto jsou zahrnuté ve všech témat [předchozím článku](iot-hub-device-sdk-c-iothubclient.md). Nejdůležitější je, že všechny tyto funkce fungují stejně jako s **serializátor** knihovny stejně jako s **pro IoTHubClient** knihovny. Například pokud chcete přiřadit vlastnosti události z vašeho modelu, použijete **IoTHubMessage\_vlastnosti** a **mapy**\_**AddorUpdate**, stejným způsobem, jak je popsáno dříve:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Zda byla událost vygenerována z **serializátor** knihovny nebo vytvořit ručně pomocí **pro IoTHubClient** knihovny nezáleží.

Pro zařízení s alternativní přihlašovací údaje, pomocí **pro IoTHubClient\_LL\_vytvořit** funguje stejně jako **pro IoTHubClient\_CreateFromConnectionString** pro přidělení **IOTHUB\_klienta\_zpracování**.

Nakonec pokud používáte **serializátor** knihovny, můžete nastavit možnosti konfigurace se **pro IoTHubClient\_LL\_SetOption –** stejně jako jste to udělali při použití **Pro IoTHubClient** knihovny.

Funkce, která je jedinečné pro **serializátor** knihovny se inicializace rozhraní API. Před zahájením práce s knihovnou, je nutné volat **serializátor\_init**:

```C
serializer_init(NULL);
```

To se provádí těsně před voláním **pro IoTHubClient\_CreateFromConnectionString**.

Podobně po dokončení práce s knihovnou, poslední volání provede je **serializátor\_deinit**:

```C
serializer_deinit();
```

V opačném případě všechny ostatní funkce uvedené výše fungovat stejně **serializátor** knihovny stejně jako **pro IoTHubClient** knihovny. Další informace o těchto tématech najdete v článku [předchozím článku](iot-hub-device-sdk-c-iothubclient.md) této série.

## <a name="next-steps"></a>Další postup

Tento článek podrobně popisuje jedinečné aspekty **serializátor** obsažené v knihovně **zařízení Azure IoT SDK pro jazyk C**. Informace k dispozici, že byste měli mít dobrý přehled o tom, jak používat modely k odesílání událostí a příjem zpráv ze služby IoT Hub.

Tím končí také třídílný seriál o tom, jak vyvíjet aplikace s **zařízení Azure IoT SDK pro jazyk C**. To by měl být dostatek informací, které nejen vám pomůžou začít, ale poskytne vám pečlivého pochopení fungování rozhraní API. Další informace nejsou v sadě SDK není součástí tohoto pár ukázek. V opačném případě [dokumentace ke službě Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-c) je vhodným místem k zadání dalších informací.

Další informace o vývoji pro službu IoT Hub, najdete v článku [sad SDK Azure IoT](iot-hub-devguide-sdks.md).

Chcete-li podrobněji prozkoumat možnosti služby IoT Hub, přečtěte si téma [nasazování AI do hraničních zařízení pomocí služby Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).