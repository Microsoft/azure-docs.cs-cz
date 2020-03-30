---
title: Principy registru identit služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – popis registru identit služby IoT Hub a jeho použití ke správě zařízení. Obsahuje informace o hromadném importu a exportu identit zařízení.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.openlocfilehash: ccb840caea5d28975daaf8cbf6f0d4985bdf006d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499139"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Principy registru identit ve vašem centru IoT hub

Každé centrum IoT má registr identit, který ukládá informace o zařízeních a modulech povolených pro připojení k centru IoT Hub. Než se zařízení nebo modul může připojit k centru IoT hub, musí být v registru identit služby IoT hub u ztotožnění položka pro toto zařízení nebo modul. Zařízení nebo modul musí také ověřit pomocí služby IoT hub na základě pověření uložených v registru identit.

ID zařízení nebo modulu uložené v registru identit rozlišuje malá a velká písmena.

Na vysoké úrovni registru identit je kolekce podporující rest prostředků identity zařízení nebo modulu. Když přidáte položku v registru identit, služba IoT Hub vytvoří sadu prostředků pro zařízení, jako je například fronta, která obsahuje zprávy z cloudu na zařízení za letu.

Registr identit použijte v případě, že potřebujete:

* Zřizovat zařízení nebo moduly, které se připojují k vašemu centru IoT hub.
* Ovládejte přístup k koncovým bodům zařízení nebo koncových bodů pro zařízení nebo modul y pro zařízení nebo moduly.

> [!NOTE]
> * Registr identit neobsahuje žádná metadata specifická pro aplikaci.
> * Identita modulu a dvojče modulu jsou ve verzi Public Preview. Níže uvedená funkce bude podporována na identitě modulu, pokud je obecně k dispozici.
>

## <a name="identity-registry-operations"></a>Operace registru identit

Registr identit služby IoT Hub zveřejňuje následující operace:

* Vytvoření identity zařízení nebo modulu
* Aktualizovat identitu zařízení nebo modulu
* Načtení identity zařízení nebo modulu podle ID
* Odstranění identity zařízení nebo modulu
* Seznam až 1000 identit
* Export identit zařízení do úložiště objektů blob Azure
* Import identit zařízení z úložiště objektů blob Azure

