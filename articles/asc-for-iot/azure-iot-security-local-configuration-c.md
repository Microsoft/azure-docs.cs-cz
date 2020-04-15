---
title: Místní konfigurace agenta zabezpečení (C)
description: Přečtěte si o Azure Security Center pro místní konfigurace agenta pro C.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: cd344b9bebb69af210c482f46af6b2dd7edf7816
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311711"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Vysvětlení souboru LocalConfiguration.json – agent v jazyce C

Azure Security Center pro agenta zabezpečení IoT používá konfigurace z místního konfiguračního souboru.
Agent zabezpečení přečte konfiguraci jednou při spuštění agenta.
Konfigurace nalezená v místním konfiguračním souboru obsahuje konfiguraci ověřování a další konfigurace související s agentem.
Soubor obsahuje konfigurace v párech "Key-Value" v zápisu JSON a konfigurace se naplní při instalaci agenta.

Ve výchozím nastavení je soubor umístěn na adrese: /var/ASCIoTAgent/LocalConfiguration.json

Změny konfiguračního souboru proběhnou při restartování agenta.

## <a name="security-agent-configurations-for-c"></a>Konfigurace agenta zabezpečení pro C

| Název konfigurace | Možné hodnoty | Podrobnosti |
|:-----------|:---------------|:--------|
| AgentId | GUID | Jedinečný identifikátor agenta |
| TriggerdEventsInterval | Řetězec ISO8601 | Interval plánovače pro shromažďování aktiv událostí |
| Connectiontimeout | Řetězec ISO8601 | Časové období před dosažením časového limitu připojení k ioThubu |
| Authentication | Objekt Json | Konfigurace ověřování. Tento objekt obsahuje všechny informace potřebné pro ověřování proti IoTHub |
| Identita | "DPS", "Bezpečnostní modul", "Zařízení" | Ověřovací identita – DPS, pokud je ověřování provedeno prostřednictvím DPS, SecurityModule, pokud je ověřování provedeno pomocí pověření modulu zabezpečení nebo zařízení, pokud je ověřování provedeno pomocí pověření zařízení |
| Metoda ověřování | "SasToken", "SelfSignedCertificate" | Tajný klíč uživatele pro ověřování – Zvolte SasToken, pokud je tajný klíč použití symetrický, zvolte certifikát podepsaný svým držitelem, pokud je tajný klíč certifikát podepsaný svým držitelem  |
| Filepath | Cesta k souboru (řetězec) | Cesta k souboru, který obsahuje tajný klíč ověřování |
| HostName | řetězec | Název hostitele azure iot hub. obvykle <my-hub>.azure-devices.net |
| DeviceId | řetězec | ID zařízení (registrované v Azure IoT Hub) |
| DPS | Objekt Json | Konfigurace související s DPS |
| IDScope | řetězec | ID rozsah DPS |
| Id registrace | řetězec  | ID registrace zařízení DPS |
| Protokolování | Objekt Json | Konfigurace související s protokolovači agenta |
| SystemLoggerMinimumZávažnost | 0 <= počet <= 4 | protokolové zprávy stejné a vyšší než tato závažnost budou zaznamenány do /var/log/syslog (0 je nejnižší závažnost) |
| DiagnosticEventMinimumSverity | 0 <= počet <= 4 | Zprávy protokolu stejné a vyšší než tato závažnost budou odeslány jako diagnostické události (0 je nejnižší závažnost) |

## <a name="security-agent-configurations-code-example"></a>Příklad kódu konfigurace agenta zabezpečení

```JSON
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si [přehled](overview.md) služby Azure Security Center for IoT
- Další informace o Azure Security Center pro [architekturu](architecture.md) IoT
- Povolení [služby](quickstart-onboard-iot-hub.md) Azure Security Center for IoT
- Přečtěte si [nejčastější dotazy](resources-frequently-asked-questions.md) ke Službě Azure Security Center for IoT
- Zjistěte, jak získat přístup k [nezpracovaným bezpečnostním datům](how-to-security-data-access.md)
- Principy [doporučení](concept-recommendations.md)
- Principy [výstrah](concept-security-alerts.md) zabezpečení
