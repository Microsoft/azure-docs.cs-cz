---
title: Přístup k datům doporučení & zabezpečení
description: Přečtěte si, jak získat přístup k datům upozornění zabezpečení a doporučení při používání Azure Security Center pro IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: bbea0accc79cafb6fea3f1438a71250dc02f4d62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311008"
---
# <a name="access-your-security-data"></a>Přístup k bezpečnostním datům

Azure Security Center pro IoT ukládá výstrahy zabezpečení, doporučení a nezpracovaná data zabezpečení (pokud se rozhodnete uložit) v pracovním prostoru Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Postup konfigurace používaného pracovního prostoru Log Analytics:

1. Otevřete centrum IoT.
1. Klikněte na okno **Přehled** v části **Zabezpečení.**
1. Klikněte na **Nastavení**a změňte konfiguraci pracovního prostoru Analýzy protokolů.

Přístup k upozorněním a doporučením v pracovním prostoru Log Analytics po konfiguraci:

1. V Azure Security Center pro IoT zvolte upozornění nebo doporučení.
1. Klikněte na **další šetření**, potom klikněte **na Chcete-li zjistit, která zařízení mají tuto výstrahu, klikněte zde a zobrazte sloupec DeviceId**.

Podrobnosti o dotazování dat z Log Analytics najdete [v tématu Začínáme s dotazy v Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Výstrahy zabezpečení

Výstrahy zabezpečení jsou uložené v tabulce _AzureSecurityOfThings.SecurityAlert_ v pracovním prostoru Log Analytics nakonfigurovaném pro řešení Azure Security Center for IoT.

Poskytli jsme řadu užitečných dotazů, které vám pomohou začít zkoumat výstrahy zabezpečení.

### <a name="sample-records"></a>Ukázkové záznamy

Výběr několika náhodných záznamů

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | Závažnost výstrahy | DisplayName                           | Popis                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Vysoká          | Útok hrubou silou byl úspěšný           | Útok hrubou silou na zařízení byl úspěšný.        |    \"{ "Full Source Address": "[ 10.165.12.18:\"]",\"\""Uživatelská jména": "[ ]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Vysoká          | Úspěšné místní přihlášení na zařízení      | Bylo zjištěno úspěšné místní přihlášení k zařízení.     | { "Vzdálená adresa": "?", "Vzdálený port": "", "Místní port": "", "Přihlašovací prostředí": "/bin/su", "Id přihlášení": "28207", "Uživatelské jméno": "útočník", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Vysoká          | Neúspěšný pokus o místní přihlášení v zařízení  | Byl zjištěn neúspěšný pokus o místní přihlášení k zařízení. |    { "Vzdálená adresa": "?", "Vzdálený port": "", "Místní port": "", "Přihlašovací prostředí": "/bin/su", "Id přihlášení": "22644", "Uživatelské jméno": "útočník", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Souhrn zařízení

Získejte počet různých výstrah zabezpečení zjištěných v posledním týdnu, seskupených podle IoT Hub, zařízení, závažnost výstrahy, typu výstrahy.

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

| IoTHubId                                                                                                       | DeviceId      | Závažnost výstrahy | DisplayName                           | Počet |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Vysoká          | Útok hrubou silou byl úspěšný           | 9   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Střednědobé používání        | Neúspěšný pokus o místní přihlášení v zařízení  | 242 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Vysoká          | Úspěšné místní přihlášení na zařízení      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Střednědobé používání        | Crypto mince Miner                     | 4   |

### <a name="iot-hub-summary"></a>Souhrn centra IoT hub

Vyberte řadu různých zařízení, která měla výstrahy v posledním týdnu, pomocí IoT Hub, závažnost výstrahy, typ výstrahy

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

| IoTHubId                                                                                                       | Závažnost výstrahy | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Vysoká          | Útok hrubou silou byl úspěšný           | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Střednědobé používání        | Neúspěšný pokus o místní přihlášení v zařízení  | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Vysoká          | Úspěšné místní přihlášení na zařízení      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Střednědobé používání        | Crypto mince Miner                     | 1          |

## <a name="security-recommendations"></a>Doporučení zabezpečení

Doporučení zabezpečení jsou uložená v tabulce _AzureSecurityOfThings.SecurityRecommendation_ v pracovním prostoru Log Analytics nakonfigurovaném pro řešení Azure Security Center for IoT.

Poskytli jsme řadu užitečných dotazů, které vám pomohou začít zkoumat doporučení zabezpečení.

### <a name="sample-records"></a>Ukázkové záznamy

Výběr několika náhodných záznamů

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

| TimeGenerated | IoTHubId | DeviceId | DoporučeníZávažnost | RecommendationState | RecommendationDisplayName | Popis | DoporučeníDalší údaje |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Střednědobé používání | Aktivní | Bylo nalezeno pravidlo permisivní brány firewall ve vstupním řetězci. | Bylo nalezeno pravidlo brány firewall, které obsahuje tolerantní vzor pro širokou škálu adres IP nebo portů. | {"Rules":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\"\"\":\",\"\"DestinationAddress :\", DestinationPort : 1337 }]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Střednědobé používání | Aktivní | Bylo nalezeno pravidlo permisivní brány firewall ve vstupním řetězci. | Bylo nalezeno pravidlo brány firewall, které obsahuje tolerantní vzor pro širokou škálu adres IP nebo portů. | {"Rules":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\"\"\":\",\"\"DestinationAddress :\", DestinationPort : 1337 }]"} |

### <a name="device-summary"></a>Souhrn zařízení

Získejte počet různých aktivních doporučení zabezpečení seskupených podle služby IoT Hub, zařízení, závažnosti doporučení a typu.

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

| IoTHubId                                                                                                       | DeviceId      | DoporučeníZávažnost | Počet |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Vysoká          | 2   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Střednědobé používání        | 1 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Vysoká          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name device_name device_name> | Střednědobé používání        | 4   |

## <a name="next-steps"></a>Další kroky

- Přečtěte si [přehled](overview.md) Centra zabezpečení Azure pro IoT
- Další informace o Azure Security Center pro [architekturu](architecture.md) IoT
- Pochopení a prozkoumání [výstrah Centra zabezpečení Azure pro IoT](concept-security-alerts.md)
- Pochopení a prozkoumání [doporučení Centra zabezpečení Azure pro IoT](concept-recommendations.md)
