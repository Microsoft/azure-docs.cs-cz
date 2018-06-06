---
title: Azure Active Directory identitu ochrana – jak odblokovat uživatele | Microsoft Docs
description: Zjistěte, jak odblokovat uživatele, které byly blokována zásady služby Azure Active Directory Identity Protection.
services: active-directory
keywords: ochrany identit Azure active directory, odblokovat uživatele
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1e96f24aeb0083e57618ad3e38163f50c23c55d3
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713452"
---
# <a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory identitu ochrana – jak odblokovat uživatele
S Azure Active Directory Identity Protection můžete nakonfigurovat zásady pro blokování uživatele, pokud jsou splněny podmínky nakonfigurované. Obvykle blokovaný uživatel kontakty technické podpory k odblokování. Tento článek vysvětluje kroky můžete provádět, abyste odblokovat blokovaný uživatel.

## <a name="determine-the-reason-for-blocking"></a>Určete důvod pro blokování
Jako první krok odblokovat uživatele budete muset určit typ zásady, které má uživatel blokován, protože na něm závisí další kroky.
S Azure Active Directory Identity Protection může se uživatel buď blokovaný přihlášení riziko zásadu nebo zásady riziko uživatele.

Můžete získat typ zásady, který má blokovaný uživatel z záhlaví v dialogu, který byl předložený uživateli při pokusu o přihlášení:

| Zásada | Dialogové okno uživatele |
| --- | --- |
| Riziko přihlášení |![Blokované přihlášení](./media/active-directory-identityprotection-unblock-howto/02.png) |
| Riziko uživatele |![Zablokovaný účet](./media/active-directory-identityprotection-unblock-howto/104.png) |

Uživatel, který je blokována:

* Zásady přihlášení riziko je také označován jako podezřelou přihlášení
* Zásady uživatele riziko je také označován jako účet v ohrožení

## <a name="unblocking-suspicious-sign-ins"></a>Odblokování podezřelé přihlášení
Chcete-li odblokovat podezřelé přihlášení, máte následující možnosti:

1. **Přihlaste se ze zařízení nebo známé umístění** -běžným důvodem pro blokované podezřelé přihlášení jsou pokusů o přihlášení z neznámých míst nebo zařízení. Vaši uživatelé můžete rychle zjistit, zda je z důvodu blokování tak, že zkusíte přihlášení ze zařízení nebo známé umístění.
2. **Vyloučit ze zásad** – Pokud se domníváte, že aktuální konfiguraci zásad přihlášení způsobuje problémy pro konkrétní uživatele, můžete vyloučit uživatele z něj. Další informace najdete v tématu [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
3. **Zakažte zásadu** – Pokud se domníváte, že vaše konfigurace zásad způsobuje problémy pro všechny uživatele, můžete zakázat zásadu. Další informace najdete v tématu [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Odblokování účty v ohrožení
Chcete-li odblokovat účtu ohrožený, máte následující možnosti:

1. **Resetovat heslo** -resetujete heslo uživatele. Další informace najdete v tématu [ruční zabezpečené heslo resetovat](active-directory-identityprotection.md#manual-secure-password-reset).
2. **Zavřít všechny události riziko** – na uživatel bloky zásad riziko uživatele Pokud uživatel nakonfigurovaný úroveň blokování přístupu rizika byl dosažen. Můžete snížit uživatele je úroveň rizika ručně ukončením hlášené rizikových událostech. Další informace najdete v tématu [zavření rizikových událostí ručně](active-directory-identityprotection.md#closing-risk-events-manually).
3. **Vyloučit ze zásad** – Pokud se domníváte, že aktuální konfiguraci zásad přihlášení způsobuje problémy pro konkrétní uživatele, můžete vyloučit uživatele z něj. Další informace najdete v tématu [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
4. **Zakažte zásadu** – Pokud se domníváte, že vaše konfigurace zásad způsobuje problémy pro všechny uživatele, můžete zakázat zásadu. Další informace najdete v tématu [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

## <a name="next-steps"></a>Další postup
 Opravdu chcete získat další informace o Azure AD Identity Protection? Podívejte se na [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
