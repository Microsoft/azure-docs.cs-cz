---
title: Aplikace rozšíření ve službě Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Obnovení aplikace b2c-extensions.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 547b625996a65999c32c1b73699e3b408be01de3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188592"
---
# <a name="azure-ad-b2c-extensions-app"></a>Aplikace Azure AD B2C: Rozšíření

Při vytvoření adresáře Azure AD B2C `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` se automaticky vytvoří aplikace snázvem uvnitř nového adresáře. Tato aplikace, označovaná jako **b2c-extensions-app**, je viditelná v *registracích aplikací*. Používá se službou Azure AD B2C k ukládání informací o uživatelích a vlastních atributech. Pokud je aplikace odstraněna, Azure AD B2C nebude fungovat správně a vaše produkční prostředí bude ovlivněna.

> [!IMPORTANT]
> Neodstraňujte aplikaci b2c-extensions, pokud neplánujete okamžitě odstranit svého klienta. Pokud aplikace zůstane smazána déle než 30 dní, informace o uživateli budou trvale ztraceny.

## <a name="verifying-that-the-extensions-app-is-present"></a>Ověření přítomnosti aplikace rozšíření

Chcete-li ověřit, zda je k dispozici aplikace b2c-extensions:

1. Uvnitř vašeho tenanta Azure AD B2C klikněte na **všechny služby** v levém navigačním menu.
1. Vyhledejte a otevřete **registrace aplikací**.
1. Podívejte se na aplikaci, která začíná **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Obnovení aplikace rozšíření

Pokud jste omylem odstranili aplikaci b2c-extensions, máte 30 dní na její obnovení. Aplikaci můžete obnovit pomocí rozhraní Graph API:

1. Přejděte [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)na .
1. Přihlaste se k webu jako globální správce adresáře Azure AD B2C, pro který chcete odstranit odstraněnou aplikaci. Tento globální správce musí mít e-mailovou adresu podobnou následující: `username@{yourTenant}.onmicrosoft.com`.
1. Vydejte HTTP GET `https://graph.windows.net/myorganization/deletedApplications` proti adrese URL s api-version=1.6. Tato operace zobrazí seznam všech aplikací, které byly odstraněny během posledních 30 dnů.
1. Najděte aplikaci v seznamu, kde název začíná 'b2c-extension-app' a zkopírujte její `objectid` hodnotu vlastnosti.
1. Vydat http post proti `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`adrese URL . Nahraďte `{OBJECTID}` část adresy `objectid` URL z předchozího kroku.

Teď byste měli mít možnost [zobrazit obnovenou aplikaci](#verifying-that-the-extensions-app-is-present) na webu Azure Portal.

> [!NOTE]
> Aplikaci lze obnovit pouze v případě, že byla odstraněna během posledních 30 dnů. Pokud to bylo více než 30 dní, data budou trvale ztracena. Pro další pomoc, soubor lístek podpory.
