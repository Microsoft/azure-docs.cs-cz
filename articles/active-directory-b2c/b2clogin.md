---
title: Pomocí b2clogin.com| Microsoft Docs
description: Další informace o použití b2clogin.com místo login.microsoftonline.com.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
#<a name="using-b2clogincom"></a>Použití b2clogin.com

>[!IMPORTANT]
>Tato funkce je ve verzi public preview 
>

Nyní máte možnost používat službu Azure AD B2C s `<YourTenantName>.b2clogin.com` místo použití `login.microsoftonline.com`.  To má mnoho výhod:
* Stejné omezení velikosti hlavičky souboru cookie budou už sdílet s dalšími produkty Microsoftu
* Můžete odebrat všechny odkazy na Microsoft v svoji adresu URL (můžete nahradit `<YourTenantName>.onmicrosoft.com` s vaše ID klienta)

 Aby bylo možné využít výhod b2clogin.com, je nutné nastavit některé z následujících akcí:

1. Pro vaše **spustit nyní koncový bod** Ujistěte se, že používáte `<YourTenantName>.b2clogin.com` místo použití `login.microsoftonline.com`.
2. Pokud používáte MSAL, budete muset nastavit `validateauthority=false`.  Důvodem je, že se změní na vydavatel tokenu`<YourTenantName>.b2clogin.com`.