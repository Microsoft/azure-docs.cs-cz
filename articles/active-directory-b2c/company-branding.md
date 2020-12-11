---
title: Přidání brandingu na přihlašovací stránku vaší organizace
titleSuffix: Azure AD B2C
description: Naučte se, jak přidat branding vaší organizace na stránky Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111304"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>Přidání brandingu na stránky Azure Active Directory B2C vaší organizace

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Můžete přizpůsobit Azure AD B2C stránky s logem banneru, obrázkem pozadí a barvou pozadí pomocí Azure Active Directory [Branding společnosti](../active-directory/fundamentals/customize-branding.md). Branding společnosti zahrnuje registraci, přihlašování, úpravy profilu a resetování hesla. 

> [!TIP]
> Chcete-li přizpůsobit další aspekty stránek toku uživatele nad rámec loga, obrázku pozadí nebo barvy pozadí, přečtěte si téma [přizpůsobení uživatelského rozhraní šablonou HTML](customize-ui-with-html.md).

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


Chcete-li přizpůsobit stránky toku uživatele, je třeba nejprve nakonfigurovat Branding společnosti v Azure Active Directory a pak ji povolit v rozložení stránky vašich uživatelských toků v Azure AD B2C.

## <a name="configure-company-branding"></a>Konfigurace brandingu společnosti

Začněte tím, že nastavíte logo banner, obrázek pozadí a barvu pozadí v rámci **brandingu společnosti**.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **Branding společnosti**.
1. Postupujte podle pokynů v části [Přidání brandingu na přihlašovací stránku Azure Active Directory vaší organizace](../active-directory/fundamentals/customize-branding.md).

Při konfiguraci brandingu společnosti v Azure AD B2C mějte na paměti tyto věci:

* Branding společnosti v Azure AD B2C je aktuálně omezen na **obrázek na pozadí**, **logo banneru** a přizpůsobení **barvy pozadí** . Ostatní vlastnosti v podokně Branding společnosti, například v části **Rozšířená nastavení**, nejsou *podporovány*.
* Na stránkách toku uživatele se zobrazuje barva pozadí před načtením obrázku na pozadí. Doporučujeme, abyste si zvolili barvu pozadí, která se přesně shoduje s barvami v obrázku na pozadí pro účely hladkého načítání.
* Logo banner se zobrazí v ověřovacích e-mailech odesílaných uživatelům při zahájení uživatelského toku registrace.

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>Povolit brandingování na stránkách toku uživatele

Po nakonfigurování firemního brandingu ho Povolte ve svých uživatelských tocích.

1. V levé nabídce Azure Portal vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **toky uživatelů (zásady)**.
1. Vyberte tok uživatele, pro který chcete povolit Branding společnosti. Branding společnosti není **podporován** pro standardní *přihlašování* a standardní *profily pro úpravu* typů toku uživatele.
1. V části **přizpůsobit** vyberte **rozložení stránky** a potom vyberte rozložení, které chcete označit jako značku. Vyberte například možnost **sjednocení registrace nebo přihlášení na přihlašovací stránce**.
1. Pro **verzi rozložení stránky (Preview)** vyberte verze **1.2.0** nebo vyšší.
1. Vyberte **Uložit**.

Chcete-li označit všechny stránky v toku uživatele, nastavte pro každé rozložení stránky v toku uživatele verzi rozložení stránky.

![Výběr rozložení stránky v Azure AD B2C Azure Portal](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>Vybrat rozložení stránky

Pokud chcete povolit Branding společnosti, musíte [definovat verzi rozložení stránky](contentdefinitions.md#migrating-to-page-layout) s `contract` verzí stránky pro *všechny* definice obsahu ve vlastních zásadách. Formát hodnoty musí obsahovat slovo `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**kontrakt**:p věk-Name: Version_. Chcete-li určit rozložení stránky ve vlastních zásadách, které používají starou hodnotu **DataUri** .

Naučte se [migrovat na rozložení stránky](contentdefinitions.md#migrating-to-page-layout) pomocí verze stránky.

Následující příklad ukazuje identifikátory definice obsahu a odpovídající **DataUri** s kontraktem stránky: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

Následující příklad ukazuje vlastní logo banneru a obrázek na pozadí na stránce flow ( *zaregistrovat) a přihlásit* se k uživatelskému toku, který používá šablonu oceánu (oceán Blue):

![Přihlašovací stránka s brandingem, která je obsluhována Azure AD B2C](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>Použití prostředků brandingu společnosti ve vlastním HTML

Chcete-li použít prostředky značky vaší společnosti ve [vlastním HTML](customize-ui-with-html.md), přidejte následující značky mimo `<div id="api">` značku:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

Zdroj obrázku je nahrazen symbolem obrázku pozadí a logem banner.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak můžete přizpůsobit uživatelské rozhraní svých aplikací, najdete v tématu [přizpůsobení uživatelského rozhraní aplikace v Azure Active Directory B2C](customize-ui-with-html.md).