---
title: Principy podpory služby MQTT služby Azure IoT Device Provisioning Service | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – podpora pro zařízení, která se připojují ke koncovému bodu orientovanému na zařízení Azure IoT Device Provisioning Service (DPS) pomocí protokolu MQTT.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 213fc3412a2dfad77946e52a355a30774d6860c7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680675"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Komunikujte se svým DPS pomocí protokolu MQTT

DPS umožňuje zařízením komunikovat s koncovým bodem zařízení DPS pomocí:

* [MQTT v3.1.1](https://mqtt.org/) na portu 8883
* [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) přes WebSocket na portu 443.

DPS není plně vybavený zprostředkovatel MQTT a nepodporuje všechna chování zadaná ve standardu MQTT v3.1.1. Tento článek popisuje, jak zařízení můžete použít podporované chování MQTT ke komunikaci s DPS.

Veškerá komunikace zařízení s DPS musí být zabezpečena pomocí TLS/SSL. Proto DPS nepodporuje nezabezpečené připojení přes port 1883.

 > [!NOTE] 
 > DPS v současné době nepodporuje zařízení používající [mechanismus ověřování](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) čipu TPM přes protokol MQTT.

## <a name="connecting-to-dps"></a>Připojení k DPS

Zařízení může použít protokol MQTT pro připojení k DPS pomocí některé z následujících možností.

* Knihovny v [sadách SDK zřizování Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks).
* Protokol MQTT přímo.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Přímé použití protokolu MQTT (jako zařízení)

Pokud zařízení nemůže používat sady SDK zařízení, může se stále připojit ke koncovým bodům veřejného zařízení pomocí protokolu MQTT na portu 8883. V paketu **CONNECT** by zařízení mělo používat následující hodnoty:

* Pro pole **ClientId** použijte **id registrace**.

* Pro pole **Uživatelské** jméno `{idScope}/registrations/{registration_id}/api-version=2019-03-31`použijte `{idScope}` , kde je [idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) DPS.

* Pro pole **Heslo** použijte token SAS. Formát tokenu SAS je stejný jako pro protokoly HTTPS i AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`ResourceURI by měl být `{idScope}/registrations/{registration_id}`ve formátu . Název zásady `registration`by měl být .

  > [!NOTE]
  > Pokud používáte ověřování certifikátu X.509, hesla tokenů SAS nejsou vyžadována.

  Další informace o tom, jak generovat tokeny SAS, naleznete v části tokeny zabezpečení [řízení přístupu k DPS](how-to-control-access.md#security-tokens).

Následuje seznam chování specifických pro implementaci DPS:

 * DPS nepodporuje funkce **příznaku CleanSession** nastaveného na **0**.

 * Když se aplikace zařízení přihlásí k odběru tématu s **QoS 2**, DPS uděluje maximální úroveň QoS 1 v **paketu SUBACK.** Poté DPS doručuje zprávy do zařízení pomocí QoS 1.

## <a name="tlsssl-configuration"></a>Konfigurace TLS/SSL

Chcete-li protokol MQTT používat přímo, *musí* se klient připojit přes Protokol TLS 1.2. Pokusy o přeskočení tohoto kroku se nezdaří s chybami připojení.


## <a name="registering-a-device"></a>Registrace zařízení

Chcete-li zaregistrovat zařízení prostřednictvím DPS, zařízení by se mělo přihlásit pomocí `$dps/registrations/res/#` jako filtr **tématu**. Víceúrovňový zástupný `#` znak ve filtru tématu se používá pouze k tomu, aby zařízení mohlo přijímat další vlastnosti v názvu tématu. DPS neumožňuje použití `#` nebo `?` zástupné znaky pro filtrování dílčích témat. Vzhledem k tomu, že DPS není univerzální zprostředkovatel zasílání zpráv pub-sub, podporuje pouze zdokumentované názvy témat a filtry témat.

Zařízení by mělo publikovat zprávu registru `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` dps pomocí jako **název tématu**. Datová část by měla obsahovat objekt [Registrace zařízení](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) ve formátu JSON.
V úspěšném scénáři zařízení obdrží odpověď `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` na název tématu, kde x je hodnota opakování po v sekundách. Datová část odpovědi bude obsahovat objekt [RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) ve formátu JSON.

## <a name="polling-for-registration-operation-status"></a>Dotazování pro stav registrační operace

Zařízení musí pravidelně dotazovat službu, aby získalo výsledek operace registrace zařízení. Za předpokladu, že se `$dps/registrations/res/#` zařízení již přihlásilo k odběru tématu, jak `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` je uvedeno výše, může publikovat zprávu stavu operace get na název tématu. ID operace v této zprávě by měla být hodnota přijatá ve zprávě odpovědi RegistrationOperationStatus v předchozím kroku. V úspěšném případě bude služba `$dps/registrations/res/200/?$rid={request_id}` reagovat na toto téma. Datová část odpovědi bude obsahovat objekt RegistrationOperationStatus. Zařízení by mělo zachovat dotazování služby, pokud je kód odpovědi 202 po zpoždění rovnající se opakování po období. Operace registrace zařízení je úspěšná, pokud služba vrátí stavový kód 200.

## <a name="connecting-over-websocket"></a>Připojení přes websocket
Při připojování přes Websocket `mqtt`zadejte podprotokol jako . Sledujte [RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Další kroky

Další informace o protokolu MQTT naleznete v [dokumentaci K MQTT](https://mqtt.org/documentation).

Chcete-li dále prozkoumat možnosti DPS, viz:

* [O ioT DPS](about-iot-dps.md)
