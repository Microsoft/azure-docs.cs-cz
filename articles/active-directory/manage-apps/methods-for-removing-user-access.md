---
title: Postup odebrání přístupu uživatele k aplikaci v Azure Active Directory
description: Princip odebrání přístupu uživatele k aplikaci v Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: iangithinji
ms.openlocfilehash: 958abc5f9be443d66037a6d9fe8d8779e6e37e0e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379583"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Postup odebrání přístupu uživatele k aplikaci

Tento článek vám pomůže pochopit, jak odebrat přístup uživatele k aplikaci.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chci odebrat přiřazení konkrétního uživatele nebo skupiny do aplikace

Chcete-li odebrat přiřazení uživatele nebo skupiny k aplikaci, postupujte podle kroků uvedených v části [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace v Azure Active Directory](./assign-user-or-group-access-portal.md) článku.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chci zakázat veškerý přístup k aplikaci pro každého uživatele

Pokud chcete zakázat přihlášení všech uživatelů k aplikaci, postupujte podle kroků uvedených v tématu [zakázání přihlášení uživatelů pro podnikovou aplikaci v Azure Active Directory](./disable-user-sign-in-portal.md) článku.

## <a name="i-want-to-delete-an-application-entirely"></a>Chci úplně odstranit aplikaci

[Série rychlý Start při správě aplikací](delete-application-portal.md) zahrnuje pokyny k odstranění aplikace z vašeho tenanta Azure Active Directory.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chci zakázat všechny operace souhlasu uživatele s budoucími uživateli u libovolné aplikace

Zákaz souhlasu uživatele pro celý adresář zabrání koncovým uživatelům v posílání do libovolné aplikace. Správci mohou i nadále souhlasit jménem uživatele. Další informace o souhlasu aplikace a o tom, proč můžete nebo nechcete, aby to bylo možné, najdete v článku [vysvětlení souhlasu uživatele a správce](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Viz také [oprávnění a souhlas](../develop/v2-permissions-and-consent.md).

Pokud chcete **Zakázat všechny operace souhlasu uživatele s ostatními uživateli v celém adresáři**, postupujte podle těchto pokynů:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** 

3.  V navigační nabídce klikněte na **podnikové aplikace** .

5.  Klikněte na **nastavení uživatele**.

6.  Nastavením **Uživatelé můžou aplikacím dovolit přístup k firemním datům** , když se jejich jménem přepne na **ne** a klikne na tlačítko Uložit.


## <a name="next-steps"></a>Další kroky

[Správa přístupu k aplikacím](what-is-access-management.md)