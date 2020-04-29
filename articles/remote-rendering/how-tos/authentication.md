---
title: Authentication
description: Vysvětluje, jak ověřování funguje.
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681633"
---
# <a name="configure-authentication"></a>Konfigurace ověřování

Vzdálené vykreslování Azure používá stejný ověřovací mechanismus jako [prostorové kotvy Azure (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Klienti musí nastavit *AccountKey*, *AuthenticationToken*nebo *ACCESSTOKEN* pro úspěšné volání rozhraní REST API. *AccountKey* se dá získat na kartě Keys (klíče) pro účet vzdáleného vykreslování na Azure Portal. *AuthenticationToken* je token Azure AD, který se dá získat pomocí [knihovny ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* je token Mr, který se dá získat ze služby STS (Azure Mixed reality Security token).

## <a name="authentication-for-deployed-applications"></a>Ověřování pro nasazené aplikace

 Použití klíčů účtu se doporučuje pro rychlé zprovoznění, ale během vývoje a vytváření prototypů. Důrazně doporučujeme, abyste aplikaci nedodali do produkčního prostředí pomocí vloženého klíče účtu a místo toho používali přístup k ověřování Azure AD založené na uživateli nebo službě.

 Ověřování Azure AD je popsané v části [ověřování uživatelů Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) ve službě [Azure prostorové kotvy (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) .

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Aby bylo možné řídit úroveň přístupu udělených aplikacím, službám nebo uživatelům Azure AD vaší služby, byly vytvořeny následující role, které můžete podle potřeby přiřadit k účtům vzdáleného vykreslování Azure:

* **Správce vzdáleného vykreslování**: poskytuje uživateli s převodem, správou možností relace, vykreslování a diagnostiky pro vzdálené vykreslování Azure.
* **Klient vzdáleného vykreslování**: poskytuje uživateli možnost spravovat relaci, vykreslování a diagnostické funkce pro vzdálené vykreslování Azure.

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu](create-an-account.md)
* [Použití rozhraní API Azure front-endu k ověřování](frontend-apis.md)
* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)
