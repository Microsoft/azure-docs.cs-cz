---
title: Přístup k datům pomocí Azure Security Center pro náhled IoT | Dokumentace Microsoftu
description: Další informace o tom, jak přistupovat k datům upozornění a doporučení zabezpečení při použití Azure Security Center pro IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 1ec6a174d05f8707bbffcc9fb013a98c2eb9196c
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200543"
---
# <a name="access-your-security-data"></a>Přístup k vašim datům zabezpečení 

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) pro službu IoT ukládá výstrahy zabezpečení, doporučení a zabezpečení nezpracovaná data (Pokud se rozhodnete ji uložit) ve vašem pracovním prostoru Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Postup konfigurace, který pracovní prostor Log Analytics se používá:

1. Otevřete své Centrum IoT.
1. Klikněte na tlačítko **zabezpečení**
2. Klikněte na tlačítko **nastavení**a změnit konfiguraci pracovního prostoru Log Analytics.

Pro přístup k pracovním prostoru Log Analytics po konfiguraci:

1. Vyberte výstrahu nebo doporučení v ASC pro IoT. 
2. Klikněte na tlačítko **další šetření**, pak klikněte na tlačítko **zobrazíte, která zařízení mají tato výstraha, klikněte sem a zobrazit sloupce DeviceId**.

Podrobnosti o dotazování dat ze služby Log Analytics najdete v tématu [Začínáme s dotazy v Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Výstrahy zabezpečení

Výstrahy zabezpečení jsou uloženy v _AzureSecurityOfThings.SecurityAlert_ tabulky v pracovním prostoru Log Analytics nakonfigurované pro ASC pro řešení IoT.

Uvádíme několik užitečných dotazy můžete začít zkoumat výstrahy zabezpečení.

### <a name="sample-records"></a>Ukázka záznamů

Vybrat několik náhodných záznamů

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | Zobrazovaný název                           | Popis                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | Útok hrubou silou byla úspěšná           | Útok hrubou silou na zařízení bylo úspěšné        |    {"Úplný zdrojový Address": "[\"10.165.12.18:\"]", "Uživatelská jména": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | Úspěšné místní přihlášení na zařízení      | Bylo zjištěno úspěšné místní přihlášení k zařízení     | {"Vzdálená adresa": "?", "Vzdálený Port": "", "Místní Port": "", "Login prostředí": "/ bin/su", "Id procesu přihlášení": "28207", "uživatelské jméno": "útočník", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | Neúspěšný pokus o místní přihlášení na zařízení  | Byl zjištěn pokus neúspěšných místní přihlášení k zařízení |  {"Vzdálená adresa": "?", "Vzdálený Port": "", "Místní Port": "", "Login prostředí": "/ bin/su", "Id procesu přihlášení": "22644", "uživatelské jméno": "útočník", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Přehledu zařízení

Vyberte počet výstrah odlišné zabezpečení detekovaných službou IoT Hub, zařízení, závažnost výstrahy, typ výstrahy minulý týden.

```
// Select number of distinct security alerts detected last week by 
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

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | Zobrazovaný název                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | Útok hrubou silou byla úspěšná           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Střednědobé používání        | Neúspěšný pokus o místní přihlášení na zařízení  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | Úspěšné místní přihlášení na zařízení      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Střednědobé používání        | Crypto Coin Miner                     | 4   |

### <a name="iot-hub-summary"></a>Souhrn služby IoT hub

Vyberte počet zařízení, u kterých oznámení v posledním týdnu, ve službě IoT Hub, závažnost výstrahy, typu výstrahy

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

| IoTHubId                                                                                                       | AlertSeverity | Zobrazovaný název                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Vysoký          | Útok hrubou silou byla úspěšná           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Střednědobé používání        | Neúspěšný pokus o místní přihlášení na zařízení  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Vysoký          | Úspěšné místní přihlášení na zařízení      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Střednědobé používání        | Crypto Coin Miner                     | 1          |

## <a name="security-recommendations"></a>Doporučení zabezpečení

Doporučení zabezpečení jsou uloženy v _AzureSecurityOfThings.SecurityRecommendation_ tabulky v pracovním prostoru Log Analytics nakonfigurované pro ASC pro řešení IoT.

Nabízíme řadu užitečné dotazy, které vám pomůžou získat počáteční zkoumání doporučení týkající se zabezpečení.

### <a name="sample-records"></a>Ukázka záznamů

Vybrat několik náhodných záznamů

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
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Střednědobé používání | Aktivní | Pravidlo povolující brány firewall ve vstupním řetězci nebyl nalezen. | V bráně firewall se zjistilo pravidlo, které obsahuje benevolentní vzor pro velký rozsah IP adres nebo portů. | {"Pravidla": "[{\"Adresa_zdroje\":\"\",\"Port_zdroje\":\"\",\"DestinationAddress\":\" \" \"DestinationPort\":\"1337\"}] "} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Střednědobé používání | Aktivní | Pravidlo povolující brány firewall ve vstupním řetězci nebyl nalezen. | V bráně firewall se zjistilo pravidlo, které obsahuje benevolentní vzor pro velký rozsah IP adres nebo portů. | {"Pravidla": "[{\"Adresa_zdroje\":\"\",\"Port_zdroje\":\"\",\"DestinationAddress\":\" \" \"DestinationPort\":\"1337\"}] "} |

### <a name="device-summary"></a>Přehledu zařízení

Vyberte počet doporučení týkající se různých active zabezpečení ve službě IoT Hub, zařízení, závažnosti doporučení a typ.

```
// Select number of distinct active security recommendations by 
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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Střednědobé používání        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Střednědobé používání        | 4   |


## <a name="next-steps"></a>Další postup

- Přečtěte si ASC pro IoT [– přehled](overview.md)
- Další informace o ASC pro IoT [architektury](architecture.md)
- Porozumění a prozkoumávání [ASC pro IoT výstrahy](concept-security-alerts.md)
- Porozumění a prozkoumávání [ASC pro IoT doporučení](concept-recommendations.md)