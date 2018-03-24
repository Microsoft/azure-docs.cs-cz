---
title: Azure Active Directory B2C:Language přizpůsobení v vlastní zásady | Microsoft Docs
description: Naučte se používat obsah vlastních zásad pro víc jazyků pro lokalizaci
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 11/13/2017
ms.author: davidmu
ms.openlocfilehash: 45cfa152615da1447cc695e0dd201e5b8d046cf4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="language-customization-in-custom-policies"></a>Vlastní nastavení jazyka v vlastní zásady

> [!NOTE]
> Tato funkce je ve verzi public preview.
> 

V vlastní zásady funguje jazyk přizpůsobení stejné jako integrovaných zásad.  V tématu integrované [dokumentace](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) jejich chování v tom, jak jazyk je zvolen v závislosti na parametry a nastavení prohlížeče, který popisuje.

## <a name="enable-supported-languages"></a>Povolit podporované jazyky
Pokud nebyl zadán uživatelského rozhraní – národní prostředí a prohlížeče uživatele požádá o jednu z těchto jazyků, podporovaných jazyků se zobrazí uživateli.  

Podporované jazyky jsou definovány v `<BuildingBlocks>` v následujícím formátu:

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

Výchozí jazyk a podporovaných jazyků, chovají stejným způsobem jako ve integrovaných zásad.

## <a name="enable-custom-language-strings"></a>Povolit vlastní jazyk řetězce

Vytváření vlastní jazyk řetězců vyžaduje dva kroky:
1. Upravit `<ContentDefinition>` pro stránku a zadejte ID prostředku pro požadované jazyky
2. Vytvořte `<LocalizedResources>` s odpovídající ID vašeho `<BuildingBlocks>`

Mějte na paměti, který můžete umístit `<ContentDefinition>` a `<BuildingBlock>` v souboru rozšíření nebo předávající soubor zásad, který v závislosti na tom, zda chcete změny být ve všech dědičných zásad, nebo ne.

### <a name="edit-the-contentdefinition-for-the-page"></a>Upravit ContentDefinition pro stránku.

Pro každou stránku lokalizovat, můžete zadat v `<ContentDefinition>` prostředky jazyk má být vyhledán každý kód jazyka.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

V této ukázce francouzština (fr) a vlastní řetězce Angličtina (en) se přidají na stránku Unified registrace nebo přihlášení.  `LocalizedResourcesReferenceId` Pro každou `LocalizedResourcesReference` je stejný jako jejich národního prostředí, ale můžete použít libovolný řetězec jako ID.  Pro každou kombinaci jazyka a stránky, budete muset vytvořit odpovídající `<LocalizedResources>` vidět v následujícím.


### <a name="create-the-localizedresources"></a>Vytvořte LocalizedResources

Vaše přepsání jsou obsaženy v vaše `<BuildingBlocks>` a je `<LocalizedResources>` pro každou stránku a jazyk jste zadali v `<ContentDefinition>` pro jednotlivé stránky.  Každý přepsání je zadán jako `<LocalizedString>` například následující ukázka:

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

Existují čtyři typy řetězec elementy na stránce:

**ClaimsProvider** -štítky pro vaši zprostředkovatelé identity (Facebook, Google, Azure AD atd.) **Typ ClaimType** -štítky pro vaši atributy a jejich odpovídající text nápovědy nebo pole chyby ověření **UxElement** – jiné řetězce elementy na stránce, které jsou ve výchozím nastavení, jako jsou tlačítka, odkazy nebo text **ErrorMessage** -formuláři chybových zpráv ověření

Ujistěte se, že `StringId`s odpovídat pro stránku, že používáte tato přepsání, jinak je blokována ověření zásad na odeslání.  