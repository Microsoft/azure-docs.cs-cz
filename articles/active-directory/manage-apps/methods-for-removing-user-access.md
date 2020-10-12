---
title: Postup odebrání přístupu uživatele k aplikaci | Microsoft Docs
description: Princip odebrání přístupu uživatele k aplikaci
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/17/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f9626c256755e2fce81b593d95b8680f4bb55ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763155"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Postup odebrání přístupu uživatele k aplikaci

Tento článek vám pomůže pochopit, jak odebrat přístup uživatele k aplikaci.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chci odebrat přiřazení konkrétního uživatele nebo skupiny do aplikace

Chcete-li odebrat přiřazení uživatele nebo skupiny k aplikaci, postupujte podle kroků uvedených v části [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) článku.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chci zakázat veškerý přístup k aplikaci pro každého uživatele

Pokud chcete zakázat přihlášení všech uživatelů k aplikaci, postupujte podle kroků uvedených v tématu [zakázání přihlášení uživatelů pro podnikovou aplikaci v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) článku.

## <a name="i-want-to-delete-an-application-entirely"></a>Chci úplně odstranit aplikaci

Pokud chcete **aplikaci odstranit**, postupujte podle těchto pokynů:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. V navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete odstranit.

7. Po načtení aplikace klikněte na **Odstranit** ikonu v horním podokně s **přehledem** aplikace.

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
