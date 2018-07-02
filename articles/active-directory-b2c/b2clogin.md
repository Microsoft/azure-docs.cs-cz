---
title: Pomocí b2clogin.com | Microsoft Docs
description: Další informace o použití b2clogin.com místo login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c4b3122984cdcb324f7b86e44a62e111d6ca0a29
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131588"
---
# <a name="using-b2clogincom"></a>Použití b2clogin.com

>[!IMPORTANT]
>Tato funkce je ve verzi public preview 
>

Nyní máte možnost používat službu Azure AD B2C s `<YourTenantName>.b2clogin.com` místo použití `login.microsoftonline.com`.  To má mnoho výhod:
* Už sdílet stejné omezení velikosti hlavičky souborů cookie s dalšími produkty Microsoftu.
* Můžete odebrat všechny odkazy na Microsoft v svoji adresu URL (můžete nahradit `<YourTenantName>.onmicrosoft.com` s vaše ID klienta). Například: `https://<tenantname>.b2clogin.com/tfp/<tenantname>/<policyname>/v2.0/.well-known/openid-configuration`.

 Aby bylo možné využít výhod b2clogin.com, je nutné nastavit některé z následujících akcí:

1. Pro vaše **spustit nyní koncový bod** Ujistěte se, že používáte `<YourTenantName>.b2clogin.com` místo použití `login.microsoftonline.com`.
2. Pokud používáte MSAL, budete muset nastavit `validateauthority=false`.  Důvodem je, že se změní na vydavatel tokenu`<YourTenantName>.b2clogin.com`.