---
title: Podporované operace Microsoft Graph
titleSuffix: Azure AD B2C
description: Index operací Microsoft Graph podporovaných pro správu prostředků Azure AD B2C, včetně uživatelů, toků uživatelů, zprostředkovatelů identity, vlastních zásad, klíčů zásad a dalších.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d8d898d3825fa40cbfd13337067c24cf14f9a544
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102013"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph operace k dispozici pro Azure AD B2C

Následující operace Microsoft Graph API se podporují pro správu prostředků Azure AD B2C, včetně uživatelů, poskytovatelů identity, toků uživatelů, uživatelských zásad a klíčů zásad.

Každé propojení v následujících částech cílí na odpovídající stránku v rámci referenčního Microsoft Graph rozhraní API pro tuto operaci.

## <a name="user-management"></a>Správa uživatelů

- [Vypsání uživatelů](https://docs.microsoft.com/graph/api/user-list)
- [Vytvoření uživatele spotřebitele](https://docs.microsoft.com/graph/api/user-post-users)
- [Získat uživatele](https://docs.microsoft.com/graph/api/user-get)
- [Aktualizace uživatele](https://docs.microsoft.com/graph/api/user-update)
- [Odstranění uživatele](https://docs.microsoft.com/graph/api/user-delete)

Další informace o správě Azure AD B2C uživatelských účtů s rozhraním API Microsoft Graph najdete v tématu [správa Azure AD B2C uživatelských účtů pomocí Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="user-phone-number-management"></a>Správa telefonního čísla uživatele

- [Přidat](https://docs.microsoft.com/graph/api/authentication-post-phonemethods)
- [Čtěte](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get)
- [Aktualizace](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update)
- [Odstranit](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete)

Další informace o tom, jak spravovat telefonní číslo přihlášení uživatele pomocí rozhraní Microsoft Graph API, najdete v tématu [metody ověřování B2C](https://docs.microsoft.com/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Zprostředkovatelé identity (tok uživatelů)

Spravujte zprostředkovatele identity, kteří jsou k dispozici vašim uživatelským tokům v Azure AD B2Cm tenantovi.

- [Seznam zprostředkovatelů identity registrovaných v tenantovi Azure AD B2C](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Vytvoření zprostředkovatele identity](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Získat zprostředkovatele identity](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Aktualizovat zprostředkovatele identity](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Odstranění zprostředkovatele identity](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Tok uživatele

Nakonfigurujte předem připravené zásady pro registraci, přihlašování, kombinované registrace a přihlášení, resetování hesla a aktualizaci profilu.

- [Výpis toků uživatelů](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Vytvoření toku uživatele](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Získání toku uživatele](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Odstranění toku uživatele](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Vlastní zásady

Následující operace umožňují spravovat zásady Azure AD B2C vztahů důvěryhodnosti, označované jako [vlastní zásady](custom-policy-overview.md).

- [Vypíše všechny zásady architektury trustu nakonfigurované v tenantovi.](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Vytvořit zásadu architektury vztahu důvěryhodnosti](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Načte vlastnosti existující zásady pro pravidlo důvěryhodnosti.](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Aktualizovat nebo vytvořit zásadu architektury trustu](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Odstraní existující zásadu pro pravidlo důvěryhodnosti.](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Klíče zásad

Rozhraní identity Experience Framework ukládá tajné kódy, na které se odkazuje ve vlastních zásadách, aby bylo možné navázat vztah důvěryhodnosti mezi komponentami. Tyto tajné klíče můžou být symetrické nebo asymetrické klíče a hodnoty. V Azure Portal se tyto entity zobrazují jako **klíče zásad**.

Prostředek nejvyšší úrovně pro klíče zásad v rozhraní Microsoft Graph API je [sada klíčů pro důvěryhodné rozhraní](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Každá **sada klíčů** obsahuje alespoň jeden **klíč**. Pokud chcete vytvořit klíč, nejdřív vytvořte prázdnou sadu klíčů a pak vygenerujte klíč v této sadu klíčů. Můžete vytvořit ruční tajný klíč, nahrát certifikát nebo PKCS12 klíč. Klíčem může být vygenerovaný tajný klíč, řetězec, který definujete (například tajný klíč aplikace Facebook), nebo certifikát, který nahrajete. Pokud má sada klíčů více klíčů, je aktivní pouze jeden z klíčů.

### <a name="trust-framework-policy-keyset"></a>Sada klíčů zásad pro Trust Framework

- [Seznam sad pro nastavení sady Trust Framework](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Vytvoření sad pro přístup k architektuře](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Získání sady klíčů](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Aktualizace sad s architekturou pro vztah důvěryhodnosti](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Odstranění sad s architekturou pro vztah důvěryhodnosti](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Klíč zásad pro Trust Framework

- [Získat aktuálně aktivní klíč v rámci sady klíčů](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Generování klíče v sady klíčů](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Nahrání tajného kódu založeného na řetězci](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Nahrání certifikátu X. 509](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Odeslat certifikát formátu PKCS12](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplikace

- [Výpis aplikací](https://docs.microsoft.com/graph/api/application-list)
- [Vytvoření aplikace](https://docs.microsoft.com/graph/api/resources/application)
- [Aktualizovat aplikaci](https://docs.microsoft.com/graph/api/application-update)
- [Vytvořit servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Vytvořit oauth2Permission grant](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Odstranit aplikaci](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Vlastnosti rozšíření aplikace

- [Seznam vlastností rozšíření](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C poskytuje adresář, který může obsahovat 100 vlastních atributů na uživatele. V případě toků uživatelů se tyto vlastnosti rozšíření [spravují pomocí Azure Portal](custom-policy-custom-attributes.md). Pro vlastní zásady Azure AD B2C vytvoří pro vás vlastnost, při prvním zapíše hodnotu do vlastnosti Extension.

## <a name="audit-logs"></a>Protokoly auditu

- [Vypsat protokoly auditu](https://docs.microsoft.com/graph/api/directoryaudit-list)

Další informace o přístupu k protokolům auditu Azure AD B2C pomocí rozhraní API Microsoft Graph najdete v tématu [přístup k protokolům auditu Azure AD B2C](view-audit-logs.md).
