---
title: Jak udělit oprávnění k aplikaci vyvinuté | Dokumentace Microsoftu
description: Jak udělit oprávnění k aplikaci vyvinuté pomocí portálu Azure AD nebo parametr adresy URL
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 10cf04fca8379f41587b1ea680c5b52a26193b53
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723986"
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Jak udělit oprávnění k vlastní vyvinuté aplikaci

Chcete-li udělit souhlas preventivně ve vaší aplikaci nebo dochází k chybě, která nebyla schválená na aplikaci, zkuste níže uvedeného postupu.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Jak provádět souhlas správce pro vaši aplikaci

To má za následek poskytnutí vyjadřujete souhlas s aplikací pro všechny uživatele ve vaší organizaci.

1. Přejděte **registrace aplikací** jako **globálního správce**, vyberte aplikaci.

2. Vyberte **požadovaná oprávnění**a nakonec klikněte **udělit oprávnění** tlačítko v horní části okna.

Alternativně můžete vytvořit žádost o *login.microsoftonline.com* s vaší konfigurací aplikace a připojení na *& příkazový řádek správce =\_souhlas*. Po přihlášení pomocí přihlašovacích údajů správce, aplikace byl udělen souhlas pro všechny uživatele.

## <a name="how-to-force-user-consent-for-your-application"></a>Jak vynutit souhlas uživatele pro vaši aplikaci

* Připojit na žádostech o ověření *& příkazový řádek = souhlasu* vyžadující koncovým uživatelům udělit souhlas pokaždé, když se ověřit.

## <a name="next-steps"></a>Další postup

[Vyjádření souhlasu a integraci aplikací do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[Videí k Azure AD v2.0 a vyjádření souhlasu konvergované aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow pro Azure AD](http://stackoverflow.com/questions/tagged/azure-active-directory)