Všechny tyto operace můžete použít optimistickou souběžnost, jak je uvedeno v [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> Jediný způsob, jak načíst všechny identity v registru identit služby IoT hub je použití funkce [exportu.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

Registr identit služby IoT Hub:

* Neobsahuje žádná metadata aplikace.
* Lze přistupovat jako slovník, pomocí **deviceId** nebo **moduleId** jako klíč.
* Nepodporuje expresivní dotazy.

Řešení IoT má obvykle samostatné úložiště specifické pro řešení, které obsahuje metadata specifická pro aplikaci. Například úložiště specifické pro řešení řešení řešení inteligentní budovy by zaznamenat místnost, ve které je nasazen teplotní senzor.

> [!IMPORTANT]
> Registr identit používejte pouze pro operace správy a zřizování zařízení. Operace s vysokou propustností za běhu by neměly záviset na provádění operací v registru identit. Například kontrola stavu připojení zařízení před odesláním příkazu není podporovaný vzor. Zkontrolujte rychlost [omezení](iot-hub-devguide-quotas-throttling.md) registru identit a vzor [prezenčního signálu zařízení.](iot-hub-devguide-identity-registry.md#device-heartbeat)

## <a name="disable-devices"></a>Zakázání zařízení

Zařízení můžete zakázat aktualizací vlastnosti **stavu** identity v registru identit. Obvykle tuto vlastnost používáte ve dvou scénářích:

* Během procesu orchestrace zřizování. Další informace naleznete v [tématu Device Provisioning](iot-hub-devguide-identity-registry.md#device-provisioning).

* Pokud se z nějakého důvodu domníváte, že je zařízení ohroženo nebo se stalo neoprávněným.

Tato funkce není k dispozici pro moduly.

## <a name="import-and-export-device-identities"></a>Import a export identit zařízení

Pomocí asynchronních operací na [koncovém bodě zprostředkovatele prostředků služby IoT Hub](iot-hub-devguide-endpoints.md) můžete hromadně exportovat identity zařízení z registru identit centra IoT Hub. Exporty jsou dlouhotrvající úlohy, které používají kontejner objektů blob dodaný zákazníkem k uložení dat identity zařízení čtených z registru identit.

Pomocí asynchronních operací na [koncovém bodě zprostředkovatele prostředků služby IoT Hub](iot-hub-devguide-endpoints.md) můžete hromadně importovat identity zařízení do registru identit služby IoT hub. Importy jsou dlouhotrvající úlohy, které používají data v kontejneru objektů blob dodané zákazníkem k zápisu dat identity zařízení do registru identit.

Další informace o importu a exportu api naleznete v tématu [IoT Hub zprostředkovatele prostředků REST API](/rest/api/iothub/iothubresource). Další informace o spuštění úloh importu a exportu najdete v [tématu Hromadná správa identit zařízení služby IoT Hub](iot-hub-bulk-identity-mgmt.md).

Identity zařízení lze také exportovat a importovat z centra IoT prostřednictvím rozhraní API služby prostřednictvím [rozhraní REST API](/rest/api/iothub/service/jobclient/createimportexportjob) nebo jednoho z sad [SDK služby](/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-service-sdks)IoT Hub .

## <a name="device-provisioning"></a>Zřizování zařízení

Data zařízení, která dané řešení IoT ukládá, závisí na konkrétních požadavcích tohoto řešení. Ale minimálně řešení musí ukládat identity zařízení a ověřovací klíče. Azure IoT Hub obsahuje registr identit, který může ukládat hodnoty pro každé zařízení, jako jsou ID, ověřovací klíče a stavové kódy. Řešení můžete použít jiné služby Azure, jako je úložiště tabulek, úložiště objektů blob nebo Cosmos DB k ukládání dalších dat zařízení.

*Zřizování zařízení* je proces přidávání počátečnídata zařízení do úložišť ve vašem řešení. Chcete-li povolit připojení nového zařízení k rozbočovači, musíte do registru identit služby IoT Hub přidat ID zařízení a klíče. Jako součást procesu zřizování může být nutné inicializovat data specifická pro zařízení v jiných úložištích řešení. Můžete také použít službu Azure IoT Hub Deviceprovisioning Service k povolení zero-touch, just-in-time zřizování do jednoho nebo více center IoT bez nutnosti lidského zásahu. Další informace naleznete v [dokumentaci ke službě zřizování](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Prezenční signál zařízení

Registr identit služby IoT Hub obsahuje pole s názvem **connectionState**. Pole **connectionState** používejte pouze během vývoje a ladění. Řešení IoT by neměla dotazovat pole za běhu. Například není dotaz **connectionState** pole zkontrolovat, zda je připojen zařízení před odesláním zprávy cloud zařízení nebo SMS. Doporučujeme přihlásit se k odběru události [ **odpojené zařízení** ](iot-hub-event-grid.md#event-types) na Event Grid získat upozornění a sledovat stav připojení zařízení. V tomto [kurzu](iot-hub-how-to-order-connection-state-events.md) se dozvíte, jak integrovat události připojeného zařízení a odpojeného zařízení z ioT hubu do vašeho řešení IoT.

Pokud vaše řešení IoT potřebuje vědět, jestli je zařízení připojené, můžete implementovat *vzor prezenčního signálu*.
Ve vzoru prezenčního signálu zařízení odesílá zprávy zařízení cloud alespoň jednou za pevnou dobu (například alespoň jednou za hodinu). Proto i v případě, že zařízení nemá žádná data k odeslání, stále odesílá prázdnou zprávu zařízení cloud (obvykle s vlastností, která identifikuje jako prezenční signál). Na straně služby řešení udržuje mapu s posledním prezenčního signálu přijatým pro každé zařízení. Pokud řešení neobdrží zprávu prezenčního signálu v očekávaném čase ze zařízení, předpokládá, že došlo k potížím se zařízením.

Složitější implementace může zahrnovat informace z [Azure Monitor](../azure-monitor/index.yml) a Azure [Resource Health](../service-health/resource-health-overview.md) k identifikaci zařízení, která se pokoušejí připojit nebo komunikovat, ale nedaří, zkontrolujte monitor [s diagnostikou](iot-hub-monitor-resource-health.md) průvodce. Při implementaci prezenčního signálu vzor, nezapomeňte zkontrolovat [kvóty služby IoT Hub a omezení](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Pokud řešení IoT používá stav připojení výhradně k určení, zda chcete odesílat zprávy typu cloud zařízení a zprávy nejsou vysílány do velkých sad zařízení, zvažte použití jednoduššího časového vzoru *krátkého vypršení platnosti.* Tento vzor dosahuje stejného výsledku jako udržování registru stavu připojení zařízení pomocí vzoru prezenčního signálu, přičemž je efektivnější. Pokud požadujete potvrzení zpráv, služba IoT Hub vás může upozornit na to, která zařízení mohou přijímat zprávy a která ne.

## <a name="device-and-module-lifecycle-notifications"></a>Oznámení o životním cyklu zařízení a modulu

IoT Hub může upozornit vaše řešení IoT při vytvoření nebo odstranění identity odesláním oznámení o životním cyklu. Chcete-li tak učinit, vaše řešení IoT musí vytvořit trasu a nastavit zdroj dat rovná *DeviceLifecycleEvents* nebo *ModuleLifecycleEvents*. Ve výchozím nastavení nejsou odesílána žádná oznámení životního cyklu, to znamená, že žádné takové trasy předem neexistují. Oznámení obsahuje vlastnosti a text.

Vlastnosti: Vlastnosti systému `$` zpráv jsou předponou se symbolem.

Oznamovací zpráva pro zařízení:

| Name (Název) | Hodnota |
| --- | --- |
|$content typu | application/json |
|$iothub doba zařazení do fronty |  Čas odeslání oznámení |
|$iothub-zdroj zprávy | deviceLifecycleEvents |
|kódování $content | utf-8 |
|opTyp | **createDeviceIdentity** nebo **deleteDeviceIdentity** |
|název hubu | Název ioT hubu |
|deviceId | ID zařízení |
|operationTimestamp | Iso8601 časové razítko provozu |
|iothub-message-schéma | deviceLifecycleNotification |

Tělo: Tato část je ve formátu JSON a představuje dvojče identity vytvořeného zařízení. Například:

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
Oznamovací zpráva pro modul:

| Name (Název) | Hodnota |
| --- | --- |
$content typu | application/json |
$iothub doba zařazení do fronty |  Čas odeslání oznámení |
$iothub-zdroj zprávy | modulUdálosti životního cyklu |
kódování $content | utf-8 |
opTyp | **createModuleIdentity** nebo **deleteModuleIdentity** |
název hubu | Název ioT hubu |
modulId | ID modulu |
operationTimestamp | Iso8601 časové razítko provozu |
iothub-message-schéma | modulOznámení životního cyklu |

Tělo: Tato část je ve formátu JSON a představuje dvojče identity vytvořeného modulu. Například:

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

Identity zařízení jsou reprezentovány jako dokumenty JSON s následujícími vlastnostmi:

| Vlastnost | Možnosti | Popis |
| --- | --- | --- |
| deviceId |vyžadováno, jen pro čtení při aktualizacích |Řetězec rozlišující malá a velká písmena (až 128 znaků) 7bitových `- . + % _ # * ? ! ( ) , = @ $ '`alfanumerických znaků ASCII a určitých speciálních znaků: . |
| generationId |povinné, jen pro čtení |Řetězec s písmeny generovaný centrem IoT s písmeny až 128 znaků. Tato hodnota se používá k rozlišení zařízení se stejným **deviceId**, pokud byly odstraněny a znovu vytvořeny. |
| Etag |povinné, jen pro čtení |Řetězec představující slabé ETag pro identitu zařízení podle [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |optional |Složený objekt obsahující ověřovací informace a bezpečnostní materiály. |
| auth.symkey |optional |Složený objekt obsahující primární a sekundární klíč uložený ve formátu base64. |
| status |Požadovaný |Indikátor přístupu. Může být **povoleno** nebo **zakázáno**. Pokud **je povoleno**, zařízení se může připojit. Pokud **je zakázáno**, toto zařízení nemůže přistupovat k žádnému koncovému bodu směřujícímu k zařízení. |
| statusReason |optional |Řetězec o 128 znaků, který ukládá důvod stavu identity zařízení. Všechny znaky UTF-8 jsou povoleny. |
| stavUpdateTime |jen pro čtení |Časový indikátor zobrazující datum a čas poslední aktualizace stavu. |
| connectionState |jen pro čtení |Pole označující stav připojení: **Připojeno** nebo **Odpojeno**. Toto pole představuje zobrazení ioT hub stavu připojení zařízení. **Důležité:** Toto pole by mělo být použito pouze pro účely vývoje/ladění. Stav připojení je aktualizován pouze pro zařízení používající MQTT nebo AMQP. Také je založen na ping na úrovni protokolu (Příkaz y ping MQTT nebo Příkazy ping AMQP) a může mít maximální zpoždění pouze 5 minut. Z těchto důvodů mohou existovat falešně pozitivní výsledky, například zařízení hlášená jako připojená, ale odpojená. |
| connectionStateUpdatedTime |jen pro čtení |Časový indikátor zobrazující datum a čas aktualizace stavu připojení. |
| lastActivityTime |jen pro čtení |Časový indikátor zobrazující datum a čas, kdy se zařízení připojilo, přijalo nebo odeslalo zprávu. |

> [!NOTE]
> Stav připojení může představovat pouze zobrazení ioT hub stavu připojení. Aktualizace tohoto stavu může být zpožděn, v závislosti na podmínkách sítě a konfigurace.

> [!NOTE]
> V současné době sady SDK zařízení `+` `#` nepodporují použití znaků a v **id zařízení**.

## <a name="module-identity-properties"></a>Vlastnosti identity modulu

Identity modulů jsou reprezentovány jako dokumenty JSON s následujícími vlastnostmi:

| Vlastnost | Možnosti | Popis |
| --- | --- | --- |
| deviceId |vyžadováno, jen pro čtení při aktualizacích |Řetězec rozlišující malá a velká písmena (až 128 znaků) 7bitových `- . + % _ # * ? ! ( ) , = @ $ '`alfanumerických znaků ASCII a určitých speciálních znaků: . |
| modulId |vyžadováno, jen pro čtení při aktualizacích |Řetězec rozlišující malá a velká písmena (až 128 znaků) 7bitových `- . + % _ # * ? ! ( ) , = @ $ '`alfanumerických znaků ASCII a určitých speciálních znaků: . |
| generationId |povinné, jen pro čtení |Řetězec s písmeny generovaný centrem IoT s písmeny až 128 znaků. Tato hodnota se používá k rozlišení zařízení se stejným **deviceId**, pokud byly odstraněny a znovu vytvořeny. |
| Etag |povinné, jen pro čtení |Řetězec představující slabé ETag pro identitu zařízení podle [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |optional |Složený objekt obsahující ověřovací informace a bezpečnostní materiály. |
| auth.symkey |optional |Složený objekt obsahující primární a sekundární klíč uložený ve formátu base64. |
| status |Požadovaný |Indikátor přístupu. Může být **povoleno** nebo **zakázáno**. Pokud **je povoleno**, zařízení se může připojit. Pokud **je zakázáno**, toto zařízení nemůže přistupovat k žádnému koncovému bodu směřujícímu k zařízení. |
| statusReason |optional |Řetězec o 128 znaků, který ukládá důvod stavu identity zařízení. Všechny znaky UTF-8 jsou povoleny. |
| stavUpdateTime |jen pro čtení |Časový indikátor zobrazující datum a čas poslední aktualizace stavu. |
| connectionState |jen pro čtení |Pole označující stav připojení: **Připojeno** nebo **Odpojeno**. Toto pole představuje zobrazení ioT hub stavu připojení zařízení. **Důležité:** Toto pole by mělo být použito pouze pro účely vývoje/ladění. Stav připojení je aktualizován pouze pro zařízení používající MQTT nebo AMQP. Také je založen na ping na úrovni protokolu (Příkaz y ping MQTT nebo Příkazy ping AMQP) a může mít maximální zpoždění pouze 5 minut. Z těchto důvodů mohou existovat falešně pozitivní výsledky, například zařízení hlášená jako připojená, ale odpojená. |
| connectionStateUpdatedTime |jen pro čtení |Časový indikátor zobrazující datum a čas aktualizace stavu připojení. |
| lastActivityTime |jen pro čtení |Časový indikátor zobrazující datum a čas, kdy se zařízení připojilo, přijalo nebo odeslalo zprávu. |

> [!NOTE]
> V současné době sady SDK zařízení `+` `#` nepodporují použití a znaky v **deviceId** a **moduleId**.

## <a name="additional-reference-material"></a>Doplňkový referenční materiál

Mezi další referenční témata v průvodci vývojáři služby IoT Hub patří:

* [Koncové body ioT hubu](iot-hub-devguide-endpoints.md) popisují různé koncové body, které každý ioT hub zveřejňuje pro operace za běhu a správy.

* [Omezení a kvóty](iot-hub-devguide-quotas-throttling.md) popisuje kvóty a omezení chování, které platí pro službu IoT Hub.

* [Sady SDK zařízení a služeb Azure IoT](iot-hub-devguide-sdks.md) uvádí různé sady SDK, které můžete použít při vývoji aplikací pro zařízení i služeb, které interagují s službou IoT Hub.

* [Dotazovací jazyk IoT Hub](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk, který můžete použít k načtení informací z ioT hubu o dvojčatech a úlohách vašeho zařízení.

* [Podpora Služby IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře služby IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili používat registr identit služby IoT Hub, vás mohou zajímat následující témata s průvodcem pro vývojáře služby IoT Hub:

* [Řízení přístupu k IoT Hubu](iot-hub-devguide-security.md)

* [Synchronizace stavu a konfigurací pomocí dvojčat zařízení](iot-hub-devguide-device-twins.md)

* [Vyvolání přímé metody na zařízení](iot-hub-devguide-direct-methods.md)

* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Chcete-li vyzkoušet některé koncepty popsané v tomto článku, podívejte se na následující kurz centra IoT Hub:

* [Začínáme s Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Chcete-li prozkoumat pomocí služby zřizování zařízení služby IoT Hub a povolit zřizování s nulovým dotykem a just-in-time, přečtěte si následující: 

* [Služba Azure IoT Hub Device Provisioning](https://azure.microsoft.com/documentation/services/iot-dps)
