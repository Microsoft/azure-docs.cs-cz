---
title: Rozšíření aplikace v Azure Active Directory B2C | Dokumentace Microsoftu
description: Obnovení aplikace rozšíření b2c.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 9/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ad3d459b1211d2777f57169f3ee896d2ab5618bc
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442820"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: Aplikace rozšíření

Když se vytvoří adresář Azure AD B2C, aplikace volá `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` se automaticky vytvoří do nového adresáře. Tato aplikace používá označení **b2c-extensions-app**, je zobrazen v *registrace aplikací*. Používá se službou Azure AD B2C k ukládání informací o uživatelích a vlastní atributy. Pokud aplikace odstraní, Azure AD B2C nebude fungovat správně, a tato akce ovlivní vaše produkční prostředí.

> [!IMPORTANT]
> Neodstraňujte b2c-extensions-app, pokud máte v úmyslu okamžitě odstranit tenanta. Pokud aplikace zůstává odstraněné déle než 30 dní, budou informace o uživateli trvale ztraceny.

## <a name="verifying-that-the-extensions-app-is-present"></a>Ověřuje se, že je k dispozici aplikace rozšíření

Chcete-li ověřit, že b2c-extensions-app je k dispozici:

1. Ve vašem tenantovi Azure AD B2C, klikněte na **všechny služby** v navigační nabídce vlevo.
1. Vyhledat a otevřít **registrace aplikací**.
1. Vyhledejte aplikaci, která začíná **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Obnovení aplikace rozšíření

Pokud omylem odstranili b2c-extensions-app, je nutné 30 dnů, abyste ho mohli obnovit. Můžete obnovit aplikaci využívající rozhraní Graph API:

1. Přejděte do [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/).
1. Přihlaste se k webu jako globální správce adresáře Azure AD B2C, který chcete obnovit odstraněné aplikace pro. Tento globální správce, musí mít e-mailovou adresu, která je podobný následujícímu: `username@{yourTenant}.onmicrosoft.com`.
1. Vydat HTTP GET na adresu URL `https://graph.windows.net/myorganization/deletedApplications` s verzí api-version = 1.6. Tato operace se zobrazí všechny aplikace, které se odstranily za posledních 30 dnů.
1. Vyhledat aplikaci v seznamu, kde název začíná řetězcem "aplikace b2c rozšíření" a zkopírujte její `objectid` hodnotu vlastnosti.
1. Vydání požadavku HTTP POST na adresu URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Nahradit `{OBJECTID}` část adresy URL se `objectid` z předchozího kroku. 

Nyní byste měli být schopni [obnovené aplikace zobrazit](#verifying-that-the-extensions-app-is-present) na webu Azure Portal.

> [!NOTE]
> Aplikace lze obnovit pouze pokud byl odstraněn během posledních 30 dnů. Pokud to bylo víc než 30 dnů, data budou trvale ztratí. O další pomoc založte lístek podpory.