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
ms.openlocfilehash: 76020b3c1f28e5b5f6363aef181b76bc93a9613e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294088"
---
### <a name="the-data-model-of-the-schema"></a>Datový model schématu

| Pole | Datový typ | Popis |
| ---- | ---- | ---- |
| **AdditionalData** | dynamické | Počet výstrah, počet záložek, počet komentářů, názvy produktů s výstrahami a taktiku |
| **AlertIds** | dynamické | Výstrahy, ze kterých byl vytvořen incident |
| **BookmarkIds** | dynamické | Entity s záložkami |
| **Classification** | řetězec | Klasifikace uzavření incidentu |
| **ClassificationComment** | řetězec | Komentář k uzavření klasifikace incidentu |
| **ClassificationReason** | řetězec | Důvod klasifikace uzavření incidentu |
| **ClosureTime** | datetime | Časové razítko (UTC), kdy se incident naposledy zavřel |
| **Komentáře** | dynamické | Komentáře k incidentu |
| **CreatedTime** | datetime | Časové razítko (UTC), kdy se incident vytvořil |
| **Popis** | řetězec | Popis incidentu |
| **FirstActivityTime** | datetime | Čas první události |
| **FirstModifiedTime** | datetime | Časové razítko (UTC), kdy se incident poprvé změnil |
| **Nazevincidentu** | řetězec | Vnitřní identifikátor GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | řetězec | Odkaz na incident |
| **Popisky** | dynamické | Značky |
| **LastActivityTime** | datetime | Čas poslední události |
| **Časposledníúpravy** | datetime | Časové razítko (UTC), kdy se incident naposledy změnil <br>(úprava popsaná aktuálním záznamem) |
| **ModifiedBy** | řetězec | Uživatel nebo systém, který změnil incident |
| **Vlastník** | dynamické |  |
| **RelatedAnalyticRuleIds** | dynamické | Pravidla, ze kterých se aktivovaly výstrahy incidentu |
| **Závažnost** | řetězec | Závažnost incidentu (vysoká/střední/nízká/informativní) |
| **SourceSystem** | řetězec | Konstanta (' Azure ') |
| **Stav** | řetězec |  |
| **TenantId** | řetězec |  |
| **TimeGenerated** | datetime | Časové razítko (UTC), kdy se vytvořil aktuální záznam <br>(při změně incidentu) |
| **Název** | řetězec | 
| **Typ** | řetězec | Konstanta (' SecurityIncident ') |
|
