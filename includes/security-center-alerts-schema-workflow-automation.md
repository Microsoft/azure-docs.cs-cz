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
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272672"
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
|**Název_zobrazení výstrah**|Řetězec|Zobrazovaný název výstrahy.|
|**Typ výstrahy**|Řetězec|Typ výstrahy. Výstrahy stejného typu by měly mít stejnou hodnotu. Toto pole je zadaný řetězec představující typ výstrahy a nikoli instance výstrahy. Všechny instance výstrahy ze stejné logiky detekce/analýzy by měly mít stejnou hodnotu pro typ výstrahy.|
|**Kompromitovaná entita**|Řetězec|Zobrazovaný název prostředku, který se nejvíce vztahuje k této výstraze.|
|**Popis**|Řetězec|Popis výstrahy.|
|**EndTimeUtc**|DateTime|Čas poslední události nebo aktivity zahrnuté ve výstraze.  Pole by mělo být řetězec, který odpovídá formátu ISO8601, včetně informací o časovém pásmu UTC.|
|**Entity**|IEnumerable (IEntity)|Seznam entit souvisejících s výstrahou. Tento seznam může obsahovat směs entit různých typů. Typ entit může být libovolný z typů definovaných v části Entity. Entity, které nejsou v seznamu níže, mohou být také odeslány, ale není zaručeno, že budou zpracovány (výstraha neselže ověření s novými typy entit).|
|**ExtendedProperties**|Slovník (řetězec,řetězec)|Zprostředkovatelé zde mohou (volitelně) zahrnovat vlastní pole.|
|**Záměr**|Výčet|Záměr související s řetězem zabití za výstrahou. Seznam podporovaných hodnot a vysvětlení podporovaných záměrů řetězce ochrany zabezpečení azure security center najdete [v tématu Záměry](../articles/security-center/alerts-reference.md#intentions).<br/>Toto pole může mít více hodnot (oddělených čárkou).|
|**IsIncident**|Logická hodnota|Toto pole určuje, zda se jedná o incident (složené seskupení několika výstrah) nebo jednu výstrahu. Výchozí hodnota pro pole je "false" (což znamená, že se jedná o jednu výstrahu).|
|**ProcessingEndTime**|DateTime|Čas, kdy byla výstraha dostupná koncovému uživateli v původním produktu, který výstrahu držel.|
|**Productname**|Řetězec|Název produktu, který tuto výstrahu publikoval (Azure Security Center, Azure ATP, Microsoft Defender ATP, O365 ATP, MCAS a tak dále).|
|**Remediasteps**|Seznamu<String>|Ruční položky akcí, které je třeba provést k nápravě výstrahy.|
|**ResourceIdentifiers**|Seznam (identifikátory prostředků)|Identifikátory prostředků pro tuto výstrahu, které lze použít k nasměrování výstrahy na správnou skupinu expozice produktu (tenant, pracovní prostor, předplatné atd.). Může existovat více identifikátorů různého typu na výstrahu.|
|**Závažnost**|Výčet|Závažnost výstrahy podle oznámení zprostředkovatele. Možné hodnoty: Informační, Nízká, Střední a Vysoká.|
|**StartTimeUtc**|DateTime|Čas první události nebo aktivity zahrnuté ve výstraze. Pole by mělo být řetězec, který odpovídá formátu ISO8601, včetně informací o časovém pásmu UTC.|
|**Stav**|Výčet|Stav životního cyklu výstrahy.<br/>Podporované stavy jsou: Nový, Vyřešeno, Zamítnuto, Neznámé.<br/>Výstraha, která určuje jinou hodnotu než podporované možnosti, má přiřazen stav Neznámý.<br/>Výstraha, která neurčuje hodnotu, má přiřazen stav Nový.|
|**Id systému výstrah**|Řetězec|Identifikátor výstrahy.|
|**TimeGenerated**|DateTime|Čas, kdy byla výstraha vygenerována poskytovatelem výstrahy. Pokud není hlášena interními poskytovateli výstrah, může se produkt rozhodnout přiřadit čas, kdy byl přijat ke zpracování produktem.  Pole by mělo být řetězec, který odpovídá formátu ISO8601, včetně informací o časovém pásmu UTC.|
|**Název_dodavatele**|Řetězec|Název dodavatele, který vyvolá výstrahu.|
|||
