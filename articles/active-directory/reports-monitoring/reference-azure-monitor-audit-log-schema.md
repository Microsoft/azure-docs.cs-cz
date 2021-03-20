---
title: Interpretace schématu protokolu auditu Azure Active Directory v Azure Monitor | Microsoft Docs
description: Popis schématu protokolu auditu Azure AD pro použití v Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4d32e0b939731d64f3ef2495ab606b5dc0780e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "100592277"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Interpretace schématu protokolů auditu Azure AD v Azure Monitor (Preview)

Tento článek popisuje schéma protokolu auditu Azure Active Directory (Azure AD) v Azure Monitor. Jednotlivé položky protokolu se ukládají jako text a naformátují jako objekt BLOB JSON, jak je znázorněno v následujících dvou příkladech: 

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

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Popisy polí a vlastností

| Název pole | Description |
|------------|-------------|
| time       | Datum a čas (UTC). |
| operationName | Název operace. |
| operationVersion | Verze REST API, kterou klient požaduje. |
| category | V současné době je jedinou podporovanou hodnotou *audit* . |
| tenantId | Identifikátor GUID tenanta, který je přidružený k protokolům. |
| resultType | Výsledek operace. Výsledkem může být *úspěch* nebo *neúspěch*. |
| resultSignature |  Toto pole není mapováno a můžete ho bezpečně ignorovat. | 
| resultDescription | Další popis výsledku, pokud je k dispozici. | 
| durationMs |  Toto pole není mapováno a můžete ho bezpečně ignorovat. |
| callerIpAddress | IP adresa klienta, který odeslal požadavek. | 
| correlationId | Volitelný identifikátor GUID, který je předán klientem. Může pomoct sladit operace na straně klienta s operacemi na straně serveru, které jsou užitečné při sledování protokolů, které zahrnují služby. |
| identity | Identita z tokenu, který byl předložen při podání žádosti. Identitou může být uživatelský účet, systémový účet nebo instanční objekt. |
| úroveň | Typ zprávy Pro protokoly auditu je úroveň vždy *informativní*. |
| location | Umístění datacentra. |
| properties | Zobrazuje seznam podporovaných vlastností, které se vztahují k protokolu auditu. Další informace najdete v další tabulce. | 

<br>

| Název vlastnosti | Description |
|---------------|-------------|
| AuditEventCategory | Typ události auditu. Může to být *Správa uživatelů*, *Správa aplikací* nebo jiný typ.|
| Typ identity | Typ může být *aplikace* nebo *uživatel*. |
| Typ operace | Typ lze *Přidat*, *aktualizovat*, *Odstranit*. nebo *jiné*. |
| Typ cílového prostředku | Určuje cílový typ prostředku, na kterém byla operace provedena. Typem může být *aplikace*, *uživatel*, *role*, *zásady* . | 
| Název cílového prostředku | Název cílového prostředku. Může to být název aplikace, název role, hlavní název uživatele nebo hlavní název služby. |
| additionalTargets | Zobrazí seznam všech dalších vlastností pro konkrétní operace. Například pro operaci aktualizace jsou staré hodnoty a nové hodnoty uvedeny v části *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Další kroky

* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Diagnostické protokoly Azure](../../azure-monitor/essentials/platform-logs-overview.md)
* [Nejčastější dotazy a známé problémy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)