---
title: 'Azure AD Connect: Další kroky a jak spravovat Azure AD Connect | Dokumenty společnosti Microsoft'
description: Zjistěte, jak rozšířit výchozí konfiguraci a provozní úlohy pro Azure AD Connect.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261291"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Další kroky a jak spravovat Azure AD Connect
Pomocí provozních postupů v tomto článku můžete přizpůsobit azure active directory (Azure AD) Connect tak, aby vyhovovaly potřebám a požadavkům vaší organizace.  

## <a name="add-additional-sync-admins"></a>Přidání dalších správců synchronizace
Ve výchozím nastavení mohou nainstalovaný synchronizační modul spravovat pouze uživatel, který provedl instalaci, a místní správci. Aby měli další uživatelé přístup k synchronizačnímu modulu a mohli jej spravovat, vyhledejte skupinu s názvem ADSyncAdmins na místním serveru a přidejte je do této skupiny.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Přiřazení licencí uživatelům Sady Azure AD Premium a Enterprise Mobility Suite
Teď, když byli vaši uživatelé synchronizováni s cloudem, musíte jim přiřadit licenci, aby mohli začít s cloudovými aplikacemi, jako je Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Přiřazení licence sady Azure AD Premium nebo Enterprise Mobility Suite

1. Přihlaste se k portálu Azure jako správce.
2. Vlevo vyberte **položku Active Directory**.
3. Na stránce **Služby Active Directory** poklikejte na adresář s uživateli, které chcete nastavit.
4. V horní části stránky adresáře vyberte možnost **Licence**.
5. Na stránce Licence vyberte **položku** **Active Directory Premium** nebo Enterprise **Mobility Suite**a klepněte na tlačítko **Přiřadit**.
6. V dialogovém okně vyberte uživatele, kterým chcete přiřadit licence, a potom změny uložte kliknutím na ikonu zaškrtnutí.

## <a name="verify-the-scheduled-synchronization-task"></a>Ověření naplánované úlohy synchronizace
Pomocí portálu Azure zkontrolujte stav synchronizace.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Ověření naplánované úlohy synchronizace
1. Přihlaste se k portálu Azure jako správce.
2. Vlevo vyberte **položku Active Directory**.
3. Na levé straně vyberte **Azure AD Connect**
4. V horní části stránky si poznamenejte poslední synchronizaci.

![Čas synchronizace adresářů](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Spuštění naplánované úlohy synchronizace
Pokud potřebujete spustit úlohu synchronizace, můžete to provést takto:

1. Poklikáním na zástupce plochy Azure AD Connect spusťte průvodce.
2. Klikněte na **Konfigurovat**.
3. Na obrazovce Úkoly vyberte **možnosti přizpůsobení synchronizace** a klepněte na **tlačítko Další.**
4. Zadejte svoje přihlašovací údaje služby Azure AD.
5. Klikněte na **Další**. Klikněte na **Další**.  Klikněte na **Další**.
5.  Na obrazovce **Připraveno ke konfiguraci** se ujistěte, že je vybráno **pole Spustit proces synchronizace po dokončení konfigurace.**
6.  Klikněte na **Konfigurovat**.

Další informace o plánovači synchronizace Azure AD Connect najdete v tématu [Plánovač připojení Azure AD](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Další úkoly dostupné ve službě Azure AD Connect
Po počáteční instalaci služby Azure AD Connect můžete průvodce spustit znovu z úvodní stránky azure a připojení nebo zástupce na ploše.  Všimněte si, že procházení průvodcem znovu poskytuje některé nové možnosti ve formě dalších úkolů.  

Následující tabulka obsahuje souhrn těchto úkolů a stručný popis jednotlivých úkolů.

![Seznam dalších úkolů](./media/how-to-connect-post-installation/addtasks2.png)

| Další úkol | Popis |
| --- | --- |
|**Nastavení ochrany osobních údajů**|Zobrazení, jaká telemetrická data jsou sdílena se společností Microsoft.|
|**Zobrazit aktuální konfiguraci**|Zobrazení aktuálního řešení Azure AD Connect.  To zahrnuje obecné nastavení, synchronizované adresáře a nastavení synchronizace. |
| **Přizpůsobení možností synchronizace** |Změňte aktuální konfiguraci, jako je přidání dalších doménových struktur služby Active Directory do konfigurace nebo povolení možností synchronizace, jako je například zpětný zápis uživatele, skupiny, zařízení nebo hesla. |
|**Konfigurace možností zařízení**|Možnosti zařízení dostupné pro synchronizaci|
|**Aktualizovat schéma adresáře**|Umožňuje přidávat nové místní objekty adresáře pro synchronizaci.|
|**Konfigurace pracovního režimu** |Informace o fázi, která není okamžitě synchronizována a není exportována do služby Azure AD nebo místní služby Active Directory.  Pomocí této funkce můžete zobrazit náhled synchronizace dříve, než k nim dojde. |
|**Změna přihlášení uživatele**|Změna metody ověřování, kterou uživatelé používají k přihlášení|
|**Správa federace**|Správa infrastruktury služby AD FS, obnovení certifikátů a přidání serverů služby AD FS|
|**Řešení potíží**|Pomoc s řešením problémů s Azure AD Connect|

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [integraci místních identit pomocí služby Azure Active Directory](whatis-hybrid-identity.md).
