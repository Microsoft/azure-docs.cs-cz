---
title: Interpretace schéma protokolů auditu Azure Active Directory ve službě Azure Monitor (preview) | Dokumentace Microsoftu
description: Popisují schéma protokolů auditu Azure AD pro použití ve službě Azure Monitor (preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e1ae8e2a4dc9ef9c21300ebfc4df8c0f1c5819f2
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239857"
---
# <a name="interpret-the-azure-active-directory-audit-logs-schema-in-azure-monitor-preview"></a>Interpretace schématu protokoly auditování Azure Active Directory ve službě Azure Monitor (preview)

Tento článek popisuje schéma protokolů auditu Azure AD ve službě Azure Monitor. Každý záznam protokolu jednotlivé uložená jako text ve formátu JSON BLOB, jak je znázorněno následující dva příklady. 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

| Název pole | Popis |
|------------|-------------|
| time       | Datum a čas (UTC) |
| operationName | Název operace |
| operationVersion | Verze rozhraní REST API požadovaná klientem |
| category | V současné době *auditu* je jediná hodnota podporovaná |
| ID Tenanta | Identifikátor Guid klienta související s protokoly |
| resultType | Výsledek operace, může být *úspěch* nebo *selhání* |
| resultSignature |  Tím se zruší mapování a toto pole můžete klidně ignorovat. | 
| resultDescription | Další popis výsledku, pokud je k dispozici | 
| durationMs |  Tím se zruší mapování a toto pole můžete klidně ignorovat. |
| callerIpAddress | IP adresa klienta, ze kterého přišel požadavek | 
| correlationId | Volitelný Guid předaný klientem. To může pomoct korelovat operací na straně klienta s operací na straně serveru a je užitečné, když trasování protokolů, které jsou rozmístěny napříč službami. |
| identity | Identita z tokenu, který byl předložený při provádění požadavku. Může být uživatelský účet, účet system nebo službu objektu zabezpečení. |
| úroveň | Typ zprávy. K protokolům auditu, je to vždy *informativní* |
| location | Polohu datového centra. |
| properties | Obsahuje seznam podporovaných vlastností související s do protokolu auditu. Další informace naleznete následující tabulka. | 


| Název vlastnosti | Popis |
|---------------|-------------|
| AuditEventCategory | Typ události auditu. Může být *Správa uživatelů*, *správy aplikací* atd.|
| Typ identity | *Aplikace* nebo *uživatele* |
| Typ operace | Může být *přidat*, *aktualizace*, *odstranit* nebo *další* |
| Typ cílového prostředku | Určuje cílového typu prostředku, který byl v operaci provést. Může být *aplikace*, *uživatele*, *Role*, *zásad* | 
| Název cílového prostředku | Název cílového prostředku. To může být například název aplikace, název role, hlavní uživatelské jméno nebo hlavní název služby |
| additionalTargets | Uvádí další vlastnosti pro konkrétní operace. Například pro operaci aktualizace staré hodnoty a nové hodnoty patří *targetUpdatedProperties* | 

## <a name="next-steps"></a>Další postup

* [Interpretace protokolů přihlášení schématu ve službě Azure monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Další informace o diagnostické protokoly Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Nejčastější dotazy a známé problémy](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)