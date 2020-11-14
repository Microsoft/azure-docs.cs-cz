---
title: 'Koncepty: mapování šablon v Azure IoT Connectoru pro funkci FHIR (Preview) rozhraní API Azure pro FHIR'
description: Naučte se vytvořit dva typy mapování šablon v Azure IoT Connectoru pro FHIR (Preview). Šablona mapování zařízení transformuje data zařízení do normalizovaného schématu. Šablona mapování FHIR transformuje normalizovanou zprávu na prostředek pozorování založený na FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 63484361a6d5a331fd9dc646c53627918ce8b246
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630545"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Šablony mapování konektoru Azure IoT pro FHIR (Preview)
Tento článek podrobně popisuje, jak nakonfigurovat Azure IoT Connector pro rychlé interoperability zdravotnictví (FHIR&#174;) * pomocí mapování šablon.

Konektor Azure IoT pro FHIR vyžaduje dva typy šablon mapování založených na JSON. Prvním typem je **mapování zařízení** , které zodpovídá za mapování datových částí zařízení odeslaných do `devicedata` koncového bodu centra událostí Azure. Extrahuje typy, identifikátory zařízení, datum a čas měření a hodnoty měření. Druhý typ, **mapování FHIR** , řídí mapování pro prostředek FHIR. Umožňuje konfiguraci délky období pozorování, datového typu FHIR, který slouží k ukládání hodnot, a kódů terminologie. 

Šablony mapování se skládají do dokumentu JSON na základě jejich typu. Tyto dokumenty JSON se pak přidají do vašeho konektoru Azure IoT Connector pro FHIR prostřednictvím Azure Portal. Dokument mapování zařízení se přidá prostřednictvím stránky **Konfigurace zařízení** mapování a dokumentu mapování FHIR prostřednictvím stránky **Konfigurace mapování FHIR** .

> [!NOTE]
> Mapování šablon je uloženo v podkladovém úložišti objektů BLOB a načteno z objektu BLOB na výpočetní výkon. Po aktualizaci by se měla projevit okamžitě. 

## <a name="device-mapping"></a>Mapování zařízení
Mapování zařízení poskytuje funkce mapování pro extrakci obsahu zařízení do společného formátu pro další vyhodnocení. Každá přijatá zpráva se vyhodnocuje proti všem šablonám. Tento přístup umožňuje promítnout jednu příchozí zprávu do několika odchozích zpráv, které jsou později namapovány na různé pozorování v FHIR. Výsledkem je normalizovaný datový objekt představující hodnotu nebo hodnoty, které jsou analyzovány šablonami. Normalizovaný datový model má několik požadovaných vlastností, které musí být nalezeny a extrahovány:

| Vlastnost | Popis |
| - | - |
|**Typ**|Název/typ pro klasifikaci měření. Tato hodnota se používá k vytvoření vazby na požadovanou šablonu mapování FHIR.  Více šablon může mít výstup na stejný typ, což vám umožní namapovat různé reprezentace napříč různými zařízeními na jeden běžný výstup.|
|**OccurenceTimeUtc**|Čas, kdy došlo k měření.|
|**DeviceId**|Identifikátor zařízení. Tato hodnota by se měla shodovat s identifikátorem prostředku zařízení, který existuje v cílovém FHIR serveru.|
 |**Vlastnosti**|Extrahujte alespoň jednu vlastnost, aby bylo možné hodnotu Uložit do vytvořeného prostředku pro sledování.  Vlastnosti jsou kolekce párů hodnot klíčů extrahovaných během normalizace.|

Níže je uveden koncepční příklad toho, co se děje během normalizace.

![Příklad normalizace](media/concepts-iot-mapping-templates/normalization-example.png)

Samotná datová část obsahu je zpráva centra událostí Azure, která se skládá ze tří částí: body, vlastnosti a SystemProperties. `Body`Je pole bajtů reprezentující řetězec kódovaný v kódování UTF-8. Při vyhodnocování šablony je pole bajtů automaticky převedeno na hodnotu řetězce. `Properties` je kolekce hodnot klíče pro použití tvůrcem zprávy. `SystemProperties` je také kolekce hodnot klíče vyhrazená architekturou centra událostí Azure s položkami, které automaticky naplní.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>Mapování s cestou JSON
Současné podporované typy šablon obsahu zařízení spoléhají na cestu JSON, aby odpovídaly požadované šabloně a extrahované hodnoty. Další informace o cestě JSON najdete [tady](https://goessner.net/articles/JsonPath/). Oba typy šablon používají [implementaci rozhraní JSON .NET](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) pro překlad výrazů cesty JSON.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate umožňuje porovnání a extrakci hodnot z zprávy centra událostí pomocí cesty JSON.

| Vlastnost | Popis |<div style="width:150px">Příklad</div>
| --- | --- | --- 
|**Popisuje**|Typ, který se má přidružit k měřením, která odpovídají šabloně.|`heartrate`
|**TypeMatchExpression**|Výraz cesty JSON, který se vyhodnocuje s datovou částí centra událostí. Pokud se najde odpovídající JToken, šablona se považuje za shodu. Všechny následné výrazy jsou vyhodnocovány proti extrahovanému JToken, který se shoduje.|`$..[?(@heartRate)]`
|**TimestampExpression**|Výraz cesty JSON pro extrakci hodnoty časového razítka pro OccurenceTimeUtc měření.|`$.endDate`
|**DeviceIdExpression**|Výraz cesty JSON pro extrakci identifikátoru zařízení.|`$.deviceId`
|**PatientIdExpression**|*Volitelné* : výraz cesty JSON pro extrakci identifikátoru pacienta.|`$.patientId`
|**EncounterIdExpression**|*Volitelné* : výraz cesty JSON pro extrakci identifikátoru výskytu.|`$.encounterId`
|**Hodnoty []. Hodnoty**|Název, který se má přidružit k hodnotě extrahované následným výrazem. Slouží k vytvoření vazby požadované hodnoty/komponenty v šabloně mapování FHIR. |`hr`
|**Hodnoty []. ValueExpression**|Výraz cesty JSON pro extrakci požadované hodnoty.|`$.heartRate`
|**Hodnoty []. Požadovanou**|Bude vyžadovat, aby byla hodnota přítomna v datové části.  Pokud se nenajde, měření se nevygeneruje a vyvolá se akce InvalidOperationException.|`true`

##### <a name="examples"></a>Příklady
---
**Rychlost srdce**

*Zpráva*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Šablona*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Krevní tlak**

*Zpráva*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Šablona*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceid",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**Vícenásobná měření projektu z jedné zprávy**

*Zpráva*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Šablona 1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*Šablona 2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**Vícenásobná měření z pole ve zprávě**

*Zpráva*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Šablona*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate
IotJsonPathContentTemplate je podobný jako JsonPathContentTemplate s výjimkou DeviceIdExpression a TimestampExpression není vyžadováno.

Předpokladem použití této šablony jsou vyhodnocené zprávy pomocí [sad SDK pro zařízení Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks). Pokud používáte tyto sady SDK, je identita zařízení (za předpokladu, že je identifikátor zařízení z Azure IoT Hub/Central registrovaný jako identifikátorem pro prostředek zařízení na cílovém serveru FHIR) a že se říká časové razítko zprávy. Pokud používáte sady SDK pro zařízení IoT Hub Azure, ale používáte vlastní vlastnosti v textu zprávy pro časové razítko pro identitu zařízení nebo měření, můžete i nadále používat JsonPathContentTemplate.

*Poznámka: při použití IotJsonPathContentTemplate by měl TypeMatchExpression překládat na celou zprávu jako na JToken. Podívejte se na následující příklady.* 
##### <a name="examples"></a>Příklady
---
**Rychlost srdce**

*Zpráva*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Šablona*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Krevní tlak**

*Zpráva*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Šablona*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

## <a name="fhir-mapping"></a>Mapování FHIR
Po extrakci obsahu zařízení do normalizovaného modelu se data shromažďují a seskupují podle identifikátoru zařízení, typu měření a časového období. Výstup tohoto seskupení se pošle na převod do prostředku FHIR (aktuálně se[pozorování](https://www.hl7.org/fhir/observation.html) ). Zde šablona mapování FHIR určuje, jak jsou data namapována na FHIR sledování. Má se vytvořit pozorování k určitému bodu v čase nebo za určitou hodinu? Jaké kódy by se měly přidat do sledování? Měla by být hodnota reprezentovaná jako [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) nebo [množství](https://www.hl7.org/fhir/datatypes.html#Quantity)? Tyto datové typy jsou všechny možnosti ovládacích prvků konfigurace mapování FHIR.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
CodeValueFhirTemplate je v současné době jedinou šablonou podporovanou v mapování FHIR.  Umožňuje definovat kódy, efektivní období a hodnotu sledování. Podporuje se více typů hodnot: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)a [množství](https://www.hl7.org/fhir/datatypes.html#Quantity). Společně s těmito konfigurovatelnými hodnotami se automaticky zpracovávají identifikátory prostředku sledování a propojuje se se správnými prostředky zařízení a pacienta.

| Vlastnost | Popis 
| --- | ---
|**Popisuje**| Typ měření, ke kterému má být tato šablona vytvořena. Měl by existovat alespoň jedna šablona mapování zařízení, která tento typ vyprodukuje.
|**PeriodInterval**|Časové období, po které by se měly vytvářené poznámky vystupovat. Podporované hodnoty jsou 0 (instance), 60 (a hodina), 1440 (za den).
|**Kategorie**|Libovolný počet [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) pro klasifikaci typu vytvářené pozorování.
|**Kódování**|Jeden nebo více [kódů](http://hl7.org/fhir/datatypes-definitions.html#coding) , které mají být použity pro vytvořené sledování.
|**Kódy []. Znakovou**|Kód pro [kódování](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Kódy []. Souborů**|Systém pro [kódování](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Kódy []. Otevřete**|Zobrazení pro [kódování](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Hodnota**|Hodnota, která má být extrahována a reprezentována ve sledování. Další informace naleznete v tématu [šablony typů hodnot](#valuetypes).
|**Komponenty**|*Volitelné:* Jedna nebo více součástí, které se mají vytvořit na sledování.
|**Součásti []. Kódování**|Jeden nebo více [kódů](http://hl7.org/fhir/datatypes-definitions.html#coding) , které mají být použity pro komponentu.
|**Součásti []. Osa**|Hodnota, která má být extrahována a reprezentována v komponentě. Další informace naleznete v tématu [šablony typů hodnot](#valuetypes).

### <a name="value-type-templates"></a>Šablony typů hodnot <a name="valuetypes"></a>
Níže jsou uvedeny aktuálně podporované šablony typů hodnot. V budoucnu mohou být přidány další šablony.
#### <a name="sampleddata"></a>SampledData
Představuje datový typ [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR. Měření měření se zapisují do datového proudu, který začíná v určitém časovém okamžiku, a zvyšuje se vpřed pomocí definovaného období. Pokud není k dispozici žádná hodnota, `E` zapíše se do datového proudu. Pokud je toto období takové, že dvě další hodnoty zaujímají stejnou pozici v datovém proudu, použije se nejnovější hodnota. Stejná logika se použije, když se aktualizuje sledování pomocí SampledData.

| Vlastnost | Popis 
| --- | ---
|**DefaultPeriod**|Výchozí perioda v milisekundách, která se má použít. 
|**Jednotka**|Jednotka, která má být nastavena na počátek SampledData. 

#### <a name="quantity"></a>Množství
Představuje datový typ FHIR [množství](http://hl7.org/fhir/datatypes.html#Quantity) . Pokud je ve seskupení přítomna více než jedna hodnota, použije se pouze první hodnota. Když se dokončí nová hodnota, která se mapuje na stejné monitorování, přepíše starou hodnotu.

| Vlastnost | Popis 
| --- | --- 
|**Jednotka**| Reprezentace jednotky.
|**Kód**| Kódovaný tvar jednotky
|**Systém**| Systém, který definuje formulář kódované jednotky.

### <a name="codeableconcept"></a>CodeableConcept
Představuje datový typ [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR. Skutečná hodnota se nepoužívá.

| Vlastnost | Popis 
| --- | --- 
|**Text**|Reprezentace prostého textu. 
|**Kódování**|Jeden nebo více [kódů](http://hl7.org/fhir/datatypes-definitions.html#coding) , které mají být použity pro vytvořené sledování.
|**Kódy []. Znakovou**|Kód pro [kódování](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Kódy []. Souborů**|Systém pro [kódování](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Kódy []. Otevřete**|Zobrazení pro [kódování](http://hl7.org/fhir/datatypes-definitions.html#coding).

### <a name="examples"></a>Příklady
**Rychlost srdce – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**Kroky – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**Krevní tlak – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**Krevní tlak – množství**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**Odebrané zařízení – CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy ke službě Azure IoT Connector pro FHIR (Preview).

>[!div class="nextstepaction"]
>[Azure IoT Connector pro nejčastější dotazy k FHIR](fhir-faq.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview). FHIR je registrovaná ochranná známka změněného HL7 a používá se s oprávněním změněného HL7.