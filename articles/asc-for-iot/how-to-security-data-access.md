---
title: Přístup k datům pomocí ASC pro náhled IoT | Dokumentace Microsoftu
description: Další informace o tom, jak přistupovat k datům upozornění a doporučení zabezpečení při použití ASC pro IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e394f6025f7898aad7dde7b1acefd9f95029a554
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541989"
---
# <a name="access-your-security-data"></a>Přístup k vašim datům zabezpečení 

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC pro IoT ukládá výstrahy zabezpečení, doporučení a zabezpečení nezpracovaná data (Pokud se rozhodnete ji uložit) ve vašem pracovním prostoru Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Postup konfigurace, který pracovní prostor Log Analytics se používá:

1. Otevřete své Centrum IoT.
1. Klikněte na tlačítko **zabezpečení**
2. Klikněte na tlačítko **nastavení**a změnit konfiguraci pracovního prostoru Log Analytics.

Pro přístup k pracovním prostoru Log Analytics po konfiguraci:

1. Vyberte výstrahu v ASC pro IoT. 
2. Klikněte na tlačítko **další šetření**, pak klikněte na tlačítko **zobrazíte, která zařízení mají tato výstraha, klikněte sem a zobrazit sloupce DeviceId**.

Podrobnosti o dotazování dat ze služby Log Analytics najdete v tématu [Začínáme s dotazy v Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Výstrahy zabezpečení

Výstrahy zabezpečení jsou uloženy v **ASCforIoT.SecurityAlert** tabulky v rámci nakonfigurovaného pracovního prostoru Log Analytics.

Následující dotazy základní kql použijte pro počáteční zkoumání výstrah zabezpečení.

### <a name="sample-records-query"></a>Ukázkový dotaz záznamů

Náhodný výběr několik záznamů: 

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

#### <a name="sample-query-results"></a>Ukázkové výsledky dotazu 

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | Zobrazovaný název                           | Popis                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | Útok hrubou silou byla úspěšná           | Útok hrubou silou na zařízení bylo úspěšné        |    {"Úplný zdrojový Address": "[\"10.165.12.18:\"]", "Uživatelská jména": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | Úspěšné místní přihlášení na zařízení      | Bylo zjištěno úspěšné místní přihlášení k zařízení     | {"Vzdálená adresa": "?", "Vzdálený Port": "", "Místní Port": "", "Login prostředí": "/ bin/su", "Id procesu přihlášení": "28207", "uživatelské jméno": "útočník", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | Neúspěšný pokus o místní přihlášení na zařízení  | Byl zjištěn pokus neúspěšných místní přihlášení k zařízení |  {"Vzdálená adresa": "?", "Vzdálený Port": "", "Místní Port": "", "Login prostředí": "/ bin/su", "Id procesu přihlášení": "22644", "uživatelské jméno": "útočník", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary-query"></a>Souhrn dotazu zařízení

Použijte tento dotaz kql vyberte počet výstrah zabezpečení různých zjistil minulý týden ve službě IoT Hub, zařízení, závažnost výstrahy, typ výstrahy.

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

#### <a name="device-summary-query-results"></a>Souhrn výsledků zařízení

| IoTHubId | DeviceId| AlertSeverity| Zobrazovaný název | Počet |
|----------|---------|------------------|---------|---------|
|/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | Útok hrubou silou byla úspěšná           | 9   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Střednědobé používání        | Neúspěšný pokus o místní přihlášení na zařízení  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Vysoký          | Úspěšné místní přihlášení na zařízení      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < název_zařízení > | Střednědobé používání        | Crypto Coin Miner                     | 4   |
|

### <a name="iot-hub-summary"></a>Souhrn služby IoT Hub

Pomocí tohoto dotazu kql vyberte počet zařízení, u kterých oznámení v posledním týdnu, ve službě IoT hub, závažnost výstrahy, typ výstrahy:

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

#### <a name="iot-hub-summary-query-results"></a>Souhrn výsledků služby IoT Hub

| IoTHubId                                                                                                       | AlertSeverity | Zobrazovaný název                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Vysoký          | Útok hrubou silou byla úspěšná           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Střednědobé používání        | Neúspěšný pokus o místní přihlášení na zařízení  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Vysoký          | Úspěšné místní přihlášení na zařízení      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Střednědobé používání        | Crypto Coin Miner                     | 1          |



## <a name="next-steps"></a>Další postup

- Přečtěte si ASC pro IoT [– přehled](overview.md)
- Další informace o ASC pro IoT [architektury](architecture.md)
- Porozumění a prozkoumávání [ASC pro IoT výstrahy](concept-security-alerts.md)
