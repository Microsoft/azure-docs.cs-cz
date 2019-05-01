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
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571304"
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
3. Na levé straně vyberte **Azure AD Connect**
4. V horní části stránky mějte na paměti poslední synchronizace.

![Čas synchronizace adresáře](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Spuštění úlohy naplánované synchronizace
Pokud je potřeba spustit úlohu synchronizace, můžete to provést:

1. Dvakrát klikněte na zástupce na ploše Azure AD Connect spusťte průvodce.
2. Klikněte na **Konfigurovat**.
3. Na obrazovce úlohy, vyberte **přizpůsobit možnosti synchronizace** a klikněte na tlačítko **další**
4. Zadejte svoje přihlašovací údaje služby Azure AD.
5. Klikněte na **Další**. Klikněte na **Další**.  Klikněte na **Další**.
5.  Na **připravení konfigurovat** obrazovky, ujistěte se, že **po dokončení konfigurace spustit proces synchronizace** je zaškrtnuté políčko.
6.  Klikněte na **Konfigurovat**.

Další informace o Plánovač synchronizace Azure AD Connect najdete v tématu [Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Další úkoly, které jsou k dispozici ve službě Azure AD Connect
Po počáteční instalaci sady Azure AD Connect můžete vždy spustíte průvodce znovu z místní služby Azure AD Connect úvodní stránky nebo plochy.  Můžete si všimnout, že průvodce znovu projít poskytuje několik nových možností ve formě další úkoly.  

Následující tabulka obsahuje souhrn těchto úloh a stručný popis jednotlivých úkolů.

![Seznam dalších úloh](./media/how-to-connect-post-installation/addtasks2.png)

| Další úlohy | Popis |
| --- | --- |
|**Nastavení ochrany osobních údajů**|Zobrazte, jaké telemetrická data se sdílí s Microsoftem.|
|**Zobrazit aktuální konfiguraci**|Zobrazení vašeho aktuálního řešení Azure AD Connect.  To zahrnuje obecných nastavení, synchronizaci adresářů a nastavení synchronizace. |
| **Přizpůsobit možnosti synchronizace** |Změňte aktuální konfiguraci, jako je přidání další doménové struktury služby Active Directory ke konfiguraci nebo povolení možnosti synchronizace, jako je například uživatel, skupina, zařízení nebo zpětný zápis hesla. |
|**Konfigurovat možnosti zařízení**|Možnosti zařízení, které jsou k dispozici pro synchronizaci|
|**Aktualizovat schéma adresáře**|Umožňuje přidat nové objekty v místním adresáři pro synchronizaci|
|**Konfigurovat pracovní režim** |Informace o fázi, který není synchronizován okamžitě a nelze exportovat do služby Azure AD nebo místní služby Active Directory.  Pomocí této funkce můžete zobrazit náhled synchronizace předtím, než k nim dojde. |
|**Změnit přihlášení uživatele**|Změnit metodu ověřování, které uživatelé používají k přihlašování|
|**Správa federace**|Správa infrastruktury služby AD FS, prodloužit platnost certifikátů a přidání serverů služby AD FS|
|**Řešení problémů**|Pomoci při řešení potíží s Azure AD Connect|

## <a name="next-steps"></a>Další postup
Další informace o [integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).
