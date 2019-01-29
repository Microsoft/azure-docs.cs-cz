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
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: a6a6ae3c89d0a9ee8177b4e943ab53f0c6680c1b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097703"
---
# <a name="azure-active-directory-app-manifest"></a>Manifest aplikace Azure Active Directory

Manifest aplikace obsahuje definici všech atributů objektu aplikace na platformě Microsoft identity. Slouží také jako mechanismus pro aktualizaci objektu aplikace. Další informace o aplikační entitě a jejím schéma najdete v [dokumentaci aplikační entity rozhraní Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Můžete nastavit atributy vaší aplikace prostřednictvím webu Azure portal nebo prostřednictvím kódu programu pomocí Microsoft Graphu. Existují však některé scénáře, ve kterém bude nutné upravit manifest aplikace konfigurovat atribut vaší aplikace. Mezi tyto scénáře patří:

* Pokud jste zaregistrovali aplikaci jako Azure AD více tenantů a osobní účty Microsoft, nelze změnit podporovaných účtů Microsoft v uživatelském rozhraní. Místo toho je nutné použít editor manifestu aplikace ke změně typu účtu podporované.
* Pokud potřebujete definovat oprávnění a rolí, které vaše aplikace podporuje, je nutné upravit manifest aplikace.

## <a name="configure-the-app-manifest"></a>Konfigurace manifestu aplikace

Konfigurace manifestu aplikace:

1. Přihlaste se [webu Azure portal](https://portal.azure.com).
1. Vyberte **Azure Active Directory** služby a pak vyberte **registrace aplikací** nebo **registrace aplikací (Preview)**.
1. Vyberte aplikaci, kterou chcete konfigurovat.
1. Na stránce **Přehled** aplikace vyberte část **Manifest**. Otevře se editor manifestu založeného na webu umožňuje upravit manifest v rámci portálu. Volitelně můžete vybrat **Stáhnout** upravit manifest místně, a potom pomocí **nahrát** znovu do vaší aplikace.

## <a name="manifest-reference"></a>Odkaz na manifest

> [!NOTE]
> Pokud nevidíte **příklad hodnotu** sloupec za **popis**, maximalizujte okno prohlížeče a přejděte/prstem, dokud se nezobrazí **příklad hodnotu** sloupec.

| Klíč  | Typ hodnoty | Popis  | Příklad hodnoty |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | S povolenou hodnotou Null Int32 | Určuje verzi tokenu přístupu očekává prostředkem. Tím se změní na verzi a formát token JWT vytváří nezávisle na koncový bod nebo klienta používá k žádostem o přístupový token.<br/><br/>Použitý koncový bod, verze 1.0 nebo 2.0, je vybrán klientem a ovlivní pouze verze id_tokens. Prostředky je potřeba explicitně konfigurovat `accesstokenAcceptedVersion` udávajících formát tokenu přístupu podporované.<br/><br/>Možné hodnoty pro `accesstokenAcceptedVersion` jsou 1, 2 nebo hodnotu null. Pokud je hodnota null, je výchozí hodnota 1, která odpovídá koncový bod verze 1.0. | `2` |
| `allowPublicClient` | Boolean | Určuje typ použití náhradní lokality aplikace. Azure AD odvodí typ aplikace z replyUrlsWithType ve výchozím nastavení. Určitých scénářů, kde Azure AD nelze určit typ klientské aplikace (například [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) toku, kde se stane požadavku HTTP bez adresy URL přesměrování). V těchto případech se služby Azure AD interpretovat typ aplikace na základě hodnoty této vlastnosti. Pokud tato hodnota nastavena na hodnotu true, typ použití náhradní lokality aplikace je nastaven jako veřejné klienta, jako je nainstalované aplikace běžící na mobilních zařízeních. Výchozí hodnota je false, což znamená, že je typ použití náhradní lokality aplikace důvěrnému klientovi, například webové aplikace. | `false` |
| `appId` | Identifikátor řetězce | Určuje jedinečný identifikátor pro aplikaci, kterou přiřadí k aplikaci Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Typ pole | Určuje kolekci rolí, které může aplikace deklarovat. Tyto role můžete přiřadit na uživatele, skupiny nebo instanční objekty. Další příklady a informace o najdete v tématu [přidání rolí aplikace ve vaší aplikaci a přijímat je v tokenu](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `groupMembershipClaims` | řetězec | Konfiguruje `groups` deklarací identity vystavených v uživatele nebo přístupový token OAuth 2.0, který aplikace očekává. K nastavení tohoto atributu, použijte jednu z následujících hodnot platného řetězce:<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (pro skupiny zabezpečení a role Azure AD)<br/>- `"All"` (tím získá všechny skupiny zabezpečení, distribučních skupin a rolí adresáře Azure AD, které je přihlášený uživatel členem. | `"SecurityGroup"` |
| `optionalClaims` | řetězec | Nepovinné deklarace identity v tokenu vrácené služby tokenů zabezpečení pro tuto konkrétní aplikaci.<br>V tuto chvíli nelze použít aplikace, které podporují osobní účty a Azure AD (registrace prostřednictvím portálu pro registraci aplikace) nepovinných deklarací identity. Aplikace registrované u právě Azure AD pomocí koncového bodu v2.0 však můžete získat nepovinných deklarací identity, které, o který žádali v manifestu. Další informace najdete v tématu [nepovinných deklarací identity](active-directory-optional-claims.md). | `null` |
| `id` | Identifikátor řetězce | Jedinečný identifikátor pro aplikaci v adresáři. Toto ID není identifikátor sloužící k identifikaci aplikace v jakékoli protokol transakce. Používá se pro odkazování na objekt v dotazy na adresář. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | Pole řetězců | URI(s) definovaný uživatelem, které jednoznačně identifikují webové aplikace v rámci jejich tenanta Azure AD, nebo v rámci ověřené vlastní doméně, je-li aplikace více tenantů. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | řetězec | Určuje odkazy na aplikace podmínkami služby a o ochraně osobních údajů. Podmínky služby a o ochraně osobních údajů se zobrazí uživatelům prostřednictvím prostředí pro vyjádření souhlasu uživatele. Další informace najdete v tématu [jak: Přidání podmínky služby a o ochraně osobních údajů pro aplikace Azure AD registrované](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Typ pole | Obsahuje odkazy na aplikace přiřazena přihlašovací údaje, založené na řetězci sdílené tajné klíče a certifikáty X.509. Tyto přihlašovací údaje se používají při vyžadování přístupové tokeny (Pokud aplikace funguje jako klient místo toho, jako prostředek). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | Typ pole | Použít pro vytváření prostředků souhlasu, pokud máte řešení, které obsahuje dvě části: klientská aplikace a aplikace s vlastní webové rozhraní API. Pokud zadáte identifikátor appID klientské aplikace do této hodnoty, uživatel bude mít pouze jednou souhlas pro klientskou aplikaci. Azure AD vědět, že vyjadřuje se souhlas klienta znamená, že implicitně vyjádření souhlasu s webovým rozhraním API a bude automaticky poskytovat instančních objektů pro klienty a webového rozhraní API ve stejnou dobu. Klient a aplikace webového rozhraní API musí být zaregistrovaný ve stejném tenantovi. | `[GUID]` |
| `logoUrl` | řetězec | Načíst pouze hodnotu, která odkazuje na adresu URL CDN loga, který byl nahrán na portálu. | `https://MyRegisteredAppLogo` |
| `logoutUrl` | řetězec | Adresa URL pro odhlášení z aplikace. | `https://MyRegisteredAppLogout` |
| `name` | řetězec | Zobrazovaný název aplikace. | `MyRegisteredApp` |
| `oauth2AllowImplicitFlow` | Boolean | Určuje, zda tato webová aplikace požádat o tokeny přístupu implicitní tok OAuth 2.0. Výchozí hodnota je false. Tento příznak se používá pro aplikace založené na prohlížeči, jako je Javascript jednostránkové aplikace. Další informace, zadejte `OAuth 2.0 implicit grant flow` v obsahu a najdete v tématech o implicitní tok. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Boolean | Určuje, zda tato webová aplikace požádat o tokeny typu ID implicitní tok OAuth 2.0. Výchozí hodnota je false. Tento příznak se používá pro aplikace založené na prohlížeči, jako je Javascript jednostránkové aplikace. | `false` |
| `oauth2Permissions` | Typ pole | Určuje kolekci obory oprávnění OAuth 2.0, které aplikaci webového rozhraní API (prostředek) uvádí pro klientské aplikace. Tyto obory oprávnění může udělit klientské aplikace během souhlas. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Boolean | Určuje, zda, jako součást žádostí o token OAuth 2.0, Azure AD umožní požadavky POST, na rozdíl od požadavků GET. Výchozí hodnota je false, která určuje, zda bude povoleno pouze požadavky GET. | `false` |
| `parentalControlSettings` | řetězec | `countriesBlockedForMinors` Určuje zemí, ve kterých je blokované aplikace pro nezletilé osoby.<br>`legalAgeGroupRule` Určuje pravidlo skupiny právních věk, která se použije pro uživatele aplikace. Je možné nastavit `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`, nebo `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Typ pole | Viz popis `keyCredentials` vlastnost. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Typ pole | Zobrazuje seznam aplikací a požadovaná oprávnění pro implicitní souhlas. Vyžaduje správce, aby poskytovaly souhlasu pro aplikaci. preAuthorizedApplications nevyžadují, aby uživatel vyjádřit souhlas se požadovaná oprávnění. Oprávnění uvedená v preAuthorizedApplications nevyžadují souhlasu uživatele. Další požadované oprávnění, která není uvedený v preAuthorizedApplications však vyžaduje souhlas uživatele. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `replyUrlsWithType` | Pole řetězců | Tato vlastnost vícehodnotový obsahuje seznam registrovaných redirect_uri hodnoty, které Azure AD bude přijímat jako cíle při vrácení tokeny. Každá hodnota identifikátoru uri by měl obsahovat hodnotu typu přidružené aplikace. Podporovaný typ hodnoty jsou: `Web`, `InstalledClient`. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Typ pole | S dynamické souhlasu `requiredResourceAccess` pohání prostředí pro vyjádření souhlasu správce a prostředí pro vyjádření souhlasu uživatele pro uživatele, kteří používají statické souhlas. To však není jednotka uživatelské prostředí pro vyjádření souhlasu pro případ, Obecné.<br>`resourceAppId` je jedinečný identifikátor pro prostředek, který vyžaduje přístup k aplikaci. Tato hodnota by měla být rovna appId deklarované u aplikace cílového prostředku.<br>`resourceAccess` je pole, které obsahuje obory oprávnění OAuth 2.0 a role aplikace, které aplikace vyžaduje ze zadaného prostředku. Obsahuje `id` a `type` hodnoty zadané prostředky. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | řetězec | Adresa URL metadat SAML pro aplikaci. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | řetězec | Určuje adresu URL na domovskou stránku aplikace. | `https://MyRegisteredApp` |
| `signInAudience` | řetězec | Určuje účty Microsoft, které jsou podporovány pro aktuální aplikaci. Podporované hodnoty jsou:<ul><li>**AzureADMyOrg** -uživatelé s Microsoft pracovní nebo školní účet v tenantovi Azure AD své organizace (tj. jeden tenant)</li><li>**AzureADMultipleOrgs** -uživatelé s Microsoft pracovní nebo školní účet v libovolné organizaci tenanta Azure AD (například s více tenanty)</li> <li>**AzureADandPersonalMicrosoftAccount** -uživatelů pomocí osobního účtu Microsoft nebo pracovní nebo školní účet v libovolné organizaci tenanta Azure AD</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | Pole řetězců | Vlastní řetězce, které slouží ke kategorizaci a identifikaci aplikace. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

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
