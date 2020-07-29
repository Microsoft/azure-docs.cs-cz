---
title: Použití rozhraní API pro Microsoft Graph ke konfiguraci zřizování-Azure Active Directory | Microsoft Docs
description: Potřebujete nastavit zřizování pro víc instancí aplikace? Naučte se, jak ušetřit čas pomocí Microsoft Graph rozhraní API k automatizaci konfigurace automatického zřizování.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 01d4475e73fd436fd0cd2a8aca1e7a946cdd7562
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782054"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Konfigurace zřizování pomocí rozhraní API Microsoft Graph

Azure Portal je pohodlný způsob, jak nakonfigurovat zřizování pro jednotlivé aplikace po jednom. Pokud ale vytváříte několik (nebo dokonce stovky) instancí aplikace, je možné jednodušší automatizaci vytváření a konfigurace aplikací pomocí rozhraní API pro Microsoft Graph. Tento článek popisuje, jak automatizovat konfiguraci zřizování prostřednictvím rozhraní API. Tato metoda se běžně používá pro aplikace, jako je [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso).

**Přehled kroků pro automatizaci konfigurace zřizování pomocí rozhraní Microsoft Graph API**


|Krok  |Podrobnosti  |
|---------|---------|
|[Krok 1. Vytvoření aplikace Galerie](#step-1-create-the-gallery-application)     |Přihlášení k klientovi rozhraní API <br> Načtení šablony aplikace Galerie <br> Vytvoření aplikace Galerie         |
|[Krok 2. Vytvořit úlohu zřizování na základě šablony](#step-2-create-the-provisioning-job-based-on-the-template)     |Načtení šablony pro zřizovací konektor <br> Vytvoření úlohy zřizování         |
|[Krok 3. Autorizovat přístup](#step-3-authorize-access)     |Otestování připojení k aplikaci <br> Uložte přihlašovací údaje.         |
|[Krok 4. Spustit úlohu zřizování](#step-4-start-the-provisioning-job)     |Spuštění úlohy         |
|[Krok 5. Zřizování monitorování](#step-5-monitor-provisioning)     |Zkontroluje stav úlohy zřizování. <br> Načtení protokolů zřizování         |

> [!NOTE]
> Objekty odpovědi uvedené v tomto článku je možné zkrátit, aby bylo možné je přečíst. Všechny vlastnosti budou vráceny ze skutečného volání.

## <a name="step-1-create-the-gallery-application"></a>Krok 1: Vytvoření aplikace Galerie

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Přihlaste se k Microsoft Graph Exploreru (doporučeno), autorovi nebo jakémukoli jinému klientovi API, který používáte.

1. Spustit [průzkumníka Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Vyberte tlačítko Přihlásit se Microsoftem a přihlaste se pomocí globálního správce služby Azure AD nebo přihlašovacích údajů správce aplikací.

    ![Přihlášení do grafu](./media/application-provisioning-configure-api/wd_export_02.png)

1. Po úspěšném přihlášení se zobrazí podrobnosti o uživatelském účtu v levém podokně.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Načtení identifikátoru šablony aplikace Galerie
Aplikace v galerii aplikací Azure AD mají každý z nich [šablonu aplikace](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) , která popisuje metadata pro danou aplikaci. Pomocí této šablony můžete ve vašem tenantovi vytvořit instanci aplikace a instančního objektu pro správu.

#### <a name="request"></a>*Žádost*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*Základě*

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

### <a name="create-the-gallery-application"></a>Vytvoření aplikace Galerie

Použijte ID šablony načtené pro vaši aplikaci v posledním kroku a [vytvořte instanci](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) aplikace a instančního objektu ve vašem tenantovi.

#### <a name="request"></a>*Žádost*

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

#### <a name="response"></a>*Základě*


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

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>Krok 2: vytvoření úlohy zřizování na základě šablony

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>Načtení šablony pro zřizovací konektor

Aplikace v galerii s povoleným zřizováním mají šablony pro zjednodušení konfigurace. Pomocí níže uvedeného požadavku [načtěte šablonu pro konfiguraci zřizování](https://docs.microsoft.com/graph/api/synchronization-synchronizationtemplate-list?view=graph-rest-beta&tabs=http). Všimněte si, že budete muset zadat ID. ID odkazuje na předchozí prostředek, který je v tomto případě ServicePrincipal. 

#### <a name="request"></a>*Žádost*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*Základě*
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
Pokud chcete povolit zřizování, budete nejdřív muset [vytvořit úlohu](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http). K vytvoření úlohy zřizování použijte níže uvedený požadavek. Použijte templateId z předchozího kroku při zadávání šablony, která se má použít pro úlohu.

#### <a name="request"></a>*Žádost*
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

#### <a name="response"></a>*Základě*
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

## <a name="step-3-authorize-access"></a>Krok 3: autorizace přístupu

### <a name="test-the-connection-to-the-application"></a>Otestování připojení k aplikaci

Otestujte připojení k aplikaci třetí strany. Níže uvedený příklad je pro aplikaci, která vyžaduje clientSecret a secretToken. Každá aplikace má své požadavky. Aplikace často používají vlastnost BaseAddress místo ClientSecret. Pokud chcete zjistit, jaké přihlašovací údaje vaše aplikace vyžaduje, přejděte na stránku konfigurace zřizování pro vaši aplikaci a v režimu pro vývojáře klikněte na test připojení. Síťový provoz zobrazí parametry používané pro přihlašovací údaje. Úplný seznam přihlašovacích údajů najdete [tady](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http). 

#### <a name="request"></a>*Žádost*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*Základě*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Uložení přihlašovacích údajů

Konfigurace zřizování vyžaduje vytvoření vztahu důvěryhodnosti mezi službou Azure AD a aplikací. Autorizovat přístup k aplikaci třetí strany. Níže uvedený příklad je pro aplikaci, která vyžaduje clientSecret a secretToken. Každá aplikace má své požadavky. V [dokumentaci k rozhraní API](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) si můžete prohlédnout dostupné možnosti. 

#### <a name="request"></a>*Žádost*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*Základě*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>Krok 4: zahájení úlohy zřizování
Teď, když je nakonfigurovaná úloha zřizování, [Spusťte úlohu](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http)pomocí následujícího příkazu. 


#### <a name="request"></a>*Žádost*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*Základě*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>Krok 5: zřizování monitorování

### <a name="monitor-the-provisioning-job-status"></a>Monitorování stavu úlohy zřizování

Teď, když je spuštěná úloha zřizování, použijte následující příkaz ke sledování průběhu aktuálního cyklu zřizování a statistik, jako je počet uživatelů a skupin, které byly vytvořeny v cílovém systému. 

#### <a name="request"></a>*Žádost*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*Základě*
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
Kromě monitorování stavu úlohy zřizování můžete použít [protokoly zřizování](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http) k dotazování na všechny události, ke kterým dochází (například dotaz na konkrétního uživatele a zjistit, jestli se úspěšně zřídily).

#### <a name="request"></a>*Žádost*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*Základě*
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

- [Projděte si dokumentaci Microsoft Graph synchronizace.](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrace vlastní aplikace v SCIM s Azure AD](use-scim-to-provision-users-and-groups.md)
