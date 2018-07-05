---
title: Přizpůsobení jazyka v Azure Active Directory B2C vlastních zásad | Dokumentace Microsoftu
description: Další informace o použití lokalizaci obsahu ve vlastních zásad pro různé jazyky.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6269ac65e5db20521346d5312bcbadd0905c36e2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440560"
---
# <a name="language-customization-in-custom-policies"></a>Přizpůsobení jazyka ve vlastních zásad

> [!NOTE]
> Tato funkce je ve verzi public preview.
> 

Vlastní zásady přizpůsobení jazyka funguje stejně jako integrované zásady.  Zobrazit předdefinované [dokumentaci](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) , který popisuje chování v tom, jak je vybrán jazyk na základě parametry a nastavení prohlížeče.

## <a name="enable-supported-languages"></a>Povolit podporované jazyky
Pokud prohlížeč uživatele požádá o jednu z těchto jazyků uživatelského rozhraní národní prostředí nebyl zadán, jsou uvedeny podporované jazyky pro uživatele.  

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

Výchozí jazyk a podporované jazyky se chovají stejným způsobem jako v předdefinované zásady.

## <a name="enable-custom-language-strings"></a>Povolit vlastní jazyk. řetězce

Vytváření vlastních jazykových řetězců sestává ze dvou kroků:
1. Upravit `<ContentDefinition>` pro stránky můžete zadat ID prostředku pro požadované jazyky
2. Vytvořte `<LocalizedResources>` s odpovídajícím ID ve vaší `<BuildingBlocks>`

Mějte na paměti, který můžete umístit `<ContentDefinition>` a `<BuildingBlock>` v souboru rozšíření nebo předávající soubor zásad v závislosti na tom, zda chcete změny bude v dědičné zásad, nebo ne.

### <a name="edit-the-contentdefinition-for-the-page"></a>Upravit ContentDefinition stránky

Pro každou stránku chcete lokalizovat, můžete zadat v `<ContentDefinition>` jazykové prostředky k vyhledání každý kód jazyka.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

V této ukázce francouzština (fr) a angličtina (en) vlastní řetězce jsou přidány na stránku registrace nebo přihlášení Unified.  `LocalizedResourcesReferenceId` Pro každou `LocalizedResourcesReference` je stejný jako jejich národní prostředí, ale můžete použít libovolný řetězec jako ID.  Pro každou kombinaci jazyka a stránky, je nutné vytvořit odpovídající `<LocalizedResources>` je znázorněno v následujícím.


### <a name="create-the-localizedresources"></a>Vytvořte LocalizedResources

Zvolená přepsání jsou obsaženy ve vaší `<BuildingBlocks>` a je `<LocalizedResources>` pro jednotlivé stránky a jazyka jste zadali v `<ContentDefinition>` pro každou stránku.  Každý přepsání je zadán jako `<LocalizedString>` například v následujícím příkladu:

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

Existují čtyři typy řetězce elementy na stránce:

**ClaimsProvider** – popisky pro svoje zprostředkovatele identit (Facebook, Google, Azure AD atd.) **Typ ClaimType** – popisky pro svoje atributy a jejich odpovídající text nápovědy nebo pole chyby ověření **UxElement** – ostatní řetězce elementy na stránce, které se nacházejí ve výchozím nastavení, jako je například tlačítka, odkazy nebo text **ErrorMessage** -tvoří chybových zpráv ověření

Ujistěte se, že `StringId`s odpovídat, že používáte tato přepsání, jinak zablokovaly zásady ověřování na odeslání stránky.  