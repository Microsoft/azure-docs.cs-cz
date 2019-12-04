---
title: Porozumění schématu a vlastním výrazům Azure AD
description: Toto téma popisuje schéma služby Azure AD, atributy, které zřizovací agent a vlastní výrazy.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae594bcc20e3c4ed1c6fbd0333699de8c9f4452
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794494"
---
# <a name="understanding-the-azure-ad-schema"></a>Principy schématu Azure AD
Objekt v Azure AD, podobně jako libovolný adresář, je programovou datovou konstrukcí vysoké úrovně, která představuje takové věci jako uživatelé, skupiny a kontakty.  Když ve službě Azure AD vytvoříte nového uživatele nebo kontakt, vytváříte novou instanci tohoto objektu.  Tyto instance se můžou odlišit na základě jejich vlastností.

Vlastnosti ve službě Azure AD jsou prvky zodpovědné za ukládání informací o instanci objektu ve službě Azure AD.  

Schéma Azure AD definuje pravidla, pro která se můžou v položce použít vlastnosti, druhy hodnot, které tyto vlastnosti můžou mít, a jak se s těmito hodnotami můžou uživatelé pracovat. 

Azure AD má dva typy vlastností.  Mezi vlastnosti patří:
- **Předdefinované vlastnosti** – vlastnosti, které jsou předem definované schématem Azure AD.  Tyto vlastnosti poskytují různá použití a mohou nebo nemusí být přístupné.
- **Rozšíření adresáře** – vlastnosti, které jsou k dispozici, aby bylo možné Azure AD přizpůsobit pro vlastní použití.  Pokud jste například rozšířili místní službu Active Directory s určitým atributem a chcete tento atribut přesměrovat, můžete použít jednu z vlastních vlastností, které jsou k dispozici. 

## <a name="attributes-and-expressions"></a>Atributy a výrazy
Když je objekt, například uživatel, zřízený do služby Azure AD, vytvoří se nová instance uživatelského objektu.  Tato tvorba zahrnuje vlastnosti daného objektu, které jsou také označovány jako atributy.  Zpočátku nově vytvořený objekt bude mít své atributy nastavené na hodnoty, které jsou určené pravidly synchronizace.  Tyto atributy se pak udržují v aktuálním stavu prostřednictvím agenta zřizování cloudu.

![](media/concept-attributes/attribute1.png)

Pokud je například uživatel součástí marketingového oddělení, jeho atribut oddělení Azure AD se zpočátku vytvoří při zřízení a pak se hodnota nastaví na marketing.  Ale po šesti měsících se později změní na prodej.  Jejich místní atribut oddělení služby AD se změní na prodej.  Tato změna se pak synchronizuje do Azure AD a projeví se na svém objektu uživatele Azure AD.

Synchronizace atributů může být buď přímá, kde hodnota ve službě Azure AD je přímo nastavená na hodnotu atributu on-premises.  Nebo může existovat programový výraz, který zpracovává tuto synchronizaci.  V případech, kdy je potřeba provést určitou logiku nebo stanovení, aby se naplnila hodnota, by se vyžadoval programový výraz.

Například pokud mám můj atribut mail ("john.smith@contoso.com") a potřebuji vydělit část "@contoso.com" a flow pouze hodnotu "Jan. Smith", použijeme něco podobného:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Vzorový vstup/výstup:** <br>

* **Vstup** (mail): "john.smith@contoso.com"
* **Výstup**: Jan. Smith

Další informace o vytváření vlastních výrazů a syntaxi naleznete v tématu [výrazy zápisu pro mapování atributů v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

Následující seznam uvádí běžné atributy a jejich synchronizaci do Azure AD.


|Místní služby Active Directory|Typ mapování|Azure AD|
|-----|-----|-----|
|CN|Direct|commonName
|countryCode|Direct|countryCode|
|displayName|Direct|displayName|
|givenName|Výraz|givenName|
|objectGUID|Direct|sourceAnchorBinary|  
|Třídy|Direct|userPrincipalName (Hlavní název uživatele)|
|ProxyAdress|Direct|proxyAddress|

## <a name="viewing-the-schema"></a>Zobrazení schématu
Chcete-li zobrazit schéma a ověřit jej, proveďte následující kroky:

1.  Přejděte do [Průzkumníka grafů](https://developer.microsoft.com/graph/graph-explorer).
2.  Přihlaste se pomocí účtu globálního správce.
3.  Na levé straně klikněte na Upravit oprávnění a zajistěte, aby byl **adresář.** **nepřístupný** . všechno se poslalo.
4.  Spusťte následující dotaz: https://graph.microsoft.com/beta/serviceprincipals/.  Tento dotaz vrátí seznam objektů služby.
5.  Vyhledejte "appDisplayName": "Active Directory to Azure Active Directory Provisioning" a poznamenejte si hodnotu "ID:".
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
6. Nahraďte {ID objektu služby} hodnotou a spusťte následující dotaz: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`
7. Vyhledejte část "ID": "AD2AADProvisioning. fd1c9b9e8077402c8bc03a7186c8f976" a poznamenejte si "ID:".
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
8. Nyní spusťte následující dotaz: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`
 
    Příklad: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

 Nahraďte {ID objektu služby} a {AD2ADD ID zřizování} hodnotami.

9. Tento dotaz vrátí schéma.
  ![](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
