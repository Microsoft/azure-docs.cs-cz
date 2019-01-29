---
title: 'Synchronizace Azure AD Connect:  Změna hesla účtu služby AD DS | Dokumentace Microsoftu'
description: Tento dokument téma popisuje postup aktualizace služby Azure AD Connect po změně hesla účtu služby AD DS.
services: active-directory
keywords: Účet AD DS, účet služby Active Directory, hesla
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: ac0237ebc9c6d9ffc6e66291f648b0176f386205
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168676"
---
# <a name="changing-the-ad-ds-account-password"></a>Změna hesla účtu služby AD DS
Účet služby AD DS odkazuje na uživatelský účet, který používá Azure AD Connect ke komunikaci s místní služby Active Directory. Pokud změníte heslo účtu služby AD DS, je nutné aktualizovat službu Azure AD Connect synchronizaci s novým heslem. V opačném případě synchronizace můžete už správně synchronizovat s místní Active Directory a dojde k následujícím chybám:

* V operaci Synchronization Service Manager, všechny importu nebo exportu v místním AD selže s **přihlašovací údaje bez zahájení** chyby.

* V prohlížeči událostí pro Windows, protokolu událostí aplikace obsahuje chybu s **6000 ID události** a zpráva **'agenta pro správu "contoso.com" se nepodařilo spustit, protože pověření je neplatné'**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Jak aktualizovat službu synchronizace s nové heslo pro účet služby AD DS
Jak aktualizovat službu synchronizace pomocí nového hesla:

1. Spusťte Synchronization Service Manager (Služba synchronizace → START).
</br>![Správce synchronizace služby](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Přejděte **konektory** kartu.

3. Vyberte **AD Connector** odpovídající účet služby AD DS, pro který se změnil heslo.

4. V části **akce**vyberte **vlastnosti**.

5. V místním dialogovém okně vyberte **připojit k doménové struktuře služby Active Directory**:

6. Zadejte nové heslo účtu služby AD DS **heslo** textového pole.

7. Klikněte na tlačítko **OK** nové heslo uložte a zavřete dialogové okno místní.

8. Restartování služby Azure AD Connect synchronizační služba v rámci správce řízení služeb Windows. Tím je zajištěno, že se z mezipaměti odebere všechny odkazy na staré heslo.

## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: Pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
