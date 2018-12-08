---
title: Vyberte stránku smlouvy v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o tom, jak vybrat stránku smlouvy v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e2e6da5df434ffd217b0521d4a13cd8ec713d5a1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109614"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Vyberte stránku smlouvu v Azure Active Directory B2C pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Kontrakt stránky můžete vybrat v Azure Active Directory (Azure AD) B2C tím, že nakonfigurujete v [vlastní zásady](active-directory-b2c-overview-custom.md). Kontrakt stránky je přidružení prvky, které poskytuje Azure AD B2C a obsah, který zadáte. Pokud máte v úmyslu použít [Javascript](javascript-samples.md), budete muset definovat verze kontraktu stránky pro všechny vaše definice obsahu ve vlastních zásadách.

## <a name="replace-datauri-values"></a>Nahraďte parametr hodnoty

Ve vaší vlastní zásady, můžete mít [ContentDefinitions](contentdefinitions.md) , které definují HTML šablony použité v cestě uživatele. **ContentDefinition** obsahuje **parametr** , který odkazuje na prvky stránky poskytuje Azure AD B2C. **LoadUri** je relativní cesta k obsahu HTML a CSS, který zadáte.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Pokud chcete vybrat stránku smlouvy, můžete změnit **parametr** hodnoty v vaše [ContentDefinitions](contentdefinitions.md) v zásady. Přepnutím ze starého **parametr** hodnoty na nové hodnoty, vybíráte nezměnitelný balíček. Výhodou používání tohoto balíčku je, že budete vědět, nelze změnit a způsobit neočekávané chování na stránce. 

Nastavení stránky smlouvy, použijte následující tabulku k vyhledání **parametr** hodnoty. 

| Původní hodnota parametr | Nová hodnota pro parametr |
| ----------------- | ----------------- |
| název urn: com:microsoft:aad:b2c:elements:idpselection:1.0.0 | název urn: com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| název urn: com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | název urn: com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| název urn: com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | název urn: com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| název urn: com:microsoft:aad:b2c:elements:multifactor:1.0.0 | název urn: com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| název urn: com:microsoft:aad:b2c:elements:multifactor:1.1.0 | název urn: com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| název urn: com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | název urn: com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| název urn: com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | název urn: com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| název urn: com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | název urn: com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| název urn: com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | název urn: com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| název urn: com:microsoft:aad:b2c:elements:globalexception:1.0.0 | název urn: com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| název urn: com:microsoft:aad:b2c:elements:globalexception:1.1.0 | název urn: com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>Další postup

Další informace o tom, jak můžete přizpůsobit uživatelského rozhraní aplikací v [přizpůsobit uživatelské rozhraní vaší aplikace pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).



