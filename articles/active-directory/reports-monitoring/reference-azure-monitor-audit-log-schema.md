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
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3df0087a21547585883f791d4d385025f92580c7
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395033"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Interpretace schéma protokolů auditu Azure AD ve službě Azure Monitor (preview)

Tento článek popisuje schéma protokolů auditu Azure Active Directory (Azure AD) ve službě Azure Monitor. Každý záznam protokolu jednotlivé je uložená jako text a formátovaný jako objekt blob JSON, jak je znázorněno v následující dva příklady: 

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

## <a name="field-and-property-descriptions"></a>Popisy polí a vlastností

| Název pole | Popis |
|------------|-------------|
| time       | Datum a čas (UTC). |
| operationName | Název operace. |
| operationVersion | Verze rozhraní REST API, které jsou požadovány klientem. |
| category | V současné době *auditu* je jediná podporovaná hodnota. |
| ID Tenanta | Klient identifikátor GUID, který je spojen s protokoly. |
| resultType | Výsledek operace. Výsledkem může být *úspěch* nebo *selhání*. |
| resultSignature |  Toto pole není mapován a můžete ho bezpečně ignorovat. | 
| resultDescription | Další popis výsledku, pokud je k dispozici. | 
| durationMs |  Toto pole není mapován a můžete ho bezpečně ignorovat. |
| callerIpAddress | IP adresa klienta, který vytvořil požadavek. | 
| correlationId | Volitelný GUID, který je předán klientem. Může být snazší korelovat operací na straně klienta s operací na straně serveru, a to je užitečné, když sledujete protokoly, které jsou rozmístěny služby. |
| identity | Identita z tokenu, který byl předložený při provedení žádosti. Identita může být uživatelský účet, účet system nebo instanční objekt služby. |
| úroveň | Typ zprávy. K protokolům auditu na úrovni je vždy *informativní*. |
| location | Umístění datového centra. |
| properties | Obsahuje seznam podporovaných vlastností, které se vztahují k protokolu auditu. Další informace najdete v následující tabulce. | 

<br>

| Název vlastnosti | Popis |
|---------------|-------------|
| AuditEventCategory | Typ události auditu. Může to být *Správa uživatelů*, *správy aplikací*, nebo jiného typu.|
| Typ identity | Typ může být *aplikace* nebo *uživatele*. |
| Typ operace | Typ může být *přidat*, *aktualizace*, *odstranit*. nebo *jiných*. |
| Typ cílového prostředku | Určuje cílového typu prostředku, který byl v operaci provést. Typ může být *aplikace*, *uživatele*, *Role*, *zásad* | 
| Název cílového prostředku | Název cílového prostředku. Může být název aplikace, název role, hlavní uživatelské jméno nebo hlavní název služby. |
| additionalTargets | Uvádí další vlastnosti pro konkrétní operace. Například pro operaci aktualizace staré hodnoty a nové hodnoty patří *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Další postup

* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Další informace o protokolech diagnostiky Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Nejčastější dotazy a známé problémy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
