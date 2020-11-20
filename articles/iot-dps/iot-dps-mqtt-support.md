---
title: Vysvětlení podpory služby Azure IoT Device Provisioning MQTT | Microsoft Docs
description: Příručka pro vývojáře – podpora zařízení, která se připojují ke koncovému bodu s přístupem k zařízení Azure IoT Device Provisioning (DPS) pomocí protokolu MQTT.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0a7ec2f4f8fdf631a6bc5096296275291ec41751
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967121"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Komunikace s DPS pomocí protokolu MQTT

DPS umožňuje zařízením komunikovat s koncovým bodem zařízení DPS pomocí:

* [MQTT v 3.1.1](https://mqtt.org/) na portu 8883
* [MQTT v 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) přes WebSocket na portu 443.

DPS není plnohodnotný zprostředkovatel MQTT a nepodporuje všechna chování zadaná ve standardu MQTT v 3.1.1. Tento článek popisuje, jak můžou zařízení pomocí podporovaných MQTT chování komunikovat s DPS.

Všechna komunikace zařízení s DPS musí být zabezpečená pomocí protokolu TLS/SSL. Proto DPS nepodporuje nezabezpečená připojení přes port 1883.

 > [!NOTE] 
 > DPS v současné době nepodporuje zařízení, která používají [mechanismus ověření](./concepts-service.md#attestation-mechanism) TPM přes protokol MQTT.

## <a name="connecting-to-dps"></a>Připojování k DPS

Zařízení může používat protokol MQTT pro připojení k DPS pomocí kterékoli z následujících možností.

* Knihovny v sadách [SDK pro zřizování Azure IoT](../iot-hub/iot-hub-devguide-sdks.md#microsoft-azure-provisioning-sdks).
* Protokol MQTT přímo.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Přímé použití protokolu MQTT (jako zařízení)

Pokud zařízení nemůže používat sady SDK pro zařízení, může se stále připojit k koncovým bodům veřejných zařízení pomocí protokolu MQTT na portu 8883. V paketu **připojení** by mělo zařízení používat následující hodnoty:

* Pro pole **ClientID** použijte **registrationId**.

* V poli **uživatelské jméno** použijte `{idScope}/registrations/{registration_id}/api-version=2019-03-31` , kde `{idScope}` je [idScope](./concepts-service.md#id-scope) v DPS.

* V poli **heslo** použijte token SAS. Formát tokenu SAS je stejný jako u protokolů HTTPS a AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` ResourceURI by měl být ve formátu `{idScope}/registrations/{registration_id}` . Název zásad by měl být `registration` .

  > [!NOTE]
  > Pokud používáte ověřování pomocí certifikátu X. 509, hesla tokenů SAS se nevyžadují.

  Další informace o tom, jak generovat tokeny SAS, najdete v části tokeny zabezpečení v tématu [řízení přístupu k DPS](how-to-control-access.md#security-tokens).

Následuje seznam chování v DPS, které je specifické pro implementaci:

 * DPS nepodporuje funkci příznaku **CleanSession** nastavenou na **hodnotu 0**.

 * Když se aplikace zařízení přihlásí k odběru tématu s **QoS 2**, DPS udělí maximální úroveň QoS 1 v paketu **SUBACK** . Pak DPS doručí zprávy zařízení pomocí technologie QoS 1.

## <a name="tlsssl-configuration"></a>Konfigurace protokolu TLS/SSL

Pokud chcete protokol MQTT použít přímo, *musí* se klient připojit přes TLS 1,2. Pokusy o přeskočení tohoto kroku selžou s chybami připojení.


## <a name="registering-a-device"></a>Registrace zařízení

Pokud chcete zařízení zaregistrovat přes DPS, zařízení by se mělo přihlásit `$dps/registrations/res/#` jako **Filtr tématu**. Zástupný znak na více úrovních `#` v rámci filtru tématu slouží pouze k tomu, aby zařízení přijímalo další vlastnosti v názvu tématu. DPS nepovoluje použití `#` `?` zástupných znaků nebo pro filtrování dílčích témat. Vzhledem k tomu, že DPS není modul pro zasílání zpráv v rámci služby Pub pro obecné účely, podporuje pouze dokumentované názvy témat a filtry témat.

Zařízení by mělo publikovat registrační zprávu k DPS pomocí `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` **názvu tématu**. Datová část by měla obsahovat objekt [registrace zařízení](/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) ve formátu JSON.
V úspěšném scénáři obdrží zařízení odpověď na `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` název tématu, kde x je hodnota opakování v sekundách. Datová část odpovědi bude obsahovat objekt [RegistrationOperationStatus](/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) ve formátu JSON.

## <a name="polling-for-registration-operation-status"></a>Cyklické dotazování na stav operace registrace

Zařízení musí pravidelně dotazovat službu, aby získala výsledek operace registrace zařízení. Za předpokladu, že se zařízení již přihlásilo k odběru `$dps/registrations/res/#` tématu, jak je uvedeno výše, může publikovat zprávu Get stav operationstatus k `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` názvu tématu. ID operace v této zprávě by měla být hodnota přijatá ve zprávě odpovědi RegistrationOperationStatus v předchozím kroku. V případě úspěšného případu bude služba reagovat na `$dps/registrations/res/200/?$rid={request_id}` téma. Datová část odpovědi bude obsahovat objekt RegistrationOperationStatus. Zařízení by mělo pokračovat ve cyklickém dotazování služby, pokud je kód odpovědi 202 po prodlevě, která je stejná jako perioda opakování. Operace registrace zařízení je úspěšná, pokud služba vrátí stavový kód 200.

## <a name="connecting-over-websocket"></a>Připojení přes protokol WebSocket
Při připojování přes protokol WebSocket zadejte jako dílčí protokol `mqtt` . Postupujte podle [dokumentu RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Další kroky

Další informace o protokolu MQTT najdete v [dokumentaci k MQTT](https://mqtt.org/).

Další zkoumání možností DPS najdete v těchto tématech:

* [Informace o IoT DPS](about-iot-dps.md)