---
title: Schéma přihlášení přihlášení přihlášení v Azure Monitoru | Dokumenty společnosti Microsoft
description: Popište schéma protokolu přihlášení služby Azure AD pro použití v Azure Monitoru
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
ms.openlocfilehash: 5525f2f8ab4ef83ba9c3aeeff945bc9d875600d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748666"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interpretace schématu protokolů přihlášení Azure AD v Azure Monitoru

Tento článek popisuje schéma přihlášení přihlášení služby Azure Active Directory (Azure AD) v Azure Monitoru. Většina informací, které souvisí s přihlášeníje je k dispozici `records` v rámci *vlastnosti* atribut objektu.


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


## <a name="field-descriptions"></a>Popisy polí

| Název pole | Popis |
|------------|-------------|
| Time | Datum a čas v utc. |
| ResourceId | Tato hodnota není namapována a toto pole můžete bezpečně ignorovat.  |
| OperationName | Pro přihlášení je tato hodnota vždy *aktivita přihlášení*. |
| OperationVersion | Verze rozhraní REST API, která je požadována klientem. |
| Kategorie | Pro přihlášení je tato hodnota vždy *Přihlásit*. | 
| TenantId | Identifikátor GUID klienta, který je přidružen k protokolům. |
| Type výsledku | Výsledkem operace přihlášení může být *úspěch* nebo *neúspěch*. | 
| ResultSignature | Obsahuje kód chyby, pokud existuje, pro operaci přihlášení. |
| VýsledekPopis | Obsahuje popis chyby pro operaci přihlášení. |
| riskDetail | riskDetail | Poskytuje "důvod" za konkrétní stav rizikového uživatele, přihlášení nebo detekce rizika. Možné hodnoty `none`jsou: `adminGeneratedTemporaryPassword` `userPerformedSecuredPasswordChange`, `userPerformedSecuredPasswordReset` `adminConfirmedSigninSafe`, `aiConfirmedSigninSafe` `userPassedMFADrivenByRiskBasedPolicy`, `adminDismissedAllRiskForUser` `adminConfirmedSigninCompromised`, `unknownFutureValue`, , , , , . Hodnota `none` znamená, že dosud nebyla provedena žádná akce u uživatele nebo přihlášení. <br>**Poznámka:** Podrobnosti o této vlastnosti vyžadují licenci Azure AD Premium P2. Jiné licence vrátí `hidden`hodnotu . |
| typy událostí rizika | typy událostí rizika | Typy zjišťování rizik přidružené k přihlášení. Možné hodnoty `unlikelyTravel`jsou: `anonymizedIPAddress` `maliciousIPAddress`, `unfamiliarFeatures` `malwareInfectedIPAddress`, `suspiciousIPAddress` `leakedCredentials`, `investigationsThreatIntelligence` `generic`, `unknownFutureValue`, , , , , a . |
| riskLevelAgregovaný | rizikoÚroveň | Agregovaná úroveň rizika. Možné hodnoty `none`jsou: `low` `medium`, `high` `hidden`, `unknownFutureValue`, , a . Hodnota `hidden` znamená, že uživatel nebo přihlášení nebylo povoleno pro Azure AD Identity Protection. **Poznámka:** Podrobnosti o této vlastnosti jsou k dispozici jenom pro zákazníky Azure AD Premium P2. Všichni ostatní zákazníci `hidden`budou vráceni . |
| riskLevelDuringSignIn | rizikoÚroveň | Úroveň rizika při přihlašování. Možné hodnoty `none`jsou: `low` `medium`, `high` `hidden`, `unknownFutureValue`, , a . Hodnota `hidden` znamená, že uživatel nebo přihlášení nebylo povoleno pro Azure AD Identity Protection. **Poznámka:** Podrobnosti o této vlastnosti jsou k dispozici jenom pro zákazníky Azure AD Premium P2. Všichni ostatní zákazníci `hidden`budou vráceni . |
| riskState | riskState | Hlásí stav rizikového uživatele, přihlášení nebo detekce rizik. Možné `none`hodnoty jsou: `confirmedSafe` `remediated`, `dismissed` `atRisk`, `confirmedCompromised` `unknownFutureValue`, , , . . |
| DurationMs |  Tato hodnota není namapována a toto pole můžete bezpečně ignorovat. |
| Adresa CallerIpAddress | IP adresa klienta, který podal požadavek. | 
| CorrelationId | Volitelný identifikátor GUID, který je předán klientem. Tato hodnota může pomoci korelovat operace na straně klienta s operacemi na straně serveru a je užitečné, když sledujete protokoly, které pokrývají služby. |
| Identita | Identita z tokenu, který byl předložen při požadavku. Může se jedná o uživatelský účet, systémový účet nebo instanční objekt. |
| Úroveň | Obsahuje typ zprávy. Pro audit je vždy *informační*. |
| Umístění | Poskytuje umístění aktivity přihlášení. |
| Vlastnosti | Zobrazí seznam všech vlastností, které jsou přidruženy k přihlášení. Další informace naleznete v [tématu Microsoft Graph API Reference](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Toto schéma používá pro čitelnost stejné názvy atributů jako prostředek přihlášení.

## <a name="next-steps"></a>Další kroky

* [Interpretace schématu protokolů auditu v Azure Monitoru](reference-azure-monitor-audit-log-schema.md)
* [Další informace o diagnostických protokolech Azure](../../azure-monitor/platform/platform-logs-overview.md)
