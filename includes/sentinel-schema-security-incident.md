---
title: zahrnout soubor
description: zahrnout soubor
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88761711"
---
### <a name="the-data-model-of-the-schema"></a>Datový model schématu

| Pole | Datový typ | Popis |
| ---- | ---- | ---- |
| **AdditionalData** | dynamic | Počet výstrah, počet záložek, počet komentářů, názvy produktů s výstrahami a taktiku |
| **AlertIds** | dynamic | Výstrahy, ze kterých byl vytvořen incident |
| **BookmarkIds** | dynamic | Entity s záložkami |
| **Classification** | řetězec | Klasifikace uzavření incidentu |
| **ClassificationComment** | řetězec | Komentář k uzavření klasifikace incidentu |
| **ClassificationReason** | řetězec | Důvod klasifikace uzavření incidentu |
| **ClosedTime** | datetime | Časové razítko (UTC), kdy se incident naposledy zavřel |
| **Komentáře** | dynamic | Komentáře k incidentu |
| **CreatedTime** | datetime | Časové razítko (UTC), kdy se incident vytvořil |
| **Popis** | řetězec | Popis incidentu |
| **FirstActivityTime** | datetime | Čas první události |
| **FirstModifiedTime** | datetime | Časové razítko (UTC), kdy se incident poprvé změnil |
| **Nazevincidentu** | řetězec | Vnitřní identifikátor GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | řetězec | Odkaz na incident |
| **Popisky** | dynamic | Značky |
| **LastActivityTime** | datetime | Čas poslední události |
| **Časposledníúpravy** | datetime | Časové razítko (UTC), kdy se incident naposledy změnil <br>(úprava popsaná aktuálním záznamem) |
| **ModifiedBy** | řetězec | Uživatel nebo systém, který změnil incident |
| **Vlastník** | dynamic |  |
| **RelatedAnalyticRuleIds** | dynamic | Pravidla, ze kterých se aktivovaly výstrahy incidentu |
| **Závažnost** | řetězec | Závažnost incidentu (vysoká/střední/nízká/informativní) |
| **SourceSystem** | řetězec | Konstanta (' Azure ') |
| **Stav** | řetězec |  |
| **TenantId** | řetězec |  |
| **TimeGenerated** | datetime | Časové razítko (UTC), kdy se vytvořil aktuální záznam <br>(při změně incidentu) |
| **Název** | řetězec | 
| **Typ** | řetězec | Konstanta (' SecurityIncident ') |
|
