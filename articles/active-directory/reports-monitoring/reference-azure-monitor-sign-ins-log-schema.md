---
title: Azure log přihlášení schéma služby Active Directory ve službě Azure Monitor | Dokumentace Microsoftu
description: Popis přihlášení Azure AD schéma protokolu pro použití ve službě Azure Monitor
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
ms.openlocfilehash: a8ac6c56dca100ea9836158f46881c4eb12213e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60285200"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interpretace schéma protokolů přihlášení Azure AD ve službě Azure Monitor

Tento článek popisuje schéma protokolu přihlašování Azure Active Directory (Azure AD) ve službě Azure Monitor. Většinu informací týkající se přihlášení není k dispozici *vlastnosti* atribut `records` objektu.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Popis pole

| Název pole | Popis |
|------------|-------------|
| Čas | Datum a čas ve standardu UTC. |
| ResourceId | Tato hodnota není mapován a toto pole můžete klidně ignorovat.  |
| OperationName | Pro přihlášení, tato hodnota je vždy *aktivit přihlašování*. |
| OperationVersion | Verze rozhraní REST API, které jsou požadovány klientem. |
| Category | Pro přihlášení, tato hodnota je vždy *SignIn*. | 
| TenantId | Klient identifikátor GUID, který je spojen s protokoly. |
| ResultType | Výsledek operace přihlášení může být *úspěch* nebo *selhání*. | 
| resultSignature | Obsahuje kód chyby, pokud existuje, pro přihlašovací operaci. |
| resultDescription | Obsahuje popis chyby pro operace přihlášení. |
| DurationMs |  Tato hodnota není mapován a toto pole můžete klidně ignorovat.|
| CallerIpAddress | IP adresa klienta, který vytvořil požadavek. | 
| CorrelationId | Volitelný GUID, který je předaný klientem. Tato hodnota může pomoct korelovat operací na straně klienta s operací na straně serveru, a to je užitečné, když sledujete protokoly, které jsou rozmístěny služby. |
| Identita | Identita z tokenu, který byl předložený při provedení žádosti. To může být uživatelský účet, účet system nebo instanční objekt služby. |
| Level | Poskytuje typ zprávy. Pro audit, je vždy *informativní*. |
| Location | Poskytuje umístění aktivit přihlašování. |
| Vlastnosti | Obsahuje seznam všech vlastností, které jsou spojeny s přihlášení. Další informace najdete v tématu [Reference k rozhraní API Microsoft Graphu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Toto schéma používá stejné názvy atributů jako prostředek přihlášení pro lepší čitelnost.

## <a name="next-steps"></a>Další postup

* [Interpretace schématu protokolů auditu ve službě Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Další informace o diagnostické protokoly Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)