---
title: 'Azure AD Connect synchronizace: Změna hesla služba AD DS účtu | Microsoft Docs'
description: Tento článek popisuje, jak aktualizovat Azure AD Connect po změně hesla služba AD DS účtu.
services: active-directory
keywords: Účet služba AD DS, účet služby Active Directory, heslo
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4077146292db1266d5dbc51cc577f952b2bff191
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85357507"
---
# <a name="changing-the-ad-ds-account-password"></a>Změna hesla účtu služby AD DS
Účet služba AD DS odkazuje na uživatelský účet používaný službou Azure AD Connect ke komunikaci s místní službou Active Directory. Pokud změníte heslo účtu služba AD DS, musíte aktualizovat Azure AD Connect synchronizační službu pomocí nového hesla. V opačném případě se synchronizace už nedokáže správně synchronizovat s místní službou Active Directory a zobrazí se následující chyby:

* V Synchronization Service Manager všechny operace importu nebo exportu s místní službou AD selžou s chybou **bez spuštění-přihlašovacích údajů** .

* V části Windows Prohlížeč událostí protokol událostí aplikace obsahuje chybu s **ID události 6000** a zprávou **"Agent pro správu" contoso.com "se nepodařilo spustit, protože přihlašovací údaje byly neplatné**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Jak aktualizovat synchronizační službu pomocí nového hesla pro služba AD DS účet
Chcete-li aktualizovat synchronizační službu pomocí nového hesla:

1. Spusťte Synchronization Service Manager (spusťte synchronizační službu →).
</br>![Synchronizovat Service Manager](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Přejít na kartu **konektory** .

3. Vyberte **konektor služby AD** , který odpovídá účtu služba AD DS, pro který se změnilo jeho heslo.

4. V části **Akce** vyberte **vlastnosti**.

5. V místním dialogovém okně vyberte **připojit k doménové struktuře služby Active Directory**:

6. Do textového pole **heslo** zadejte nové heslo účtu služba AD DS.

7. Kliknutím na tlačítko **OK** uložte nové heslo a zavřete automaticky otevírané okno.

8. Restartujte službu Azure AD Connect synchronizace pod správcem řízení služeb systému Windows. K tomu je potřeba zajistit, aby se všechny odkazy na staré heslo odebraly z mezipaměti paměti.

## <a name="next-steps"></a>Další kroky
**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
