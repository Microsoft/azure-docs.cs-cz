---
title: Řešení potíží s chybou služby Azure IoT Hub 401003 IoTHubNeautorizováno
description: Pochopit, jak opravit chybu 401003 IoTHubNeautorizováno
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f46d41c8287d03cbe9582ed560244cbd85cdeeaa
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759595"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Tento článek popisuje příčiny a řešení pro **401003 IoTHubNeautorizované** chyby.

## <a name="symptoms"></a>Příznaky

### <a name="symptom-1"></a>Příznak 1

V diagnostických protokolech se zobrazí vzor zařízení odpojení s **401003 IoTHubUnauthorized**, následuje **404104 DeviceConnectionClosedRemotely**a potom úspěšně připojení krátce poté.

### <a name="symptom-2"></a>Příznak 2

Požadavky na službu IoT Hub se nezdaří s jednou z následujících chybových zpráv:

* Chybí hlavička autorizace.
* IotHub\*' ' neobsahuje zadané\*zařízení "
* Autorizační\*pravidlo " " neumožňuje přístup pro "\*
* Ověřování se nezdařilo pro toto zařízení, obnovení tokenu nebo certifikátu a opětovné připojení
* Kryptografický otisk neodpovídá konfiguraci: Kryptografický otisk: SHA1Hash=\*, SHA2Hash=\*; Konfigurace: PrimaryThumbprint=\*, SecondaryThumbprint=\*

## <a name="cause"></a>Příčina

### <a name="cause-1"></a>Příčina 1

Pro MQTT některé sady SDK spoléhají na ioT Hub k vydání odpojení při vypršení platnosti tokenu SAS vědět, kdy jej aktualizovat. Takže 

1. Platnost tokenu SAS vyprší
1. IoT Hub si všimne vypršení platnosti a odpojí zařízení s **401003 IoTHubNeautorizované**
1. Zařízení dokončí odpojení pomocí **404104 DeviceConnectionClosedly**
1. Sada IoT SDK generuje nový token SAS
1. Zařízení se úspěšně znovu připojí k službě IoT Hub

### <a name="cause-2"></a>Příčina 2

IoT Hub nemohl ověřit ověřovací hlavičku, pravidlo nebo klíč.

## <a name="solution"></a>Řešení

### <a name="solution-1"></a>Řešení 1

Pokud používáte sadu IoT SDK pro připojení pomocí připojovacího řetězce zařízení, není nutná žádná akce. Sada IoT SDK regeneruje nový token pro opětovné připojení při vypršení platnosti tokenu SAS. 

Pokud objem chyb je problém, přepněte na C SDK, který obnoví token SAS před vypršením platnosti. Navíc pro AMQP token SAS lze aktualizovat bez odpojení.

### <a name="solution-2"></a>Řešení 2

Obecně platí, že chybová zpráva prezentovány by měl vysvětlit, jak opravit chybu. Pokud z nějakého důvodu nemáte přístup k podrobnostem chybové zprávy, ujistěte se, že:

- Platnost sas nebo jiného tokenu zabezpečení, který používáte, vypršela. 
- Autorizační pověření je dobře vytvořenpro protokol, který používáte. Další informace najdete v tématu [Řízení přístupu k centru IoT Hub](iot-hub-devguide-security.md).
- Použité autorizační pravidlo má oprávnění pro požadovanou operaci.

## <a name="next-steps"></a>Další kroky

Chcete-li usnadnit ověřování služby IoT Hub, doporučujeme používat [sady Azure IoT SDK](iot-hub-devguide-sdks.md).