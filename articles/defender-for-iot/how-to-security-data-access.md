---
title: Přístup k datům o doporučeních & zabezpečení
description: Přečtěte si, jak získat přístup k údajům o výstrahách zabezpečení a doporučeních při používání programu Defender pro IoT.
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 160f7c014c890f5d8c4dd6366d3acca70f21ad11
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781683"
---
# <a name="access-your-security-data"></a>Přístup k datům zabezpečení

Defender pro IoT ukládá výstrahy zabezpečení, doporučení a nezpracovaná data zabezpečení (Pokud se rozhodnete ji uložit) do svého pracovního prostoru Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Postup při konfiguraci používaného pracovního prostoru Log Analytics:

1. Otevřete Centrum IoT.
1. V části **zabezpečení** klikněte na okno **Nastavení** .
1. Klikněte na **shromažďování dat** a změňte konfiguraci pracovního prostoru Log Analytics.

Přístup k výstrahám a doporučením v pracovním prostoru Log Analytics po konfiguraci:

1. Vyberte výstrahu nebo doporučení v programu Defender pro IoT.
1. Klikněte na **Další šetření** a potom kliknutím na zobrazit, **která zařízení mají tato upozornění, klikněte sem a zobrazte sloupec DeviceID**.

Podrobnosti o dotazování na data z Log Analytics najdete v tématu Začínáme [s dotazy v Log Analytics](../azure-monitor/logs/get-started-queries.md).

## <a name="security-alerts"></a>Výstrahy zabezpečení

Výstrahy zabezpečení jsou uloženy v tabulce _AzureSecurityOfThings. SecurityAlert_ v pracovním prostoru Log Analytics nakonfigurovaném pro řešení Defender for IoT.

Nabízíme řadu užitečných dotazů, které vám pomůžou začít zkoumat výstrahy zabezpečení.

### <a name="sample-records"></a>Ukázkové záznamy

Vyberte několik náhodných záznamů.

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Popis                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Vysoká          | Útok hrubou silou uspěl           | Útok hrubou silou na zařízení byl úspěšný.        |    {"Úplná zdrojová adresa": "[ \" 10.165.12.18: \" ]", "uživatelská jména": "[ \" \" ]", "DeviceID": "IoT-Device-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Vysoká          | Místní přihlášení na zařízení bylo úspěšné.      | Zjistilo se úspěšné místní přihlášení k zařízení.     | {"Vzdálená adresa": "?", "vzdálený port": "", "místní port": "", "přihlašovací prostředí": "/bin/su", "ID procesu přihlášení": "28207", "uživatelské jméno": "útočník", "DeviceId": "IoT-Device-Linux"} |
| 2018-11-19T12:40:31.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Vysoká          | Neúspěšný pokus o přihlášení k místnímu zařízení  | Zjistil se neúspěšný místní pokus o přihlášení do zařízení. |    {"Vzdálená adresa": "?", "vzdálený port": "", "místní port": "", "přihlašovací prostředí": "/bin/su", "ID procesu přihlášení": "22644", "uživatelské jméno": "útočník", "DeviceId": "IoT-Device-Linux"} |

### <a name="device-summary"></a>Souhrn zařízení

Získejte počet jedinečných výstrah zabezpečení zjištěných za poslední týden seskupený podle IoT Hub, zařízení, závažnosti výstrahy, typu výstrahy.

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Počet |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Vysoká          | Útok hrubou silou uspěl           | 9   |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Střední        | Neúspěšný pokus o přihlášení k místnímu zařízení  | 242 |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Vysoká          | Místní přihlášení na zařízení bylo úspěšné.      | 31  |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Střední        | Kryptografická mince – Miner                     | 4   |

### <a name="iot-hub-summary"></a>Shrnutí centra IoT

Vyberte počet různých zařízení, která v minulém týdnu obsahovala výstrahy, IoT Hub, závažnost výstrahy, typ výstrahy.

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Vysoká          | Útok hrubou silou uspěl           | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Střední        | Neúspěšný pokus o přihlášení k místnímu zařízení  | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Vysoká          | Místní přihlášení na zařízení bylo úspěšné.      | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Střední        | Kryptografická mince – Miner                     | 1          |

## <a name="security-recommendations"></a>Doporučení zabezpečení

Doporučení zabezpečení jsou uložená v tabulce _AzureSecurityOfThings. SecurityRecommendation_ v pracovním prostoru Log Analytics nakonfigurovaném pro řešení Defender for IoT.

Nabízíme řadu užitečných dotazů, které vám pomůžou začít zkoumat doporučení týkající se zabezpečení.

### <a name="sample-records"></a>Ukázkové záznamy

Vyberte několik náhodných záznamů.

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Popis | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Střední | Aktivní | Bylo nalezeno opravňující pravidlo brány firewall ve vstupním řetězci. | Bylo zjištěno pravidlo brány firewall, které obsahuje povolující vzor pro široké spektrum IP adres nebo portů. | {"Rules": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" DestinationPort \" : \" 1337 \" }]"} |
| 2019-03-22T10:50:27.237 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Střední | Aktivní | Bylo nalezeno opravňující pravidlo brány firewall ve vstupním řetězci. | Bylo zjištěno pravidlo brány firewall, které obsahuje povolující vzor pro široké spektrum IP adres nebo portů. | {"Rules": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" DestinationPort \" : \" 1337 \" }]"} |

### <a name="device-summary"></a>Souhrn zařízení

Získejte počet různých aktivních doporučení pro zabezpečení, které jsou seskupené podle IoT Hub, zařízení, závažnosti doporučení a typu.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Počet |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Vysoká          | 2   |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Střední        | 1 |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Vysoká          | 1  |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Střední        | 4   |

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Přehled](overview.md) programu Defender for IoT.
- Další informace o programu Defender pro IoT [Architecture](architecture.md)
- Pochopení a prozkoumávání [výstrah v programu Defender pro IoT](concept-security-alerts.md)
- Pochopení a prozkoumávání [doporučení pro IoT v programu Defender](concept-recommendations.md)