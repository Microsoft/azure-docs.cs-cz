---
title: Použití atributů rozšíření schématu Azure AD v deklaracích identity
titleSuffix: Microsoft identity platform
description: Popisuje způsob použití atributů rozšíření schématu adresáře pro posílání uživatelských dat do aplikací v deklaracích tokenů.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 0127c8d796126d1e99b1fa38a9506df477c7eb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755726"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>Použití atributů rozšíření schématu adresáře v deklaracích identity

Atributy rozšíření schématu adresáře poskytují způsob, jak ukládat další data v Azure Active Directory uživatelských objektů a dalších objektech adresáře, jako jsou skupiny, podrobnosti o tenantovi, instanční objekty.  Pro generování deklarací pro aplikace lze použít pouze atributy rozšíření u objektů uživatele. Tento článek popisuje, jak používat atributy rozšíření schématu adresáře pro posílání uživatelských dat do aplikací v deklaracích tokenů.

> [!NOTE]
> Microsoft Graph poskytuje dva další mechanismy rozšíření pro přizpůsobení objektů grafu. Tato rozšíření se nazývají Microsoft Graph otevřít rozšíření a Microsoft Graph schémat. Podrobnosti najdete v [dokumentaci k Microsoft Graph](/graph/extensibility-overview) . Data uložená v Microsoft Graph objekty pomocí těchto možností nejsou k dispozici jako zdroje pro deklarace identity v tokenech.

Atributy rozšíření schématu adresáře jsou vždy spojeny s aplikací v tenantovi a jsou odkazovány v atributu *ApplicationId* aplikace v jejich názvu.

Identifikátor atributu rozšíření schématu adresáře má *Extension_xxxxxxxxx_AttributeName* formuláře.  Kde *XXXXXXXXX* je *ApplicationId* aplikace, pro kterou bylo rozšíření definováno.

## <a name="registering-and-using-directory-schema-extensions"></a>Registrace a použití rozšíření schématu adresáře
Atributy rozšíření schématu adresáře lze registrovat a naplnit jedním ze dvou způsobů:

- Konfigurací služby AD Connect tak, aby se vytvořila a synchronizoval data do nich z místní služby AD. Viz [Azure AD Connect synchronizace adresářů](../hybrid/how-to-connect-sync-feature-directory-extensions.md).
- Použitím Microsoft Graph k registraci, nastavení hodnot a čtení z [rozšíření schématu](/graph/extensibility-overview). K dispozici jsou také [rutiny prostředí PowerShell](/powershell/azure/active-directory/using-extension-attributes-sample) .

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>Generování deklarací identity s daty z atributů rozšíření schématu adresáře vytvořených pomocí služby AD Connect
Atributy rozšíření schématu adresáře vytvořené a synchronizované pomocí služby AD Connect jsou vždycky přidružené k ID aplikace používané službou AD Connect. Je možné je použít jako zdroj pro deklarace identity tím, že je nakonfigurujete jako deklarace identity v konfiguraci **podnikových aplikací** v uživatelském rozhraní portálu pro aplikace SAML zaregistrované v rámci **podnikových aplikací** pomocí Galerie nebo pomocí zásad mapování deklarací pro aplikace zaregistrované prostřednictvím prostředí pro registraci aplikací.  Jakmile se atribut rozšíření adresáře, který se vytvoří prostřednictvím služby AD Connect, nachází v adresáři, zobrazí se v uživatelském rozhraní konfigurace deklarací pro jednotné přihlašování SAML.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>Generování deklarací identity s daty z atributů rozšíření schématu adresáře vytvořených pro aplikaci pomocí grafu nebo PowerShellu
Pokud je atribut rozšíření schématu adresáře registrovaný pro aplikaci pomocí Microsoft Graph nebo PowerShellu (prostřednictvím počátečního instalačního programu nebo kroku zřizování pro instanci), můžete stejnou aplikaci nakonfigurovat v Azure Active Directory, aby přijímala data v daném atributu z uživatelského objektu deklarace identity, když se uživatel přihlásí.  Aplikace se dá nakonfigurovat tak, aby přijímala data v rozšířeních schématu adresáře, která jsou registrovaná na stejné aplikaci pomocí [volitelných deklarací identity](active-directory-optional-claims.md#configuring-directory-extension-optional-claims).  Ty lze nastavit v manifestu aplikace.  To umožňuje víceklientské aplikaci registrovat atributy rozšíření schématu adresáře pro vlastní použití. Když se aplikace zřídí do tenanta, budou se k dispozici přidružená rozšíření schématu adresáře pro uživatele v tomto tenantovi a budou se spotřebovávat.  Jakmile se nakonfiguruje v tenantovi a udělení souhlasu, dá se použít k ukládání a načítání dat pomocí grafu a k mapování na deklarace identity v tokenech, které Microsoft Identity Platform generuje do aplikací.

Atributy rozšíření schématu adresáře lze registrovat a naplnit pro libovolnou aplikaci.

Pokud aplikace potřebuje odeslat deklarace identity s daty z atributu rozšíření registrovaného v jiné aplikaci, je nutné použít [zásadu mapování deklarací](active-directory-claims-mapping.md) k namapování atributu Extension na deklaraci identity.  Běžným vzorem pro správu atributů rozšíření schématu adresáře je vytvoření aplikace konkrétně pro účely registrace všech potřebných rozšíření schématu.  Nemusí to být skutečná aplikace a tato technika znamená, že všechna rozšíření mají v názvu stejné ID aplikace.

Tady je například zásada pro mapování deklarací identity k vygenerování jediné deklarace identity z atributu rozšíření schématu adresáře v tokenu OAuth/OIDC:

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            },
        ]
    }
}
```

Kde *xxxxxxx* je ID aplikace, se kterou bylo rozšíření registrováno.

> [!TIP]
> Při nastavování atributů rozšíření adresáře u objektů je důležité konzistence velkých a malých písmen.  Názvy atributů rozšíření v případě nastavování nerozlišují velká a malá písmena, ale při čtení z adresáře pomocí služby tokenů se rozlišují velká a malá písmena.  Pokud je atribut rozšíření nastaven u objektu uživatele s názvem "LegacyId" a na jiném objektu uživatele s názvem "LegacyId", při mapování atributu na deklaraci identity pomocí názvu "LegacyId" budou data úspěšně načtena a deklarace identity zahrnuté do tokenu pro prvního uživatele, ale ne za sekundu.
>
> Parametr ID ve schématu deklarací identity použitý pro předdefinované atributy adresáře je "ExtensionID" pro atributy rozšíření adresáře.

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [Přidat vlastní nebo další deklarace k tokenům SAML 2,0 a JSON Webtokens (Jwt)](active-directory-optional-claims.md).
- Přečtěte si, jak [přizpůsobit deklarace identity vydávané v tokenech pro konkrétní aplikaci](active-directory-claims-mapping.md).