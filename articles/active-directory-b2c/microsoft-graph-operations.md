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
ms.custom: fasttrack-edit
ms.openlocfilehash: fed1e31380381b864530b3fa0b9e8c0886737d04
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033604"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph operace k dispozici pro Azure AD B2C

Následující operace Microsoft Graph API se podporují pro správu prostředků Azure AD B2C, včetně uživatelů, poskytovatelů identity, toků uživatelů, uživatelských zásad a klíčů zásad.

Každé propojení v následujících částech cílí na odpovídající stránku v rámci referenčního Microsoft Graph rozhraní API pro tuto operaci.

## <a name="user-management"></a>Správa uživatelů

- [Vypsání uživatelů](/graph/api/user-list)
- [Vytvoření uživatele spotřebitele](/graph/api/user-post-users)
- [Získat uživatele](/graph/api/user-get)
- [Aktualizace uživatele](/graph/api/user-update)
- [Odstranění uživatele](/graph/api/user-delete)

Další informace o správě Azure AD B2C uživatelských účtů s rozhraním API Microsoft Graph najdete v tématu [správa Azure AD B2C uživatelských účtů pomocí Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="user-phone-number-management"></a>Správa telefonního čísla uživatele

- [Přidat](/graph/api/authentication-post-phonemethods)
- [Čtěte](/graph/api/b2cauthenticationmethodspolicy-get)
- [Aktualizace](/graph/api/b2cauthenticationmethodspolicy-update)
- [Odstranit](/graph/api/phoneauthenticationmethod-delete)

Další informace o tom, jak spravovat telefonní číslo přihlášení uživatele pomocí rozhraní Microsoft Graph API, najdete v tématu [metody ověřování B2C](/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Zprostředkovatelé identity (tok uživatelů)

Spravujte zprostředkovatele identity, kteří jsou k dispozici vašim uživatelským tokům v Azure AD B2Cm tenantovi.

- [Seznam zprostředkovatelů identity registrovaných v tenantovi Azure AD B2C](/graph/api/identityprovider-list)
- [Vytvoření zprostředkovatele identity](/graph/api/identityprovider-post-identityproviders)
- [Získat zprostředkovatele identity](/graph/api/identityprovider-get)
- [Aktualizovat zprostředkovatele identity](/graph/api/identityprovider-update)
- [Odstranění zprostředkovatele identity](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Tok uživatele

Nakonfigurujte předem připravené zásady pro registraci, přihlašování, kombinované registrace a přihlášení, resetování hesla a aktualizaci profilu.

- [Výpis toků uživatelů](/graph/api/identitycontainer-list-b2cuserflows)
- [Vytvoření toku uživatele](/graph/api/identitycontainer-post-b2cuserflows)
- [Získání toku uživatele](/graph/api/b2cidentityuserflow-get)
- [Odstranění toku uživatele](/graph/api/b2cidentityuserflow-delete)

## <a name="custom-policies"></a>Vlastní zásady

Následující operace umožňují spravovat zásady Azure AD B2C vztahů důvěryhodnosti, označované jako [vlastní zásady](custom-policy-overview.md).

- [Vypíše všechny zásady architektury trustu nakonfigurované v tenantovi.](/graph/api/trustframework-list-trustframeworkpolicies)
- [Vytvořit zásadu architektury vztahu důvěryhodnosti](/graph/api/trustframework-post-trustframeworkpolicy)
- [Načte vlastnosti existující zásady pro pravidlo důvěryhodnosti.](/graph/api/trustframeworkpolicy-get)
- [Aktualizovat nebo vytvořit zásadu architektury trustu](/graph/api/trustframework-put-trustframeworkpolicy)
- [Odstraní existující zásadu pro pravidlo důvěryhodnosti.](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Klíče zásad

Rozhraní identity Experience Framework ukládá tajné kódy, na které se odkazuje ve vlastních zásadách, aby bylo možné navázat vztah důvěryhodnosti mezi komponentami. Tyto tajné klíče můžou být symetrické nebo asymetrické klíče a hodnoty. V Azure Portal se tyto entity zobrazují jako **klíče zásad**.

Prostředek nejvyšší úrovně pro klíče zásad v rozhraní Microsoft Graph API je [sada klíčů pro důvěryhodné rozhraní](/graph/api/resources/trustframeworkkeyset). Každá **sada klíčů** obsahuje alespoň jeden **klíč**. Pokud chcete vytvořit klíč, nejdřív vytvořte prázdnou sadu klíčů a pak vygenerujte klíč v této sadu klíčů. Můžete vytvořit ruční tajný klíč, nahrát certifikát nebo PKCS12 klíč. Klíčem může být vygenerovaný tajný klíč, řetězec, který definujete (například tajný klíč aplikace Facebook), nebo certifikát, který nahrajete. Pokud má sada klíčů více klíčů, je aktivní pouze jeden z klíčů.

### <a name="trust-framework-policy-keyset"></a>Sada klíčů zásad pro Trust Framework

- [Seznam sad pro nastavení sady Trust Framework](/graph/api/trustframework-list-keysets)
- [Vytvoření sad pro přístup k architektuře](/graph/api/trustframework-post-keysets)
- [Získání sady klíčů](/graph/api/trustframeworkkeyset-get)
- [Aktualizace sad s architekturou pro vztah důvěryhodnosti](/graph/api/trustframeworkkeyset-update)
- [Odstranění sad s architekturou pro vztah důvěryhodnosti](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Klíč zásad pro Trust Framework

- [Získat aktuálně aktivní klíč v rámci sady klíčů](/graph/api/trustframeworkkeyset-getactivekey)
- [Generování klíče v sady klíčů](/graph/api/trustframeworkkeyset-generatekey)
- [Nahrání tajného kódu založeného na řetězci](/graph/api/trustframeworkkeyset-uploadsecret)
- [Nahrání certifikátu X. 509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Odeslat certifikát formátu PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplikace

- [Výpis aplikací](/graph/api/application-list)
- [Vytvoření aplikace](/graph/api/resources/application)
- [Aktualizovat aplikaci](/graph/api/application-update)
- [Vytvořit servicePrincipal](/graph/api/resources/serviceprincipal)
- [Vytvořit oauth2Permission grant](/graph/api/resources/oauth2permissiongrant)
- [Odstranit aplikaci](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Vlastnosti rozšíření aplikace

- [Seznam vlastností rozšíření](/graph/api/application-list-extensionproperty)

Azure AD B2C poskytuje adresář, který může obsahovat 100 vlastních atributů na uživatele. V případě toků uživatelů se tyto vlastnosti rozšíření [spravují pomocí Azure Portal](user-flow-custom-attributes.md). Pro vlastní zásady Azure AD B2C vytvoří pro vás vlastnost, při prvním zapíše hodnotu do vlastnosti Extension.

## <a name="audit-logs"></a>Protokoly auditu

- [Vypsat protokoly auditu](/graph/api/directoryaudit-list)

Další informace o přístupu k protokolům auditu Azure AD B2C pomocí rozhraní API Microsoft Graph najdete v tématu [přístup k protokolům auditu Azure AD B2C](view-audit-logs.md).