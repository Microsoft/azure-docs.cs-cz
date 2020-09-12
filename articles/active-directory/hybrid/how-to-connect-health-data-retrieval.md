---
title: Načítání dat instrukcí pro Azure AD Connect Health | Microsoft Docs
description: Tato stránka popisuje, jak načíst data z Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463519"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Pokyny pro Azure AD Connect Health načtení dat

Tento dokument popisuje, jak pomocí Azure AD Connect načíst data z Azure AD Connect Health.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>Načte všechny e-mailové adresy pro uživatele s nakonfigurovaným upozorněním na stav.

K načtení e-mailových adres pro všechny uživatele, kteří jsou nakonfigurováni v Azure AD Connect Health pro příjem výstrah, použijte následující postup.

1.  Spusťte okno Azure Active Directory Connect stav a v levém navigačním panelu vyberte **synchronizační služby** .
 ![Synchronizační služby](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  Klikněte na dlaždici **výstrahy** .</br>
 ![Výstrahy](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  Klikněte na **Nastavení oznámení**.
 ![Oznámení](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  V okně **Nastavení oznámení** najdete seznam e-mailových adres, které byly povoleny jako příjemci pro oznámení o stavu.
 ![E-maily](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>Načte účty, které byly označené příznakem AD FS špatných pokusů o zadání hesla.

Chcete-li načíst účty, které byly označeny příznakem AD FS špatných pokusů o zadání hesla, postupujte podle následujících pokynů.

1.  V okně Azure Active Directory stav vyberte **chyby synchronizace**.
 ![Chyby synchronizace](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  V okně **chyby synchronizace** klikněte na **exportovat**. Tím se vyexportuje seznam zaznamenaných chyb synchronizace.
 ![Export](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>Další kroky
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalace agenta Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operace služby Azure AD Connect Health](how-to-connect-health-operations.md)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
* [Historie verze Azure AD Connect Health](reference-connect-health-version-history.md)