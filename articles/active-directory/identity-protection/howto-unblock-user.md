---
title: Jak odblokovat uživatele s Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak odblokovat uživatele, kteří se zablokoval pomocí zásad Azure Active Directory Identity Protection.
services: active-directory
keywords: Azure active directory identity protection odblokování uživatele
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dd27e022524e3dd6211591104020f81124245c3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209553"
---
# <a name="how-to-unblock-users"></a>Jak: Odblokování uživatelů

S Azure Active Directory Identity Protection můžete nakonfigurovat zásady pro blokování uživatelů jsou při splnění nakonfigurovaných podmínek. Obvykle blokovaný uživatel kontakty technické podpory o budou odblokována. Tento článek vysvětluje kroky můžete provést pro odblokování blokovaný uživatel.

## <a name="determine-the-reason-for-blocking"></a>Zjistěte důvod pro blokování
Jako první krok pro odblokování uživatele je nutné určit typ zásady, které má uživatel blokován, protože na něm závisí další kroky.
S Azure Active Directory Identity Protection může být uživatel buď blokováno jiným zásady rizik přihlašování nebo zásady rizik uživatelů.

Typ zásady, který se zablokoval uživatele z záhlaví v dialogovém okně, které se budou zobrazovat uživateli při pokusu o přihlášení můžete získat:

| Zásada | Dialogové okno uživatele |
| --- | --- |
| Riziko přihlášení |![Zablokování přihlášení](./media/howto-unblock-user/02.png) |
| Riziko uživatele |![Blokované účtu](./media/howto-unblock-user/104.png) |

Uživatel, který je blokovaný podle:

* Zásady rizik přihlašování se také označuje jako podezřelou přihlášení
* Zásady rizik uživatelů se také označuje jako účet u rizik

## <a name="unblocking-suspicious-sign-ins"></a>Odblokování podezřelé přihlášení
K blokování podezřelých přihlášení, máte následující možnosti:

1. **Přihlášení ze známého umístění nebo zařízení** -Častým důvodem, proč blokované podezřelé přihlášení jsou pokusů o přihlášení z neznámých míst nebo zařízení. Vaši uživatelé mohli rychle zjistit, zda se jedná Důvod blokování tak, že zkusíte přihlásit ze známého umístění nebo zařízení.
2. **Vyloučit ze zásad** – Pokud se domníváte, že aktuální konfigurace zásady přihlášení způsobuje problémy pro konkrétní uživatele, můžete vyloučit uživatele z něj. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Zakázat zásadu** – Pokud se domníváte, že vaše konfigurace zásad způsobuje problémy pro všechny uživatele, můžete zakázat zásadu. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Účty, odblokování
Odblokování účtu ohroženy, máte následující možnosti:

1. **Resetovat heslo** – může resetovat heslo uživatele. 
2. **Skrýt všechny rizikové události** – bloky zásady rizik uživatelů uživatele Pokud uživatel nakonfigurovaný rizika úroveň pro zablokování přístupu se dosáhlo. Uživatele můžete snížit na úroveň rizika ručně ukončením hlášené rizikové události. 
3. **Vyloučit ze zásad** – Pokud se domníváte, že aktuální konfigurace zásady přihlášení způsobuje problémy pro konkrétní uživatele, můžete vyloučit uživatele z něj. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Zakázat zásadu** – Pokud se domníváte, že vaše konfigurace zásad způsobuje problémy pro všechny uživatele, můžete zakázat zásadu. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Další postup
 
Opravdu chcete dozvědět víc o službě Azure AD Identity Protection? Podívejte se na [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
