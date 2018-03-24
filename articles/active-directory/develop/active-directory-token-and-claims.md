---
title: Další informace o různých token a deklarace identity typy podporované službou Azure AD | Microsoft Docs
description: Příručka pro pochopení a zhodnocení deklarace identity v tokenech SAML 2.0 a webové tokeny JSON (JWT), vystavené pomocí Azure Active Directory (AAD)
documentationcenter: na
author: hpsin
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8f9eb95f49e0d2bd01d9ead7eda7d13288bfd573
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-token-reference"></a>Odkaz tokenu Azure AD
Azure Active Directory (Azure AD) vysílá několik typů tokenů zabezpečení ve zpracování každý tok ověřování. Tento dokument popisuje formát, zabezpečení vlastnosti a obsah každého typu token.

## <a name="types-of-tokens"></a>Typy tokenů
Azure AD podporuje [protokol autorizace OAuth 2.0](active-directory-protocols-oauth-code.md), které využívá access_tokens a refresh_tokens.  Také podporuje ověřování a přihlásit se přes [OpenID Connect](active-directory-protocols-openid-connect-code.md), který představuje třetí typ tokenu, požadavku id_token.  Všechny tyto tokeny je reprezentován jako "nosný token".

Je token nosiče tokenu lightweight zabezpečení, který uděluje přístup "nosiče" k chráněnému prostředku. V tomto smyslu je "nosiče" libovolné strany, který může být token. I když příjem token nosiče vyžaduje, aby se vyžaduje ověření službou Azure AD, musí třeba učinit zabezpečit tokenu, aby se zabránilo zachycení nezamýšleným strana. Protože nosné tokeny nemají integrovanou mechanismus zabránit neoprávněným stranám jejich používání, musí být přenosu v zabezpečený kanál, jako je například transport layer security (HTTPS). Pokud token nosiče je přenesen v nešifrované podobě, man-in střední útoku slouží k získání tokenu a získání neoprávněného přístupu k chráněnému prostředku. Při ukládání nebo ukládání do mezipaměti nosné tokeny pro pozdější použití platí stejné zásady zabezpečení. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny zabezpečeným způsobem. Další aspekty zabezpečení na nosné tokeny, najdete v části [RFC 6750 část 5](http://tools.ietf.org/html/rfc6750).

Řadu tokeny vydané službou Azure AD jsou implementované jako webové tokeny JSON nebo Jwt.  Token JWT je compact, adresa URL bezpečných způsob přenosu informací mezi dvěma účastníky.  Informace obsažené v Jwt se označují jako "deklarace identity" a tvrzením informací o nosiče a předmět tokenu.  Deklarace identity v Jwt jsou objekty JSON kódování a serializovat pro přenos.  Vzhledem k tomu, že tokeny Jwt vydaného Azure AD jsou podepsané, ale nejsou šifrovaná, můžete snadno zkontrolovat obsah token JWT pro účely ladění.  Nejsou k dispozici jak to udělat, jako například několik nástrojů [jwt.ms](https://jwt.ms/). Další informace o Jwt, najdete [JWT specifikace](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens
Id_tokens jsou tokenu zabezpečení přihlášení, která bude přijímat vaše aplikace při provádění ověřování pomocí formuláře [OpenID Connect](active-directory-protocols-openid-connect-code.md).  Jsou reprezentovány jako [Jwt](#types-of-tokens)a obsahují deklarace identity, které můžete použít k podepisování uživatele do vaší aplikace.  Deklarace identity můžete použít v požadavku id_token podle svých potřeb – běžně se používají pro zobrazení informací o účtu nebo provedení rozhodnutí o řízení přístupu v aplikaci.

Id_tokens jsou podepsané, ale v tuto chvíli nejsou šifrovaná.  Aplikace obdrží požadavku id_token, musí [ověřit podpis](#validating-tokens) k potvrzení pravosti tokenu a ověření několik deklarací identity v tokenu k prokázání jeho platnosti.  Deklarace identity ověřit aplikací lišit v závislosti na požadavcích scénáře, ale některé [běžné ověření deklarace identity](#validating-tokens) , aplikace musí provést v každý scénář.

Najdete v následující části informace na id_tokens deklarace identity, jakož i požadavku id_token ukázka.  Všimněte si, že deklarace identity v id_tokens nebudou zobrazeny v libovolném pořadí.  Kromě toho nových deklarací identity, můžete dát do id_tokens v libovolném bodě v čase – aplikace by neměl rozdělit zavedeném nových deklarací identity.  Následující seznam obsahuje deklarace identity, které vaše aplikace může spolehlivě interpretovat v době psaní tohoto textu.  Pokud potřeby i další podrobnosti najdete v [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Ukázka požadavku id_token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> Postup kontroly deklarace identity v požadavku id_token ukázka vložením do vyzkoušejte [jwt.ms](https://jwt.ms/).
> 
> 

#### <a name="claims-in-idtokens"></a>Deklarace identity v id_tokens
> [!div class="mx-codeBreakAll"]
| Deklarace identity JWT | Název | Popis |
| --- | --- | --- |
| `appid` |ID aplikace |Určuje aplikace, která je pomocí tokenu pro přístup k prostředkům. Aplikace může fungovat jako sám sebe nebo jménem uživatele. ID aplikace obvykle představuje objekt, aplikace, ale může také představovat objekt zabezpečení služby ve službě Azure AD. <br><br> **Příklad JWT hodnoty**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud` |Cílová skupina |Zamýšlený příjemce tokenu. Aplikace, která přijme token musí ověřte, zda hodnota cílové skupiny je správný a odmítnout všechny tokeny, určený pro jinou cílovou skupinu. <br><br> **Příklad SAML hodnoty**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Příklad JWT hodnoty**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Application Authentication Context Class Reference |Určuje, jak došlo k ověření klienta. Pro veřejné klienta hodnota je 0. Pokud používáte ID klienta a tajný klíč klienta, hodnota je 1. <br><br> **Příklad JWT hodnoty**: <br> `"appidacr": "0"` |
| `acr` |Authentication Context Class Reference |Určuje, jak byla ověřena předmět, na rozdíl od klienta do Application Authentication Context Class Reference deklarace identity. Hodnota 0, označuje, že ověřování koncového uživatele nesplňuje požadavky ISO/IEC 29115. <br><br> **Příklad JWT hodnoty**: <br> `"acr": "0"` |
| Ověřování prostřednictvím rychlých |Zaznamenává datum a čas, kdy došlo k chybě ověřování. <br><br> **Příklad SAML hodnoty**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Metoda ověřování |Určuje, jak byla ověřena předmět tokenu. <br><br> **Příklad SAML hodnoty**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Příklad JWT hodnoty**: `“amr”: ["pwd"]` |
| `given_name` |Jméno |Poskytuje první nebo "zadány" jméno uživatele, nastavené na objekt uživatele Azure AD. <br><br> **Příklad SAML hodnoty**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Příklad JWT hodnoty**: <br> `"given_name": "Frank"` |
| `groups` |Skupiny |Poskytuje ID objektů, které představují členství ve skupinách daného subjektu. Tyto hodnoty jsou jedinečné (viz ID objektu) a bezpečně slouží ke správě přístupu, jako je například vynucování autorizace pro přístup k prostředkům. Skupiny součástí skupiny deklarace identity jsou nakonfigurované na základě jednotlivých aplikací prostřednictvím vlastnosti "groupMembershipClaims" manifestu aplikace. Hodnotu null vyloučí všechny skupiny, hodnotu "Objektu SecurityGroup" bude obsahovat pouze členství ve skupinách zabezpečení Active Directory a hodnotu "Vše" bude zahrnovat skupin zabezpečení a Office 365 distribučním seznamům. <br><br> **Příklad SAML hodnoty**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Příklad JWT hodnoty**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` |Zprostředkovatel identity |Zaznamenává poskytovatele identit, předmět token k ověření. Tato hodnota je shodné s hodnotou vystavitele deklarace identity, pokud není uživatelský účet v jiné klienta než vystavitele. <br><br> **Příklad SAML hodnoty**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Příklad JWT hodnoty**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Ukládá čas, kdy byl token vydán. Se často používá k měření aktuálnosti tokenu. <br><br> **Příklad SAML hodnoty**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Příklad JWT hodnoty**: <br> `"iat": 1390234181` |
| `iss` |Vystavitel |Identifikuje služby tokenů na zabezpečení (STS), která vytvoří a vrátí token. V tokeny, které vrací Azure AD je vystavitele sts.windows.net. Identifikátor GUID v hodnotě vystavitele deklarace je ID klienta adresáře Azure AD. ID klienta je identifikátor neměnné a spolehlivé adresáře. <br><br> **Příklad SAML hodnoty**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Příklad JWT hodnoty**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Příjmení |Jak jsou definovány v objektu uživatele Azure AD, poskytuje poslední jméno, příjmení nebo příjmení uživatele. <br><br> **Příklad SAML hodnoty**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Příklad JWT hodnoty**: <br> `"family_name": "Miller"` |
| `unique_name` |Název |Poskytuje lidského čitelný hodnotu, která identifikuje předmět tokenu. Tato hodnota není musí být jedinečný v rámci klienta a je určen k použití pouze pro účely zobrazení. <br><br> **Příklad SAML hodnoty**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Příklad JWT hodnoty**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |ID objektu |Obsahuje jedinečný identifikátor objektu ve službě Azure AD. Tato hodnota se nedá změnit a nemůže být přiřazeny nebo znovu použít. ID objektu slouží k identifikaci objektu v dotazech ke službě Azure AD. <br><br> **Příklad SAML hodnoty**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Příklad JWT hodnoty**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Role |Představuje všechny role aplikace, které předmět byla udělena přímo i nepřímo prostřednictvím členství ve skupinách a slouží k vynucení řízení přístupu na základě rolí. Aplikační role jsou definovány na základě jednotlivých aplikací pomocí `appRoles` vlastnost manifestu aplikace. `value` Vlastnost každý aplikační role je hodnota, která se zobrazí v deklarace role. <br><br> **Příklad SAML hodnoty**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Příklad JWT hodnoty**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Rozsah |Označuje zosobnění oprávnění udělená aplikaci klienta. Výchozí oprávnění je `user_impersonation`. Vlastníkem prostředku zabezpečené můžete zaregistrovat další hodnoty ve službě Azure AD. <br><br> **Příklad JWT hodnoty**: <br> `"scp": "user_impersonation"` |
| `sub` |Předmět |Identifikuje objekt o tom, které vyhodnotí token informace, například uživatele aplikace. Tato hodnota se nedá změnit a nelze přiřadit nebo opakovaně, takže může sloužit ke kontrole autorizace bezpečně. Protože subjekt se vždy nachází v tokenech problémy Azure AD, je doporučeno použití této hodnoty autorizace systému obecné účely. <br> `SubjectConfirmation` není deklarace identity. Popisuje, jak ověřit předmět tokenu. `Bearer` Označuje, předmět byl potvrzen mít k dispozici tokenu. <br><br> **Příklad SAML hodnoty**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Příklad JWT hodnoty**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |ID tenanta |Neměnné, jednorázovým identifikátor, který identifikuje klienta directory, která vydala token. Tuto hodnotu můžete použít pro přístup k prostředkům directory konkrétního klienta v aplikaci na více klientů. Tuto hodnotu můžete například použít k identifikaci klienta v volání rozhraní Graph API. <br><br> **Příklad SAML hodnoty**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Příklad JWT hodnoty**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |Životnost tokenu |Definuje časový interval, ve kterém je token platný. Služba, která ověří token měli ověřit, že je aktuální datum v rámci dobu životnosti tokenu, else ho měli odmítnout token. Služba může povolit pro až pět minut mimo rozsah dobu životnosti tokenu aby se zohlednily případné rozdíly v času hodin ("čas zkosení") mezi službami Azure AD a služby. <br><br> **Příklad SAML hodnoty**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Příklad JWT hodnoty**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |Hlavní název uživatele (UPN) |Ukládá uživatelské jméno hlavní název uživatele.<br><br> **Příklad JWT hodnoty**: <br> `"upn": frankm@contoso.com` |
| `ver` |Verze |Ukládá číslo verze tokenu. <br><br> **Příklad JWT hodnoty**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Přístupové tokeny
Po úspěšném ověření Azure AD vrátí přístupový token, který umožňuje přístup k chráněným prostředkům. Přístupový token je JSON Web Token (JWT) s kódováním base 64 a její obsah může být prověřovány spuštěním prostřednictvím dekodér.

Pokud vaše aplikace jenom *používá* přístupové tokeny a získat tak přístup k rozhraní API, můžete (a měli) považovat za přístupové tokeny zcela neprůhledný – jsou jenom řetězce, které vaše aplikace může předat k prostředkům v požadavky HTTP.

Pokud budete požadovat token přístupu, Azure AD také vrátí hodnotu některá metadata o tokenu přístupu pro používání vaší aplikace.  Tyto informace zahrnují čas vypršení platnosti přístupového tokenu a oborů, pro které je platný.  To umožňuje aplikaci, kterou chcete provést inteligentní ukládání do mezipaměti přístupové tokeny bez nutnosti analyzovat otevřete přístupový token, sám sebe.

Pokud je vaše aplikace rozhraní API chránit pomocí Azure AD, která očekává přístupové tokeny požadavků HTTP, pak byste měli provést ověření a kontroly, které obdržíte tokenů. Aplikace musí provést ověření tokenu přístupu před jeho použitím pro přístup k prostředkům. Další informace o ověření, najdete v tématu [ověřování tokenů](#validating-tokens).  
Podrobnosti o tom, jak to provést pomocí rozhraní .NET najdete v tématu [chránit webového rozhraní API pomocí nosných tokenů z Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

## <a name="refresh-tokens"></a>Obnovovacích tokenů

Aktualizujte tokeny jsou tokeny zabezpečení, které aplikace můžete použít k získání nové přístupové tokeny v tok OAuth 2.0.  Umožňuje aplikaci, kterou chcete dosáhnout dlouhodobé přístup k prostředkům jménem uživatele bez nutnosti zásahu uživatele.

Tokeny obnovení jsou více prostředků.  To znamená, že obnovovací token obdržel během žádosti o token pro jeden prostředek lze uplatnit přístup tokenů úplně jiných prostředků. Chcete-li to provést, nastavte `resource` parametr v požadavku na cílový prostředek.

Tokeny obnovení jsou zcela neprůhledné do vaší aplikace. Jsou dlohotrvající, ale aplikace nemá zapisovat očekávat, že token obnovení vydrží období.  Tokeny obnovení může být zneplatněné v každém okamžiku v čase pro celou řadu důvodů - najdete v části [tokenu odvolání](#token-revocation) z těchto důvodů.  Jediný způsob, jakým aplikace potřebujete vědět, jestli je platný token obnovení je pokus o uplatněte ho pomocí tokenu požadavku na koncový bod tokenu Azure AD.

Pokud uplatníte obnovovací token pro nový přístupový token, zobrazí se nové obnovovací token v odpovědi tokenu.  Byste měli uložit nově vydané aktualizace tokenu nahrazení, které jste použili v požadavku.  Tím se zaručí, že jsou dál platné pro stejně dlouho obnovovacích tokenů.

## <a name="validating-tokens"></a>Ověřování tokenů

K ověřování požadavku id_token nebo access_token, aplikace by měl ověřit podpis tokenu a deklarace identity. Chcete-li ověřit přístupové tokeny, by mělo aplikace také ověřit vystavitele, cílovou skupinu a podepisování tokenů. Tyto muset ověřit pomocí hodnoty v dokumentu zjišťování OpenID. Například se nachází na klienta nezávislé verzi dokumentu [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). Azure AD middleware obsahuje integrované funkce pro ověření přístupové tokeny, a můžete procházet naše [ukázky](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) vyhledat některé v jazyce podle svého výběru. Další informace o tom, jak explicitně ověřit JWT token, najdete v tématu [ruční Ukázka ověřování tokenů JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).  

Poskytujeme knihovny a ukázky kódu, které ukazují, jak snadno zpracovat ověření tokenu - níže uvedené informace se jednoduše poskytuje pro ty, kteří chtějí porozumět v podkladovém procesu.  Existují také několik třetích stran opensourcové knihovny k dispozici pro ověřování JWT – je alespoň jednu možnost pro téměř každé platformě a jazyk odhlašování došlo. Další informace o Azure AD authentication knihovny a ukázky kódu, najdete v tématu [knihovny ověřování služby Azure AD](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Ověření podpisu

Token JWT obsahuje tři segmenty, které jsou odděleny `.` znak.  První segment se označuje jako **záhlaví**, druhý jako **textu**a třetí jako **podpis**.  Segment podpis slouží k ověření pravosti tokenu, aby důvěryhodné aplikace.

Tokeny vydané službou Azure AD přihlášení pomocí odvětví standardní asymetrických šifrovacích algoritmů, například RSA 256. Hlavička token JWT obsahuje informace o metodě klíč a šifrování používaný k podepisování token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

`alg` Deklarace identity Určuje algoritmus, který se používá k podepisování tokenů, než `x5t` deklarace identity označuje konkrétní veřejný klíč, který se použil k podepsání token.

V libovolném časovém okamžiku v čase Azure AD může přihlásit požadavku id_token pomocí jedné z sadu páry klíčů veřejný soukromý. Azure AD otočí možné sada klíčů v pravidelných intervalech, aby vaše aplikace by měly být zapsány pro zpracování těchto změn klíče automaticky.  Přiměřené frekvence ke kontrole aktualizací do veřejných klíčů používaných službou Azure AD je každých 24 hodin.

Můžete získat podpisového klíče data potřebná k ověření podpisu pomocí dokument metadat OpenID Connect, který se nachází v:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Vyzkoušejte tuto adresu URL v prohlížeči.
> 
> 

Tento dokument metadat je objekt JSON obsahující několik užitečné informací, jako je například umístění různých koncových bodů, které jsou potřebné pro provedení ověřování OpenID Connect.  

Zahrnuje také `jwks_uri`, což dává umístění sady veřejných klíčů, které se používá k podepisování tokenů.  Nacházející se v dokumentu JSON `jwks_uri` obsahuje všechny informací veřejného klíče používaných na konkrétní v daném časovém okamžiku.  Aplikace můžete použít `kid` deklarací identity v hlavičce JWT vybrat, které veřejný klíč v tomto dokumentu jsou využívány k podepsání konkrétní token.  Pak můžete provádět ověřování podpisu pomocí správný veřejný klíč a algoritmus uvedené.

Provádění ověřování podpisu je mimo rámec tohoto dokumentu – mnoho opensourcové knihovny jsou k dispozici pro pomoc, můžete to provést v případě potřeby.

#### <a name="validating-the-claims"></a>Ověřování deklarací identity

Pokud vaše aplikace získá token zabezpečení (požadavku id_token při přihlášení uživatele, nebo token přístupu jako token nosiče v požadavku HTTP) má také provést několik kontrol proti deklarace identity v tokenu.  Patří mezi ně nejsou omezeny na:

* **Cílovou skupinu** deklarace identity – chcete-li ověřit, že token neměla být poskytnut do vaší aplikace.
* **Neplatný před** a **čas vypršení platnosti** deklarace identity – chcete-li ověřit, zda nevypršela platnost tokenu.
* **Vystavitele** deklarace identity – chcete-li ověřit, zda byl token skutečně vydán do vaší aplikace Azure AD.
* **Hodnotu Nonce** – zmírnit útok opětovného přehrání tokenu.
* a další...

Úplný seznam ověření deklarace identity aplikace měli provést pro tokeny typu ID, najdete v části [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). Podrobnosti o očekávaných hodnot pro tyto deklarace identity jsou zahrnuty v předchozím [část požadavku id_token](#id-tokens) části.

## <a name="token-revocation"></a>Token zrušení

Aktualizujte tokeny můžete zrušena nebo odvolat kdykoli z různých důvodů.  Ty lze rozdělit do dvou hlavních kategorií: vypršení časových limitů a odvolání. 
* Token vypršení časových limitů
  * MaxInactiveTime: Pokud token obnovení nebyl použit v čase, závisí na MaxInactiveTime, aktualizovat Token se již nebude platný. 
  * MaxSessionAge: Pokud MaxAgeSessionMultiFactor nebo MaxAgeSessionSingleFactor byla nastavena na jinou hodnotu než výchozí (dokud odvolat), pak opětovné ověření se bude vyžadovat po uplynutí doby nastavení v MaxAgeSession *.  
  * Příklady:
    * Klient má MaxInactiveTime 5 dní a uživatel přešel na dovolenou týden a proto nebylo AAD nikdy nové žádosti o token od uživatele za 7 dnů.  Při příštím uživatel požádá o nový token se najdou jejich aktualizace tokenu je odvolaný a musí znovu zadat své přihlašovací údaje. 
    * K citlivé aplikaci má MaxAgeSessionSingleFactor 1 den.  Pokud se uživatel přihlásí v pondělí a úterý (po 25 hodin uplynuly), bude vyžadovat opakované ověření.  
* Odvolání
  * Změna hesla dobrovolná: Pokud uživatel změní heslo, pravděpodobně znovu provést ověření v některé z jejich vlastních aplikací, v závislosti na způsob, jakým bylo dosaženo tokenu.  Viz poznámky níže výjimky. 
  * Pohybem Změna hesla: Pokud správce určuje, že uživatel, chcete-li změnit své heslo nebo obnoví jeho, pak tokeny uživatele se zruší, pokud jejich bylo dosaženo pomocí hesla.  Viz poznámky níže výjimky. 
  * Porušení zabezpečení: V případě porušení zabezpečení (například místní úložiště hesel nedodržení) správce můžete odvolat všechny aktuálně vystavené tokeny obnovení.  Tato akce vynutí všem uživatelům znovu provést ověření. 

Poznámka: 

Pokud byl jiný heslo metoda ověřování (pro Windows Hello, ověřovací aplikaci, biometrika jako vzhled nebo otisků prstů) k dosažení použity token, změna hesla nebude Vynutit opakované ověření uživatele (ale vynutí jejich ověřovací aplikaci opakované ověření).  Důvodem je, že jejich zvolené ověřování vstupu (a vzhled, například) se nezměnila a proto lze znovu znovu provést ověření.

## <a name="sample-tokens"></a>Ukázka tokeny

V této části zobrazí ukázky tokeny SAML a JWT, které vrátí Azure AD. Tyto ukázky umožňuje sledovat deklarace identity v kontextu.

### <a name="saml-token"></a>SAML Token

Toto je vzorový typické tokenu SAML.

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

### <a name="jwt-token---user-impersonation"></a>Token JWT - vystupovat jako jiný uživatel
Toto je ukázka typické JSON web token (JWT) používány udělení tok autorizačního kódu.
Kromě deklarace identity a token zahrnuje číslo verze v **ver** a **appidacr**, odkaz třídy kontextu ověřování, který určuje, jak došlo k ověření klienta. Pro veřejné klienta hodnota je 0. Pokud bylo použito ID klienta nebo sdílený tajný klíč klienta, hodnota je 1.

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
* V tématu Azure AD Graph [zásad operations](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) a [zásad entity](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), další informace o správě zásad životnost tokenu přes Azure AD Graph API.
* Další informace a ukázky na Správa zásad prostřednictvím rutin prostředí PowerShell, včetně ukázky, najdete v části [konfigurovat životnosti tokenu ve službě Azure AD](../active-directory-configurable-token-lifetimes.md). 
* Přidat [volitelné a vlastní deklarace identity](active-directory-optional-claims.md) na tokeny pro vaši aplikaci. 