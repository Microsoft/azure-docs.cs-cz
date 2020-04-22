---
title: Principy koncových bodů služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – referenční informace o koncových bodech pro zařízení ioT hubu a koncových bodech směřujících ke službám.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 53660ad93ab2218d546ae6f363873c4d66872e2b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730308"
---
# <a name="reference---iot-hub-endpoints"></a>Reference – koncové body centra IoT Hub

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Názvy ioT hubů

Název hostitele služby IoT hub, který hostuje vaše koncové body, najdete na portálu na stránce **Přehled** vašeho centra. Ve výchozím nastavení vypadá název DNS služby `{your iot hub name}.azure-devices.net`IoT hub: .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Seznam předdefinovaných koncových bodů ioT hubu

Azure IoT Hub je služba s více tenanty, která zpřístupňuje jeho funkce různým aktérům. Následující diagram znázorňuje různé koncové body, které zpřístupňuje ioT Hub.

![Koncové body IoT Hubu](./media/iot-hub-devguide-endpoints/endpoints.png)

Následující seznam popisuje koncové body:

* **Zprostředkovatel prostředků**. Poskytovatel prostředků služby IoT Hub zveřejňuje rozhraní [Správce prostředků Azure.](../azure-resource-manager/management/overview.md) Toto rozhraní umožňuje vlastníkům předplatného Azure vytvářet a odstraňovat centra IoT a aktualizovat vlastnosti služby IoT hub. Vlastnosti služby IoT Hub řídí [zásady zabezpečení na úrovni hubu](iot-hub-devguide-security.md#access-control-and-permissions), na rozdíl od řízení přístupu na úrovni zařízení a funkčních možností pro zasílání zpráv mezi cloudy a mezi zařízeními a cloudy. Zprostředkovatel prostředků služby IoT Hub také umožňuje [exportovat identity zařízení](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Správa identit zařízení**. Každý ioT hub zpřístupňuje sadu koncových bodů HTTPS REST pro správu identit zařízení (vytvoření, načtení, aktualizace a odstranění). [Identity zařízení](iot-hub-devguide-identity-registry.md) se používají pro ověřování zařízení a řízení přístupu.

* **Správa dvojčete zařízení**. Každý ioT hub zveřejňuje sadu koncový bod HTTPS REST orientované na služby pro dotazování a aktualizace [dvojčat zařízení](iot-hub-devguide-device-twins.md) (aktualizace značek a vlastností).

* **Správa pracovních míst**. Každý centrum IoT zpřístupňuje sadu koncový bod HTTPS REST orientované na služby pro dotazování a správu [úloh](iot-hub-devguide-jobs.md).

* **Koncové body zařízení**. Pro každé zařízení, které je v registru identit, zveřejní IoT Hub sadu koncových bodů:

  * *Odesílat zprávy mezi zařízeními a cloudy*. Zařízení používá tento koncový bod k [odesílání zpráv mezi zařízeními a cloudy](iot-hub-devguide-messages-d2c.md).

  * *Příjem zpráv z cloudu na zařízení*. Zařízení používá tento koncový bod k přijímání cílených [zpráv z cloudu na zařízení](iot-hub-devguide-messages-c2d.md).

  * *Zahájit nahrávání souborů*. Zařízení používá tento koncový bod k přijetí Identifikátoru URI úložiště Azure z ioT hubu k [nahrání souboru](iot-hub-devguide-file-upload.md).

  * *Načíst a aktualizovat vlastnosti dvojčete zařízení*. Zařízení používá tento koncový bod pro přístup k vlastnostem [dvojčete zařízení.](iot-hub-devguide-device-twins.md)

  * *Přijímat přímé požadavky na metody*. Zařízení používá tento koncový bod k naslouchání požadavkům [přímé metody.](iot-hub-devguide-direct-methods.md)

    Tyto koncové body jsou vystaveny pomocí protokolů [MQTT v3.1.1](https://mqtt.org/), HTTPS 1.1 a [AMQP 1.0.](https://www.amqp.org/) AMQP je také k dispozici přes [WebSockets](https://tools.ietf.org/html/rfc6455) na portu 443.

* **Koncové body služby**. Každý ioT hub zveřejňuje sadu koncových bodů pro back-end vašeho řešení pro komunikaci s vašimi zařízeními. S jednou výjimkou jsou tyto koncové body vystaveny pouze pomocí protokolu [AMQP.](https://www.amqp.org/) Koncový bod vyvolání metody je vystaven přes protokol HTTPS.
  
  * *Příjem zpráv mezi zařízeními a cloudy*. Tento koncový bod je kompatibilní s [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Back-endová služba ji může použít ke čtení [zpráv mezi zařízeními](iot-hub-devguide-messages-d2c.md) a cloudy odeslaných vašimi zařízeními. Kromě tohoto předdefinovaného koncového bodu můžete ve službě IoT hub vytvořit vlastní koncové body.
  
  * *Odesílejte zprávy z cloudu na zařízení a přijímat potvrzení o doručení*. Tyto koncové body umožňují back-endu vašeho řešení odesílat spolehlivé [zprávy z cloudu na zařízení](iot-hub-devguide-messages-c2d.md)a přijímat odpovídající potvrzení o doručení nebo vypršení platnosti.
  
  * *Příjem oznámení o souborech*. Tento koncový bod zasílání zpráv umožňuje přijímat oznámení o tom, kdy vaše zařízení úspěšně nahrát soubor. 
  
  * *Přímé vyvolání metody*. Tento koncový bod umožňuje back-endové služby vyvolat [přímou metodu](iot-hub-devguide-direct-methods.md) na zařízení.
  
  * *Příjem událostí monitorování operací*. Tento koncový bod umožňuje přijímat události monitorování operací, pokud vaše centrum IoT byl nakonfigurován tak, aby je emitovat. Další informace naleznete v tématu [monitorování operací ioT Hubu](iot-hub-operations-monitoring.md).

Článek [sad Azure IoT SDKpopisuje](iot-hub-devguide-sdks.md) různé způsoby přístupu k těmto koncovým bodům.

Všechny koncové body IoT Hub používají protokol [TLS](https://tools.ietf.org/html/rfc5246) a žádný koncový bod je nikdy vystaven na nešifrovaných/nezabezpečených kanálech.

## <a name="custom-endpoints"></a>Vlastní koncové body

Existující služby Azure ve vašem předplatném můžete propojit s vaším centrem IoT hub a fungovat jako koncové body pro směrování zpráv. Tyto koncové body fungují jako koncové body služby a používají se jako jímky pro trasy zpráv. Zařízení nelze zapisovat přímo do dalších koncových bodů. Další informace o [směrování zpráv](../iot-hub/iot-hub-devguide-messages-d2c.md).

Služba IoT Hub aktuálně podporuje následující služby Azure jako další koncové body:

* Kontejnery azure storage
* Event Hubs
* Fronty služby Service Bus
* Témata služby Service Bus

Omezení počtu koncových bodů, které můžete přidat, naleznete v [tématu Kvóty a omezení](iot-hub-devguide-quotas-throttling.md).

K získání stavu koncových bodů můžete použít stav rozhraní REST API [Get Endpoint Health.](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) Doporučujeme používat [metriky služby IoT Hub](iot-hub-metrics.md) související s latencí směrování zpráv k identifikaci a ladění chyb, když je stav koncového bodu mrtvý nebo není v pořádku, protože očekáváme, že latence bude vyšší, když je koncový bod v jednom z těchto stavů.

|Stav|Popis|
|---|---|
|Zdravé|Koncový bod přijímá zprávy podle očekávání.|
|Nezdravé|Koncový bod nepřijímá zprávy podle očekávání a IoT Hub se pokouší odeslat data do tohoto koncového bodu. Stav koncového bodu není v pořádku bude aktualizován na v pořádku, když služba IoT Hub vytvořila nakonec konzistentní stav stavu.|
|Neznámý|Služba IoT Hub nenavázala připojení ke koncovému bodu. Z tohoto koncového bodu nebyly doručeny nebo odmítnuty žádné zprávy.|
|Mrtvý|Koncový bod nepřijímá zprávy poté, co ioT Hub zopakoval odesílání zpráv pro období obnovení řízení.|

## <a name="field-gateways"></a>Brány v terénu

V řešení IoT *je brána pole* mezi vašimi zařízeními a koncovými body služby IoT Hub. Obvykle se nachází v blízkosti vašich zařízení. Vaše zařízení komunikují přímo s bránou pole pomocí protokolu podporovaného zařízeními. Brána polí se připojuje ke koncovému bodu služby IoT Hub pomocí protokolu podporovaného službou IoT Hub. Bránou v poli může být vyhrazené hardwarové zařízení nebo počítač s nízkou spotřebou energie s vlastním softwarem brány.

[Azure IoT Edge](/azure/iot-edge/) můžete použít k implementaci brány pole. IoT Edge nabízí funkce, jako je multiplexování komunikace z více zařízení na stejné připojení IoT Hub.

## <a name="next-steps"></a>Další kroky

Mezi další referenční témata v tomto průvodci vývojáři služby IoT Hub patří:

* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md)
* [Kvóty a omezování](iot-hub-devguide-quotas-throttling.md)
* [Podpora služby IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Vysvětlení ip adresy centra IoT hubu](iot-hub-understand-ip-address.md)