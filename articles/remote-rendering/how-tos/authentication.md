---
title: Authentication
description: Vysvětluje, jak ověřování funguje
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681633"
---
# <a name="configure-authentication"></a>Konfigurace ověřování

Azure Remote Rendering používá stejný mechanismus ověřování jako [Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Klienti musí nastavit *AccountKey*, *AuthenticationToken*nebo *AccessToken* pro úspěšné volání repo zem skála. *AccountKey* lze získat na kartě "Klíče" pro účet vzdáleného vykreslování na webu Azure Portal. *AuthenticationToken* je token Azure AD, který lze získat pomocí [knihovny ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* je token MR, který lze získat ze služby Token tokenu zabezpečení Azure mixed reality (STS).

## <a name="authentication-for-deployed-applications"></a>Ověřování pro nasazené aplikace

 Použití klíčů účtu se doporučuje pro rychlé napalubě, ale pouze při vývoji / prototypování. Důrazně doporučujeme nedoručovat vaši aplikaci do produkčního prostředí pomocí vloženého klíče účtu a místo toho použít přístupy ověřování Azure AD založené na uživateli nebo službě.

 Ověřování Azure AD je popsané v části [ověřování uživatelů Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) [služby Azure Spatial Anchors (ASA).](https://docs.microsoft.com/azure/spatial-anchors/)

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Chcete-li pomoci řídit úroveň přístupu uděleného aplikacím, službám nebo uživatelům služby Azure AD vaší služby, byly vytvořeny následující role, které můžete podle potřeby přiřadit k účtům vzdáleného vykreslování Azure:

* **Správce vzdáleného vykreslování**: Poskytuje uživateli možnosti převodu, správy relace, vykreslování a diagnostiky pro vzdálené vykreslování Azure.
* **Klient vzdáleného vykreslování**: Poskytuje uživateli možnosti správy relace, vykreslování a diagnostiky pro vzdálené vykreslování Azure.

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu](create-an-account.md)
* [Použití api pro front-end Azure pro ověřování](frontend-apis.md)
* [Příklad skriptů prostředí PowerShell](../samples/powershell-example-scripts.md)
