---
title: Vysvětlení manifestu aplikace Azure Active Directory | Dokumentace Microsoftu
description: Podrobné pokrytí manifestu aplikace Azure Active Directory, který představuje konfigurace identity aplikace v tenantovi Azure AD a používá se pro usnadnění OAuth autorizace, prostředí pro vyjádření souhlasu a další.
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
ms.openlocfilehash: a0b39f5ac4347e48dc834bf5f5408c36df107aff
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601784"
---
# <a name="azure-active-directory-application-manifest"></a>Manifest aplikace Azure Active Directory
Aplikace, které se integrují s Azure AD musí být zaregistrovaný s tenantem Azure AD. Tuto aplikaci lze nakonfigurovat v manifestu aplikace (pod oknem Azure AD) [webu Azure portal](https://portal.azure.com).

## <a name="manifest-reference"></a>Odkaz na manifest

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Klíč  |Typ hodnoty |Příklad hodnoty  |Popis  |
|---------|---------|---------|---------|
|ID aplikace     |  Identifikátor řetězce       |""|  Jedinečný identifikátor pro aplikaci, kterou přiřadí k aplikaci Azure AD.|
|appRoles     |    Typ pole     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|Kolekce rolí, které může aplikace deklarovat. Tyto role můžete přiřadit na uživatele, skupiny nebo instanční objekty.|
|availableToOtherTenants|Boolean|`true`|Pokud tato hodnota nastavená na true, aplikace je k dispozici pro jiné tenanty. Pokud je nastaven na hodnotu false, aplikace je dostupná jenom pro tenanta je registrován v. Další informace najdete v tématu: [postup přihlášení libovolného uživatele služby Azure Active Directory (AD) pomocí vzoru aplikace s více tenanty](howto-convert-app-to-be-multi-tenant.md). |
|displayName     |řetězec         |`MyRegisteredApp`         |Zobrazovaný název aplikace. |
|errorURL     |řetězec         |`http://MyRegisteredAppError`         |Adresa URL v aplikaci došlo k chybám. |
|groupMembershipClaims     |    řetězec     |    `1`     |   Bitová maska, která konfiguruje deklaraci identity "groups" vydané v uživatele nebo přístupový token OAuth 2.0, který aplikace očekává. Hodnoty bitové masky jsou: 0: žádné, 1: skupiny zabezpečení a role Azure AD, 2: vyhrazené a 4: rezervované. Nastavení bitové masky na 7 získá všechny skupiny zabezpečení, distribučních skupin a rolí adresáře Azure AD, které je přihlášený uživatel členem. |
|optionalClaims     |  řetězec       |     `null`    |    [Nepovinných deklarací identity](active-directory-optional-claims.md) vrácené služby tokenů zabezpečení pro tuto konkrétní aplikaci v tokenu. |
|acceptMappedClaims    |      Boolean   | `true`        |    Pokud tato hodnota nastavena na hodnotu true, umožňuje aplikaci pro použití mapování deklarací bez zadání vlastní podpisový klíč.|
|Domovská stránka     |  řetězec       |`http://MyRegistererdApp`         |    Adresa URL domovské stránky vaší aplikace. |
|identifierUris     |  Pole řetězců       | `http://MyRegistererdApp`        |   URI(s) definovaný uživatelem, který jednoznačně identifikují webové aplikace v rámci jejich tenanta Azure AD, nebo v rámci ověřené vlastní doméně, je-li aplikace více tenantů. |
|keyCredentials     |   Typ pole      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   Tato vlastnost obsahuje odkazy na aplikace přiřazené přihlašovací údaje, založené na řetězci sdílené tajné klíče a certifikáty X.509. Tyto přihlašovací údaje se používají při vyžadování přístupové tokeny (Pokud aplikace funguje jako klient místo toho, jako prostředek). |
|knownClientApplications     |     Typ pole    |    [identifikátor guid]     |     Hodnota slouží k vytváření prostředků souhlasu, pokud máte řešení, které obsahuje dvě části, klientská aplikace a vlastního webového rozhraní API aplikace. Pokud zadáte identifikátor appID klientské aplikace do této hodnoty, uživatel bude mít pouze jednou souhlas do klientské aplikace. Azure AD vědět, že vyjadřuje se souhlas klienta znamená, že implicitně vyjádření souhlasu s webovým rozhraním API a bude automaticky poskytovat instančních objektů pro klienty a webového rozhraní API ve stejnou dobu. Klient i webové rozhraní API aplikace musí být zaregistrovaný ve stejném tenantovi.|
|logoutUrl     |   řetězec      |     `http://MyRegisteredAppLogout`    |   Adresa URL pro odhlášení z aplikace. |
|oauth2AllowImplicitFlow     |   Boolean      |  `false`       |       Určuje, zda tuto webovou aplikaci můžete požádat o tokeny implicitní tok OAuth 2.0. Výchozí hodnota je false. Tento příznak se používá pro aplikace založené na prohlížeči, jako je Javascript jednostránkové aplikace. |
|oauth2AllowUrlPathMatching     |   Boolean      |  `false`       |   Určuje, zda, jako součást žádostí o token OAuth 2.0, Azure AD bude umožňovat odpovídající cesty identifikátoru URI přesměrování proti adres Replyurl vaší aplikace. Výchozí hodnota je false. |
|oauth2Permissions     | Typ pole         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Kolekce obory oprávnění OAuth 2.0, které webové aplikaci s rozhraním API (prostředek) uvádí pro klientské aplikace. Tyto obory oprávnění může udělit klientské aplikace během souhlas. |
|oauth2RequiredPostResponse     | Boolean        |    `false`     |      Určuje, zda, jako součást žádostí o token OAuth 2.0, Azure AD umožní požadavky POST, na rozdíl od požadavků GET. Výchozí hodnota je false, která určuje, zda bude povoleno pouze požadavky GET. 
|ID objektu     | Identifikátor řetězce        |     ""    |    Jedinečný identifikátor pro aplikaci v adresáři. Toto ID není identifikátor sloužící k identifikaci aplikace v jakékoli protokol transakce. Uživatel je pro odkazování na objekt v dotazy na adresář.|
|passwordCredentials     | Typ pole        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    Naleznete v popisu keyCredentials vlastnosti. |
|publicClient     |  Boolean       |      `false`   | Určuje, zda je stav aplikace veřejným klientem (například nainstalovaná aplikace běžící na mobilním zařízení). Výchozí hodnota je false. |
|supportsConvergence     |  Boolean       |   `false`      | Tato vlastnost se nesmí upravovat. Přijměte výchozí hodnotu. |
|adres Replyurl     |  Pole řetězců       |   `http://localhost`     |  Při návratu tokeny tato vícehodnotový vlastnost obsahuje seznam registrovaných redirect_uri hodnoty, které Azure AD bude přijímat jako cíle. |
|requiredResourceAccess     |     Typ pole    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}] </code>    |   Určuje prostředky, které tato aplikace vyžaduje přístup k a sadu obory oprávnění OAuth a aplikačních rolí, které jsou potřebné u každého z těchto prostředků. Tuto předběžnou konfiguraci přístup požadovaný prostředek jednotky prostředí pro vyjádření souhlasu.|
|resourceAppId     |    Identifikátor řetězce     |  ""      |   Jedinečný identifikátor pro prostředek, který vyžaduje přístup k aplikaci. Tato hodnota by měla být rovna appId deklarované u cílové aplikace prostředků. |
|resourceAccess     |  Typ pole       | Zobrazit příklad hodnotu pro vlastnost requiredResourceAccess. |   Seznam obory oprávnění OAuth 2.0 a role aplikace, které aplikace vyžaduje ze zadaného prostředku (ID a typ hodnoty zadané prostředky obsahuje)        |
|samlMetadataUrl    |řetězec| `http://MyRegisteredAppSAMLMetadata` |Adresa URL metadat SAML pro aplikaci| 

## <a name="next-steps"></a>Další postup
* Další informace o vztahu mezi aplikace a instanční objekty aplikace najdete v tématu [aplikace a instanční objekty ve službě Azure AD](app-objects-and-service-principals.md).
* Zobrazit [Glosář vývojáře Azure AD](developer-glossary.md) obsahuje definice některých základní koncepty pro vývojáře Azure Active Directory (AD).

Použijte následující oddíl komentáře k poskytnutí zpětné vazby, který pomáhá vylepšit a obrazce náš obsah.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:../../../azure-ad-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

