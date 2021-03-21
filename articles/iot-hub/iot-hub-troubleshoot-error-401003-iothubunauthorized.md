---
title: Řešení potíží s chybami Azure IoT Hub 401003 IoTHubUnauthorized
description: Vysvětlení, jak opravit chybu 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 8fb891d5a47203c9905a7def9d04199d24327f70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357245"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Tento článek popisuje příčiny a řešení 401003 chyb **IoTHubUnauthorized** .

## <a name="symptoms"></a>Příznaky

### <a name="symptom-1"></a>Příznak 1

V protokolech vidíte, že se zařízení odpojí s **401003 IoTHubUnauthorized**, následované **404104 DeviceConnectionClosedRemotely** a pak se úspěšně připojí krátce po.

### <a name="symptom-2"></a>Příznak 2

Požadavky na IoT Hub selžou s jednou z následujících chybových zpráv:

* Chybí autorizační hlavička.
* IotHub neobsahuje \* zadané zařízení. \*
* Autorizační pravidlo \* nepovoluje přístup pro \* .
* Pro toto zařízení se nepovedlo ověřování, obnovit token nebo certifikát a znovu se připojit.
* Kryptografický otisk neodpovídá konfiguraci: kryptografický otisk: SHA1Hash = \* , SHA2Hash = \* ; Konfigurace: PrimaryThumbprint = \* , SecondaryThumbprint =\*

## <a name="cause"></a>Příčina

### <a name="cause-1"></a>Příčina 1

U MQTT se některé sady SDK spoléhají na IoT Hub k vydání odpojení, když vyprší platnost tokenu SAS, který ví, kdy se má obnovit. Proto:

1. Vyprší platnost tokenu SAS.
1. IoT Hub si vyvšimla vypršení platnosti a odpojí zařízení s **401003 IoTHubUnauthorized**
1. Zařízení dokončí odpojení s **404104 DeviceConnectionClosedRemotely**
1. Sada IoT SDK generuje nový token SAS.
1. Zařízení se úspěšně znovu připojí k IoT Hub

### <a name="cause-2"></a>Příčina 2

IoT Hub nemůže ověřit záhlaví, pravidlo nebo klíč ověřování. To může být způsobeno jakýmkoli z důvodů uvedených v symptomech.

## <a name="solution"></a>Řešení

### <a name="solution-1"></a>Řešení 1

Pokud používáte sadu IoT SDK pro připojení pomocí připojovacího řetězce zařízení, není nutná žádná akce. Sada IoT SDK znovu vygeneruje nový token pro opětovné připojení k vypršení platnosti tokenu SAS.

Výchozí životnost tokenu je 60 minut napříč sadami SDK. pro některé sady SDK ale je životnost tokenu a prahová hodnota pro obnovení tokenu ale konfigurovatelná. Kromě toho se v každé sadě SDK liší chyby vygenerované při odpojení zařízení a opětovné připojení k obnovení tokenu. Další informace o tom, jak určit, kterou sadu SDK vaše zařízení používá v protokolech, najdete v tématu [MQTT – chování odpojení zařízení pomocí sad Azure IoT SDK](iot-hub-troubleshoot-connectivity.md#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

V případě vývojářů zařízení, pokud se jedná o problém, přepněte na sadu C SDK, která před vypršením platnosti obnoví token SAS. V případě AMQP se token SAS dá aktualizovat bez odpojení.

### <a name="solution-2"></a>Řešení 2

Obecně uvedená chybová zpráva by měla vysvětlit, jak chybu opravit. Pokud z nějakého důvodu nemáte přístup k podrobnostem chybové zprávy, ujistěte se, že:

- Platnost SAS nebo jiného tokenu zabezpečení, který používáte, už nevypršela.
- Pro ověřování pomocí certifikátu X. 509 nevypršela platnost certifikátu zařízení nebo certifikátu certifikační autority přidruženého k tomuto zařízení. Další informace o registraci certifikátů CA X. 509 pomocí IoT Hub najdete v tématu [nastavení zabezpečení X. 509 ve službě Azure IoT Hub](iot-hub-security-x509-get-started.md).
- V případě ověřování pomocí kryptografického otisku certifikátu X. 509 je kryptografický otisk certifikátu zařízení zaregistrován u IoT Hub.
- Přihlašovací údaje pro autorizaci jsou ve správném formátu pro protokol, který používáte. Další informace najdete v tématu [řízení přístupu k IoT Hub](iot-hub-devguide-security.md).
- Použité autorizační pravidlo má oprávnění pro požadovanou operaci.

## <a name="next-steps"></a>Další kroky

- Pro snazší ověřování IoT Hub doporučujeme použít sady [SDK Azure IoT](iot-hub-devguide-sdks.md).
- Podrobnosti o ověřování pomocí IoT Hub najdete v tématu [řízení přístupu k IoT Hub](iot-hub-devguide-security.md).
