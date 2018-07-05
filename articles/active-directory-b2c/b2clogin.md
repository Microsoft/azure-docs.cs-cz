---
title: Použití b2clogin.com | Dokumentace Microsoftu
description: Další informace o použití b2clogin.com místo login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1d42d9a97244eeff501b9d02b0f143d6ef0c91b2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440625"
---
# <a name="using-b2clogincom"></a>Použití b2clogin.com

>[!IMPORTANT]
>Tato funkce je ve verzi public preview 
>

Nyní máte možnost používat službu Azure AD B2C s `<YourTenantName>.b2clogin.com` namísto použití `login.microsoftonline.com`.  To má mnoho výhod:
* Je už nemůžete sdílet stejné omezení velikosti hlavičky souborů cookie s dalšími produkty Microsoftu.
* V adrese URL můžete odebrat všechny odkazy na Microsoft (můžete nahradit `<YourTenantName>.onmicrosoft.com` za ID vašeho tenanta). Například: `https://<tenantname>.b2clogin.com/tfp/<tenantname>/<policyname>/v2.0/.well-known/openid-configuration`.

 Pokud chcete využít výhod b2clogin.com, je nutné nastavit některé z následujících akcí:

1. Pro vaše **koncový bod pro okamžité spuštění** Ujistěte se, že používáte `<YourTenantName>.b2clogin.com` namísto použití `login.microsoftonline.com`.
2. Pokud používáte MSAL, je nutné nastavit `validateauthority=false`.  Důvodem je, že vydavatel tokenu stane`<YourTenantName>.b2clogin.com`.