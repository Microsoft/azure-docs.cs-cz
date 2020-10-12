---
title: Porozumění schématu a vlastním výrazům Azure AD
description: Tento článek popisuje schéma Azure AD, atributy, které zřizovací agent poskytuje, a vlastní výrazy.
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
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fff90777c63820b54a1cb37156021c894de19c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89226926"
---
# <a name="understand-the-azure-ad-schema"></a>Pochopení schématu Azure AD
Objekt v Azure Active Directory (Azure AD), podobně jako jakýkoli adresář, je programovou datovou konstrukcí vysoké úrovně, která představuje takové věci jako uživatelé, skupiny a kontakty. Když vytvoříte nového uživatele nebo kontakt ve službě Azure AD, vytváříte novou instanci tohoto objektu. Tyto instance se můžou odlišit na základě jejich vlastností.

Vlastnosti ve službě Azure AD jsou prvky zodpovědné za ukládání informací o instanci objektu ve službě Azure AD.

Schéma Azure AD definuje pravidla, pro která se můžou v položce použít vlastnosti, druhy hodnot, které tyto vlastnosti můžou mít, a jak se můžou uživatelé s těmito hodnotami pracovat. 

Azure AD má dva typy vlastností:
- **Předdefinované vlastnosti**: vlastnosti, které jsou předdefinované schématem Azure AD. Tyto vlastnosti poskytují různá použití a mohou nebo nemusí být přístupné.
- **Přípony adresářů**: k dispozici jsou vlastnosti, které vám umožní přizpůsobit Azure AD pro vlastní použití. Pokud jste například rozšířili místní službu Active Directory s určitým atributem a chcete tento atribut flowovat, můžete použít jednu z poskytnutých vlastních vlastností. 

## <a name="attributes-and-expressions"></a>Atributy a výrazy
Když je objekt, jako je uživatel, zřízený do Azure AD, vytvoří se nová instance uživatelského objektu. Tato tvorba zahrnuje vlastnosti daného objektu, které jsou také označovány jako atributy. Zpočátku má nově vytvořený objekt své atributy nastavené na hodnoty, které jsou určené pravidly synchronizace. Tyto atributy se pak udržují v aktuálním stavu prostřednictvím agenta zřizování cloudu.

![Zřizování objektů](media/concept-attributes/attribute1.png)

Uživatel může být například součástí marketingového oddělení. Při zřizování se zpočátku vytvoří jeho atribut oddělení Azure AD a hodnota je nastavená na marketing. Po šesti měsících, pokud se změní na prodej, změní se jejich místní atribut oddělení služby Active Directory na prodej. Tato změna se synchronizuje do Azure AD a projeví se v objektu uživatele Azure AD.

Synchronizace atributů může být přímá, kde hodnota ve službě Azure AD je přímo nastavená na hodnotu atributu on-premises. Nebo, programový výraz může synchronizaci zpracovat. Programový výraz je zapotřebí v případech, kdy je nutné provést určitou logiku nebo určení k naplnění hodnoty.

Například pokud byste měli atribut mail "" a museli byste john.smith@contoso.com vydělit @contoso.com část "" a flow pouze hodnotu "Jan. Smith", použijte něco podobného:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Vzorový vstup/výstup:** <br>

* **Vstup** (pošta): " john.smith@contoso.com "
* **Výstup**: Jan. Smith

Další informace o tom, jak napsat vlastní výrazy a syntaxi, najdete v tématu [zápis výrazů pro mapování atributů v Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).

V následující tabulce jsou uvedeny běžné atributy a jejich synchronizace do služby Azure AD.


|Místní služby Active Directory|Typ mapování|Azure AD|
|-----|-----|-----|
|CN|Direct|commonName
|countryCode|Direct|countryCode|
|displayName|Direct|displayName|
|givenName|Výraz|givenName|
|objectGUID|Direct|sourceAnchorBinary|  
|Třídy|Direct|userPrincipalName (Hlavní název uživatele)|
|ProxyAdress|Direct|ProxyAddress|

## <a name="view-the-schema"></a>Zobrazit schéma
> [!WARNING]
> Konfigurace zřizování cloudu vytvoří instanční objekt. Objekt služby je viditelný v Azure Portal. Mapování atributů byste neměli měnit pomocí rozhraní instančního objektu v Azure Portal.  Tato funkce není podporovaná.

Chcete-li zobrazit schéma a ověřit jej, postupujte podle těchto kroků.

1.  Přejít na [Průzkumník grafů](https://developer.microsoft.com/graph/graph-explorer).
1.  Přihlaste se pomocí účtu globálního správce.
1.  Na levé straně vyberte změnit oprávnění a zajistěte, aby byl adresář. **nepřístupný** **. všechno** se *poslalo*.
1.  Spusťte dotaz `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')` . Tento dotaz vrátí seznam objektů služby, které jsou vyfiltrovány.
1.  Vyhledejte `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` a poznamenejte si hodnotu pro `"id"` .
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
1. Nahraďte `{Service Principal id}` hodnotou a spusťte dotaz `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/` .
1. Vyhledejte `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` a poznamenejte si hodnotu pro `"id"` .
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
1. Nyní spusťte dotaz `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema` .
 
    Příklad: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Nahraďte `{Service Principal Id}` a `{AD2ADD Provisioning Id}` hodnotami.

1. Tento dotaz vrátí schéma.

   ![Vrácené schéma](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Další kroky

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)