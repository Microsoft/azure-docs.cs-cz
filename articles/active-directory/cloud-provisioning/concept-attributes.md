---
title: Principy schématu Azure AD a vlastních výrazů
description: Tento článek popisuje schéma Azure AD, atributy, které toky agenta zřizování a vlastní výrazy.
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
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299325"
---
# <a name="understand-the-azure-ad-schema"></a>Principy schématu Azure AD
Objekt ve službě Azure Active Directory (Azure AD), stejně jako jakýkoli adresář, je programový konstrukce dat vysoké úrovně, která představuje například uživatele, skupiny a kontakty. Když vytvoříte nového uživatele nebo kontakt ve službě Azure AD, vytváříte novou instanci tohoto objektu. Tyto instance lze rozlišit na základě jejich vlastností.

Vlastnosti ve službě Azure AD jsou prvky, které jsou zodpovědné za ukládání informací o instanci objektu ve službě Azure AD.

Schéma Azure AD definuje pravidla, pro které vlastnosti může být použit v položce, druhy hodnot, které tyto vlastnosti mohou mít a jak uživatelé mohou pracovat s těmito hodnotami. 

Azure AD má dva typy vlastností:
- **Předdefinované vlastnosti:** Vlastnosti, které jsou předdefinovány schématem Azure AD. Tyto vlastnosti poskytují různá použití a může nebo nemusí být přístupné.
- **Rozšíření adresáře:** Vlastnosti, které jsou k dispozici, takže můžete přizpůsobit Azure AD pro vlastní použití. Pokud jste například rozšířili místní službu Active Directory o určitý atribut a chcete tento atribut tokovat, můžete použít jednu z vlastních vlastností, která je k dispozici. 

## <a name="attributes-and-expressions"></a>Atributy a výrazy
Když je objekt, jako je například uživatel zřízendo Služby Azure AD, vytvoří se nová instance objektu uživatele. Toto vytvoření zahrnuje vlastnosti tohoto objektu, které jsou také známé jako atributy. Zpočátku nově vytvořený objekt má své atributy nastaveny na hodnoty, které jsou určeny pravidly synchronizace. Tyto atributy jsou pak průběžně aktuální prostřednictvím agenta zřizování cloudu.

![Zřizování objektů](media/concept-attributes/attribute1.png)

Uživatel může být například součástí marketingového oddělení. Jejich atribut oddělení Azure AD se zpočátku vytvoří, když jsou zřízeny a hodnota je nastavena na marketing. O šest měsíců později, pokud se změní na Prodej, jejich místní atribut oddělení služby Active Directory se změní na Prodej. Tato změna se synchronizuje s Azure AD a projeví se v jejich objektu uživatele Azure AD.

Synchronizace atributů může být přímá, kde je hodnota ve službě Azure AD přímo nastavena na hodnotu místního atributu. Nebo programový výraz může zpracovat synchronizaci. Programový výraz je nutný v případech, kdy musí být provedena určitá logika nebo určení k naplnění hodnoty.

Pokud byste například měli atributjohn.smith@contoso.commail " " a@contoso.compotřebovali jste odstranit část " a tok pouze hodnotu "john.smith", použili byste něco takového:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Vstup/výstup vzorku:** <br>

* **VSTUP** (pošta): "john.smith@contoso.com
* **VÝSTUP**: "john.smith"

Další informace o psaní vlastních výrazů a syntaxe najdete v [tématu Zápis výrazů pro mapování atributů ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

V následující tabulce jsou uvedeny běžné atributy a způsob jejich synchronizace se službou Azure AD.


|Místní služby Active Directory|Typ mapování|Azure AD|
|-----|-----|-----|
|Kn|Direct|commonName
|kód země|Direct|kód země|
|displayName|Direct|displayName|
|givenName|Expression|givenName|
|objectGUID|Direct|sourceAnchorBinary|  
|Userprincipalname|Direct|userPrincipalName (Hlavní název uživatele)|
|ProxyAdress|Direct|Proxyaddress|

## <a name="view-the-schema"></a>Zobrazení schématu
> [!WARNING]
> Konfigurace zřizování cloudu vytvoří instanční objekt. Instanční objekt je viditelný na webu Azure Portal. Mapování atributů byste neměli upravovat pomocí prostředí instančního objektu na webu Azure Portal.  Tato funkce není podporovaná.

Chcete-li zobrazit schéma a ověřit jej, postupujte takto.

1.  Přejděte do [Průzkumníka grafů](https://developer.microsoft.com/graph/graph-explorer).
1.  Přihlaste se pomocí účtu globálního správce.
1.  Na levé straně vyberte **změnit oprávnění** a ujistěte se, že **Directory.ReadWrite.All** je *consented*.
1.  Spusťte `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`dotaz . Tento dotaz vrátí filtrovaný seznam instančních objektů.
1.  Vyhledejte `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` hodnotu `"id"`pro program .
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
1. Nahraďte `{Service Principal id}` hodnotu a `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`spusťte dotaz .
1. Vyhledejte `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` hodnotu `"id"`pro program .
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
1. Nyní spusťte dotaz `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`.
 
    Příklad: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Nahraďte `{Service Principal Id}` a `{AD2ADD Provisioning Id}` s vašimi hodnotami.

1. Tento dotaz vrátí schéma.

   ![Vrácené schéma](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Další kroky

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
