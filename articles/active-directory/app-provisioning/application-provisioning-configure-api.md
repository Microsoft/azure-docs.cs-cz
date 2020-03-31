---
title: Použití rozhraní API microsoft graphu ke konfiguraci zřizování – Azure Active Directory | Dokumenty společnosti Microsoft
description: Potřebujete nastavit zřizování pro více instancí aplikace? Zjistěte, jak ušetřit čas pomocí rozhraní API aplikace Microsoft Graph k automatizaci konfigurace automatického zřizování.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72217a565071f9531281af1862ba3681e353a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481462"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Konfigurace zřizování pomocí rozhraní API aplikace Microsoft Graph

Portál Azure je pohodlný způsob, jak nakonfigurovat zřizování pro jednotlivé aplikace po jednom. Ale pokud vytváříte několik nebo dokonce stovky instancí aplikace, může být jednodušší automatizovat vytváření aplikací a konfiguraci pomocí rozhraní API Microsoft Graphu. Tento článek popisuje, jak automatizovat konfiguraci zřizování prostřednictvím api. Tato metoda se běžně používá pro aplikace, jako je [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso).

**Přehled kroků pro použití rozhraní API aplikace Microsoft Graph k automatizaci konfigurace zřizování**


|Krok  |Podrobnosti  |
|---------|---------|
|[Krok 1. Vytvoření aplikace galerie](#step-1-create-the-gallery-application)     |Přihlášení ke klientovi rozhraní API <br> Načtení šablony aplikace galerie <br> Vytvoření aplikace galerie         |
|[Krok 2. Vytvořit úlohu zřizování na základě šablony](#step-2-create-the-provisioning-job-based-on-the-template)     |Načtení šablony pro zřizovací konektor <br> Vytvoření úlohy zřizování         |
|[Krok 3. Autorizovat přístup](#step-3-authorize-access)     |Otestujte připojení k aplikaci <br> Uložení přihlašovacích údajů         |
|[Krok 4. Zahájit úlohu zřizování](#step-4-start-the-provisioning-job)     |Spuštění úlohy         |
|[Krok 5. Monitorování zřizování](#step-5-monitor-provisioning)     |Kontrola stavu úlohy zřizování <br> Načíst protokoly zřizování         |

> [!NOTE]
> Odpovědi objekty uvedené v tomto článku může být zkrácena pro čitelnost. Všechny vlastnosti budou vráceny z skutečnévolání.

## <a name="step-1-create-the-gallery-application"></a>Krok 1: Vytvoření aplikace galerie

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Přihlaste se k aplikaci Microsoft Graph Explorer (doporučeno), Posuňovači nebo jinému klientovi rozhraní API, kterého používáte

1. Spuštění [aplikace Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Vyberte tlačítko "Přihlásit se s Microsoftem" a přihlaste se pomocí globálního správce Azure AD nebo přihlašovacích údajů správce aplikace.

    ![Přihlášení do grafu](./media/application-provisioning-configure-api/wd_export_02.png)

1. Po úspěšném přihlášení se podrobnosti o uživatelském účtu zobrazí v levém podokně.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Načtení identifikátoru šablony aplikace galerie
Aplikace v galerii aplikací Azure AD mají každý [šablonu aplikace,](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) která popisuje metadata pro tuto aplikaci. Pomocí této šablony můžete vytvořit instanci aplikačního a instančního objektu ve vašem tenantovi pro správu.

#### <a name="request"></a>*Požadavek*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*Reakce*

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Vytvoření aplikace galerie

Pomocí ID šablony načtené pro vaši aplikaci v posledním kroku [vytvořte instanci](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) aplikačního a instančního objektu ve vašem tenantovi.

#### <a name="request"></a>*Požadavek*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*Reakce*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>Krok 2: Vytvoření úlohy zřizování na základě šablony

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>Načtení šablony pro zřizovací konektor

Aplikace v galerii, které jsou povoleny pro zřizování mají šablony pro zjednodušení konfigurace. Pomocí níže uvedeného požadavku [načtěte šablonu pro konfiguraci zřizování](https://docs.microsoft.com/graph/api/synchronization-synchronizationtemplate-list?view=graph-rest-beta&tabs=http). Všimněte si, že budete muset zadat ID. ID odkazuje na předchozí prostředek, který v tomto případě je ServicePrincipal. 

#### <a name="request"></a>*Požadavek*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*Reakce*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>Vytvoření úlohy zřizování
Chcete-li povolit zřizování, musíte nejprve [vytvořit úlohu](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http). Pomocí níže uvedeného požadavku vytvořte úlohu zřizování. Při zadávání šablony, která má být použita pro úlohu, použijte templateId z předchozího kroku.

#### <a name="request"></a>*Požadavek*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*Reakce*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>Krok 3: Autorizace přístupu

### <a name="test-the-connection-to-the-application"></a>Otestujte připojení k aplikaci

Otestujte připojení k aplikaci jiného výrobce. Následující příklad je pro aplikaci, která vyžaduje clientSecret a secretToken. Každá aplikace má své požadavky. Aplikace často používají BaseAddress místo ClientSecret. Chcete-li zjistit, jaká pověření vaše aplikace vyžaduje, přejděte na stránku konfigurace zřizování pro vaši aplikaci a v režimu vývojáře klikněte na testovací připojení. Síťový provoz zobrazí parametry použité pro pověření. Úplný seznam pověření naleznete [zde](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http). 

#### <a name="request"></a>*Požadavek*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*Reakce*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Uložení přihlašovacích údajů

Konfigurace zřizování vyžaduje vytvoření vztahu důvěryhodnosti mezi Službou Azure AD a aplikací. Autorizujte přístup k aplikaci jiného výrobce. Následující příklad je pro aplikaci, která vyžaduje clientSecret a secretToken. Každá aplikace má své požadavky. Dostupné možnosti najdete v dokumentaci k [rozhraní API.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) 

#### <a name="request"></a>*Požadavek*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*Reakce*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>Krok 4: Spuštění úlohy zřizování
Teď, když je úloha zřizování nakonfigurována, [spusťte úlohu pomocí následujícího příkazu](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 


#### <a name="request"></a>*Požadavek*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*Reakce*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>Krok 5: Monitorování zřizování

### <a name="monitor-the-provisioning-job-status"></a>Sledování stavu úlohy zřizování

Teď, když je spuštěna úloha zřizování, použijte následující příkaz ke sledování průběhu aktuálního cyklu zřizování a statistiky k dnešnímu dni, jako je počet uživatelů a skupin, které byly vytvořeny v cílovém systému. 

#### <a name="request"></a>*Požadavek*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*Reakce*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>Monitorování událostí zřizování pomocí protokolů zřizování
Kromě sledování stavu úlohy zřizování můžete pomocí [protokolů zřizování](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http) dotazovat na všechny události, které se vyskytují (např. dotaz pro konkrétního uživatele a zjistit, zda byly úspěšně zřízené).

#### <a name="request"></a>*Požadavek*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*Reakce*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>Související články

- [Kontrola dokumentace k synchronizaci aplikace Microsoft Graph](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrace vlastní aplikace SCIM s Azure AD](use-scim-to-provision-users-and-groups.md)
