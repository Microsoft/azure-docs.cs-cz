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
ms.openlocfilehash: c41c02acaeffa170d55f3c59f34a4b1ecae1c523
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712449"
---
# <a name="using-b2clogincom"></a>Použití b2clogin.com

>[!IMPORTANT]
>Tato funkce je ve verzi public preview 
>

Nyní máte možnost používat službu Azure AD B2C s `<YourTenantName>.b2clogin.com` místo použití `login.microsoftonline.com`.  To má mnoho výhod:
* Stejné omezení velikosti hlavičky souboru cookie budou už sdílet s dalšími produkty Microsoftu
* Můžete odebrat všechny odkazy na Microsoft v svoji adresu URL (můžete nahradit `<YourTenantName>.onmicrosoft.com` s vaše ID klienta)

 Aby bylo možné využít výhod b2clogin.com, je nutné nastavit některé z následujících akcí:

1. Pro vaše **spustit nyní koncový bod** Ujistěte se, že používáte `<YourTenantName>.b2clogin.com` místo použití `login.microsoftonline.com`.
2. Pokud používáte MSAL, budete muset nastavit `validateauthority=false`.  Důvodem je, že se změní na vydavatel tokenu`<YourTenantName>.b2clogin.com`.