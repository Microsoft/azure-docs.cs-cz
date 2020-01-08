---
title: Princip manifestu aplikace Azure Active Directory | Microsoft Docs
description: Podrobné pokrytí manifestu Azure Active Directory aplikace, který představuje konfiguraci identity aplikace v tenantovi Azure AD a používá se k usnadnění autorizace OAuth, používání souhlasu a dalších.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64823dbe2595d7896b9339745f6c8642a32f74e9
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638693"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory manifest aplikace

Manifest aplikace obsahuje definici všech atributů objektu aplikace na platformě Microsoft identity. Slouží také jako mechanismus pro aktualizaci objektu aplikace. Další informace o entitě aplikace a jejím schématu naleznete v dokumentaci k [entitě aplikace Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Atributy aplikace můžete nakonfigurovat pomocí Azure Portal nebo programově pomocí [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) nebo [PowerShellu](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). Existují však situace, kdy budete muset upravit manifest aplikace a nakonfigurovat atribut aplikace. Mezi tyto scénáře patří:

* Pokud jste aplikaci zaregistrovali jako víceklientské a osobní účty Microsoft Azure AD, nemůžete v uživatelském rozhraní měnit podporované účty Microsoft. Místo toho je nutné použít editor manifestu aplikace ke změně podporovaného typu účtu.
* Pokud potřebujete definovat oprávnění a role, které vaše aplikace podporuje, je nutné změnit manifest aplikace.

## <a name="configure-the-app-manifest"></a>Konfigurace manifestu aplikace

Konfigurace manifestu aplikace:

1. Přejděte na [portál Azure](https://portal.azure.com). Vyhledejte a vyberte službu **Azure Active Directory** .
1. Vyberte **Registrace aplikací**.
1. Vyberte aplikaci, kterou chcete nakonfigurovat.
1. Na stránce **Přehled** aplikace vyberte část **Manifest**. Otevře se webový editor manifestu, který umožňuje upravovat manifest v rámci portálu. Volitelně můžete vybrat **Stáhnout** a upravit manifest místně a potom použít **nahrávání** pro jeho opakované použití do aplikace.

## <a name="manifest-reference"></a>Odkaz na manifest

> [!NOTE]
> Pokud se po **popisu**nezobrazí sloupec **Příklad hodnoty** , maximalizujte okno prohlížeče a posuňte se nebo potáhněte prstem, dokud se nezobrazí sloupec **Příklad hodnoty** .

| Klíč  | Typ hodnoty | Popis  | Příklad hodnoty |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Hodnota Int32 s možnou hodnotou null | Určuje verzi přístupového tokenu, kterou prostředek očekával. Tento parametr změní verzi a formát tokenu JWT vyprodukovaného nezávisle na koncovém bodu nebo klientovi, který se použil k vyžádání přístupového tokenu.<br/><br/>Koncový bod použitý v 1.0 nebo v 2.0 je vybraný klientem a má vliv jenom na verzi id_tokens. Prostředky musí explicitně konfigurovat `accesstokenAcceptedVersion`, aby označovaly formát podporovaného přístupového tokenu.<br/><br/>Možné hodnoty pro `accesstokenAcceptedVersion` jsou 1, 2 nebo null. Pokud je hodnota null, tento parametr se nastaví na hodnotu 1, která odpovídá koncovému bodu v 1.0. <br/><br/>Pokud je `AzureADandPersonalMicrosoftAccount``signInAudience`, musí být hodnota `2`  | `2` |
| `addIns` | Kolekce | Definuje vlastní chování, které může přijímající služba použít k volání aplikace v konkrétních kontextech. Například aplikace, které mohou vykreslovat datové proudy, mohou nastavovat vlastnost addIns pro její funkci "Nástroj pro vyřizování souborů". Tento parametr umožní službám, jako je Office 365, volat aplikaci v kontextu dokumentu, na kterém uživatel pracuje. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Logická hodnota | Určuje typ záložní aplikace. Služba Azure AD ve výchozím nastavení odvodí typ aplikace z replyUrlsWithType. Existují některé scénáře, kdy služba Azure AD nemůže určit typ klientské aplikace. Například jedním z takových scénářů je [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) tok, ve kterém se požadavek HTTP stane bez přesměrování adresy URL). V těchto případech služba Azure AD bude interpretovat typ aplikace na základě hodnoty této vlastnosti. Pokud je tato hodnota nastavená na true, typ záložní aplikace se nastaví jako veřejný klient, jako je například nainstalovaná aplikace spuštěná v mobilním zařízení. Výchozí hodnota je false, což znamená, že typ záložní aplikace je důvěrný klient, jako je například webová aplikace. | `false` |
| `availableToOtherTenants` | Logická hodnota | true, pokud je aplikace sdílená s ostatními klienty; v opačném případě false. <br><br> _Poznámka: Tato možnost je k dispozici pouze v prostředí Registrace aplikací (starší verze). V prostředí [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) nahrazeno `signInAudience`._ | |
| `appId` | Řetězec | Určuje jedinečný identifikátor pro aplikaci, která je přiřazená aplikaci pomocí Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Kolekce | Určuje kolekci rolí, které může aplikace deklarovat. Tyto role se dají přiřadit uživatelům, skupinám nebo objektům služby. Další příklady a informace najdete v tématu [Přidání rolí aplikace v aplikaci a jejich přijetí v tokenu](howto-add-app-roles-in-azure-ad-apps.md) . | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | Řetězec | Zobrazovaný název aplikace <br><br> _Poznámka: Tato možnost je k dispozici pouze v prostředí Registrace aplikací (starší verze). V prostředí [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) nahrazeno `name`._ | `"MyRegisteredApp"` |
| `errorUrl` | Řetězec | Nepodporováno. | |
| `groupMembershipClaims` | Řetězec | Nakonfiguruje deklaraci identity `groups` vydanou v uživatelském nebo přístupovém tokenu OAuth 2,0, kterou očekává aplikace. Chcete-li nastavit tento atribut, použijte jednu z následujících platných řetězcových hodnot:<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (pro skupiny zabezpečení a role Azure AD)<br/>- `"All"` (zobrazí se všechny skupiny zabezpečení, distribuční skupiny a role adresáře služby Azure AD, kterých je přihlášený uživatel členem. | `"SecurityGroup"` |
| `homepage` | Řetězec | Adresa URL domovské stránky aplikace <br><br> _Poznámka: Tato možnost je k dispozici pouze v prostředí Registrace aplikací (starší verze). V prostředí [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) nahrazeno `signInUrl`._ | `"https://MyRegisteredApp"` |
| `objectId` | Řetězec | Jedinečný identifikátor aplikace v adresáři <br><br> _Poznámka: Tato možnost je k dispozici pouze v prostředí Registrace aplikací (starší verze). V prostředí [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) nahrazeno `id`._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | Řetězec | Volitelné deklarace identity vrácené v tokenu službou tokenu zabezpečení pro tuto konkrétní aplikaci.<br>V tuto chvíli aplikace, které podporují osobní účty i službu Azure AD (zaregistrované prostřednictvím portálu pro registraci aplikací), nemůžou používat volitelné deklarace identity. Aplikace zaregistrované pro jenom Azure AD pomocí koncového bodu v 2.0 ale můžou získat volitelné deklarace identity, které požadoval v manifestu. Další informace najdete v tématu [volitelné deklarace identity](active-directory-optional-claims.md). | `null` |
| `id` | Řetězec | Jedinečný identifikátor aplikace v adresáři Toto ID není identifikátor používaný k identifikaci aplikace v jakékoli transakci protokolu. Používá se pro odkazování na objekt v dotazech adresářů. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | Uživatelsky definované identifikátory URI, které jedinečně identifikují webovou aplikaci v rámci jejího tenanta Azure AD, nebo v ověřené vlastní doméně, pokud je aplikace více tenantů. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | Řetězec | Určuje odkazy na podmínku služby a prohlášení o zásadách ochrany osobních údajů aplikace. Podmínky služby a prohlášení o zásadách ochrany osobních údajů jsou v souladu s uživatelským prostředím týkajícím se souhlasu uživatele. Další informace najdete v tématu [Postup: Přidání podmínek služby a prohlášení o zásadách ochrany osobních údajů pro registrované aplikace služby Azure AD](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Kolekce | Obsahuje odkazy na přihlašovací údaje přiřazené aplikacím, sdílené tajné klíče založené na řetězci a certifikáty X. 509. ). | <code>[<br>&nbsp;{<br>&nbsp;&These credentials are used when requesting access tokens (when the app is acting as a client rather that as resourcenbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | Používá se ke sdružování souhlasu, pokud máte řešení, které obsahuje dvě části: klientská aplikace a vlastní aplikace webového rozhraní API. Pokud zadáte appID klientské aplikace do této hodnoty, bude uživatel muset pro klientskou aplikaci pouze odsouhlasit. Služba Azure AD bude mít za to, že se souhlasem s klientem znamená, že implicitně souhlasí s webovým rozhraním API. Budou automaticky zřizovat instanční objekty pro klientské i webové rozhraní API současně. Klient i aplikace webového rozhraní API musí být zaregistrované ve stejném tenantovi. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | Řetězec | Hodnota jen pro čtení, která odkazuje na adresu URL CDN na logo, které se nahrálo na portálu. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | Řetězec | Adresa URL pro odhlášení z aplikace | `"https://MyRegisteredAppLogout"` |
| `name` | Řetězec | Zobrazovaný název aplikace | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Logická hodnota | Určuje, jestli tato webová aplikace může vyžádat tokeny přístupu implicitního toku OAuth 2.0. Výchozí hodnotou je hodnota false. Tento příznak se používá pro aplikace založené na prohlížeči, jako jsou například jednostránkové aplikace JavaScriptu. Pokud se chcete dozvědět víc, zadejte `OAuth 2.0 implicit grant flow` v obsahu a podívejte se na témata o implicitním toku. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Logická hodnota | Určuje, jestli tato webová aplikace může vyžádat tokeny pro implicitní ID toku OAuth 2.0. Výchozí hodnotou je hodnota false. Tento příznak se používá pro aplikace založené na prohlížeči, jako jsou například jednostránkové aplikace JavaScriptu. | `false` |
| `oauth2Permissions` | Kolekce | Určuje kolekci oborů oprávnění OAuth 2,0, které aplikace webového rozhraní API (Resource) zpřístupňuje klientským aplikacím. Tyto obory oprávnění se můžou klientským aplikacím udělit během souhlasu. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code>|
| `oauth2RequiredPostResponse` | Logická hodnota | Určuje, jestli v rámci požadavků na token OAuth 2,0 Azure AD umožní žádosti POST, a to na rozdíl od získání požadavků. Výchozí hodnota je false (NEPRAVDA), která určuje, že budou povoleny pouze požadavky GET. | `false` |
| `parentalControlSettings` | Řetězec | `countriesBlockedForMinors` určuje země, ve kterých je aplikace blokovaná pro nezletilé.<br>`legalAgeGroupRule` Určuje pravidlo věkové skupiny platné pro uživatele aplikace. Dá se nastavit na `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`nebo `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>}</code> |
| `passwordCredentials` | Kolekce | Přečtěte si popis vlastnosti `keyCredentials`. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>]</code> |
| `preAuthorizedApplications` | Kolekce | Vypíše aplikace a požadovaná oprávnění pro implicitní souhlas. Vyžaduje, aby správce poskytl souhlas s aplikací. preAuthorizedApplications nevyžaduje, aby uživatel mohl udělit souhlas s požadovanými oprávněními. Oprávnění uvedená v preAuthorizedApplications nevyžadují souhlas uživatele. Nicméně jakákoli další požadovaná oprávnění, která nejsou uvedená v preAuthorizedApplications, vyžadují souhlas uživatele. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Logická hodnota | Určuje, jestli je tato aplikace veřejným klientem (například nainstalovaná aplikace spuštěná v mobilním zařízení). <br><br> _Poznámka: Tato vlastnost je k dispozici pouze v prostředí Registrace aplikací (starší verze). V prostředí [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) nahrazeno `allowPublicClient`._ | |
| `publisherDomain` | Řetězec | Ověřená doména vydavatele pro aplikaci. Jen pro čtení. | https://www.contoso.com |
| `replyUrls` | Pole řetězců | Tato vlastnost s více hodnotami obsahuje seznam registrovaných redirect_uri hodnot, které služba Azure AD přijme jako cílová umístění při vracení tokenů. <br><br> _Poznámka: Tato vlastnost je k dispozici pouze v prostředí Registrace aplikací (starší verze). V prostředí [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) nahrazeno `replyUrlsWithType`._ | |
| `replyUrlsWithType` | Kolekce | Tato vlastnost s více hodnotami obsahuje seznam registrovaných redirect_uri hodnot, které služba Azure AD přijme jako cílová umístění při vracení tokenů. Každá hodnota identifikátoru URI by měla obsahovat přidruženou hodnotu typu aplikace. Podporované hodnoty typu jsou: <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> Přečtěte si další informace o [omezeních a](https://docs.microsoft.com/azure/active-directory/develop/reply-url)omezeních replyUrl. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Kolekce | S dynamickým souhlasem `requiredResourceAccess` zařídí prostředí pro vyjádření souhlasu správce a možnosti souhlasu uživatele pro uživatele, kteří používají statický souhlas. Tento parametr ale neřídí uživatelské prostředí pro vyjádření souhlasu uživatele pro obecný případ.<br>`resourceAppId` je jedinečný identifikátor pro prostředek, ke kterému aplikace vyžaduje přístup. Tato hodnota by měla být stejná jako appId deklarované v cílové aplikaci prostředků.<br>`resourceAccess` je pole, ve kterém jsou uvedené obory oprávnění OAuth 2.0 a aplikační role, které aplikace od zadaného prostředku vyžaduje. Obsahuje hodnoty `id` a `type` zadaných prostředků. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `samlMetadataUrl` | Řetězec | Adresa URL metadat SAML pro aplikaci | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | Řetězec | Určuje adresu URL domovské stránky aplikace. | `https://MyRegisteredApp` |
| `signInAudience` | Řetězec | Určuje, jaké účty Microsoft se pro aktuální aplikaci podporují. Podporované hodnoty jsou:<ul><li>**AzureADMyOrg** – uživatelé s pracovním nebo školním účtem Microsoft v TENANTOVI Azure AD ve vaší organizaci (například jeden tenant)</li><li>**AzureADMultipleOrgs** – uživatelé s pracovním nebo školním účtem Microsoft v TENANTOVI Azure AD ve vaší organizaci (například víceklientské víceklientské úrovni)</li> <li>**AzureADandPersonalMicrosoftAccount** – uživatelé s osobní účet Microsoft nebo pracovní nebo školní účet v TENANTOVI Azure AD organizace</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | Vlastní řetězce, které lze použít ke kategorizaci a identifikaci aplikace. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Běžné problémy

### <a name="manifest-limits"></a>Omezení manifestu

Manifest aplikace má více atributů, které jsou označovány jako kolekce; například approles, přihlašovací údaje, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess a oauth2Permissions. V rámci kompletního manifestu aplikace pro libovolnou aplikaci byl celkový počet položek ve všech kolekcích kombinovaný v kombinaci s omezené v 1200. Pokud jste v manifestu aplikace dříve zadali 100 identifikátorů URI pro přesměrování, budete mít k pouze 1100 zbývajících záznamů pro použití ve všech ostatních kolekcích, které dohromady tvoří manifest.

> [!NOTE]
> V případě, že se pokusíte přidat více než 1200 záznamů v manifestu aplikace, může se zobrazit chyba **"Nepodařilo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: Velikost manifestu překročila svůj limit. Snižte počet hodnot a opakujte požadavek. "**

### <a name="unsupported-attributes"></a>Nepodporované atributy

Manifest aplikace představuje schéma základního aplikačního modelu ve službě Azure AD. V rámci vývoje základního schématu se editor manifestu aktualizuje tak, aby odrážel nové schéma od času až po čas. V důsledku toho můžete všimnout, že se nové atributy zobrazí v manifestu aplikace. Ve výjimečných případech si můžete všimnout syntaktické nebo sémantické změny v existujících atributech. můžete ale najít již dříve nepodporovaný atribut. V [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908)se například zobrazí nové atributy, které se v prostředí registrace aplikací (starší verze) označují jiným názvem.


| Registrace aplikací (starší verze)| Registrace aplikací           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Popisy těchto atributů naleznete v části Reference k [manifestu](#manifest-reference) .

Při pokusu o nahrání dříve staženého manifestu se může zobrazit jedna z následujících chyb. Tato chyba je pravděpodobně způsobena tím, že editor manifestu teď podporuje novější verzi schématu, která neodpovídá tomu, který se pokoušíte nahrát.

- **Nepovedlo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: neplatný identifikátor objektu undefined. [].** "
- **Nepovedlo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: jedna nebo více zadaných hodnot vlastností je neplatných. [].** "
- **Nepovedlo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: nepovoluje se nastavit availableToOtherTenants v této verzi rozhraní API pro aktualizaci. [].** "
- **Nepovedlo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: aktualizace vlastnosti ' replyUrls ' není pro tuto aplikaci povolena. Místo toho použijte vlastnost ' replyUrlsWithType '. [].** "
- **Nepovedlo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: byla nalezena hodnota bez názvu typu a není k dispozici žádný očekávaný typ. Při zadání modelu musí mít každá hodnota v datové části typ, který může být buď zadán v datové části, explicitně volajícím nebo implicitně odvozený z nadřazené hodnoty. []** "

Když se zobrazí jedna z těchto chyb, doporučujeme, abyste provedli následující akce:

1. Upravte atributy jednotlivě v editoru manifestu namísto nahrávání dříve staženého manifestu. Použijte [referenční tabulku manifestu](#manifest-reference) k pochopení syntaxe a sémantiky starých a nových atributů, abyste mohli úspěšně upravit atributy, které vás zajímají. 
1. Pokud váš pracovní postup vyžaduje, abyste uložili manifesty ve zdrojovém úložišti pro pozdější použití, doporučujeme, abyste uložili manifesty v úložišti, které jste viděli v prostředí **Registrace aplikací** .

## <a name="next-steps"></a>Další kroky

* Další informace o vztahu mezi aplikací aplikace a objekty instančních objektů najdete v tématu [aplikace a instanční objekty služby v Azure AD](app-objects-and-service-principals.md).
* V tématu [Microsoft Identity Platform Developer Glosář](developer-glossary.md) najdete definice některých základních konceptů vývojářů platformy Microsoft Identity Platform.

Následující komentáře vám poskytnou zpětnou vazbu, která pomáhá Upřesnit a natvarovat obsah.

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
