---
title: Azure Active Directory Identity Protection – jak odblokovat uživatele | Dokumentace Microsoftu
description: Zjistěte, jak odblokovat uživatele, kteří se zablokoval pomocí zásad Azure Active Directory Identity Protection.
services: active-directory
keywords: Azure active directory identity protection odblokování uživatele
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1bfc70680ecef2ee4fe162f81aac71430c773740
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005074"
---
# <a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory Identity Protection – jak odblokovat uživatele
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

1. **Resetovat heslo** – může resetovat heslo uživatele. Další informace najdete v tématu [ruční zabezpečené heslo resetovat](overview.md#manual-secure-password-reset).
2. **Skrýt všechny rizikové události** – bloky zásady rizik uživatelů uživatele Pokud uživatel nakonfigurovaný rizika úroveň pro zablokování přístupu se dosáhlo. Uživatele můžete snížit na úroveň rizika ručně ukončením hlášené rizikové události. Další informace najdete v tématu [ručnímu zavření rizikových událostí](overview.md#closing-risk-events-manually).
3. **Vyloučit ze zásad** – Pokud se domníváte, že aktuální konfigurace zásady přihlášení způsobuje problémy pro konkrétní uživatele, můžete vyloučit uživatele z něj. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Zakázat zásadu** – Pokud se domníváte, že vaše konfigurace zásad způsobuje problémy pro všechny uživatele, můžete zakázat zásadu. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Další postup
 
Opravdu chcete dozvědět víc o službě Azure AD Identity Protection? Podívejte se na [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
