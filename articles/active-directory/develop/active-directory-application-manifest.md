---
title: Principy manifest aplikace Azure Active Directory | Microsoft Docs
description: Podrobné pokrytí manifestu aplikace Azure Active Directory, která představuje konfiguraci identity aplikace v klientovi služby Azure AD a se používá k zajištění OAuth autorizace, souhlasu prostředí a další.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, sureshja
ms.openlocfilehash: 7448a6c37df2c0bbffbebf23d211e3ace8d12edc
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317391"
---
# <a name="azure-active-directory-application-manifest"></a>Manifest aplikace Azure Active Directory
Aplikace, které se integrují s Azure AD musí být zaregistrován u klienta služby Azure AD. Tuto aplikaci lze nastavit v manifest aplikace (v okně Azure AD) [portál Azure](https://portal.azure.com).

## <a name="manifest-reference"></a>Odkaz na manifestu

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Klíč  |Typ hodnoty |Příklad hodnoty  |Popis  |
|---------|---------|---------|---------|
|appID     |  Řetězec identifikátoru       |""|  Jedinečný identifikátor pro aplikaci, která je přiřazena k aplikaci Azure AD.|
|appRoles     |    Typ pole     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|Kolekce rolí, které aplikace může deklarovat. Tyto role lze přiřadit na uživatele, skupiny nebo objekty služby.|
|availableToOtherTenants|Boolean|`true`|Pokud tato hodnota nastavena na hodnotu true, aplikace je k dispozici pro ostatní klienty. Pokud je k dispozici pro klienta pouze nastaven na hodnotu false, aplikace se registruje v. Další informace najdete v tématu: [jak se přihlásit žádné uživatele Azure Active Directory (AD) pomocí vzoru víceklientské aplikace](active-directory-devhowto-multi-tenant-overview.md). |
|displayName     |řetězec         |`MyRegisteredApp`         |Zobrazovaný název pro aplikaci. |
|errorURL     |řetězec         |`http://MyRegisteredAppError`         |Adresa URL pro chyb zjištěných v aplikaci. |
|groupMembershipClaims     |    řetězec     |    `1`     |   Bitová maska, která nakonfiguruje "skupiny" deklarace identity vystavené v uživatele nebo přístupový token OAuth 2.0, který aplikace očekává. Bitová maska hodnoty jsou: 0: None, 1: skupin zabezpečení a role se službou Azure AD, 2: vyhrazené a 4: vyhrazené. Nastavení bitové masky do 7, získají všechny skupiny zabezpečení, distribuční skupiny a role adresář Azure AD, které je přihlášený uživatel členem. |
|optionalClaims     |  řetězec       |     `null`    |    [Volitelné deklarací](active-directory-optional-claims.md) vrácené služby tokenů zabezpečení pro tuto konkrétní aplikaci v tokenu. |
|acceptMappedClaims    |      Boolean   | `true`        |    Pokud tato hodnota nastavena na hodnotu true, umožňuje aplikace pro použití deklarací mapování bez zadání vlastní podpisový klíč.|
|Domovská stránka     |  řetězec       |`http://MyRegistererdApp`         |    Adresa URL na domovskou stránku aplikace. |
|identifierUris     |  Pole řetězců       | `http://MyRegistererdApp`        |   URI(s) definovaný uživatelem, který jednoznačně webové aplikace v rámci jeho klienta Azure AD, nebo ověřené vlastní doménu, pokud je aplikace víceklientské. |
|keyCredentials     |   Typ pole      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   Tato vlastnost obsahuje odkazy na aplikace přiřazené přihlašovací údaje, na základě řetězec sdílených tajných klíčů a certifikátů X.509. Tyto přihlašovací údaje se používají při žádosti přístupové tokeny (Pokud je aplikace funguje jako klient místo, jako prostředek). |
|knownClientApplications     |     Typ pole    |    [identifikátor guid]     |     Hodnota se používá pro instalujícího souhlasu, pokud máte řešení, které obsahuje dvě části, klientská aplikace a webové aplikace rozhraní API. Pokud zadáte appID klientské aplikace do této hodnoty, uživatel bude mít pouze o souhlas jednou do klientské aplikace. Azure AD bude vědět, že souhlas klientovi znamená implicitně souhlas webovému rozhraní API a automaticky zřizovat objekty služby pro klienta i webové rozhraní API ve stejnou dobu. Klient i webové rozhraní API aplikace musí být zaregistrované ve stejném klientovi.|
|logoutUrl     |   řetězec      |     `http://MyRegisteredAppLogout`    |   Adresa URL pro odhlášení aplikace. |
|oauth2AllowImplicitFlow     |   Boolean      |  `false`       |       Určuje, zda tato webová aplikace může požadovat OAuth2.0 implicitního toku tokeny. Výchozí hodnota je false. Tento příznak se používá pro aplikace založené na prohlížeči, třeba Javascript jednostránkové aplikace. |
|oauth2AllowUrlPathMatching     |   Boolean      |  `false`       |   Určuje, zda, v rámci žádostí o token OAuth 2.0, Azure AD bude umožňovat cesta shody se identifikátor URI přesměrování proti replyUrls aplikace. Výchozí hodnota je false. |
|oauth2Permissions     | Typ pole         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Kolekce obory oprávnění OAuth 2.0, které zpřístupní webové aplikace rozhraní API (prostředků) pro klientské aplikace. Tyto obory oprávnění může být poskytnuta pro klientské aplikace během souhlasu. |
|oauth2RequiredPostResponse     | Boolean        |    `false`     |      Určuje, zda v rámci žádostí o token OAuth 2.0, Azure AD umožní požadavků POST a požadavky GET. Výchozí hodnota je nastavena hodnota false, která určuje, zda bude povoleno pouze požadavků GET. 
|objectId     | Řetězec identifikátoru        |     ""    |    Jedinečný identifikátor pro aplikaci v adresáři. Toto ID není identifikátor použitý k identifikaci aplikace v jakékoli protokol transakce. Uživatel je pro objekt v adresáři dotazy odkazující.|
|passwordCredentials     | Typ pole        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    Naleznete v popisu pro vlastnost keyCredentials. |
|publicClient     |  Boolean       |      `false`   | Určuje, zda aplikace veřejné klienta (například nainstalované aplikace spuštěné na mobilní zařízení). Výchozí hodnota je false. |
|supportsConvergence     |  Boolean       |   `false`      | Tato vlastnost by neměla být upravována. Přijměte výchozí hodnotu. |
|replyUrls     |  Pole řetězců       |   `http://localhost`     |  Tato vlastnost s více hodnotami obsahuje seznam registrovaných redirect_uri hodnoty, které bude přijímat Azure AD jako cíle při vrácení tokeny. |
|requiredResourceAccess     |     Typ pole    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}] </code>    |   Určuje prostředky, které tato aplikace vyžaduje přístup k a sadu obory oprávnění OAuth a aplikační role, které je nutné v každém z těchto prostředků. Tato předběžná konfigurace přístupu požadovaný prostředek jednotky prostředí souhlasu.|
|resourceAppId     |    Řetězec identifikátoru     |  ""      |   Jedinečný identifikátor pro prostředek, který vyžaduje přístup k aplikaci. Tato hodnota musí být roven appId deklarovaná u cílové aplikace prostředků. |
|resourceAccess     |  Typ pole       | Najdete příklad hodnotu pro vlastnost requiredResourceAccess. |   Seznam OAuth2.0 oprávnění oborech a rolích aplikace, které aplikace vyžaduje, aby zadaný prostředek (obsahuje ID a typ hodnoty zadané prostředky)        |
|samlMetadataUrl    |řetězec| `http://MyRegisteredAppSAMLMetadata` |Adresa URL metadat SAML pro aplikaci| 

## <a name="next-steps"></a>Další postup
* Další informace o vztah mezi objekty aplikace a služby hlavní aplikace v tématu [aplikace a služby hlavní objekty ve službě Azure AD][AAD-APP-OBJECTS].
* Najdete v článku [Glosář vývojáře Azure AD] [ AAD-DEVELOPER-GLOSSARY] definice některých koncepty pro vývojáře Azure Active Directory (AD) jádra.

Použijte následující sekci komentáře k poskytnutí zpětné vazby, která pomáhá upřesnit a utvářejí náš obsah.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

