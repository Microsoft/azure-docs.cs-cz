---
title: Exportovat data z IoT Central | Microsoft Docs
description: Jak používat novou export dat k exportu dat IoT do Azure a vlastních cílů cloudu.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: f51630154b77233aeb2587ac3a2d603c1da6fa4f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036551"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>Export dat IoT do cílů cloudu pomocí exportu dat (Preview)

> [!Note]
> Hledáte starší verzi exportu dat? Dokumenty pro export dat najdete [tady](./howto-export-data.md). Další informace o rozdílech mezi novým exportem dat a exportem dat starší verze najdete v [srovnávací tabulce](#comparison-of-legacy-data-export-and-new-data-export).

Tento článek popisuje, jak používat nové funkce pro export dat ve verzi Preview v Azure IoT Central. Pomocí této funkce můžete průběžně exportovat filtrovaná a obohacená data IoT do svých cloudových služeb. Export dat můžete použít k nahrání změn téměř v reálném čase do dalších částí vašeho cloudového řešení, které vám umožní získat přehledy, analýzu a úložiště. 

 Můžete například:
-   Průběžně exportovat data telemetrie a změny vlastností ve formátu JSON prakticky v reálném čase
-   Filtrováním těchto datových proudů můžete exportovat konkrétní možnosti, které odpovídají vlastním podmínkám.
-   Obohacení datových proudů o vlastní hodnoty a hodnoty vlastností ze zařízení
-   Odeslat tato data do cílových umístění, jako jsou například Azure Event Hubs, Azure Service Bus, Azure Blob Storage a koncových bodů Webhooku

> [!Note]
> Když zapnete export dat, dostanete od tohoto okamžiku pouze data. V současné době nelze data po vypnutí exportu dat načíst. Pokud chcete zachovat více historických dat, zapněte nejdříve export dat.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít export dat (Preview), musíte mít aplikaci v3 a musíte mít oprávnění pro export dat.

## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Před konfigurací exportu dat musí existovat váš cíl exportu. Jedná se o dostupné typy cílů:
  - Azure Event Hubs
  - Fronta služby Azure Service Bus
  - Téma služby Azure Service Bus
  - Azure Blob Storage
  - Webhook

### <a name="create-an-event-hubs-destination"></a>Vytvoření cíle Event Hubs

Pokud nemáte existující Event Hubs obor názvů pro export do, postupujte podle těchto kroků:

1. Vytvořte [Nový obor názvů Event Hubs v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Další informace najdete v [dokumentaci k Azure Event Hubs](../../event-hubs/event-hubs-create.md).

2. Vytvořte centrum událostí v oboru názvů Event Hubs. Vytvořte instanci centra událostí tak, že přejdete do svého oboru názvů a vyberete **+ centrum událostí** v horní části.

3. Vygenerujte klíč, který použijete v IoT Central k nastavení exportu dat. 
    - Klikněte na instanci centra událostí, kterou jste vytvořili. 
    - Klikněte na **nastavení/zásady sdíleného přístupu**. 
    - Vytvořte nový klíč nebo vyberte existující klíč, který má oprávnění **Odeslat** . 
    - Zkopírujte buď primární nebo sekundární připojovací řetězec. Použijete ho k nastavení nového cíle v IoT Central.

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Vytvoření Service Bus fronty nebo cíle tématu

Pokud nemáte existující Service Bus obor názvů pro export do, postupujte podle těchto kroků:

1. Vytvořte [Nový obor názvů Service Bus v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Další informace najdete v [dokumentaci Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).

2. Chcete-li vytvořit frontu nebo téma pro export do, přejít na obor názvů Service Bus a vybrat **+ fronta** nebo **+ téma**.

3. Vygenerujte klíč, který použijete v IoT Central k nastavení exportu dat. 
    - Klikněte na vytvořenou frontu nebo téma. 
    - Klikněte na **nastavení/zásady sdíleného přístupu**. 
    - Vytvořte nový klíč nebo vyberte existující klíč, který má oprávnění **Odeslat** . 
    - Zkopírujte buď primární nebo sekundární připojovací řetězec. Použijete ho k nastavení nového cíle v IoT Central.

### <a name="create-an-azure-blob-storage-destination"></a>Vytvoření cíle Azure Blob Storage

Pokud nemáte existující účet úložiště Azure pro export do, postupujte takto:

1. Vytvořte [nový účet úložiště v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Můžete si přečíst další informace o vytváření nových [účtů úložiště Azure Blob](https://aka.ms/blobdocscreatestorageaccount) nebo [Azure Data Lake Storagech účtů úložiště v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md). Export dat může zapisovat jenom data do účtů úložiště, které podporují objekty blob bloku. Následující seznam uvádí známé kompatibilní typy účtů úložiště:

    |Úroveň výkonu|Typ účtu|
    |-|-|
    |Standard|Pro obecné účely v2|
    |Standard|Pro obecné účely v1|
    |Standard|Blob Storage|
    |Premium|Úložiště objektů blob bloku|

2. Vytvořte kontejner v účtu úložiště. Přejít na účet úložiště. V části **BLOB Service**vyberte **Procházet objekty blob**. V horní části vyberte **+ kontejner** a vytvořte nový kontejner.

3. Přejděte na **Nastavení/přístupové klíče**a vytvořte připojovací řetězec pro váš účet úložiště. Zkopírujte jeden ze dvou připojovacích řetězců.

### <a name="create-a-webhook-endpoint"></a>Vytvoření koncového bodu Webhooku
Pro data, která se mají exportovat do, můžete poskytnout veřejně dostupný koncový bod HTTP. Pomocí RequestBin můžete vytvořit testovací koncový bod Webhooku. Mějte na paměti, že RequestBin má nastaven limit požadavků ještě předtím, než se požadavky omezí.

1. Otevřete [RequestBin](https://requestbin.net/).
2. Vytvořte novou RequestBin a zkopírujte adresu URL přihrádky.

## <a name="set-up-data-export"></a>Nastavení exportu dat

Teď, když máte cíl pro export dat, postupujte podle těchto kroků a nastavte export dat.

1. Přihlaste se k aplikaci IoT Central.

2. V levém podokně vyberte **exportovat data**.

    > [!Tip]
    > Pokud nevidíte **exportovat data** v levém podokně, nemáte oprávnění ke konfiguraci exportu dat ve vaší aplikaci. Pokud chcete nastavit export dat, obraťte se na správce.

3. Vyberte tlačítko **+ Nový export** . 

4. Zadejte zobrazovaný název pro nový export a ujistěte se, že je zapnutý přepínač **zapnuto** , aby se data mohla přesměrovat.

## <a name="1-choose-the-type-of-data-to-export"></a>1. Vyberte typ dat k exportu.
Můžete si vybrat mezi průběžným exportem různých typů dat. Tady jsou podporované datové typy:

| Datový typ | Popis | Formát dat |
| :------------- | :---------- | :----------- |
|  Telemetrie | Exportujte zprávy telemetrie ze zařízení téměř v reálném čase. Každá exportovaná zpráva bude obsahovat úplný obsah původní zprávy zařízení, normalizovat.   |  [Formát zprávy telemetrie](#telemetry-format)   |
| Změny vlastností | Exportujte změny do vlastností zařízení a cloudu téměř v reálném čase. U vlastností zařízení jen pro čtení se exportují změny hlášených hodnot. Pro vlastnosti pro čtení i zápis budou všechny hlášené i požadované hodnoty exportovány. | [Formát zprávy o změně vlastnosti](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2. (volitelné) přidat filtry
Přidejte filtry pro snížení objemu exportovaných dat na základě podmínek filtru. Pro každý typ dat k exportu jsou k dispozici různé typy filtrů.

### <a name="telemetry-filters"></a>Filtry telemetrie
  - **Filtr schopností**: Pokud zvolíte položku telemetrie v rozevíracím seznamu, bude exportovaný datový proud obsahovat jenom telemetrii, která splňuje podmínku filtru. Pokud v rozevíracím seznamu zvolíte položku pro zařízení nebo cloudovou vlastnost, exportovaný datový proud bude obsahovat jenom telemetrii ze zařízení, která mají vlastnosti odpovídající podmínce filtru.
  - **Filtr vlastností zpráv**: zařízení používající sady SDK pro zařízení mají povoleno odesílat *vlastnosti zprávy* nebo *Vlastnosti aplikace* v každé zprávě telemetrie, což je kontejner párů klíč-hodnota obecně používaný k označení zprávy pomocí vlastních identifikátorů. Filtr vlastností zprávy můžete vytvořit tak, že zadáte klíč vlastnosti zprávy, který hledáte, a určíte podmínku. Budou exportovány pouze zprávy telemetrie, které mají vlastnosti zprávy, které odpovídají zadané podmínce filtru. Jsou podporovány pouze operátory porovnání řetězců (je rovno, není rovno, obsahuje, neexistuje, existuje). [Přečtěte si další informace o vlastnostech aplikace z IoT Hub docs](../../iot-hub/iot-hub-devguide-messages-construct.md).

### <a name="property-changes-filters"></a>Filtry změn vlastností
**Filtr vlastností**: vyberte položku Vlastnosti v rozevíracím seznamu a exportovaný datový proud bude obsahovat pouze změny vybrané vlastnosti, která splňuje podmínku filtru.

## <a name="3-optional-add-enrichments"></a>3. (volitelné) přidání rozšíření
Přidejte obohacení pro obohacení exportovaných zpráv s dalšími metadaty v páru klíč-hodnota. Tato rozšíření jsou dostupná pro úpravy telemetrie a vlastností datových typů:
  - **Vlastní řetězec**: přidá do každé zprávy vlastní statický řetězec. Zadejte libovolný klíč a zadejte libovolnou hodnotu řetězce.
  - **Vlastnost**: přidá do každé zprávy aktuální nahlášenou vlastnost nebo hodnotu vlastnosti cloudu. Zadejte libovolný klíč a vyberte vlastnost zařízení nebo cloudu. Pokud je vyexportovaná zpráva ze zařízení, které nemá zadanou vlastnost zařízení nebo cloudu, exportovaná zpráva nebude mít tato rozšíření.

## <a name="4-add-destinations"></a>4. Přidejte cíle.
Vytvořte nový cíl nebo přidejte cíl, který jste již vytvořili. 
  
1. Klikněte na odkaz **vytvořit nový cíl** . Zadejte následující informace:
    - **Název cíle**: zobrazovaný název cíle v IoT Central
    - **Cílový typ**: Vyberte typ cíle. Pokud jste to ještě neudělali, [nastavte cíl exportu](#set-up-export-destination) .
    - Pro Azure Event Hubs Azure Service Bus Queue nebo téma vložte připojovací řetězec pro váš prostředek. 
    - Pro Azure Blob Storage vložte připojovací řetězec pro váš prostředek a zadejte název kontejneru (rozlišuje velká a malá písmena).
    - Pro Webhook vložte adresu URL zpětného volání pro svůj koncový bod Webhooku. 
    - Klikněte na **Vytvořit**.

2. Klikněte na **+ cíl** a vyberte cíl z rozevíracího seznamu. Přidáte až 5 cílů do jednoho exportu.

3. Po dokončení nastavení exportu klikněte na **Uložit**. Po několika minutách se vaše data zobrazí ve vašich cílech.

## <a name="export-contents-and-format"></a>Exportovat obsah a formát

### <a name="azure-blob-storage-destination"></a>Cíl Azure Blob Storage

Data se exportují jednou za minutu a každý soubor, který obsahuje dávku změn od posledního exportovaného souboru. Exportovaná data jsou umístěna ve formátu JSON ve třech složkách. Výchozí cesty v účtu úložiště jsou:

- Telemetrie: _{Container}/{app-id}/{partition_id}/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Změny vlastností: _{Container}/{app-id}/{partition_id}/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_

Chcete-li procházet exportované soubory v Azure Portal, přejděte do souboru a vyberte kartu **Upravit objekt BLOB** .

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Cíle Azure Event Hubs a Azure Service Bus

Data se exportují prakticky v reálném čase. Data jsou v těle zprávy a jsou ve formátu JSON kódovaném jako UTF-8. 

V kontejneru poznámky nebo vlastnosti systému zprávy můžete najít `iotcentral-device-id` , `iotcentral-application-id` , `iotcentral-message-source` a, `iotcentral-message-type` které mají stejné hodnoty jako odpovídající pole v těle zprávy.

### <a name="webhook-destination"></a>Cíl Webhooku
V případě míst pro Webhooky se data exportují taky téměř v reálném čase. Data jsou v textu zprávy ve stejném formátu jako pro Event Hubs a Service Bus.


## <a name="telemetry-format"></a>Formát telemetrie
Každá exportovaná zpráva obsahuje normalizovanou formu plné zprávy, kterou zařízení odeslalo v těle zprávy ve formátu JSON kódovaném jako UTF-8. Mezi další informace, které jsou součástí každé zprávy, patří:

- `applicationId`aplikace IoT Central
- `messageSource`což je *telemetrie* pro export dat telemetrie
- `deviceId`zařízení, které odeslalo zprávu telemetrie
- `schema`je název a verze schématu datové části.
- `templateId`je ID šablony zařízení přidružené k zařízení
- `enrichments`jsou jakékoli obohacení, které byly nastaveny při exportu
- `messageProperties`jsou k diskusy další data, která zařízení odesílá společně s touto zprávou. Toto nastavení se označuje také jako *Vlastnosti aplikace*. [Další informace najdete v dokumentaci IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Pro Event Hubs a Service Bus IoT Central exportuje novou zprávu rychle po přijetí zprávy ze zařízení.

Pro úložiště objektů BLOB se zprávy účtují a exportují jednou za minutu.

Následující příklad ukazuje exportovanou zprávu telemetrie:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>Formát změn vlastností

Každá zpráva nebo záznam představuje jednu změnu vlastnosti zařízení nebo cloudu. U vlastností zařízení se jako samostatná zpráva exportují jenom změny v hlášené hodnotě. Mezi další informace zahrnuté do exportované zprávy patří:

- `applicationId`aplikace IoT Central
- `messageSource`*vlastnosti* pro export dat změn vlastností
- `messageType`což je buď *cloudPropertyChange* nebo *devicePropertyDesiredChange*, *devicePropertyReportedChange*
- `deviceId`zařízení, jehož vlastnosti se změnily
- `schema`je název a verze schématu datové části.
- `templateId`je ID šablony zařízení přidružené k zařízení
- `enrichments`jsou jakékoli obohacení, které byly nastaveny při exportu

V případě Event Hubs a Service Bus IoT Central exportuje data nových zpráv do centra událostí nebo Service Bus fronty nebo tématu téměř v reálném čase.

Pro úložiště objektů BLOB se zprávy účtují a exportují jednou za minutu.

Následující příklad ukazuje zprávu o změně exportovaných vlastností přijatou v Azure Blob Storage.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>Porovnání exportu zastaralých dat a nové exportu dat
Toto je tabulka, která zvýrazňuje rozdíly mezi exportem starších dat a novým exportem dat. Informace o exportu starších dat najdete [tady](howto-export-data.md).

| Možnosti  | Export zastaralých dat | Nový export dat |
| :------------- | :---------- | :----------- |
| Dostupné datové typy | Telemetrie, zařízení a šablony zařízení | Telemetrie, změny vlastností |
| Filtrování | Žádné | Závisí na typu exportovaného dat. Pro telemetrii, filtrování podle telemetrie, vlastností zpráv a hodnot vlastností |
| Obohacení | Žádné | Obohacení vlastním řetězcem nebo hodnotou vlastnosti v zařízení |
| Cíle | Azure Event Hubs, Azure Service Bus fronty a témata, Azure Blob Storage | Stejné jako pro export a Webhooky dat starší verze| 
| Podporované aplikace | V2, V3 | Jenom V3 |
| Významné limity | 5 exportů na aplikaci, 1 cíl na export | 10 EXPORTS – cílová připojení na aplikaci | 

## <a name="next-steps"></a>Další kroky

Teď, když už víte, jak používat novou export dat, pokračujte k dalšímu kroku:

> [!div class="nextstepaction"]
> [Jak používat analýzy v IoT Central](./howto-create-analytics.md)
