---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91401113"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>Datový model schématu

|Pole|Datový typ|Popis|
|----|----|----|
|**AlertDisplayName**|Řetězec|Zobrazovaný název výstrahy.|
|**AlertType**|Řetězec|Typ výstrahy Výstrahy stejného typu by měly mít stejnou hodnotu. Toto pole je řetězec s klíčem, který představuje typ výstrahy, nikoli instanci výstrahy. Všechny instance výstrahy ze stejné logiky detekce a analytiky by měly mít stejnou hodnotu jako typ výstrahy.|
|**CompromisedEntity**|Řetězec|Zobrazovaný název prostředku, který se nejvíce týká této výstrahy.|
|**Popis**|Řetězec|Popis výstrahy|
|**EndTimeUtc**|DateTime|Čas poslední události nebo aktivity obsažené v upozornění.  Pole by měl být řetězec, který odpovídá formátu ISO8601, včetně informací o časovém pásmu UTC.|
|**Entity**|IEnumerable (IEntity)|Seznam entit souvisejících s výstrahou. Tento seznam může obsahovat různé entity různých typů. Typ entity může být libovolný z typů definovaných v části Entities. Entity, které nejsou v seznamu níže, je možné také odeslat, ale není zaručeno, že budou zpracovány (výstraha nebude při ověřování u nových typů entit neúspěšná).|
|**ExtendedProperties**|Dictionary (String; String)|Poskytovatelé můžou (volitelně) zahrnovat vlastní pole.|
|**Záměr**|Výčet|Záměr související s dezaktivačním řetězem za výstrahou. Seznam podporovaných hodnot a vysvětlení podporovaných záměrů dezaktivačního řetězu Azure Security Center naleznete v tématu [záměry](../articles/security-center/alerts-reference.md#intentions).<br/>Toto pole může obsahovat více hodnot (oddělených čárkami).|
|**Incident**|Logická hodnota|Toto pole určuje, zda je výstraha incidentem (složené seskupení několika výstrah) nebo jedinou výstrahou. Výchozí hodnota pro pole je false (to znamená, že se jedná o jedinou výstrahu).|
|**ProcessingEndTime**|DateTime|Čas, kdy byla výstraha přístupná koncovému uživateli v původním produktu, který tuto výstrahu zachovává.|
|**NázevVýrobku**|Řetězec|Název produktu, který publikoval toto upozornění (Azure Security Center, Azure ATP, Microsoft Defender ATP, MCAS atd.).|
|**RemediationSteps**|Seznam<String>|Položky ručních akcí, které se mají provést k nápravě výstrahy|
|**ResourceIdentifiers**|Seznam (identifikátory prostředků)|Identifikátory prostředků pro tuto výstrahu, které se dají použít k přesměrování výstrahy do správné skupiny expozice produktů (tenant, pracovní prostor, předplatné atd.). Pro každou výstrahu může existovat více identifikátorů různého typu.|
|**Závažnost**|Výčet|Závažnost výstrahy, jak je hlášena zprostředkovatelem. Možné hodnoty: informativní, nízká, střední a vysoká.|
|**StartTimeUtc**|DateTime|Čas první události nebo aktivity obsažené v upozornění. Pole by měl být řetězec, který odpovídá formátu ISO8601, včetně informací o časovém pásmu UTC.|
|**Stav**|Výčet|Stav životního cyklu výstrahy.<br/>Podporované stavy jsou: nové, vyřešené, chybějící, neznámé.<br/>Výstraha, která určuje jinou hodnotu než podporované možnosti, má přiřazen stav "Neznámý".<br/>Výstraha, která neurčuje hodnotu, má přiřazen stav New.|
|**SystemAlertId**|Řetězec|Identifikátor výstrahy.|
|**TimeGenerated**|DateTime|Čas, kdy byla výstraha vygenerována poskytovatelem výstrahy. Pokud nehlásí interní zprostředkovatelé výstrah, produkt se může rozhodnout přiřadit čas, který byl přijat pro zpracování produktem.  Pole by měl být řetězec, který odpovídá formátu ISO8601, včetně informací o časovém pásmu UTC.|
|**Dodavatel**|Řetězec|Název dodavatele, který vyvolá výstrahu.|
|||
