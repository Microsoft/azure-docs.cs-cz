---
title: Vysvětlení registru identit služby Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – Popis registru identit služby IoT Hub a jak ji používat ke správě svých zařízení. Obsahuje informace o importu a exportu identit zařízení hromadně.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: fdbe88492d6260d19955b39ac8eaf6cfb9dba130
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144542"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Vysvětlení registru identit ve službě IoT hub

Každá služba IoT hub obsahuje registr identit, která uchovává informace o zařízení a moduly, které jsou povolené pro připojení ke službě IoT hub. Než zařízení nebo modulu se můžou připojit do služby IoT hub, musí existovat položka pro toto zařízení nebo modulu v registru identit služby IoT hub. Zařízení nebo modul musí ověřovat také pomocí založené na přihlašovací údaje uložené v registru identit služby IoT hub.

ID zařízení nebo modulu uložené v registru identit rozlišuje velká a malá písmena.

Na vysoké úrovni je registr identit podporující REST kolekci zařízení nebo modul identity prostředků. Při přidání záznam v registru identit služby IoT Hub vytvoří sadu prostředků podle zařízení, jako jsou fronty, který obsahuje neukládají žádné zprávy typu cloud zařízení.

Registr identit použijte, pokud potřebujete:

* Zřízení zařízení nebo moduly, které se připojují ke službě IoT hub.
* Řízení přístupu podle zařízení/za – moduly pro vaše Centrum zařízení nebo přístupem k modulu koncové body.

> [!NOTE]
> Registr identit neobsahuje žádná metadata specifická pro aplikaci.

## <a name="identity-registry-operations"></a>Operace registru identit

Registr identit služby IoT Hub zveřejňuje následující operace:

* Vytvoření identity zařízení nebo modul
* Aktualizace zařízení nebo modul identity
* Načíst identitu zařízení nebo modul podle ID
* Odstranit zařízení nebo modul identity
* Seznam až 1 000 identit
> Dvojče zařízení identit a modul je ve verzi public preview. Následující funkce budou podporované v modulu identity po obecné k dispozici.
* Export identit zařízení do služby Azure blob storage
* Importovat zařízení identit z úložiště objektů blob v Azure

