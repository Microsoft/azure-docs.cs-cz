---
title: Podporované operace microsoft graphu
titleSuffix: Azure AD B2C
description: Index operací Microsoft Graphu podporovaný pro správu prostředků Azure AD B2C, včetně uživatelů, toků uživatelů, poskytovatelů identit, vlastních zásad, klíčů zásad a dalších.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 27fe1a41365d96a4179f8c659b63dc22c7b9fc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184244"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Operace Microsoft Graphu dostupné pro Azure AD B2C

Následující operace rozhraní MICROSOFT Graph API jsou podporované pro správu prostředků Azure AD B2C, včetně uživatelů, poskytovatelů identit, toků uživatelů, vlastních zásad a klíčů zásad.

Každý odkaz v následujících částech cílí na odpovídající stránku v rámci odkazu rozhraní MICROSOFT Graph API pro tuto operaci.

## <a name="user-management"></a>Správa uživatelů

- [Vypsání uživatelů](https://docs.microsoft.com/graph/api/user-list)
- [Vytvoření spotřebitele uživatele](https://docs.microsoft.com/graph/api/user-post-users)
- [Získání uživatele](https://docs.microsoft.com/graph/api/user-get)
- [Aktualizace uživatele](https://docs.microsoft.com/graph/api/user-update)
- [Odstranit uživatele](https://docs.microsoft.com/graph/api/user-delete)

Další informace o správě uživatelských účtů Azure AD B2C pomocí rozhraní Microsoft Graph API najdete v [tématu Správa uživatelských účtů Azure AD B2C pomocí Microsoft Graphu](manage-user-accounts-graph-api.md).

## <a name="identity-providers-user-flow"></a>Zprostředkovatelé identit (tok uživatelů)

Spravujte poskytovatele identit, kteří jsou k dispozici pro vaše toky uživatelů ve vašem tenantovi Azure AD B2C.

- [Seznam zprostředkovatelů identit registrovaných v tenantovi Azure AD B2C](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Vytvoření zprostředkovatele identity](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Získání poskytovatele identity](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Aktualizovat zprostředkovatele identity](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Odstranění zprostředkovatele identity](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Tok uživatele

Nakonfigurujte předem vytvořené zásady pro registraci, přihlášení, kombinovanou registraci a přihlášení, resetování hesla a aktualizaci profilu.

- [Seznam uživatelských toků](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Vytvoření toku uživatele](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Získání toku uživatele](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Odstranění toku uživatele](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Vlastní zásady

Následující operace umožňují spravovat zásady architektury důvěryhodnosti Azure AD B2C, známé jako [vlastní zásady](custom-policy-overview.md).

- [Seznam všech zásad architektury důvěryhodnosti nakonfigurovaných v tenantovi](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Vytvořit zásadu architektury důvěryhodnosti](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Čtení vlastností existující zásady architektury důvěryhodnosti](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Aktualizujte nebo vytvořte zásady architektury důvěryhodnosti.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Odstranění existující zásady architektury důvěryhodnosti](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Klíče zásad

Rozhraní Identity Experience Framework ukládá tajné klíče odkazované ve vlastní zásady vytvořit vztah důvěryhodnosti mezi součástmi. Tyto tajné klíče mohou být symetrické nebo asymetrické klíče/hodnoty. Na webu Azure Portal se tyto entity zobrazují jako **klíče zásad**.

Zdrojem zásad nejvyšší úrovně pro klíče zásad v rozhraní Microsoft Graph API je [sada klíčů Trusted Framework](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Každá **sada klíčů** obsahuje alespoň **jednu klávesu**. Chcete-li vytvořit klíč, nejprve vytvořte prázdnou sadu klíčů a potom vygenerujte klíč v sadě klíčů. Můžete vytvořit ruční tajný klíč, nahrát certifikát nebo klíč PKCS12. Klíčem může být generovaný tajný klíč, řetězec, který definujete (například tajný klíč aplikace Facebook) nebo certifikát, který nahrajete. Pokud sada klíčů obsahuje více klíčů, je aktivní pouze jeden z těchto klíčů.

### <a name="trust-framework-policy-keyset"></a>Sada klíčů zásad důvěryhodného rámce

- [Seznam sad klíčů architektury důvěryhodnosti](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Vytvoření sad klíčů architektury důvěryhodnosti](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Získejte sadu klíčů](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Aktualizace sad klíčů architektury důvěryhodnosti](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Odstranění sad klíčů architektury důvěryhodnosti](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Klíč zásad rámce důvěryhodnosti

- [Získat aktuálně aktivní klíč v sadě klíčů](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Generovat klíč v sadě klíčů](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Nahrání tajného klíče založeného na řetězci](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Nahrání certifikátu X.509](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Nahrání certifikátu formátu PKCS12](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplikace

- [Výpis aplikací](https://docs.microsoft.com/graph/api/application-list)
- [Vytvoření aplikace](https://docs.microsoft.com/graph/api/resources/application)
- [Aktualizovat aplikaci](https://docs.microsoft.com/graph/api/application-update)
- [Vytvořit servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Vytvořit oauth2Permission Grant](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Odstranit aplikaci](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Vlastnosti rozšíření aplikace

- [Vlastnosti rozšíření seznamu](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C poskytuje adresář, který může obsahovat 100 vlastní atributy na uživatele. Pro toky uživatelů se tyto vlastnosti rozšíření [spravují pomocí portálu Azure](custom-policy-custom-attributes.md). Pro vlastní zásady Azure AD B2C vytvoří vlastnost pro vás při prvním zásady zapíše hodnotu vlastnost rozšíření.

## <a name="audit-logs"></a>Protokoly auditu

- [Seznam protokolů auditu](https://docs.microsoft.com/graph/api/directoryaudit-list)

Další informace o přístupu k protokolům auditu Azure AD B2C pomocí rozhraní Microsoft Graph API najdete [v tématu Přístup k protokolům auditu Azure AD B2C](view-audit-logs.md).
