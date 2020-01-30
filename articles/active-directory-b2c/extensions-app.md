---
title: Aplikace rozšíření v Azure Active Directory B2C | Microsoft Docs
description: Obnovuje se aplikace B2C-Extensions-App.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e5d38d8d791c6b932d8a28a898f2e2b80caff7ac
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846962"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: rozšíření aplikace

Při vytvoření adresáře Azure AD B2C je aplikace s názvem `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` automaticky vytvořena v novém adresáři. Tato aplikace, která se označuje jako **B2C-Extensions-App**, je v *Registrace aplikací*viditelná. Služba Azure AD B2C ji používá k ukládání informací o uživatelích a vlastních atributech. Pokud se aplikace odstraní, Azure AD B2C nebude správně fungovat a bude to mít vliv na vaše produkční prostředí.

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
1. Přihlaste se k webu jako globální správce pro adresář Azure AD B2C, pro který chcete obnovit odstraněnou aplikaci. Tento globální správce musí mít e-mailovou adresu podobnou následující: `username@{yourTenant}.onmicrosoft.com`.
1. Vydejte požadavek HTTP GET na adresu URL `https://graph.windows.net/myorganization/deletedApplications` s rozhraním API-Version = 1.6. Tato operace zobrazí seznam všech aplikací, které byly odstraněny během posledních 30 dnů.
1. Vyhledejte aplikaci v seznamu, kde název začíná na ' B2C-Extension-App ' a zkopírujte hodnotu vlastnosti `objectid`.
1. Vydejte `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`příspěvek HTTP proti adrese URL. Nahraďte `{OBJECTID}` část adresy URL `objectid` z předchozího kroku.

Nyní by se měla [Zobrazit obnovená aplikace](#verifying-that-the-extensions-app-is-present) v Azure Portal.

> [!NOTE]
> Aplikaci lze obnovit pouze v případě, že byla odstraněna během posledních 30 dnů. Pokud je více než 30 dní, data budou trvale ztracena. Pokud potřebujete další pomoc, požádejte o pomoc lístek podpory.
