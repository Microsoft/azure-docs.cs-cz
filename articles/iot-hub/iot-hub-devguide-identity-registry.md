---
title: Vysvětlení registru identit Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – popis IoT Hub registru identit a jeho použití ke správě zařízení. Obsahuje informace o hromadném importu a exportu identit zařízení.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 3157eda4e2a21b0d153e7300db54f445fdb6878d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547754"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Vysvětlení registru identit ve službě IoT Hub

Každé centrum IoT má registr identit, který ukládá informace o zařízeních a modulech povolených pro připojení ke službě IoT Hub. Předtím, než se zařízení nebo modul může připojit ke službě IoT Hub, musí existovat položka pro toto zařízení nebo modul v registru identit služby IoT Hub. Zařízení nebo modul se musí také ověřit ve službě IoT Hub na základě přihlašovacích údajů uložených v registru identit.

ID zařízení nebo modulu uloženého v registru identity rozlišuje velká a malá písmena.

Registr identit je na vysoké úrovni shromažďování prostředků identity zařízení nebo modulů, které podporuje REST. Když přidáte položku do registru identity, IoT Hub vytvoří sadu prostředků pro zařízení, například frontu, která obsahuje zprávy ve službě cloud-zařízení.

Registr identit použijte v případě, že potřebujete:

* Zřídí zařízení nebo moduly, které se připojují ke službě IoT Hub.
* Řízení přístupu podle zařízení/modulu k koncovým bodům pro zařízení nebo modul s přístupem ke svému rozbočovači.

> [!NOTE]
> * Registr identit neobsahuje žádná metadata specifická pro aplikaci.
> * Identita modulu a modul s dvojitou identitou jsou ve verzi Public Preview. Tato funkce bude podporována u identity modulu, pokud je Obecná dostupnost.
>

## <a name="identity-registry-operations"></a>Operace registru identit

IoT Hub registr identit zpřístupňuje tyto operace:

* Vytvoření identity zařízení nebo modulu
* Aktualizace identity zařízení nebo modulu
* Načíst identitu zařízení nebo modulu podle ID
* Odstranit identitu zařízení nebo modulu
* Seznam identit až 1000
* Export identit zařízení do úložiště objektů BLOB v Azure
* Import identit zařízení ze služby Azure Blob Storage

