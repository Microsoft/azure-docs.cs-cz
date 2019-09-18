---
title: Správa relací jednotného přihlašování pomocí vlastních zásad v Azure Active Directory B2C | Microsoft Docs
description: Naučte se spravovat relace jednotného přihlašování pomocí vlastních zásad v Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ae30b316133b7479b66a69a3467497a7151dbc8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065379"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Správa relací jednotného přihlašování v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Správa relací jednotného přihlašování (SSO) v Azure Active Directory B2C (Azure AD B2C) umožňuje správci řídit interakci s uživatelem poté, co uživatel již ověřený. Správce může třeba určit, jestli se má zobrazit výběr zprostředkovatelů identity, nebo jestli se mají zadat podrobnosti o místním účtu znovu. Tento článek popisuje, jak nakonfigurovat nastavení jednotného přihlašování pro Azure AD B2C.

Správa relace jednotného přihlašování má dvě části. První se zabývá interakcemi uživatele přímo s Azure AD B2C a dalšími obchody s interakcemi uživatele s externími stranami, jako je Facebook. Azure AD B2C nepřepisuje ani neobejde relace jednotného přihlašování, které mohou být uloženy externími stranami. Místo toho, aby se trasa mezi Azure AD B2C dostala na externí stranu, je "rememberd", takže není nutné znovu vyzvat uživatele k výběru poskytovatele identity pro sociální nebo podnikové prostředí. Konečné rozhodnutí SSO zůstává u externí strany.

Správa relací jednotného přihlašování používá stejnou sémantiku jako jakýkoliv jiný technický profil ve vlastních zásadách. Při spuštění kroku orchestrace se pro `UseTechnicalProfileForSessionManagement` referenci zobrazí dotaz na technický profil přidružený k tomuto kroku. Pokud existuje, pak se v odkazovaném poskytovateli relace jednotného přihlašování kontroluje, jestli je uživatel účastníkem relace. V takovém případě se k naplnění relace použije poskytovatel relace jednotného přihlašování. Podobně platí, že po dokončení kroku orchestrace se poskytovatel používá k ukládání informací v relaci, pokud byl zadán zprostředkovatel relace jednotného přihlašování.

Azure AD B2C definovali počet zprostředkovatelů relací jednotného přihlašování, které se dají použít:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Třídy správy jednotného přihlašování jsou určené `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` pomocí elementu technického profilu.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Když název určíte, tento zprostředkovatel neprovede žádnou akci. Tento zprostředkovatel se dá použít pro potlačení chování jednotného přihlašování pro konkrétní technický profil.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Tento zprostředkovatel se dá použít k ukládání deklarací identity v relaci. Na tohoto poskytovatele se obvykle odkazuje v technickém profilu, který se používá pro správu místních účtů. Při použití DefaultSSOSessionProvider k ukládání deklarací identity v relaci je potřeba zajistit, aby všechny deklarace identity, které je potřeba vrátit do aplikace nebo které používají podmínky v následujících krocích, jsou uložené v relaci nebo rozšířeny čtením z profilu uživatelů v nástroji. službě. Tím se zajistí, že v případě chybějících deklarací selže vaše cesta pro ověřování.

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

Pokud chcete v relaci přidat deklarace identity, použijte `<PersistedClaims>` element technického profilu. Pokud se k naplnění relace použije zprostředkovatel, budou trvalé deklarace identity přidány do kontejneru deklarací identity. `<OutputClaims>`slouží k načítání deklarací identity z relace.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Pomocí tohoto poskytovatele se potlačí obrazovka "zvolit zprostředkovatele identity". Obvykle se na něj odkazuje v technickém profilu nakonfigurovaném pro externího zprostředkovatele identity, jako je Facebook.

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Tento zprostředkovatel se používá ke správě Azure AD B2C relací SAML mezi aplikacemi a externími zprostředkovateli identity SAML.

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

V technickém profilu existují dvě položky metadat:

| Položka | Výchozí hodnota | Možné hodnoty | Popis
| --- | --- | --- | --- |
| IncludeSessionIndex | true | true nebo false | Indikuje poskytovateli, že by měl být uložen index relace. |
| RegisterServiceProviders | true | true nebo false | Indikuje, že by měl poskytovatel zaregistrovat všechny poskytovatele služeb SAML, u kterých bylo vydaný kontrolní výraz. |

Při použití zprostředkovatele pro ukládání relace zprostředkovatele identity SAML by měly být výše uvedené položky nepravdivé. Při použití poskytovatele pro uložení relace SAML B2C by výše uvedené položky měly být pravda nebo vynechány, protože výchozí hodnoty jsou true. Odhlášení relace SAML vyžaduje `SessionIndex` a `NameID` k dokončení.

