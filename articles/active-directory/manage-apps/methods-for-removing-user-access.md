---
title: Jak odebrat uživateli přístup k aplikaci | Dokumenty společnosti Microsoft
description: Pochopit, jak odebrat přístup uživatele k aplikaci
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b69502995eff88df53af3671a8e611809f83e59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65826104"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Jak odebrat uživateli přístup k aplikaci

Tento článek vám pomůže pochopit, jak odebrat přístup uživatele k aplikaci.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chci odebrat přiřazení konkrétního uživatele nebo skupiny k aplikaci

Pokud chcete odebrat přiřazení uživatele nebo skupiny do aplikace, postupujte podle pokynů uvedených v článku [Odebrání uživatele nebo skupiny z podnikové aplikace.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chci zakázat veškerý přístup k aplikaci pro každého uživatele

Pokud chcete zakázat všechna přihlášení uživatelů k aplikaci, postupujte podle pokynů uvedených v článku [Zakázat přihlášení uživatelů k podnikové aplikaci.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)

## <a name="i-want-to-delete-an-application-entirely"></a>Chci zcela odstranit aplikaci

Chcete-li **aplikaci odstranit**, postupujte podle následujících pokynů:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete odstranit.

7. Po načtení aplikace **klikněte** na Odstranit ikonu v podokně **Přehled** horní aplikace.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chci zakázat všechny budoucí operace se souhlasem uživatele pro libovolnou aplikaci

Zakázání souhlasu uživatele pro celý adresář zabrání koncovým uživatelům v souhlasu s jakoukoli aplikací. Správci mohou souhlasit jménem uživatele. Další informace o souhlasu s aplikací a o tom, proč to můžete nebo nemusíte chtít provést, najdete v článek [Principy souhlasu uživatele a správce](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Viz také [Oprávnění a souhlas](../develop/v2-permissions-and-consent.md).

**Chcete-li zakázat všechny budoucí operace se souhlasem uživatele v celém adresáři**, postupujte podle následujících pokynů:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevření **rozšíření Služby Azure Active Directory** 

3.  V navigační nabídce klikněte na **Podnikové aplikace.**

5.  Klepněte na **položku Uživatelská nastavení**.

6.  Nastavte, **aby uživatelé mohli aplikacím povolit přístup k firemním datům jejich jménem,** přepněte na **Ne** a klikněte na tlačítko Uložit.


## <a name="next-steps"></a>Další kroky

[Správa přístupu k aplikacím](what-is-access-management.md)
