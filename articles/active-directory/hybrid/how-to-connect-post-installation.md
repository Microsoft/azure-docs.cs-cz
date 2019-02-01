---
title: 'Azure AD Connect: Další kroky a jak spravovat Azure AD Connect | Dokumentace Microsoftu'
description: Zjistěte, jak rozšířit výchozí konfigurace a provozní úlohy pro Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 23ab8e60df81fc3c20068fe819e98216b16cad72
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490324"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Další kroky a jak spravovat Azure AD Connect
Přizpůsobení služby Azure Active Directory (Azure AD) Connect pro splnění potřeb vaší organizace a požadavky pomocí provozní postupy v tomto článku.  

## <a name="add-additional-sync-admins"></a>Přidat další synchronizace správce
Ve výchozím nastavení budou moct spravovat nainstalované synchronizační modul jenom uživatel, který nebyl instalace a místní správci. Pro další uživatele, bude moct používat a spravovat synchronizační modul vyhledejte skupinu s názvem ADSyncAdmins na místním serveru a přidat je do této skupiny.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Přiřazení licencí uživatelům Azure AD Premium a Enterprise Mobility Suite
Teď, když uživatelé byly synchronizovány do cloudu, budete muset přiřaďte jim licence, takže můžete začít využívat cloudové aplikace, jako je Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Přiřadit k Azure AD Premium nebo Enterprise Mobility Suite licence

1. Přihlaste se k webu Azure portal jako správce.
2. Vlevo vyberte možnost **Active Directory**.
3. Na **služby Active Directory** stránce, poklikejte na adresář, který obsahuje uživatele, kterou chcete nastavit.
4. V horní části stránky adresáře vyberte možnost **Licence**.
5. Na **licence** stránce **Active Directory Premium** nebo **Enterprise Mobility Suite**a potom klikněte na tlačítko **přiřadit**.
6. V dialogovém okně vyberte uživatele, kterým chcete přiřadit licence, a potom změny uložte kliknutím na ikonu zaškrtnutí.

## <a name="verify-the-scheduled-synchronization-task"></a>Ověření úlohy naplánované synchronizace
Pomocí webu Azure portal a zkontrolujte stav synchronizace.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Ověření úlohy naplánované synchronizace
1. Přihlaste se k webu Azure portal jako správce.
2. Vlevo vyberte možnost **Active Directory**.
3. Na **služby Active Directory** stránce, poklikejte na adresář, který obsahuje uživatele, kterou chcete nastavit.
4. V horní části stránky adresáře vyberte **integrace adresáře**.
5. V části **integraci s místní službou active directory**, poznamenejte si čas poslední synchronizace.

<center>![Čas synchronizace adresáře](./media/how-to-connect-post-installation/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>Spuštění úlohy naplánované synchronizace
Pokud je potřeba spustit úlohu synchronizace, můžete to provést opětovným spuštěním Průvodce Azure AD Connect.  Budete muset zadat svoje přihlašovací údaje Azure AD.  V průvodci vyberte **přizpůsobit možnosti synchronizace** úloh a klikněte na tlačítko **Další** procházení průvodce. Na konci, ujistěte se, že **spustit proces synchronizace ihned po dokončení počáteční konfigurace** je zaškrtnuté políčko.

<center>![Spustit synchronizaci](./media/how-to-connect-post-installation/startsynch.png)</center>

Další informace o Plánovač synchronizace Azure AD Connect najdete v tématu [Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Další úkoly, které jsou k dispozici ve službě Azure AD Connect
Po počáteční instalaci sady Azure AD Connect můžete vždy spustíte průvodce znovu z místní služby Azure AD Connect úvodní stránky nebo plochy.  Můžete si všimnout, že průvodce znovu projít poskytuje několik nových možností ve formě další úkoly.  

Následující tabulka obsahuje souhrn těchto úloh a stručný popis jednotlivých úkolů.

![Seznam dalších úloh](./media/how-to-connect-post-installation/addtasks.png)

| Další úlohy | Popis |
| --- | --- |
| **Zobrazit vybraný scénář** |Zobrazení vašeho aktuálního řešení Azure AD Connect.  To zahrnuje obecných nastavení, synchronizaci adresářů a nastavení synchronizace. |
| **Přizpůsobit možnosti synchronizace** |Změňte aktuální konfiguraci, jako je přidání další doménové struktury služby Active Directory ke konfiguraci nebo povolení možnosti synchronizace, jako je například uživatel, skupina, zařízení nebo zpětný zápis hesla. |
| **Zapnout pracovní režim** |Informace o fázi, který není synchronizován okamžitě a nelze exportovat do služby Azure AD nebo místní služby Active Directory.  Pomocí této funkce můžete zobrazit náhled synchronizace předtím, než k nim dojde. |

## <a name="next-steps"></a>Další postup
Další informace o [integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).