Všechny tyto operace můžete použít optimistické řízení souběžnosti, jak je uvedeno v [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> Jediný způsob, jak načíst všechny identity v registru identit služby IoT hub je použít [exportovat] [ lnk-export] funkce.

Registr identit služby IoT Hub:

* Neobsahuje žádné metadat aplikace.
* Podobně jako slovník, lze přistupovat pomocí **deviceId** nebo **moduleId** jako klíč.
* Nepodporuje výrazové dotazy.

Řešení IoT obvykle má samostatné úložiště specifické pro řešení, který obsahuje metadata specifická pro aplikaci. Specifické pro řešení úložiště v inteligentní sestavování řešení by například zaznamenat místnosti, ve kterém je nasazen senzoru teploty.

> [!IMPORTANT]
> Registr identit použijte pouze pro správu zařízení a zřizování operace. Vysoká propustnost operací v době běhu neměli spoléhat na provádění operací v registru identit. Například kontroluje se stav připojení zařízení před odesláním příkazu není podporovaný model. Ujistěte se, že ke kontrole [míru omezení] [ lnk-quotas] pro registr identit a [prezenčního signálu zařízení] [ lnk-guidance-heartbeat] vzor.

## <a name="disable-devices"></a>Zakázat zařízení

Zařízení můžete zakázat aktualizací **stav** vlastnost identity v registru identit. Obvykle tuto vlastnost použijete ve dvou scénářích:

* Během procesu zřizování Orchestrace. Další informace najdete v tématu [Device Provisioning][lnk-guidance-provisioning].
* Pokud z nějakého důvodu domníváte, že dojde k narušení nebo přestal neoprávněné zařízení.

Tato funkce není k dispozici pro moduly.

## <a name="import-and-export-device-identities"></a>Import a export identit zařízení

Použití asynchronních operací v [koncový bod poskytovatele prostředků služby IoT Hub] [ lnk-endpoints] identit zařízení hromadně exportovat z registru identit služby IoT hub. Exporty se dlouhotrvajících úloh, které používají k ukládání dat identity zařízení čtení z registru kontejneru objektů blob poskytnuté zákazníkem.

Použití asynchronních operací v [koncový bod poskytovatele prostředků služby IoT Hub] [ lnk-endpoints] identit zařízení hromadně importovat do registru identity služby IoT hub. Importuje se dlouho běžící úlohy, které používají data v kontejneru objektů blob poskytnuté zákazníkem pro zápis dat identitu zařízení v registru identit.

Další informace o import a export rozhraní API najdete v tématu [poskytovatele prostředků služby IoT Hub rozhraní REST API][lnk-resource-provider-apis]. Další informace o spuštění import a export úloh najdete v tématu [Hromadná Správa identit zařízení služby IoT Hub][lnk-bulk-identity].

## <a name="device-provisioning"></a>Zřizování zařízení

Data zařízení, která ukládá daného řešení IoT, závisí na konkrétní požadavky tohoto řešení. Ale minimálně, musí ukládat řešení identit zařízení a ověřovací klíče. Služba Azure IoT Hub obsahuje registr identit, která může ukládat hodnoty pro každé zařízení, jako je například ID, ověřovací klíče a stavové kódy. Řešení můžete použít další služby Azure, jako jsou úložiště tabulek, úložiště objektů blob nebo Cosmos DB k ukládání dat další zařízení.

*Zřizování zařízení* je proces přidávání počáteční zařízení data ukládá ve vašem řešení. Povolit nové zařízení pro připojení k centru, musíte přidat ID zařízení a klíče v registru identit služby IoT Hub. Jako součást procesu zřizování může být potřeba inicializovat data specifická pro zařízení v jiných úložištích řešení. Azure IoT Hub Device Provisioning Service můžete také zajistit plně automatizované, just-in-time zřizování pro jeden nebo více centra IoT bez zásahu člověka. Další informace najdete v tématu [dokumentace ke službě zřizování][lnk-dps].

## <a name="device-heartbeat"></a>Prezenční signál zařízení

Registr identit služby IoT Hub obsahuje pole s názvem **connectionState**. Použít pouze **connectionState** pole při vývoji a ladění. Řešení IoT by neměl dotaz na pole v době běhu. Například není dotaz **connectionState** pole, které chcete zkontrolovat, jestli je zařízení připojené před odesláním zprávy typu cloud zařízení nebo zprávu SMS.

Pokud je potřeba vědět, pokud je zařízení připojené, měli byste implementovat řešení IoT *prezenčního signálu vzor*.
Ve vzoru prezenčního signálu zařízení odesílá zprávy typu zařízení cloud alespoň jednou každých pevné množství času (například alespoň jednou za hodinu). Proto i v případě, že zařízení nemá žádná data k odeslání, stále odešle prázdnou zprávu typu zařízení cloud (obvykle s vlastností, který ji identifikuje jako prezenční signál). Na straně služby řešení udržuje mapu s poslední prezenční signál pro každé zařízení. Pokud řešení neobdrží zprávu prezenčního signálu v očekávaném čase ze zařízení, předpokládá, že dojde k problému se zařízením.

Složitější implementace by mohla obsahovat informace z [monitorování operací] [ lnk-devguide-opmon] identifikovat zařízení, která se pokouší o připojení nebo komunikaci ale služeb při selhání. Pokud implementujete vzor prezenčního signálu, nezapomeňte zaškrtnout [IoT Hub kvóty a omezení][lnk-quotas].

> [!NOTE]
> Pokud řešení IoT používá stav připojení výhradně k určení, zda chcete odesílat zprávy typu cloud zařízení a zprávy nejsou vysílat velké sady zařízení, zvažte použití jednodušší *krátký čas vypršení platnosti* vzor. Tento model dosáhne stejného výsledku jako uchovávat registr stavu připojení zařízení pomocí vzoru prezenčního signálu při zachování efektivnější. Pokud jste požádali o zprávy potvrzení, můžete služby IoT Hub informovat vás o tom, která zařízení jsou schopných přijímat zprávy, které nejsou.

## <a name="device-and-module-lifecycle-notifications"></a>Oznámení životního cyklu zařízení a modul

IoT Hub může upozornit řešení IoT, při vytvoření nebo odstranění zasláním oznámení životního cyklu identity. Uděláte to tak, musí vaše řešení IoT má být vytvořena trasa a nastavení zdroje dat rovná *DeviceLifecycleEvents* nebo *ModuleLifecycleEvents*. Ve výchozím nastavení jsou odeslány žádné oznámení životního cyklu, to znamená, předem neexistuje žádný takový trasy. Oznámení obsahuje vlastnosti a text.

Vlastnosti: Vlastnosti zprávy systému začínají `'$'` symbol.

Oznámení pro zařízení:

| Název | Hodnota |
| --- | --- |
|$content – typ | application/json |
|$iothub-enqueuedtime |  Čas odeslání oznámení |
|$iothub – zpráva – zdroj | deviceLifecycleEvents |
|$content – kódování | utf-8 |
|opType | **createDeviceIdentity** nebo **deleteDeviceIdentity** |
|HubName | Název služby IoT Hub |
|deviceId | ID zařízení |
|operationTimestamp | Časové razítko ISO8601 operace |
|schéma iothub zprávy | deviceLifecycleNotification |

Subjekt: Tato část je ve formátu JSON a představuje dvojčete identitou zařízení vytvořenou. Například:

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
Oznámení pro modul:

| Název | Hodnota |
| --- | --- |
$content – typ | application/json |
$iothub-enqueuedtime |  Čas odeslání oznámení |
$iothub – zpráva – zdroj | moduleLifecycleEvents |
$content – kódování | utf-8 |
opType | **createModuleIdentity** nebo **deleteModuleIdentity** |
HubName | Název služby IoT Hub |
ID modulu | ID modulu |
operationTimestamp | Časové razítko ISO8601 operace |
schéma iothub zprávy | moduleLifecycleNotification |

Text: Tato část je ve formátu JSON a představuje dvojčete vytvořený modul identity. Například:

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

## <a name="device-identity-properties"></a>Vlastnosti identit zařízení

Identit zařízení jsou reprezentovány jako dokumenty JSON s následujícími vlastnostmi:

| Vlastnost | Možnosti | Popis |
| --- | --- | --- |
| deviceId |aktualizace vyžaduje, na jen pro čtení |Řetězec malá a velká písmena (maximálně 128 znaků) alfanumerické znaky ASCII 7 bitů a některé speciální znaky: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |vyžaduje jen pro čtení |IoT generované rozbočovače, velká a malá písmena řetězci až 128 znaků. Tato hodnota se používá k rozlišení zařízení se stejnou **deviceId**, když se odstraní a znovu vytvořen. |
| Značka Etag |vyžaduje jen pro čtení |Řetězce představují slabou značku ETag pro identitu zařízení, jak je uvedeno [RFC7232][lnk-rfc7232]. |
| ověřování |nepovinné |Složený objekt obsahující ověřování zabezpečení informací a materiály. |
| auth.symkey |nepovinné |Složený objekt obsahující primární a sekundární klíč uložený ve formátu base64. |
| status |povinné |Indikátor přístup. Může být **povoleno** nebo **zakázané**. Pokud **povoleno**, zařízení může připojit. Pokud **zakázané**, toto zařízení nemá přístup k libovolné koncového bodu připojeného k zařízení. |
| statusReason |nepovinné |128 znaků dlouhý řetězec, který ukládá důvod stavu identity zařízení. Jsou povoleny všechny znaky UTF-8. |
| statusUpdateTime |jen pro čtení |Dočasné indikátor zobrazuje datum a čas poslední aktualizace stavu. |
| Vlastnost connectionState |jen pro čtení |Pole určující stav připojení: buď **připojeno** nebo **odpojeno**. Toto pole představuje služby IoT Hub zobrazení stavu připojení zařízení. **Důležité**: Toto pole by měla sloužit pouze pro účely vývoje a ladění. Stav připojení se aktualizuje jenom pro zařízení používat protokol MQTT nebo AMQP. Také je založená na úrovni protokolu za příkazy ping (příkazy ping pro zjištění protokol MQTT nebo příkazy ping protokolu AMQP) a může mít maximální zpoždění, jenom 5 minut. Z těchto důvodů může být počet falešně pozitivních výsledků, například zařízení hlášená jako připojená, ale, která jsou odpojené. |
| connectionStateUpdatedTime |jen pro čtení |Dočasné indikátor zobrazuje datum a čas posledního stavu připojení byla aktualizována. |
| lastActivityTime |jen pro čtení |Dočasné indikátor zobrazuje datum a čas posledního zařízení připojené, přijetí nebo byla odeslána zpráva. |

> [!NOTE]
> Stav připojení může představovat pouze služby IoT Hub pohled na stav připojení. Aktualizace tohoto stavu se může zpozdit, v závislosti na stavu sítě a konfigurace.

## <a name="module-identity-properties"></a>Vlastnosti modulu identity

Modul identity jsou reprezentovány jako dokumenty JSON s následujícími vlastnostmi:

| Vlastnost | Možnosti | Popis |
| --- | --- | --- |
| deviceId |aktualizace vyžaduje, na jen pro čtení |Řetězec malá a velká písmena (maximálně 128 znaků) alfanumerické znaky ASCII 7 bitů a některé speciální znaky: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| ID modulu |aktualizace vyžaduje, na jen pro čtení |Řetězec malá a velká písmena (maximálně 128 znaků) alfanumerické znaky ASCII 7 bitů a některé speciální znaky: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |vyžaduje jen pro čtení |IoT generované rozbočovače, velká a malá písmena řetězci až 128 znaků. Tato hodnota se používá k rozlišení zařízení se stejnou **deviceId**, když se odstraní a znovu vytvořen. |
| Značka Etag |vyžaduje jen pro čtení |Řetězce představují slabou značku ETag pro identitu zařízení, jak je uvedeno [RFC7232][lnk-rfc7232]. |
| ověřování |nepovinné |Složený objekt obsahující ověřování zabezpečení informací a materiály. |
| auth.symkey |nepovinné |Složený objekt obsahující primární a sekundární klíč uložený ve formátu base64. |
| status |povinné |Indikátor přístup. Může být **povoleno** nebo **zakázané**. Pokud **povoleno**, zařízení může připojit. Pokud **zakázané**, toto zařízení nemá přístup k libovolné koncového bodu připojeného k zařízení. |
| statusReason |nepovinné |128 znaků dlouhý řetězec, který ukládá důvod stavu identity zařízení. Jsou povoleny všechny znaky UTF-8. |
| statusUpdateTime |jen pro čtení |Dočasné indikátor zobrazuje datum a čas poslední aktualizace stavu. |
| Vlastnost connectionState |jen pro čtení |Pole určující stav připojení: buď **připojeno** nebo **odpojeno**. Toto pole představuje služby IoT Hub zobrazení stavu připojení zařízení. **Důležité**: Toto pole by měla sloužit pouze pro účely vývoje a ladění. Stav připojení se aktualizuje jenom pro zařízení používat protokol MQTT nebo AMQP. Také je založená na úrovni protokolu za příkazy ping (příkazy ping pro zjištění protokol MQTT nebo příkazy ping protokolu AMQP) a může mít maximální zpoždění, jenom 5 minut. Z těchto důvodů může být počet falešně pozitivních výsledků, například zařízení hlášená jako připojená, ale, která jsou odpojené. |
| connectionStateUpdatedTime |jen pro čtení |Dočasné indikátor zobrazuje datum a čas posledního stavu připojení byla aktualizována. |
| lastActivityTime |jen pro čtení |Dočasné indikátor zobrazuje datum a čas posledního zařízení připojené, přijetí nebo byla odeslána zpráva. |

## <a name="additional-reference-material"></a>Další referenční materiál

Další referenční témata v příručce pro vývojáře IoT Hub patří:

* [Koncové body IoT Hubu] [ lnk-endpoints] popisuje různé koncové body, které každý IoT hub zpřístupní pro operace za běhu a správy.
* [Omezování a kvótách] [ lnk-quotas] popisuje kvóty a omezování chování, které se vztahují ke službě IoT Hub.
* [Azure IoT zařízení a služby sady SDK] [ lnk-sdks] uvádí různé jazykové sady SDK můžete použít při vývoji aplikace s zařízení i služby, které pracují s centrem IoT.
* [Dotazovací jazyk služby IoT Hub] [ lnk-query] popisuje dotazovací jazyk, slouží k načtení informací ze služby IoT Hub o dvojčata zařízení a úlohy.
* [Podpora IoT Hub MQTT] [ lnk-devguide-mqtt] poskytuje další informace o podpoře služby IoT Hub pro protokolu MQTT.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak pomocí registru identit služby IoT Hub, vás může zajímat v následujících tématech příručky pro vývojáře IoT Hub:

* [Řízení přístupu ke službě IoT Hub][lnk-devguide-security]
* [Použití dvojčat zařízení k synchronizaci stavu a konfigurace][lnk-devguide-device-twins]
* [Vyvolání přímé metody v zařízení][lnk-devguide-directmethods]
* [Plánování úloh na několika zařízeních][lnk-devguide-jobs]

Vyzkoušet si některé koncepty popsané v tomto článku, najdete v následujícím kurzu služby IoT Hub:

* [Začínáme s Azure IoT Hub][lnk-getstarted-tutorial]

Prozkoumat pomocí IoT Hub Device Provisioning Service umožňuje plně automatizované, just-in-time zřizování, najdete v tématech: 

* [Azure IoT Hub Device Provisioning Service][lnk-dps]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: quickstart-send-telemetry-dotnet.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
