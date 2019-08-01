---
title: Přístup k datům pomocí Azure Security Center pro IoT | Microsoft Docs
description: Přečtěte si, jak získat přístup k údajům o výstrahách zabezpečení a doporučeních při použití Azure Security Center pro IoT.
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
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597178"
---
# <a name="access-your-security-data"></a>Přístup k datům zabezpečení 

Azure Security Center pro IoT ukládá výstrahy zabezpečení, doporučení a nezpracovaná data zabezpečení (Pokud se v pracovním prostoru Log Analytics rozhodnete ji uložit).

## <a name="log-analytics"></a>Log Analytics

Postup při konfiguraci používaného pracovního prostoru Log Analytics:

1. Otevřete Centrum IoT.
1. Klikněte na okno **Přehled** v části **zabezpečení** .
2. Klikněte na **Nastavení**a změňte konfiguraci Log Analytics pracovního prostoru.

Přístup k výstrahám a doporučením v pracovním prostoru Log Analytics po konfiguraci:

1. Vyberte výstrahu nebo doporučení v Azure Security Center pro IoT. 
2. Klikněte na **Další šetření**a potom kliknutím na zobrazit, **která zařízení mají tato upozornění, klikněte sem a zobrazte sloupec DeviceID**.

Podrobnosti o dotazování na data z Log Analytics najdete v tématu Začínáme [s dotazy v Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Výstrahy zabezpečení

Výstrahy zabezpečení jsou uloženy v tabulce _AzureSecurityOfThings. SecurityAlert_ v pracovním prostoru Log Analytics nakonfigurovaném pro řešení Azure Security Center for IoT.

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
| 2018-11-18T18:10:29.000 | /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Vysoká          | Útok hrubou silou uspěl           | Útok hrubou silou na zařízení byl úspěšný.        |    {"Úplná zdrojová adresa": "[\"10.165.12.18:\"]", "uživatelská jména": "[\"\"]", "DeviceID": "IoT-Device-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Vysoká          | Místní přihlášení na zařízení bylo úspěšné.      | Zjistilo se úspěšné místní přihlášení k zařízení.     | {"Vzdálená adresa": "?", "vzdálený port": "", "místní port": "", "přihlašovací prostředí": "/bin/su", "ID procesu přihlášení": "28207", "uživatelské jméno": "útočník", "DeviceId": "IoT-Device-Linux"} |
| 2018-11-19T12:40:31.000 | /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Vysoká          | Neúspěšný pokus o přihlášení k místnímu zařízení  | Zjistil se neúspěšný místní pokus o přihlášení do zařízení. |  {"Vzdálená adresa": "?", "vzdálený port": "", "místní port": "", "přihlašovací prostředí": "/bin/su", "ID procesu přihlášení": "22644", "uživatelské jméno": "útočník", "DeviceId": "IoT-Device-Linux"} |

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

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Vysoká          | Útok hrubou silou uspěl           | 9   |   
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Střední        | Neúspěšný pokus o přihlášení k místnímu zařízení  | 242 |    
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Vysoká          | Místní přihlášení na zařízení bylo úspěšné.      | 31  |
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Střední        | Kryptografická mince – Miner                     | 4   |

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
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | Vysoká          | Útok hrubou silou uspěl           | 1          |    
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | Střední        | Neúspěšný pokus o přihlášení k místnímu zařízení  | 1          | 
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | Vysoká          | Místní přihlášení na zařízení bylo úspěšné.      | 1          |
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | Střední        | Kryptografická mince – Miner                     | 1          |

## <a name="security-recommendations"></a>Doporučení zabezpečení

Doporučení zabezpečení jsou uložená v tabulce _AzureSecurityOfThings. SecurityRecommendation_ v pracovním prostoru Log Analytics nakonfigurovaném pro řešení Azure Security Center for IoT.

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
| 2019-03-22T10:21:06.060 | /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Střední | Aktivní | Bylo nalezeno opravňující pravidlo brány firewall ve vstupním řetězci. | V bráně firewall se zjistilo pravidlo, které obsahuje benevolentní vzor pro velký rozsah IP adres nebo portů. | {"Rules": "[\"{\"SourceAddress\":\"\",\"SourcePort\":\",DestinationAddress\":\" \"\" ,\"DestinationPort\":\"1337}]"}\" |
| 2019-03-22T10:50:27.237 | /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Střední | Aktivní | Bylo nalezeno opravňující pravidlo brány firewall ve vstupním řetězci. | V bráně firewall se zjistilo pravidlo, které obsahuje benevolentní vzor pro velký rozsah IP adres nebo portů. | {"Rules": "[\"{\"SourceAddress\":\"\",\"SourcePort\":\",DestinationAddress\":\" \"\" ,\"DestinationPort\":\"1337}]"}\" |

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

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Vysoká          | 2   |    
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Střední        | 1 |  
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Vysoká          | 1  |
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < DEVICE_NAME > | Střední        | 4   |


## <a name="next-steps"></a>Další postup

- Přečtěte si [přehled](overview.md) Azure Security Center pro IoT.
- Informace o [architektuře](architecture.md) Azure Security Center pro IoT
- Pochopení a zkoumání [Azure Security Center výstrah IoT](concept-security-alerts.md)
- Pochopení a prozkoumání [Azure Security Center pro doporučení IoT](concept-recommendations.md)
