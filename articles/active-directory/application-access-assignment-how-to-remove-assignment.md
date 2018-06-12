---
title: Postup odebrání přístupu uživatele k aplikaci | Microsoft Docs
description: Pochopit, jak k odebrání přístupu uživatele k aplikaci
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3731049d2bc42c7fac492f7609f54b5d4a1443b6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292316"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Postup odebrání přístupu uživatele k aplikaci

Tento článek pomáhá pochopit, jak k odebrání přístupu uživatele k aplikaci.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chcete odstranit přiřazení konkrétního uživatele nebo skupiny do aplikace

Chcete-li odebrat uživatele nebo skupiny přiřazení k aplikaci, postupujte podle kroků uvedených v [odebrat uživatele nebo skupinu přiřazení z podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) článku.

. ## Chcete zakázat veškerý přístup k aplikaci pro každého uživatele

Pokud chcete zakázat všechna uživatelská přihlášení k aplikaci, postupujte podle kroků uvedených v [zakázat uživatelská přihlášení pro podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) článku.

## <a name="i-want-to-delete-an-application-entirely"></a>Chcete odstranit aplikaci zcela

K **odstranit aplikaci**, postupujte podle těchto pokynů:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

   * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete odstranit.

7.  Po načtení aplikace, klikněte na **odstranit** ikonu z hlavní aplikace **přehled** podokně.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcete zakázat všechny operace souhlasu budoucí uživatele do jakékoli aplikace

Zakázání souhlas uživatele pro souhlas pro žádnou aplikaci koncovým uživatelům zabránit celý adresář. Správci mohou stále souhlas na behalves uživatele. Další informace o aplikaci souhlas, a proto může nebo nemusí Chcete to provést, číst [Principy uživatelů a správce souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

K **zakažte všechny operace souhlasu budoucí uživatele v adresáři celý**, postupujte podle těchto pokynů:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **uživatelská nastavení**.

6.  Zakažte všechny budoucí uživatele souhlasu operace nastavením **uživatelé můžou aplikace přistupovat ke svým datům** přepnutím **ne** a klikněte na tlačítko **Uložit** tlačítko.


# <a name="next-steps"></a>Další postup
[Správa přístupu k aplikacím](manage-apps/what-is-access-management.md)
