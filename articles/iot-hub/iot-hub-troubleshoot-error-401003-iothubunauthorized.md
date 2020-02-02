---
title: Řešení potíží s chybami Azure IoT Hub 401003 IoTHubUnauthorized
description: Vysvětlení, jak opravit chybu 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d0a9f643516af5945037acb3dd1da24b06944171
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960630"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Tento článek popisuje příčiny a řešení 401003 chyb **IoTHubUnauthorized** .

## <a name="symptoms"></a>Příznaky

### <a name="symptom-1"></a>Příznak 1

V diagnostických protokolech vidíte vzor zařízení odpojování s **401003 IoTHubUnauthorized**, následovaný **404104 DeviceConnectionClosedRemotely**a pak se úspěšně připojí krátce po.

### <a name="symptom-2"></a>Příznak 2

Požadavky na IoT Hub selžou s jednou z následujících chybových zpráv:

* Chybí autorizační hlavička.
* IotHub '\*' neobsahuje zadané zařízení '\*'
* Autorizační pravidlo\*nepovoluje přístup pro\*.
* Pro toto zařízení se nepovedlo ověřování, obnovit token nebo certifikát a znovu se připojit.
* Kryptografický otisk neodpovídá konfiguraci: kryptografický otisk: SHA1Hash =\*, SHA2Hash =\*; Konfigurace: PrimaryThumbprint =\*, SecondaryThumbprint =\*

## <a name="cause"></a>Příčina

### <a name="cause-1"></a>Příčiny 1

U MQTT se některé sady SDK spoléhají na IoT Hub k vydání odpojení, když vyprší platnost tokenu SAS, který ví, kdy se má obnovit. Abyste 

1. Vyprší platnost tokenu SAS.
1. IoT Hub si vyvšimla vypršení platnosti a odpojí zařízení s **401003 IoTHubUnauthorized**
1. Zařízení dokončí odpojení s **404104 DeviceConnectionClosedRemotely**
1. Sada IoT SDK generuje nový token SAS.
1. Zařízení se úspěšně znovu připojí k IoT Hub

### <a name="cause-2"></a>Příčiny 2

IoT Hub nemůže ověřit záhlaví, pravidlo nebo klíč ověřování.

## <a name="solution"></a>Řešení

### <a name="solution-1"></a>Řešení 1

Pokud používáte sadu IoT SDK pro připojení pomocí připojovacího řetězce zařízení, není nutná žádná akce. Sada IoT SDK znovu vygeneruje nový token pro opětovné připojení k vypršení platnosti tokenu SAS. 

Pokud se jedná o problém, přepněte se na sadu C SDK, která před vypršením platnosti obnoví token SAS. Navíc pro AMQP se může token SAS aktualizovat bez odpojení.

### <a name="solution-2"></a>Řešení 2

Obecně uvedená chybová zpráva by měla vysvětlit, jak chybu opravit. Pokud z nějakého důvodu nemáte přístup k podrobnostem chybové zprávy, ujistěte se, že:

- Platnost SAS nebo jiného tokenu zabezpečení, který používáte, už nevypršela. 
- Přihlašovací údaje pro autorizaci jsou ve správném formátu pro protokol, který používáte. Další informace najdete v tématu [IoT Hub Access Control](iot-hub-devguide-security.md).
- Použité autorizační pravidlo má oprávnění pro požadovanou operaci.

## <a name="next-steps"></a>Další kroky

Pro snazší ověřování IoT Hub doporučujeme použít sady [SDK Azure IoT](iot-hub-devguide-sdks.md).