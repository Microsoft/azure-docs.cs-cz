---
title: 'Synchronizace služby Azure AD Connect: Změna hesla účtu služby AD DS | Dokumenty společnosti Microsoft'
description: Toto téma dokument popisuje, jak aktualizovat Azure AD Connect po změně hesla účtu služby AD DS.
services: active-directory
keywords: Účet Služby AD DS, účet služby Active Directory, heslo
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35e04be046e20883f60c576745a29342add68a81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60241594"
---
# <a name="changing-the-ad-ds-account-password"></a>Změna hesla účtu služby AD DS
Účet služby AD DS odkazuje na uživatelský účet používaný službou Azure AD Connect ke komunikaci s místní službou Active Directory. Pokud změníte heslo účtu služby AD DS, je nutné aktualizovat službu synchronizace azure a připojení pomocí nového hesla. V opačném případě již synchronizace nemůže správně synchronizovat s místní službou Active Directory a dojde k následujícím chybám:

* Ve Správci synchronizačních služeb se nezdaří všechny operace importu nebo exportu s místním službou AD s **chybou bez spuštění pověření.**

* V prohlížeči událostí systému Windows obsahuje protokol událostí aplikace chybu s **ID události 6000** a zprávu **"Agent pro správu contoso.com" se nepodařilo spustit, protože pověření byla neplatná**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Jak aktualizovat synchronizační službu novým heslem pro účet služby AD DS
Aktualizace služby synchronizace novým heslem:

1. Spusťte Správce synchronizačních služeb (START → Služba synchronizace).
</br>![Správce synchronizačních služeb](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Přejděte na kartu **Konektory.**

3. Vyberte **konektor ad,** který odpovídá účtu ad ds, pro který bylo změněno jeho heslo.

4. V části **Akce**vyberte **Vlastnosti**.

5. V rozbalovacím dialogovém okně vyberte **Připojit k doménové struktuře služby Active Directory**:

6. Do textového pole **Heslo** zadejte nové heslo účtu ad ds.

7. Klepnutím na **tlačítko OK** uložte nové heslo a zavřete rozbalovací dialogové okno.

8. Restartujte synchronizační službu Azure AD Connect ve Správci řízení služeb systému Windows. Tím je zajištěno, že všechny odkazy na staré heslo je odebrán z mezipaměti paměti.

## <a name="next-steps"></a>Další kroky
**Přehledná témata**

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
