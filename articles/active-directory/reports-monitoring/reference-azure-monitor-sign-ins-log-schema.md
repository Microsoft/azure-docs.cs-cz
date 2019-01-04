---
title: Interpretace schéma protokolu přihlašování Azure Active Directory ve službě Azure Monitor (preview) | Dokumentace Microsoftu
description: Popis přihlášení Azure AD schéma protokolu pro použití ve službě Azure Monitor (preview)
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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0c44501a483b9a988c3ef048d12455eb3e5ae337
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546395"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor-preview"></a>Interpretace schéma protokolů přihlášení Azure AD ve službě Azure Monitor (preview)

Tento článek popisuje schéma protokolu přihlašování Azure Active Directory (Azure AD) ve službě Azure Monitor. Většinu informací týkající se přihlášení není k dispozici *vlastnosti* atribut `records` objektu.

```json
{ 
    "records": [ 
    { 
        "time": "2018-05-16T16:09:58.4634578Z", 
        "resourceId": null, 
        "operationName": "Sign-in activity", 
        "operationVersion": "1.0", 
        "category": "SignIn", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "50140", 
        "resultSignature": "None", 
        "resultDescription": "Other", 
        "durationMs": 0, 
        "callerIpAddress": "167.220.0.158", 
        "correlationId": "13e19598-e040-487f-bd32-d38a2cd75d9a", 
        "identity": "arvind harinder", 
        "Level": 4, 
        "location": "US", 
        "properties": { 
            "id": "0782c515-08b6-4029-a65c-29d9a3d20800", 
            "createdDateTime": "2018-05-16T16:09:58.4634578+00:00", 
            "userDisplayName": "Arvind Harinder", 
            "userPrincipalName": "ah@wingtiptoysonline.onmicrosoft.com", 
            "userId": "5b9f356d-9592-42fd-9ec4-d70963909534", 
            "appId": "c44b4083-3bb0-49c1-b47d-974e53cbdf3c", 
            "appDisplayName": "Azure Portal", 
            "ipAddress": "167.220.0.158", 
            "status": { 
                "errorCode": 50140, 
                "failureReason": "Other" 
            }, 
            "clientAppUsed": "Browser", 
            "deviceDetail": { 
                "operatingSystem": "Windows 10", 
                "browser": "Chrome 66.0.3359" 
            }, 
            "location": { 
                "city": "Sammamish", 
                "state": "Washington", 
                "countryOrRegion": "US", 
                "geoCoordinates": { 
                    "latitude": 47.66630935668945, 
                    "longitude": -122.09821319580078 
                } 
            }, 
            "correlationId": "13e19598-e040-487f-bd32-d38a2cd75d9a", 
            "conditionalAccessStatus": 2, 
            "conditionalAccessPolicies": [ 
            { 
                "id": "de7e60eb-ed89-4d73-8205-2227def6b7c9", 
                "displayName": "[billg] SharePoint limited access policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "7412a2d8-cbb1-4f1c-96cf-8410b4b8b37b", 
                "displayName": "[BillG] AIP MFA Policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "727ed8ea-059d-4d8f-aba5-c1dc500e8b06", 
                "displayName": "[billg] mfa for mail", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "6701123a-b4c6-48af-8565-565c8bf7cabc", 
                "displayName": "Medium signin risk block", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "fbafa2da-cf7f-4ec3-83cf-281188e53f76", 
                "displayName": "Require MFA for admins [Ignite talk] ", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "15339054-709d-4e06-a9ec-342bf043ea56", 
                "displayName": "Enhanced proofing for Azure portal [Ignite talk]", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "2ff9436f-bc72-4ce6-b17e-e7e51153146e", 
                "displayName": "[calebb] AIP policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "46ab586b-9447-4847-a889-e60705d96e56", 
                "displayName": "Test policy, OR", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "ceb6e17e-a5d0-4b3a-a150-6c2be2d5b0e9", 
                "displayName": "mm policy with Duo", 
                "enforcedGrantControls": [ 
                    "Require Duo Mfa" 
                ], 
            "enforcedSessionControls": [], 
            "result": 2 
            }, 
            ], 
            "isRisky": false 
            } 
        } 
    } 
```

## <a name="field-descriptions"></a>Popis pole

| Název pole | Popis |
|------------|-------------|
| Čas | Datum a čas ve standardu UTC. |
| ID prostředku | Tato hodnota není mapován a toto pole můžete klidně ignorovat.  |
| OperationName | Pro přihlášení, tato hodnota je vždy *aktivit přihlašování*. |
| OperationVersion | Verze rozhraní REST API, které jsou požadovány klientem. |
| Kategorie | Pro přihlášení, tato hodnota je vždy *SignIn*. | 
| TenantId | Klient identifikátor GUID, který je spojen s protokoly. |
| Hodnota resultType | Výsledek operace přihlášení může být *úspěch* nebo *selhání*. | 
| resultSignature | Obsahuje kód chyby, pokud existuje, pro přihlašovací operaci. |
| resultDescription | Obsahuje popis chyby pro operace přihlášení. |
| doby trvání v MS |  Tato hodnota není mapován a toto pole můžete klidně ignorovat.|
| callerIpAddress | IP adresa klienta, který vytvořil požadavek. | 
| CorrelationId | Volitelný GUID, který je předaný klientem. Tato hodnota může pomoct korelovat operací na straně klienta s operací na straně serveru, a to je užitečné, když sledujete protokoly, které jsou rozmístěny služby. |
| Identita | Identita z tokenu, který byl předložený při provedení žádosti. To může být uživatelský účet, účet system nebo instanční objekt služby. |
| Úroveň | Poskytuje typ zprávy. Pro audit, je vždy *informativní*. |
| Umístění | Poskytuje umístění aktivit přihlašování. |
| Vlastnosti | Obsahuje seznam všech vlastností, které jsou spojeny s přihlášení. Další informace najdete v tématu [Reference k rozhraní API Microsoft Graphu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Toto schéma používá stejné názvy atributů jako prostředek přihlášení pro lepší čitelnost.

## <a name="next-steps"></a>Další postup

* [Interpretace schématu protokolů auditu ve službě Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Další informace o diagnostické protokoly Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
