---
title: Další informace o jiný token a typy, které podporuje Azure AD | Dokumentace Microsoftu
description: Příručka pro pochopení a zhodnocení deklarace identity v tokenech SAML 2.0 a webové tokeny JSON (JWT) tokeny vystavené službou Azure Active Directory (AAD)
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 5655490ab724a5570bfa7e74aa513f9b0d900b7e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581298"
---
# <a name="azure-ad-token-reference"></a>Odkaz tokenu Azure AD
Azure Active Directory (Azure AD) vysílá několika typy tokenů zabezpečení ve zpracování každý tok ověřování. Tento dokument popisuje formátu, vlastnosti zabezpečení a obsah každého typu token. 

## <a name="types-of-tokens"></a>Typy tokenů
Azure AD podporuje [protokol autorizace OAuth 2.0](v1-protocols-oauth-code.md), díky kterému budou používat access_tokens a refresh_tokens. Podporuje také ověřování a přihlásit se přes [OpenID Connect](v1-protocols-openid-connect-code.md), který představuje třetí typ token požadavku id_token. Každý z těchto prvků je reprezentován jako "nosný token".

Nosný token je token zjednodušené zabezpečení, která uděluje "nosiče" přístup k chráněnému prostředku. V tomto smyslu "nosiče" je každá strana, která může představovat token. I když se vyžaduje ověření pomocí Azure AD za účelem přijímání nosný token, musí k zabezpečení tokenu, aby se zabránilo odposlouchávání nežádoucí osobou přijmout opatření. Protože nosné tokeny nemá předdefinovaný mechanismus neoprávněnými osobami zabránit v jejich používání, musí být přenášet v zabezpečený kanál, jako je zabezpečení transportní vrstvy (HTTPS). Pokud nosný token je přenesen v nezašifrované podobě, man-in střední útoku slouží k získání tokenu a získání neoprávněného přístupu k chráněnému prostředku. Stejné zásady zabezpečení platí při ukládání nebo ukládání do mezipaměti nosné tokeny pro pozdější použití. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny bezpečným způsobem. Další informace o zabezpečení na nosné tokeny, naleznete v tématu [5 část dokumentu RFC 6750](http://tools.ietf.org/html/rfc6750).

Mnoho tokeny vystavené službou Azure AD jsou implementovány jako webových tokenů JSON nebo tokeny Jwt. Token JWT je compact, adresa URL typově bezpečný způsob přenosu informací mezi dvěma stranami. Informace obsažené v tokeny Jwt jsou označovány jako "deklarace", nebo kontrolní výrazy s informací o nosiče a předmětem token. Deklarace identity v tokeny Jwt jsou objekty JSON kódování a serializovat pro přenos. Protože tokeny Jwt vydaného službě Azure AD jsou podepsané, nikoli však šifrován, můžete snadno kontrolovat obsah token JWT pro účely ladění. Nejsou k dispozici to udělat, jako například několik nástrojů [jwt.ms](https://jwt.ms/). Další informace o tokeny Jwt, najdete [JWT specifikace](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens
Id_tokens jsou formou token zabezpečení přihlášení, který vaše aplikace obdrží při ověřování pomocí provádí [OpenID Connect](v1-protocols-openid-connect-code.md). Jsou reprezentovány jako [tokeny Jwt](#types-of-tokens)a obsahují deklarace identity, které můžete použít k podepisování uživatele do vaší aplikace. Deklarace identity můžete použít v tokentu id_token podle svých potřeb – běžně se používají pro zobrazení informací o účtu a rozhodování o řízení přístupu v aplikaci.

Id_tokens jsou podepsané, ale v tuto chvíli nejsou šifrovaná. Když aplikace obdrží tokentu id_token, musí [ověřit podpis](#validating-tokens) potvrzení pravosti tokenu a ověření několik deklarací identity v tokenu prokázat, že jeho platnost. Deklarace ověřené aplikací se liší v závislosti na požadavcích scénáře, ale existují některé [běžné deklarace identity ověření](#validating-tokens) , která vaše aplikace musí provádět v každé situaci.

Naleznete v následující části pro informace na id_tokens deklarace identity, jakož i id_token vzorku. Všimněte si, že deklarace identity v id_tokens nebudou zobrazeny v libovolném pořadí. Kromě toho je možné vytvářet nové deklarace identity do id_tokens v libovolném bodě v čase - aplikace nesmí přerušit zavedeném nových deklarací identity. Následující seznam obsahuje deklarace, které spolehlivě interpretovat vaše aplikace v době psaní tohoto návodu. Pokud se potřeby i další podrobnosti najdete v [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Ukázka id_token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> Postup, zkuste to zkontrolujete deklarace identity v požadavku id_token ukázka vložením do [jwt.ms](https://jwt.ms/).
> 
> 

#### <a name="claims-in-idtokens"></a>Deklarace identity v id_tokens
> [!div class="mx-codeBreakAll"]
| Deklarace identity tokenů JWT | Název | Popis |
| --- | --- | --- |
| `aud` |Cílová skupina |Zamýšlený příjemce tokenu. Aplikace, která přijímá token musí zkontrolujte, že hodnoty cílové skupiny je správný a odmítnout všechny tokeny, určené pro jinou cílovou skupinu. <br><br> **Příklad hodnoty SAML**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Application Authentication Context Class Reference |Určuje, jak došlo k ověření klienta. Pro veřejné klienta hodnota je 0. Pokud používáte ID klienta a tajný kód klienta, hodnota je 1. Pokud certifikát klienta byla použita pro ověřování, hodnota je 2. <br><br> **Příklad hodnoty tokenů JWT**: <br> `"appidacr": "0"` |
| `acr` |Authentication Context Class Reference |Určuje, jak byl ověřen předmět, na rozdíl od klienta v deklaraci identity Application Authentication Context Class Reference. Hodnota "0" značí, že ověřování koncového uživatele nesplňuje požadavky ISO/IEC 29115. <br><br> **Příklad hodnoty tokenů JWT**: <br> `"acr": "0"` |
| Ověřování rychlých zpráv |Zaznamenává data a času, kdy došlo k ověřování. <br><br> **Příklad hodnoty SAML**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Metoda ověřování |Určuje, jak byl předmět token ověření. <br><br> **Příklad hodnoty SAML**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Příklad hodnoty tokenů JWT**: `“amr”: ["pwd"]` |
| `given_name` |Jméno |Nabízí první nebo "zadány" jméno uživatele, jako je nastaven na objekt uživatele Azure AD. <br><br> **Příklad hodnoty SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `"given_name": "Frank"` |
| `groups` |Skupiny |Poskytuje ID objektů, které představují členství ve skupinách daného subjektu. Tyto hodnoty jsou jedinečné (viz ID objektu) a lze jej bezpečně používat při správě přístupu, jako je například vynucování autorizaci pro přístup k prostředku. Skupiny součástí skupiny deklarace identity jsou nakonfigurované na základě jednotlivých aplikací prostřednictvím vlastnosti "groupMembershipClaims" manifestu aplikace. Hodnotu null, vyloučí všechny skupiny, hodnota "Skupiny SecurityGroup" bude obsahovat pouze členství ve skupinách zabezpečení Active Directory a hodnota "Vše" bude obsahovat skupiny zabezpečení a Office 365 distribuční seznamy. <br><br> **Poznámky k**: <br> Najdete v článku `hasgroups` deklarace identity níže podrobnosti o použití `groups` deklarace identity s implicitního udělení. <br> Pro další toky Pokud počet skupin, které je uživatel v překročí limit (150 pro SAML, 200 pro token JWT) a deklarace identity Nadlimitní využití se přidají zdroje deklarací identity směřující na koncový bod grafu obsahující seznam skupin pro daného uživatele. (v. <br><br> **Příklad hodnoty SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
|`hasgroups` | Při překročení limitu indikátor skupiny tokenů JWT implicitní tok| Pokud jsou k dispozici, vždy `true`, které označuje, že uživatel je v aspoň jedné skupině. Použité místo `groups` deklaraci identity pro tokeny Jwt implicitní grant toků, pokud celé skupiny deklarací identity i fragment URI nad rámec omezení délky adresu URL (aktuálně 6 nebo více skupin). Označuje, že klient musí použít graf k určení skupin uživatele (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` <br> `http://schemas.microsoft.com/claims/groups.link` | Při překročení limitu indikátor skupiny | Pro žádosti o tokeny, které nejsou omezena délka (naleznete v tématu `hasgroups` výše), ale stále příliš velký pro daný token, odkaz na seznam úplné skupiny pro uživatele, budou zahrnuty. Pro tokeny Jwt jako deklaraci distribuovaný pro SAML jako novou deklaraci místo `groups` deklarací identity. <br><br> **Příklad hodnoty SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/claims/groups.link”>`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `"groups":"src1` <br> `_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `idp` |Zprostředkovatel identity |Zaznamenává zprostředkovatele identity, která ověřena předmětem token. Tato hodnota se shoduje s hodnotu deklarace identity vystavitele, pokud je uživatelský účet v jiném tenantovi než vystavitele. <br><br> **Příklad hodnoty SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Ukládá čas, kdy byl token vydán. Často se používá k měření token aktuálnosti. <br><br> **Příklad hodnoty SAML**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Příklad hodnoty tokenů JWT**: <br> `"iat": 1390234181` |
| `iss` |Vystavitel |Identifikuje službu tokenů zabezpečení (STS), který vytvoří a vrátí token. V tokenech, které vrací Azure AD že vystavitel je sts.windows.net. Identifikátor GUID v hodnotě deklarace identity vystavitele je ID tenanta adresáře Azure AD. ID tenanta je identifikátor neměnné a spolehlivé adresáře. <br><br> **Příklad hodnoty SAML**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Příklad hodnoty tokenů JWT**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Příjmení |Jak je definováno v objektu uživatele Azure AD poskytuje poslední jméno, příjmení nebo příjmení uživatele. <br><br> **Příklad hodnoty SAML**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `"family_name": "Miller"` |
| `unique_name` |Název |Poskytuje lidské čitelné hodnotu, která identifikuje předmětem token. Tato hodnota nemusí být jedinečný v rámci tenanta a je určený pouze pro účely zobrazení. <br><br> **Příklad hodnoty SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |ID objektu |Obsahuje jedinečný identifikátor objektu ve službě Azure AD. Tato hodnota je neměnná a nelze přiřadit nebo znovu použít. ID objektu použijte k identifikaci objektu v dotazech ke službě Azure AD. <br><br> **Příklad hodnoty SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Role |Představuje všechny aplikační role, které předmět bylo uděleno přímo i nepřímo prostřednictvím členství ve skupinách a je možné vynutit řízení přístupu na základě rolí. Aplikační role jsou definované na základě jednotlivých aplikací prostřednictvím `appRoles` vlastnosti manifestu aplikace. `value` Vlastnost jednotlivé aplikační role je hodnota, která se zobrazí v deklarace identity rolí. <br><br> **Příklad hodnoty SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Rozsah |Označuje zosobnění oprávnění udělená do klientské aplikace. Výchozí oprávnění je `user_impersonation`. Vlastník prostředku zabezpečené můžete zaregistrovat další hodnoty ve službě Azure AD. <br><br> **Příklad hodnoty tokenů JWT**: <br> `"scp": "user_impersonation"` |
| `sub` |Předmět |Určuje objekt o tom, které token vyhodnocuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nejde ji přiřadit nebo opakovaně používat, proto ji lze použít k provedení kontroly autorizace bezpečně. Protože předmět je vždy přítomna v tokenech problémy s Azure AD, je doporučeno použití této hodnoty v rámci obecné účely autorizace systému. <br> `SubjectConfirmation` není deklarace identity. Popisuje, jak ověřit předmětem token. `Bearer` Označuje, předmět je potvrzen jejich vlastnictví tokenu. <br><br> **Příklad hodnoty SAML**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |ID tenanta |Neměnné a jednorázovým identifikátor, který identifikuje tenantu Active directory, která token vydala. Tuto hodnotu můžete použít pro přístup k prostředkům specifickým pro tenanta adresáře v aplikaci s více tenanty. Tuto hodnotu můžete například použít k identifikaci tenanta služby ve volání rozhraní Graph API. <br><br> **Příklad hodnoty SAML**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |Životnost tokenu |Definuje časový interval, ve kterém je token platný. Služba, která ověří token, který by měl ověřte, že aktuální datum v rámci dobu životnosti tokenu, jiný ji by měl odmítnout token. Služba může umožnit až pět minut mimo rozsah životnost tokenu aby se zohlednily případné rozdíly v čase ("čas Nerovnoměrná distribuce") mezi službami Azure AD a služby. <br><br> **Příklad hodnoty SAML**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Příklad hodnoty tokenů JWT**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |Hlavní název uživatele (UPN) |Uloží uživatelské jméno hlavní název uživatele.<br><br> **Příklad hodnoty tokenů JWT**: <br> `"upn": frankm@contoso.com` |
| `ver` |Verze |Ukládá číslo verze tokenu. <br><br> **Příklad hodnoty tokenů JWT**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Přístupové tokeny
Po úspěšném ověření Azure AD vrací přístupový token, který umožňuje přístup k chráněným prostředkům. JSON Web Token (JWT) s kódováním base 64 a její obsah můžete zkontrolovat spuštěním prostřednictvím dekodér je přístupový token.

Pokud vaše aplikace pouze *používá* přístupové tokeny získat přístup k rozhraním API, můžete (a měli) považovat za přístupové tokeny stane zcela neprůhledný – jsou pouze řetězce, které vaše aplikace může předat prostředky v požadavcích HTTP.

Při žádosti o přístupový token Azure AD také vrátí hodnotu některá metadata o přístupový token pro využití vaší aplikace. Tyto informace zahrnují čas vypršení platnosti přístupového tokenu a oborů, pro které je platný. To umožňuje aplikaci inteligentního ukládání přístupových tokenů bez nutnosti parsovat otevřít do mezipaměti přístupový token, samotného.

Pokud je vaše aplikace rozhraní API chráněné službou Azure AD, která očekává přístupových tokenů v požadavcích HTTP, pak byste měli provádět ověřování a kontroly tokenů, který jste dostali. Vaše aplikace by měla provést ověření přístupového tokenu před použitím pro přístup k prostředkům. Další informace o ověřování najdete v tématu [ověřování tokenů](#validating-tokens). Podrobnosti o tom, jak to udělat pomocí .NET najdete v tématu [ochrana webového rozhraní API pomocí nosných tokenů z Azure AD](quickstart-v1-dotnet-webapi.md).

## <a name="refresh-tokens"></a>Obnovovacích tokenů

Aktualizovat tokeny jsou tokeny zabezpečení, které vaše aplikace můžete použít k získání nových přístupových tokenů ve toku OAuth 2.0. To umožňuje vaší aplikaci zajistit dlouhodobou přístup k prostředkům jménem uživatele bez nutnosti zásahu uživatele.

Obnovovací tokeny jsou více prostředků. To znamená, že obnovovací token přijatých během žádosti o token pro jeden prostředek je možné uplatnit pro přístupové tokeny pro úplně jiných zdrojů. Chcete-li to provést, nastavte `resource` parametr do požadavku na cílový prostředek.

Obnovovací tokeny se stane zcela neprůhledný do vaší aplikace. Jsou dlouhodobé, ale neměl by být zapsaný vaší aplikace můžete očekávat, že bude trvat obnovovací token pro libovolné časové období. Tokeny obnovení může být neplatné v každém okamžiku v čase z různých důvodů – viz [Token zrušení](#token-revocation) z těchto důvodů. Jedině pro vaši aplikaci vědět, jestli je platný token obnovení je pokus o uplatněte ho tak, že žádosti o token na koncový bod tokenu Azure AD.

Když uplatníte obnovovací token pro nový přístupový token, zobrazí se nového tokenu obnovení v odpovědi tokenu. Měli byste uložit nově vydaný obnovovací token nahrazení, který jste použili v požadavku. Tím se zaručí, že obnovovací tokeny jsou dál platné pro co nejdéle.

## <a name="validating-tokens"></a>Ověřování tokenů

Chcete-li ověřit tokentu id_token nebo access_token, by měla vaše aplikace ověřit podpis tokenu a deklarace identity. Chcete-li ověřit přístupové tokeny, by měla vaše aplikace také ověření vystavitele, cílovou skupinu a podepisování tokenů. Tyto názvy musí být ověřena hodnoty v dokumentu zjišťování OpenID. Například se nachází v tenantovi nezávislé verzi dokumentu [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). Azure AD middleware obsahuje integrované funkce pro ověřování tokenů přístupu, a můžete procházet pomocí našich [ukázky](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) najít v jazyce podle vašeho výběru. Další informace o tom, jak explicitně ověřit JWT token, najdete v tématu [ruční Ukázka ověřování tokenů JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation). 

Poskytuje knihovny a ukázky kódu, které ukazují, jak snadno zpracovávat ověřování tokenů - níže uvedené informace se jednoduše poskytuje pro ty, kteří chtějí porozumět základního procesu. Existují také několik open source knihoven třetích stran k dispozici pro ověřování tokenů JWT: k dispozici aspoň jedna možnost pro téměř každou platformu a jazyk tam. Další informace o knihoven ověřování služby Azure AD a ukázky kódu najdete v tématu [knihoven ověřování služby Azure AD](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Ověření podpisu

Token JWT obsahuje tři segmenty, které jsou odděleny `.` znak. První segment se označuje jako **záhlaví**, druhý jako **tělo**a třetí jako **podpis**. Segment podpis slouží k ověření pravosti tokenu tak, aby může být důvěřuje vaší aplikace.

Tokeny vystavené službou Azure AD jsou podepsány pomocí algoritmů standardní asymetrického šifrování odvětví, jako je například RSA 256. Záhlaví tokenů JWT obsahuje informace o metodě a šifrovací klíče použitý k podpisu tokenu:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` Deklarace identity označuje algoritmus, který se použil k podepsání tokenů, přestože `x5t` deklarace identity označuje konkrétní veřejný klíč, který se použil k podepsání token.

V libovolném časovém okamžiku v čase může vyžadovat přihlášení Azure AD tokentu id_token pomocí některé z sadu párů veřejného a privátního klíče. Azure AD otočí možné sadu klíčů v pravidelných intervalech, takže vaše aplikace by měla napsané tak, aby tyto klíče změny automaticky. Přiměřené intervalech a kontrolují dostupnost aktualizací pro veřejné klíče používané službou Azure AD je každých 24 hodin.

Můžete získat podpisové klíče data potřebná k ověření podpisu pomocí dokument metadat OpenID Connect, který se nachází v:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Vyzkoušejte tuto adresu URL v prohlížeči.

Tento dokument metadat je objekt JSON, který obsahuje několik užitečné údaje, jako je například umístění různých koncových bodů požadovaných pro ověřování OpenID Connect. 

Zahrnuje také `jwks_uri`, což dává umístění sady veřejného klíče používané k podepisování tokenů. Umístění dokumentu JSON `jwks_uri` obsahuje všechny údaje o veřejném klíči použité v této konkrétní okamžik v čase. Vaše aplikace může používat `kid` deklarací identity v hlavičce JWT k výběru, který veřejný klíč v tomto dokumentu se použil k podepsání konkrétní token. Pak můžete provádět ověření podpisu pomocí správný veřejný klíč a označený algoritmus.

> [!NOTE]
> Koncový bod verze 1.0 vrací i `x5t` a `kid` deklarací identity. `x5t` Chybí deklarace identity z verze 2.0 tokenů. Koncový bod v2.0 odpoví `kid` deklarací identity. Od této chvíle, vám doporučujeme používat `kid` deklarace identity k ověření tokenu.

Provádí se ověření podpisu je mimo rámec tohoto dokumentu – k dispozici řada open source knihoven pomáhá vám tak v případě potřeby.

#### <a name="validating-the-claims"></a>Ověřování deklarací identity

Když aplikace obdrží token (tokentu id_token při přihlášení uživatele, nebo přístupový token jako nosný token v požadavku HTTP) má také provést několik kontroly před deklarací identity v tokenu. Patří mezi ně jsou mimo jiné tyto:

* **Cílovou skupinu** deklarace identity – chcete-li ověřit, že token, který měla předávat do aplikace.
* **Není před** a **čas vypršení platnosti** deklarace – ověřte, že nevypršela platnost tokenu.
* **Vystavitele** deklarace identity – chcete-li ověřit, že byl token ve skutečnosti vydán do vaší aplikace pomocí Azure AD.
* **Nonce** – Pokud chcete zmírnit útok opětovného přehrání tokenu.
* a další...

Úplný seznam deklarací identity ověřování vaší aplikace měli provést pro tokeny typu ID, najdete [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). Podrobnosti o očekávané hodnoty pro tyto deklarace jsou zahrnuty v předchozím [id_token](#id-tokens) oddílu.

## <a name="token-revocation"></a>Token zrušení

Aktualizovat tokeny můžete platnost nebo kdykoli pro celou řadu důvodů odvolat. Spadají do dvou hlavních kategorií: vypršení časového limitu a zrušení. 

**Vypršení časového limitu pro token**

* MaxInactiveTime: Pokud obnovovací token nebyl použit v čase, závisí MaxInactiveTime, aktualizovat Token nebude platný. 
* MaxSessionAge: Pokud MaxAgeSessionMultiFactor nebo MaxAgeSessionSingleFactor byla nastavena na jinou hodnotu než výchozí (až do zrušeno), pak opakované ověření se bude vyžadovat po dobu nastavenou v MaxAgeSession * uplyne. 
* Příklady:
  * Klient má MaxInactiveTime 5 dní a uživatel přešel na dovolenou týden a proto AAD není vidět nové žádosti o token uživatele za 7 dní. Při příštím uživatel požaduje nový token, najdou jejich aktualizovat Token se odvolal, a musí znovu zadat své přihlašovací údaje. 
  * Citlivé aplikace má MaxAgeSessionSingleFactor 1 den. Pokud se uživatel přihlásí pondělí a Tuesday (po uplynutí 25 hodin), se bude muset znovu ověřit. 

**Odvolání**

|   | Heslo na základě souborů cookie | Založené na token | Heslo bez souborů cookie s využitím | Na základě tokenu bez hesla | Token důvěrnému klientovi| 
|---|-----------------------|----------------------|---------------------------|--------------------------|--------------------------|
|Vypršení platnosti hesla| Zůstane aktivní|Zůstane aktivní|Zůstane aktivní|Zůstane aktivní|Zůstane aktivní|
|Heslo změněno uživatelem| Odvolaný | Odvolaný | Zůstane aktivní|Zůstane aktivní|Zůstane aktivní|
|Uživatel provede samoobslužné resetování HESLA|Odvolaný | Odvolaný | Zůstane aktivní|Zůstane aktivní|Zůstane aktivní|
|Resetování hesla správce|Odvolaný | Odvolaný | Zůstane aktivní|Zůstane aktivní|Zůstane aktivní|
|Uživatel Odvolá jejich obnovovací tokeny [přes PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Odvolaný | Odvolaný |Odvolaný | Odvolaný |Odvolaný | Odvolaný |
|Správce pro tenanta odvolá všechny obnovovací tokeny [přes PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Odvolaný | Odvolaný |Odvolaný | Odvolaný |Odvolaný | Odvolaný |
|[Jednotné přihlašování,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code#single-sign-out) na webu | Odvolaný | Zůstane aktivní |Odvolaný | Zůstane aktivní |Zůstane aktivní |Zůstane aktivní |

> [!NOTE]
> Kde uživatele nebylo zadáno heslo se dá stáhnout je "bez hesla na základě" přihlášení.  Například při použití vaší pro rozpoznávání tváře s Windows Hello, FIDO klíč nebo PIN kód. 
>
> Známý problém s Windows primární aktualizovat Token existuje.  Pokud PRT je získali prostřednictvím heslo a pak uživatel přihlásí pomocí Hello, nezmění se původ PRT a bude možné odvolat, pokud uživatel změní heslo. 

## <a name="sample-tokens"></a>Ukázka tokeny

Tento oddíl zobrazuje ukázky tokeny SAML a token JWT, které vrací Azure AD. Tyto ukázky vám umožňují zobrazit deklarací identity v kontextu.

### <a name="saml-token"></a>SAML Token

Toto je ukázka typické tokenu SAML.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>Token JWT - zosobnění uživatele
Toto je ukázka typické JSON web token (JWT) používané toku přidělení kódu autorizace.
Kromě deklarace identity a token, který obsahuje číslo verze v **ver** a **appidacr**, ověřování kontextu odkazech na třídy rozhraní, která určuje, jak došlo k ověření klienta. Pro veřejné klienta hodnota je 0. Pokud bylo použito ID klienta a tajný kód klienta, hodnota je 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Související obsah
* V tématu Azure AD Graph [operace se zásadami](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) a [entita zásad](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), další informace o správě životnosti tokenu zásad prostřednictvím rozhraní Azure AD Graph API.
* Další informace a ukázky pro správu zásad prostřednictvím rutin Powershellu, včetně ukázek, naleznete v tématu [konfigurovatelné životností tokenů ve službě Azure AD](../active-directory-configurable-token-lifetimes.md). 
* Přidat [vlastní a volitelných deklarací](active-directory-optional-claims.md) tokeny pro vaši aplikaci. 
