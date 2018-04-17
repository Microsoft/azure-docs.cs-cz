---
title: Správa relací jednotného přihlašování pomocí vlastních zásad – Azure AD B2C | Microsoft Docs
description: Naučte se spravovat relace jednotného přihlašování pomocí vlastních zásad v Azure AD B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/20/2017
ms.author: davidmu
ms.openlocfilehash: ca7160d39d5d26ca69345ce636f22afbe44b25db
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Azure AD B2C: Jednotné přihlašování (SSO) relace správy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C umožňuje správci řídit, jak Azure AD B2C komunikuje s uživatelem po má již byl uživatel ověřen. To se provádí přes správu relace jednotného přihlašování. Správce může například řídit, jestli se zobrazí výběr zprostředkovatelů identity, nebo zda je nutné znovu zadat podrobnosti o místní účet. Tento článek popisuje postup konfigurace nastavení jednotného přihlašování pro Azure AD B2C.

## <a name="overview"></a>Přehled

Správa relací jednotné přihlašování má dvě části. První se zabývá interakcí přímo s Azure AD B2C a jiné obchody s interakce uživatele s externími uživateli, jako je Facebook. Azure AD B2C nepřepisuje ani vynechat relace jednotného přihlašování, které může uchovávat externího stranami. Místo směrovat přes Azure AD B2C, abyste se dostali na externí strany je "zapamatovaných", nebudete muset reprompt uživateli vybrat jejich sociálního nebo enterprise zprostředkovatele identity. Ultimate jednotného přihlašování k rozhodnutí zůstává s externí strany.

## <a name="how-does-it-work"></a>Jak to funguje?

Správa relace jednotného přihlašování k využívá stejnou sémantiku jako další technické profil ve vlastní zásady. Když se spustí na krok orchestration, technické profil spojený s krokem je dotazován na `UseTechnicalProfileForSessionManagement` odkaz. Pokud existuje, odkazovaná zprostředkovatele relace jednotného přihlašování k kontroluje zda uživatel je účastníkem relace. Pokud tak zprostředkovatele relace jednotného přihlašování se používá k znovu vytvořit relace. Podobně po dokončení provádění na krok orchestration zprostředkovatele se používá k ukládání informací v relaci, pokud byla zadána poskytovatele relace jednotného přihlašování.

Azure AD B2C je definovaný několik poskytovatelů relace jednotného přihlašování, které lze použít:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Jednotné přihlašování třídy pro správu jsou určeny pomocí `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` element technické profilu.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Jako název stanoví, tohoto zprostředkovatele neprovede žádnou akci. Tento zprostředkovatel slouží pro potlačení chování jednotného přihlašování pro konkrétní technické profil.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Tento zprostředkovatel mohou být použity k uložení deklarace identity v relaci. Tento zprostředkovatel se obvykle odkazuje v profilu technické používaným ke správě místních účtů. 

> [!NOTE]
> Při použití DefaultSSOSessionProvider k uložení deklarací identity v relaci, je potřeba zajistit, že všechny deklarace, které je třeba vrátí aplikaci nebo používá předběžné podmínky v dalších krocích, jsou uložené v relaci nebo rozšířen o čtení z profilu uživatele v adresář. Tím bude zajištěno, že vám dobře slouží ověřování nebudou na deklaracích identity chybí.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

K přidávání deklarací identit v relaci, použijte `<PersistedClaims>` element technické profilu. Když se zprostředkovatel používá k znovu vytvořit relaci, trvalou deklarace identity se nepřidají do kontejneru deklarací identity. `<OutputClaims>` slouží k načítání deklarace identity z relace.

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Tento zprostředkovatel se používá k potlačení obrazovce "Vyberte zprostředkovatele identity". Obvykle se odkazuje v profilu technické nakonfigurovaný pro zprostředkovatele externí identity, jako je Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Tento zprostředkovatel se používá pro správu Azure AD B2C SAML relací mezi aplikací, jakož i externí zprostředkovatele identity SAML.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Existují dvě položky metadat v technické profilu:

| Položka | Výchozí hodnota | Možné hodnoty | Popis
| --- | --- | --- | --- |
| IncludeSessionIndex | true (pravda) | hodnotu true nebo false | K poskytovateli označuje, že by měly být uložené relace index. |
| RegisterServiceProviders | true (pravda) | hodnotu true nebo false | Označuje, že zprostředkovatel měli zaregistrovat všechny poskytovatele služby SAML, které byly vydané kontrolní výrazy. |

Při použití zprostředkovatele pro ukládání relace poskytovatele identity SAML, uvedených položek musí mít oba hodnotu false. Při použití zprostředkovatele pro ukládání relace B2C SAML, uvedených položek musí být true nebo není uveden jako výchozí hodnoty jsou true.

>[!NOTE]
> Odhlášení relace SAML vyžaduje `SessionIndex` a `NameID` k dokončení.

## <a name="next-steps"></a>Další postup

Jsme rádi, názory a návrhy! Pokud máte jakékoli problémy s tímto tématem, můžete zveřejnit na Stack Overflow pomocí značky [, azure ad b2c,](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Pro žádosti o funkce, hlasování u nich naše [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

