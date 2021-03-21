---
title: Přizpůsobení uživatelského rozhraní
titleSuffix: Azure AD B2C
description: Naučte se, jak přizpůsobit uživatelské rozhraní pro aplikace, které používají Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055733"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Přizpůsobení uživatelského rozhraní v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Branding a přizpůsobení uživatelského rozhraní, které Azure Active Directory B2C (Azure AD B2C) zobrazováno vašim zákazníkům, pomáhá zajistit bezproblémové uživatelské prostředí ve vaší aplikaci. Mezi tyto možnosti patří registrace, přihlašování, úpravy profilu a resetování hesla. V tomto článku upravíte Azure AD B2C stránky pomocí šablony stránky a Branding společnosti. 

> [!TIP]
> Chcete-li přizpůsobit další aspekty stránek toku uživatele za šablonu stránky, logo banner, obrázek pozadí nebo barvu pozadí, přečtěte si téma Postup [přizpůsobení uživatelského rozhraní šablonou HTML](customize-ui-with-html.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>Přehled

Azure AD B2C poskytují několik integrovaných šablon, ze kterých si můžete vybrat a poskytnout tak profesionální vzhled na stránkách uživatele. Tyto šablony stránky můžou také sloužit jako výchozí bod pro vlastní úpravy pomocí funkce [Branding společnosti](#company-branding) .

### <a name="ocean-blue"></a>Mořská modrá

Příklad šablony oceánu Blue vygenerované na přihlašovací stránce pro registraci:

![Snímek s modrou šablonou pro oceán](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>Břidlicová šedá

Příklad šablony s barvou břidlice vykreslené na přihlašovací stránce pro registraci:

![Obrázek stínové kopie břidlicové šablony](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>Klasický

Příklad klasické šablony vykreslené na přihlašovací stránce pro registraci:

![Snímek klasické šablony](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>Branding společnosti

Můžete přizpůsobit Azure AD B2C stránky s logem banneru, obrázkem pozadí a barvou pozadí pomocí Azure Active Directory [Branding společnosti](../active-directory/fundamentals/customize-branding.md). Branding společnosti zahrnuje registraci, přihlašování, úpravy profilu a resetování hesla. 

Následující příklad ukazuje přihlašovací stránku pro *registraci a přihlašování* s vlastním logem, obrázkem na pozadí s použitím oceánu modrých šablon:

![Přihlašovací stránka s brandingem, která je obsluhována Azure AD B2C](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>Vybrat šablonu stránky

::: zone pivot="b2c-user-flow"

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Vyberte uživatelský tok, který chcete upravit.
1. V části **přizpůsobit** v nabídce vlevo vyberte **rozložení stránky** a potom vyberte **šablonu**.
    ![Rozevírací seznam pro výběr šablony na stránce toku uživatele Azure Portal](./media/customize-ui/select-page-template.png)

Když zvolíte šablonu, vybraná šablona se použije na všechny stránky v toku uživatele. Identifikátor URI pro každou stránku je viditelný v poli **URI vlastní stránky** .

::: zone-end

::: zone pivot="b2c-custom-policy"

Chcete-li vybrat šablonu stránky, nastavte `LoadUri` prvek [definice obsahu](contentdefinitions.md). Následující příklad ukazuje identifikátory definice obsahu a odpovídající `LoadUri` . 

Mořská modrá:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Břidlicová šedá:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Standardním 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>Konfigurace brandingu společnosti

Chcete-li přizpůsobit stránky toku uživatele, nejprve v Azure Active Directory nakonfigurujete Branding společnosti, a pak ji ve svých uživatelských tocích povolíte Azure AD B2C.

Začněte tím, že nastavíte logo banner, obrázek pozadí a barvu pozadí v rámci **brandingu společnosti**.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **Branding společnosti**.
1. Postupujte podle pokynů v části [Přidání brandingu na přihlašovací stránku Azure Active Directory vaší organizace](../active-directory/fundamentals/customize-branding.md).

Při konfiguraci brandingu společnosti v Azure AD B2C mějte na paměti tyto věci:

* Branding společnosti v Azure AD B2C je aktuálně omezen na **obrázek na pozadí**, **logo banneru** a přizpůsobení **barvy pozadí** . Ostatní vlastnosti v podokně Branding společnosti, například **Rozšířená nastavení**, nejsou *podporovány*.
* Na stránkách toku uživatele se zobrazuje barva pozadí před načtením obrázku na pozadí. Doporučujeme, abyste si zvolili barvu pozadí, která se přesně shoduje s barvami v obrázku na pozadí pro účely hladkého načítání.
* Logo banner se zobrazí v ověřovacích e-mailech odesílaných uživatelům při zahájení uživatelského toku registrace.



## <a name="enable-company-branding-in-user-flow-pages"></a>Povolit Branding společnosti na stránkách toku uživatele

::: zone pivot="b2c-user-flow"

Po nakonfigurování firemního brandingu ho Povolte ve svých uživatelských tocích.

1. V levé nabídce Azure Portal vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **toky uživatelů (zásady)**.
1. Vyberte tok uživatele, pro který chcete povolit Branding společnosti. Branding společnosti není **podporován** pro standardní *přihlašování* a standardní *profily pro úpravu* typů toku uživatele.
1. V části **přizpůsobit** vyberte **rozložení stránky** a potom vyberte stránku, na kterou chcete vytvořit značku. Vyberte například možnost **sjednocení registrace nebo přihlášení na přihlašovací stránce**.
1. Pro **verzi rozložení stránky (Preview)** vyberte verze **1.2.0** nebo vyšší.
1. Vyberte **Uložit**.

Chcete-li označit všechny stránky v toku uživatele, nastavte pro každé rozložení stránky v toku uživatele verzi rozložení stránky.

![Výběr rozložení stránky v Azure AD B2C Azure Portal](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Jakmile nakonfigurujete Branding společnosti, povolte ji ve vlastních zásadách. Nakonfigurujte [verzi rozložení stránky](contentdefinitions.md#migrating-to-page-layout) o verzi stránky `contract` pro *všechny* definice obsahu ve vlastních zásadách. Formát hodnoty musí obsahovat slovo `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**kontrakt**:p věk-Name: Version_. Chcete-li určit rozložení stránky ve vlastních zásadách, které používají starou hodnotu **DataUri** . Další informace najdete v tématu Postup [migrace na rozložení stránky](contentdefinitions.md#migrating-to-page-layout) pomocí verze stránky.

Následující příklad ukazuje definice obsahu s odpovídajícím kontraktem stránky a *modrou* šablonou stránky pro oceán: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak můžete přizpůsobit uživatelské rozhraní svých aplikací, najdete v tématu [přizpůsobení uživatelského rozhraní aplikace v Azure Active Directory B2C](customize-ui-with-html.md).