Všechny tyto operace mohou používat optimistickou souběžnost, jak je uvedeno v [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> Jediným způsobem, jak načíst všechny identity v registru identit služby IoT Hub, je použití funkcí [exportu](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) .

IoT Hub registru identit:

* Neobsahuje žádná metadata aplikace.
* Může být k dispozici jako slovník pomocí **deviceId** nebo **moduleId** jako klíče.
* Nepodporuje dotazy na dotazy.

Řešení IoT má obvykle samostatné úložiště specifické pro řešení, které obsahuje metadata specifická pro aplikaci. Například úložiště specifické pro řešení v rámci inteligentního řešení sestavení by znamenalo místo, ve kterém je senzor teploty nasazen.

> [!IMPORTANT]
> Pro operace správy a zřizování zařízení používejte jenom registr identit. Operace vysoké propustnosti v době běhu by neměly záviset na provádění operací v registru identit. Například kontrola stavu připojení zařízení před odesláním příkazu není podporovaný vzor. Ujistěte se, že jste zkontrolovali [míry omezování](iot-hub-devguide-quotas-throttling.md) pro registr identit a vzorek [prezenčního signálu zařízení](iot-hub-devguide-identity-registry.md#device-heartbeat) .

## <a name="disable-devices"></a>Zakázání zařízení

Zařízení můžete zakázat tak, že aktualizujete vlastnost **stav** identity v registru identit. Obvykle tuto vlastnost použijete ve dvou scénářích:

* Během procesu orchestrace zřizování. Další informace najdete v tématu [zřizování zařízení](iot-hub-devguide-identity-registry.md#device-provisioning).

* Pokud z nějakého důvodu máte podezření, že dojde k ohrožení zabezpečení zařízení nebo se stane neoprávněným.

Tato funkce není k dispozici pro moduly.

## <a name="import-and-export-device-identities"></a>Import a export identit zařízení

Pomocí asynchronních operací na [koncovém bodu poskytovatele prostředků IoT Hub](iot-hub-devguide-endpoints.md) můžete hromadně exportovat identity zařízení z registru identit služby IoT Hub. Exporty jsou dlouhotrvající úlohy, které pomocí kontejneru objektů BLOB zadaného zákazníkem ukládají data identity zařízení načtená z registru identity.

Pomocí asynchronních operací na [koncovém bodu poskytovatele prostředků IoT Hub](iot-hub-devguide-endpoints.md) můžete importovat identity zařízení hromadně do registru identit služby IoT Hub. Importy jsou dlouhodobě běžící úlohy, které používají data v kontejneru objektů BLOB poskytovaných zákazníkem k zápisu dat identity zařízení do registru identity.

Další informace o rozhraních API pro import a export najdete v tématu [IoT Hub rozhraní REST API poskytovatele prostředků](/rest/api/iothub/iothubresource). Další informace o spouštění úloh importu a exportu najdete v tématu [Hromadná Správa identit zařízení IoT Hub](iot-hub-bulk-identity-mgmt.md).

Identity zařízení se taky dají exportovat a importovat z IoT Hub přes rozhraní API služby prostřednictvím [REST API](/rest/api/iothub/service/jobs/createimportexportjob) nebo jedné ze [sad SDK služby](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)IoT Hub.

## <a name="device-provisioning"></a>Zřizování zařízení

Data zařízení, která dané řešení IoT ukládá, závisí na konkrétních požadavcích tohoto řešení. Kromě toho musí řešení ukládat identity zařízení a ověřovací klíče. Azure IoT Hub zahrnuje registr identit, který může ukládat hodnoty pro každé zařízení, jako jsou ID, ověřovací klíče a stavové kódy. Řešení může použít další služby Azure, jako je Table Storage, BLOB Storage nebo Cosmos DB k uložení jakýchkoli dalších dat zařízení.

*Zřizování zařízení* je proces přidávání počátečních dat zařízení do úložišť ve vašem řešení. Pokud chcete novému zařízení povolit připojení k vašemu rozbočovači, musíte do registru IoT Hub identity přidat ID zařízení a klíče. V rámci procesu zřizování může být potřeba inicializovat data specifická pro zařízení v jiných úložištích řešení. Azure IoT Hub Device Provisioning Service můžete použít také k povolení nulového dotykového zřizování pro jedno nebo více rozbočovačů IoT bez nutnosti zásahu člověka. Další informace najdete v dokumentaci ke [službě zřizování](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Prezenční signál zařízení

IoT Hub registr identit obsahuje pole s názvem **vlastnost ConnectionState** . Při vývoji a ladění používejte pouze pole **vlastnost ConnectionState** . Řešení IoT by se neměla dotazovat na pole v době běhu. Například nedotazujte pole **vlastnost ConnectionState** , abyste zkontrolovali, jestli je zařízení připojené, než odešlete zprávu typu cloud-zařízení nebo SMS. Doporučujeme přihlášení k odběru události [ **odpojení zařízení**](iot-hub-event-grid.md#event-types) v Event Grid, aby se zobrazily výstrahy a sledovaly stav připojení zařízení. V tomto [kurzu](iot-hub-how-to-order-connection-state-events.md) se dozvíte, jak integrovat události připojené k zařízení a odpojené zařízení z IoT Hub ve vašem řešení IoT.

Pokud vaše řešení IoT potřebuje zjistit, jestli je zařízení připojené, můžete použít *vzor prezenčního signálu* .
Ve vzorku prezenčního signálu zařízení odesílá zprávy typu zařízení-Cloud nejméně jednou za určitou dobu (například nejméně jednou za hodinu). Proto i v případě, že zařízení nemá žádná data k odeslání, stále pošle prázdnou zprávu typu zařízení-Cloud (obvykle s vlastností, která ji identifikuje jako prezenční signál). Řešení na straně služby udržuje mapu s posledním obdrženým prezenčním signálem pro každé zařízení. Pokud řešení neobdrží zprávu prezenčního signálu v očekávaném čase ze zařízení, předpokládá se, že došlo k potížím se zařízením.

Složitější implementace by mohla zahrnovat informace z [Azure monitor](../azure-monitor/index.yml) a [Azure Resource Health](../service-health/resource-health-overview.md) k identifikaci zařízení, která se pokoušejí připojit nebo komunikovat, ale selže. Další informace najdete v tématu [monitorování IoT Hub](monitor-iot-hub.md) a [kontroly stavu prostředků IoT Hub](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health). Při implementaci vzoru prezenčního signálu nezapomeňte zkontrolovat [IoT Hub kvóty a omezení](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Pokud řešení IoT používá stav připojení výhradně k určení toho, jestli se mají odesílat zprávy z cloudu na zařízení, a zprávy se neodesílají do velkých sad zařízení, zvažte použití zjednodušeného vzoru *času vypršení platnosti* . Tento model dosahuje stejného výsledku jako udržování registru stavu připojení zařízení pomocí vzoru prezenčního signálu, a přitom je efektivnější. Pokud si vyžádáte potvrzení zprávy, IoT Hub vás může informovat o tom, která zařízení budou moci přijímat zprávy a které nejsou.

## <a name="device-and-module-lifecycle-notifications"></a>Oznámení o životním cyklu zařízení a modulů

IoT Hub může upozornění na vaše řešení IoT při vytvoření nebo odstranění identity odesláním oznámení životního cyklu. K tomu je potřeba, aby vaše řešení IoT vytvořilo trasu a nastavilo zdroj dat na hodnotu *DeviceLifecycleEvents* nebo *ModuleLifecycleEvents* . Ve výchozím nastavení se neodesílají žádná oznámení o životním cyklu, to znamená, že žádné takové trasy již neexistují. Zpráva s oznámením obsahuje vlastnosti a text.

Vlastnosti: vlastnosti systému zprávy jsou předpony s `$` symbolem.

Zpráva oznámení pro zařízení:

| Název | Hodnota |
| --- | --- |
|$content – typ | application/json |
|$iothub – enqueuedtime |  Čas odeslání oznámení |
|$iothub-Message-source | deviceLifecycleEvents |
|$content – kódování | UTF-8 |
|opType | **createDeviceIdentity** nebo **deleteDeviceIdentity** |
|hubName | Název IoT Hub |
|deviceId | ID zařízení |
|operationTimestamp | ISO8601 časové razítko operace |
|iothub-Message-Schema | deviceLifecycleNotification |

Tělo: Tato část je ve formátu JSON a představuje vlákna vytvořené identity zařízení. Příklad:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Zpráva oznámení pro modul:

| Název | Hodnota |
| --- | --- |
$content – typ | application/json |
$iothub – enqueuedtime |  Čas odeslání oznámení |
$iothub-Message-source | moduleLifecycleEvents |
$content – kódování | UTF-8 |
opType | **createModuleIdentity** nebo **deleteModuleIdentity** |
hubName | Název IoT Hub |
moduleId | ID modulu |
operationTimestamp | ISO8601 časové razítko operace |
iothub-Message-Schema | moduleLifecycleNotification |

Tělo: Tato část je ve formátu JSON a představuje vlákna vytvořené identity modulu. Příklad:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Vlastnosti identity zařízení

Identity zařízení se reprezentují jako dokumenty JSON s následujícími vlastnostmi:

| Vlastnost | Možnosti | Popis |
| --- | --- | --- |
| deviceId |požadováno, jen pro čtení v aktualizacích |Řetězec s rozlišováním velkých a malých písmen (maximálně 128 znaků dlouhý) alfanumerických znaků ASCII a některé speciální znaky: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |požadováno, jen pro čtení |Řetězec s rozlišováním velikosti písmen, který je v IoT Hub generovaný, je dlouhý až 128 znaků. Tato hodnota se používá k rozlišení zařízení se stejným **deviceId** , kdy byly odstraněny a znovu vytvořeny. |
| značk |požadováno, jen pro čtení |Řetězec představující slabou značku ETag pro identitu zařízení, jak je na [RFC7232](https://tools.ietf.org/html/rfc7232). |
| auth |optional |Složený objekt obsahující ověřovací informace a materiály pro zabezpečení. |
| auth. symkey |optional |Složený objekt obsahující primární a sekundární klíč uložený ve formátu base64. |
| status |vyžadováno |Indikátor přístupu. Lze **Povolit** nebo **Zakázat** . Pokud je tato možnost **povolená** , může se zařízení připojit. Pokud je toto zařízení **zakázané** , nemůže získat přístup ke koncovému bodu, který se týká zařízení. |
| statusReason |optional |Řetězec dlouhého znaku 128, který ukládá důvod pro stav identity zařízení. Všechny znaky UTF-8 jsou povoleny. |
| statusUpdateTime |jen pro čtení |Dočasný indikátor zobrazující datum a čas poslední aktualizace stavu. |
| Vlastnost ConnectionState |jen pro čtení |Pole indikující stav připojení: **připojeno** nebo **Odpojeno** . Toto pole představuje IoT Hub zobrazení stavu připojení zařízení. **Důležité** : Toto pole by mělo být používáno pouze pro účely vývoje a ladění. Stav připojení se aktualizuje jenom pro zařízení, která používají MQTT nebo AMQP. Také je založena na protokolech příkazového testu na úrovni protokolu (MQTT příkazového testu) a může mít maximální zpoždění pouze 5 minut. Z těchto důvodů můžou existovat falešně pozitivní, například zařízení nahlášená jako připojená, ale odpojená. |
| connectionStateUpdatedTime |jen pro čtení |Dočasný indikátor zobrazující datum a čas poslední aktualizace stavu připojení. |
| lastActivityTime |jen pro čtení |Dočasná indikátor zobrazující datum a čas, kdy se zařízení připojilo, přijalo nebo poslalo zprávu. |

> [!NOTE]
> Stav připojení může představovat pouze IoT Hub zobrazení stavu připojení. Aktualizace tohoto stavu můžou být zpožděné v závislosti na podmínkách a konfiguracích sítě.

> [!NOTE]
> Sady SDK pro zařízení v současné době nepodporují `+` použití `#` znaků a v **deviceId** .

## <a name="module-identity-properties"></a>Vlastnosti identity modulu

Identity modulů jsou reprezentovány jako dokumenty JSON s následujícími vlastnostmi:

| Vlastnost | Možnosti | Popis |
| --- | --- | --- |
| deviceId |požadováno, jen pro čtení v aktualizacích |Řetězec s rozlišováním velkých a malých písmen (maximálně 128 znaků dlouhý) alfanumerických znaků ASCII a některé speciální znaky: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| moduleId |požadováno, jen pro čtení v aktualizacích |Řetězec s rozlišováním velkých a malých písmen (maximálně 128 znaků dlouhý) alfanumerických znaků ASCII a některé speciální znaky: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |požadováno, jen pro čtení |Řetězec s rozlišováním velikosti písmen, který je v IoT Hub generovaný, je dlouhý až 128 znaků. Tato hodnota se používá k rozlišení zařízení se stejným **deviceId** , kdy byly odstraněny a znovu vytvořeny. |
| značk |požadováno, jen pro čtení |Řetězec představující slabou značku ETag pro identitu zařízení, jak je na [RFC7232](https://tools.ietf.org/html/rfc7232). |
| auth |optional |Složený objekt obsahující ověřovací informace a materiály pro zabezpečení. |
| auth. symkey |optional |Složený objekt obsahující primární a sekundární klíč uložený ve formátu base64. |
| status |vyžadováno |Indikátor přístupu. Lze **Povolit** nebo **Zakázat** . Pokud je tato možnost **povolená** , může se zařízení připojit. Pokud je toto zařízení **zakázané** , nemůže získat přístup ke koncovému bodu, který se týká zařízení. |
| statusReason |optional |Řetězec dlouhého znaku 128, který ukládá důvod pro stav identity zařízení. Všechny znaky UTF-8 jsou povoleny. |
| statusUpdateTime |jen pro čtení |Dočasný indikátor zobrazující datum a čas poslední aktualizace stavu. |
| Vlastnost ConnectionState |jen pro čtení |Pole indikující stav připojení: **připojeno** nebo **Odpojeno** . Toto pole představuje IoT Hub zobrazení stavu připojení zařízení. **Důležité** : Toto pole by mělo být používáno pouze pro účely vývoje a ladění. Stav připojení se aktualizuje jenom pro zařízení, která používají MQTT nebo AMQP. Také je založena na protokolech příkazového testu na úrovni protokolu (MQTT příkazového testu) a může mít maximální zpoždění pouze 5 minut. Z těchto důvodů můžou existovat falešně pozitivní, například zařízení nahlášená jako připojená, ale odpojená. |
| connectionStateUpdatedTime |jen pro čtení |Dočasný indikátor zobrazující datum a čas poslední aktualizace stavu připojení. |
| lastActivityTime |jen pro čtení |Dočasná indikátor zobrazující datum a čas, kdy se zařízení připojilo, přijalo nebo poslalo zprávu. |

> [!NOTE]
> Sady SDK pro zařízení v současné době nepodporují `+` použití `#` znaků a v **deviceId** a **moduleId** .

## <a name="additional-reference-material"></a>Další referenční materiály

Další referenční témata v IoT Hub příručce pro vývojáře zahrnují:

* [IoT Hub koncové body](iot-hub-devguide-endpoints.md) popisují různé koncové body, které jednotlivé služby IoT Hub zpřístupňují pro operace run-time a Management.

* [Omezení a kvóty](iot-hub-devguide-quotas-throttling.md) popisují chování kvót a omezení, která se vztahují na službu IoT Hub.

* Sady [SDK pro zařízení a služby Azure IoT](iot-hub-devguide-sdks.md) obsahují různé jazykové sady SDK, které můžete použít při vývoji aplikací pro zařízení i služby, které komunikují s IoT Hub.

* Dotazovací jazyk [IoT Hub](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk, který můžete použít k načtení informací z IoT Hub o nečinnosti zařízení a úlohách.

* [Podpora IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s použitím IoT Hub registru identit, může vás zajímat následující témata IoT Hub příručka pro vývojáře:

* [Řízení přístupu k IoT Hubu](iot-hub-devguide-security.md)

* [Pro synchronizaci stavu a konfigurací použít vlákna zařízení](iot-hub-devguide-device-twins.md)

* [Vyvolání přímé metody v zařízení](iot-hub-devguide-direct-methods.md)

* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Pokud si chcete vyzkoušet některé z konceptů popsaných v tomto článku, přečtěte si následující IoT Hub kurz:

* [Začínáme s Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Pokud chcete prozkoumat použití IoT Hub Device Provisioning Service k povolení nulového dotykového zřizování za běhu, přečtěte si téma: 

* [Služba Azure IoT Hub Device Provisioning](https://azure.microsoft.com/documentation/services/iot-dps)