---
title: Odinstalace služby Azure AD Connect
description: Tento dokument popisuje, jak odinstalovat Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96934168"
---
# <a name="uninstall-azure-ad-connect"></a>Odinstalace služby Azure AD Connect

Tento dokument popisuje, jak správně odinstalovat Azure AD Connect.

## <a name="uninstall-azure-ad-connect-from-the-server"></a>Odinstalace Azure AD Connect ze serveru
První věc, kterou potřebujete udělat, je odebrat Azure AD Connect ze serveru, na kterém je spuštěná.  Použijte k tomu následující postup:

 1. Na serveru se spuštěným Azure AD Connect přejděte na **Ovládací panely**.
 2. Klikněte na **Odinstalovat program** 
  ![ Odinstalovat program.](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. Vyberte **Azure AD Connect**.
 ![Vyberte Azure AD Connect](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. Po zobrazení výzvy potvrďte kliknutím na **Ano** .
 5. Toto potvrzení zobrazí obrazovku Azure AD Connect.  Klikněte na **Odebrat**.
 ![Odebrat](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. Po dokončení této akce klikněte na tlačítko **konec**.
 7. ![Ukončit](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. Zpět v **Ovládacích panelech** klikněte na tlačítko **aktualizovat** a všechny součásti by měly být odebrány.


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
- [Instalace nástroje Azure AD Connect s využitím existující databáze ADSync](how-to-connect-install-existing-database.md)
- [Instalace nástroje Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md)

