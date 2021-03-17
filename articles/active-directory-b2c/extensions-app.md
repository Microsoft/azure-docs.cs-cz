---
title: Aplikace rozšíření v Azure Active Directory B2C | Microsoft Docs
description: Obnovuje se aplikace B2C-Extensions-App.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc536fa4292d794e8d89a2564ad10a3c10dd0a3d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560850"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: rozšíření aplikace

Při vytvoření adresáře Azure AD B2C `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` se v novém adresáři automaticky vytvoří aplikace s názvem. Tato aplikace, která se označuje jako **B2C-Extensions-App** , je v *Registrace aplikací* viditelná. Služba Azure AD B2C ji používá k ukládání informací o uživatelích a vlastních atributech. Pokud se aplikace odstraní, Azure AD B2C nebude správně fungovat a bude to mít vliv na vaše produkční prostředí.

> [!IMPORTANT]
> Pokud neplánujete okamžitě odstranit tenanta, neodstraňujte B2C-Extensions-App. Pokud aplikace zůstane smazána déle než 30 dní, informace o uživateli budou trvale ztraceny.

## <a name="verifying-that-the-extensions-app-is-present"></a>Ověření, jestli je aplikace rozšíření k dispozici

Chcete-li ověřit, zda je aplikace B2C-Extensions-App k dispozici:

1. V rámci vašeho tenanta Azure AD B2C v navigační nabídce vlevo klikněte na **všechny služby** .
1. Vyhledejte a otevřete **Registrace aplikací**.
1. Vyhledat aplikaci, která začíná **B2C-Extensions-App**

## <a name="recover-the-extensions-app"></a>Obnovení aplikace rozšíření

Pokud jste B2C-Extensions-App omylem odstranili, budete mít 30 dní na jeho obnovení. Aplikaci můžete obnovit pomocí Graph API:

1. Přejděte na [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Přihlaste se k webu jako globální správce pro adresář Azure AD B2C, pro který chcete obnovit odstraněnou aplikaci. Tento globální správce musí mít e-mailovou adresu podobnou následující: `username@{yourTenant}.onmicrosoft.com` .
1. Vydejte požadavek HTTP GET na adresu URL `https://graph.windows.net/myorganization/deletedApplications` s rozhraním API-Version = 1.6. Tato operace zobrazí seznam všech aplikací, které byly odstraněny během posledních 30 dnů.
1. Najde aplikaci v seznamu, kde název začíná na ' B2C-Extensions-App ' a zkopíruje jeho `objectid` hodnotu vlastnosti.
1. Vydejte požadavek HTTP POST na adresu URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore` . Nahraďte `{OBJECTID}` část adresy URL `objectid` z předchozího kroku.

Nyní by se měla [Zobrazit obnovená aplikace](#verifying-that-the-extensions-app-is-present) v Azure Portal.

> [!NOTE]
> Aplikaci lze obnovit pouze v případě, že byla odstraněna během posledních 30 dnů. Pokud je více než 30 dní, data budou trvale ztracena. Pokud potřebujete další pomoc, požádejte o pomoc lístek podpory.
