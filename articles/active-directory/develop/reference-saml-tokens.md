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
ms.openlocfilehash: 4ad03d5872d041017d0be9e4ce33eb18dc3d762a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238417"
---
# <a name="azure-ad-saml-token-reference"></a>Odkaz tokenu Azure AD SAML

Azure Active Directory (Azure AD) vysílá několika typy tokenů zabezpečení ve zpracování každý tok ověřování. Tento dokument popisuje formátu, vlastnosti zabezpečení a obsah každého typu token.

## <a name="claims-in-saml-tokens"></a>Deklarace identity v tokenech SAML

> [!div class="mx-codeBreakAll"]
| Název | Deklarace identity odpovídá tokenů JWT | Popis | Příklad: |
| --- | --- | --- | ------------|
|Cílová skupina | `aud` |Zamýšlený příjemce tokenu. Aplikace, která přijímá token musí zkontrolujte, že hodnoty cílové skupiny je správný a odmítnout všechny tokeny, určené pro jinou cílovou skupinu. | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
| Okamžik ověření | |Zaznamenává data a času, kdy došlo k ověřování. | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | 
|Metoda ověřování | `amr` |Určuje, jak byl předmět token ověření. | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
|Jméno | `given_name` |Nabízí první nebo "zadány" jméno uživatele, jako je nastaven na objekt uživatele Azure AD. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
|Skupiny | `groups` |Poskytuje ID objektů, které představují členství ve skupinách daného subjektu. Tyto hodnoty jsou jedinečné (viz ID objektu) a lze jej bezpečně používat při správě přístupu, jako je například vynucování autorizaci pro přístup k prostředku. Skupiny součástí skupiny deklarace identity jsou nakonfigurované na základě jednotlivých aplikací prostřednictvím vlastnosti "groupMembershipClaims" manifestu aplikace. Hodnotu null, vyloučí všechny skupiny, hodnota "Skupiny SecurityGroup" bude obsahovat pouze členství ve skupinách zabezpečení Active Directory a hodnota "Vše" bude obsahovat skupiny zabezpečení a Office 365 distribuční seznamy. <br><br> **Poznámky k**: <br> Pokud počet skupin, které je uživatel v překročí limit (150 pro SAML, 200 pro token JWT) pak deklaraci identity při překročení limitu se přidají zdroje deklarací identity směřující na koncový bod grafu obsahující seznam skupin pro daného uživatele. (v. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
| Při překročení limitu indikátor skupiny | `groups:src1` | Pro žádosti o tokeny, které nejsou omezena délka (naleznete v tématu `hasgroups` výše), ale stále příliš velký pro daný token, odkaz na seznam úplné skupiny pro uživatele, budou zahrnuty. Pro SAML, tím se přidá jako novou deklaraci místo `groups` deklarací identity. | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
|Zprostředkovatel identity | `idp` |Zaznamenává zprostředkovatele identity, který ověřil subjekt tokenu. Tato hodnota se shoduje s hodnotu deklarace identity vystavitele, pokud je uživatelský účet v jiném tenantovi než vystavitele. | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
|IssuedAt | `iat` |Ukládá čas, kdy byl token vydán. Často se používá k měření token aktuálnosti. | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
|Vystavitel | `iss` |Identifikuje službu tokenů zabezpečení (STS), který vytvoří a vrátí token. V tokenech, které vrací Azure AD že vystavitel je sts.windows.net. Identifikátor GUID v hodnotě deklarace identity vystavitele je ID tenanta adresáře Azure AD. ID tenanta je identifikátor neměnné a spolehlivé adresáře. | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
|Příjmení | `family_name` |Jak je definováno v objektu uživatele Azure AD poskytuje poslední jméno, příjmení nebo příjmení uživatele. | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
|Název | `unique_name` |Poskytuje lidsky čitelnou hodnotu, která identifikuje subjekt tokenu. Tato hodnota nemusí být jedinečný v rámci tenanta a je určený pouze pro účely zobrazení. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
|ID objektu | `oid` |Obsahuje jedinečný identifikátor objektu ve službě Azure AD. Tato hodnota je neměnná a nelze přiřadit nebo znovu použít. ID objektu použijte k identifikaci objektu v dotazech ke službě Azure AD. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
|Role | `roles` |Představuje všechny aplikační role, které předmět bylo uděleno přímo i nepřímo prostřednictvím členství ve skupinách a je možné vynutit řízení přístupu na základě rolí. Aplikační role jsou definované na základě jednotlivých aplikací prostřednictvím `appRoles` vlastnosti manifestu aplikace. `value` Vlastnost jednotlivé aplikační role je hodnota, která se zobrazí v deklarace identity rolí. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
|Subjekt | `sub` |Určuje objekt o tom, které token vyhodnocuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nejde ji přiřadit nebo opakovaně používat, proto ji lze použít k provedení kontroly autorizace bezpečně. Protože předmět je vždy přítomna v tokenech problémy s Azure AD, je doporučeno použití této hodnoty v rámci obecné účely autorizace systému. <br> `SubjectConfirmation` není deklarace identity. Popisuje, jak ověřit předmětem token. `Bearer` Označuje, předmět je potvrzen jejich vlastnictví tokenu. | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
|ID tenanta | `tid` |Neměnné a jednorázovým identifikátor, který identifikuje tenantu Active directory, která token vydala. Tuto hodnotu můžete použít pro přístup k prostředkům specifickým pro tenanta adresáře v aplikaci s více tenanty. Tuto hodnotu můžete například použít k identifikaci tenanta služby ve volání rozhraní Graph API. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
|Živostnost tokenu | `nbf`, `exp` |Definuje časový interval, ve kterém je token platný. Služba, která ověří token, který by měl ověřte, že aktuální datum v rámci dobu životnosti tokenu, jiný ji by měl odmítnout token. Služba může umožnit až pět minut mimo rozsah životnost tokenu aby se zohlednily případné rozdíly v čase ("čas Nerovnoměrná distribuce") mezi službami Azure AD a služby. | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>Ukázka tokenu SAML

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

## <a name="related-content"></a>Související obsah
* V tématu Azure AD Graph [operace se zásadami](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) a [entita zásad](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), další informace o správě životnosti tokenu zásad prostřednictvím rozhraní Azure AD Graph API.
* Další informace a ukázky pro správu zásad prostřednictvím rutin Powershellu, včetně ukázek, naleznete v tématu [konfigurovatelné životností tokenů ve službě Azure AD](active-directory-configurable-token-lifetimes.md). 
* Přidat [vlastní a volitelných deklarací](active-directory-optional-claims.md) tokeny pro vaši aplikaci.
* Použití [jednotné přihlašování (SSO) pomocí SAML](single-sign-on-saml-protocol.md).
* Použití [Azure jednotné přihlašování SAML protokol](single-sign-out-saml-protocol.md)
