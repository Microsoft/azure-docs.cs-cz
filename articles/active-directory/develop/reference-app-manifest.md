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
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127016"
---
# <a name="azure-active-directory-app-manifest"></a>Manifest aplikace Azure Active Directory

Aplikace, které se integrují s Azure Active Directory (Azure AD) musí být zaregistrovaný s tenantem Azure AD. Aplikace můžete nakonfigurovat [webu Azure portal](https://portal.azure.com) tak, že vyberete **Azure Active Directory**, vyberete aplikaci, kterou chcete konfigurovat a výběrem **Manifest**.

## <a name="manifest-reference"></a>Odkaz na manifest

> [!NOTE]
> Pokud nevidíte popisy, maximalizujte okno prohlížeče nebo přejděte/prstem naleznete v popisu.

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| Klíč  | Typ hodnoty | Příklad hodnoty | Popis  |
|---------|---------|---------|---------|
| `appID` | Identifikátor řetězce | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | Určuje jedinečný identifikátor pro aplikaci, kterou přiřadí k aplikaci Azure AD. |
| `appRoles` | Typ pole | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | Určuje kolekci rolí, které může aplikace deklarovat. Tyto role můžete přiřadit na uživatele, skupiny nebo instanční objekty. |
| `availableToOtherTenants`| Boolean | `true` | Pokud tato hodnota nastavená na hodnotu true, aplikace je k dispozici pro jiné tenanty. Pokud je nastaven na hodnotu false, aplikace je dostupná jenom pro tenanta je registrován v. Další informace najdete v tématu [postupy: přihlášení jakéhokoli uživatele Azure AD pomocí vzoru aplikace s více tenanty](howto-convert-app-to-be-multi-tenant.md). |
| `displayName` | řetězec | `MyRegisteredApp` | Zobrazovaný název aplikace. |
| `errorURL` | řetězec | `http://MyRegisteredAppError` | Adresa URL v aplikaci došlo k chybám. |
| `groupMembershipClaims` | řetězec | `1` | Bitová maska, která se konfiguruje `groups` deklarací identity vystavených v uživatele nebo přístupový token OAuth 2.0, který aplikace očekává. Hodnoty bitové masky jsou následující:<br>0: žádné<br>1: skupiny zabezpečení a role Azure AD<br>2: vyhrazené<br>4: vyhrazené<br>Nastavení bitové masky na 7 získá všechny skupiny zabezpečení, distribučních skupin a rolí adresáře Azure AD, které je přihlášený uživatel členem. |
| `optionalClaims` | řetězec | `null` | Nepovinné deklarace identity v tokenu vrácené služby tokenů zabezpečení pro tuto konkrétní aplikaci. Další informace najdete v tématu [nepovinných deklarací identity](active-directory-optional-claims.md). |
| `acceptMappedClaims` | Boolean | `true` | Pokud tato hodnota nastavená na `true`, umožňuje aplikaci používat deklarace identity mapování bez zadání vlastní podpisový klíč. |
| `homepage` | řetězec | `http://MyRegisteredApp` | Určuje adresu URL na domovskou stránku aplikace. |
| `informationalUrls` | řetězec | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | Určuje odkazy na aplikace podmínkami služby a o ochraně osobních údajů. Podmínky služby a o ochraně osobních údajů se zobrazí uživatelům prostřednictvím prostředí pro vyjádření souhlasu uživatele. Další informace najdete v tématu [postupy: Přidání podmínkami služby a o ochraně osobních údajů pro aplikace Azure AD registrované](howto-add-terms-of-service-privacy-statement.md). |
| `identifierUris` | Pole řetězců | `http://MyRegistererdApp` | URI(s) definovaný uživatelem, které jednoznačně identifikují webové aplikace v rámci jejich tenanta Azure AD, nebo v rámci ověřené vlastní doméně, je-li aplikace více tenantů. |
| `keyCredentials` | Typ pole | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | Obsahuje odkazy na aplikace přiřazena přihlašovací údaje, založené na řetězci sdílené tajné klíče a certifikáty X.509. Tyto přihlašovací údaje se používají při vyžadování přístupové tokeny (Pokud aplikace funguje jako klient místo toho, jako prostředek). |
| `knownClientApplications` | Typ pole | `[GUID]` | Použít pro vytváření prostředků souhlasu, pokud máte řešení, které obsahuje dvě části: klientská aplikace a aplikace s vlastní webové rozhraní API. Pokud zadáte identifikátor appID klientské aplikace do této hodnoty, uživatel bude mít pouze jednou souhlas pro klientskou aplikaci. Azure AD vědět, že vyjadřuje se souhlas klienta znamená, že implicitně vyjádření souhlasu s webovým rozhraním API a bude automaticky poskytovat instančních objektů pro klienty a webového rozhraní API ve stejnou dobu. Klient a aplikace webového rozhraní API musí být zaregistrovaný ve stejném tenantovi. |
| `logoutUrl` | řetězec | `http://MyRegisteredAppLogout` | Adresa URL pro odhlášení z aplikace. |
| `oauth2AllowImplicitFlow` | Boolean | `false` | Určuje, zda tato webová aplikace požádat o tokeny implicitní tok OAuth 2.0. Výchozí hodnota je false. Tento příznak se používá pro aplikace založené na prohlížeči, jako je Javascript jednostránkové aplikace. |
| `oauth2AllowUrlPathMatching` | Boolean | `false` | Určuje, zda, jako součást žádostí o token OAuth 2.0, Azure AD bude umožňovat odpovídající cesty identifikátoru URI přesměrování proti adres Replyurl aplikace. Výchozí hodnota je false. |
| `oauth2Permissions` | Typ pole | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | Určuje kolekci obory oprávnění OAuth 2.0, které aplikaci webového rozhraní API (prostředek) uvádí pro klientské aplikace. Tyto obory oprávnění může udělit klientské aplikace během souhlas. |
| `oauth2RequiredPostResponse` | Boolean | `false` | Určuje, zda, jako součást žádostí o token OAuth 2.0, Azure AD umožní požadavky POST, na rozdíl od požadavků GET. Výchozí hodnota je false, která určuje, zda bude povoleno pouze požadavky GET. |
| `objectId` | Identifikátor řetězce | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | Jedinečný identifikátor pro aplikaci v adresáři. Toto ID není identifikátor sloužící k identifikaci aplikace v jakékoli protokol transakce. Používá se pro odkazování na objekt v dotazy na adresář. |
| `parentalControlSettings` | řetězec | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors` Určuje zemí, ve kterých je blokované aplikace pro nezletilé osoby.<br>`legalAgeGroupRule` Určuje pravidlo skupiny právních věk, která se použije pro uživatele aplikace. Je možné nastavit `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`, nebo `BlockMinors`.  |
| `passwordCredentials` | Typ pole | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | Viz popis `keyCredentials` vlastnost. |
| `publicClient` | Boolean | `false` | Určuje, zda je aplikace veřejným klientem, například nainstalované aplikace běžící na mobilních zařízeních. Výchozí hodnota je false. |
| `replyUrls` | Pole řetězců | `"http://localhost"` | Tato vlastnost vícehodnotový obsahuje seznam registrovaných redirect_uri hodnoty, které Azure AD bude přijímat jako cíle při vrácení tokeny. |
| `requiredResourceAccess` | Typ pole | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | S dynamické souhlasu `requiredResourceAccess` pohání prostředí pro vyjádření souhlasu správce a prostředí pro vyjádření souhlasu uživatele pro uživatele, kteří používají statické souhlas. To však není jednotka uživatelské prostředí pro vyjádření souhlasu pro případ, Obecné.<br>`resourceAppId` je jedinečný identifikátor pro prostředek, který vyžaduje přístup k aplikaci. Tato hodnota by měla být rovna appId deklarované u aplikace cílového prostředku.<br>`resourceAccess` je pole, které obsahuje obory oprávnění OAuth 2.0 a role aplikace, které aplikace vyžaduje ze zadaného prostředku. Obsahuje `id` a `type` hodnoty zadané prostředky. |
| `samlMetadataUrl` | řetězec | `http://MyRegisteredAppSAMLMetadata` | Adresa URL metadat SAML pro aplikaci. |

## <a name="next-steps"></a>Další postup

* Další informace o vztahu mezi aplikace a instanční objekty vaší aplikace, najdete v části [aplikace a instanční objekty ve službě Azure AD](app-objects-and-service-principals.md).
* Zobrazit [Glosář vývojáře Azure AD](developer-glossary.md) obsahuje definice některých základní koncepty pro vývojáře Azure Active Directory (AD).

Použijte následující oddíl komentáře k poskytnutí zpětné vazby, který pomáhá vylepšit a obrazce náš obsah.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
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

