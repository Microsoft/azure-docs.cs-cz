---
title: 'Azure AD Connect: další kroky a Správa Azure AD Connect | Microsoft Docs'
description: Naučte se, jak můžete roztáhnout výchozí konfiguraci a provozní úlohy pro Azure AD Connect.
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
ms.topic: how-to
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d6689b4b67b8462e983ae9b111e0fbc60c422b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657119"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Další kroky a Správa Azure AD Connect
Použijte provozní postupy v tomto článku k přizpůsobení služby Azure Active Directory (Azure AD) k tomu, aby splňovaly potřeby a požadavky vaší organizace.  

## <a name="add-additional-sync-admins"></a>Přidat další Správce synchronizace
Ve výchozím nastavení můžou nainstalovaná synchronizační modul spravovat jenom uživatel, který měl instalaci a místní správci. Aby mohli uživatelé získat přístup k synchronizačnímu modulu a spravovat ho, vyhledejte skupinu s názvem ADSyncAdmins na místním serveru a přidejte je do této skupiny.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Přiřazení licencí k Azure AD Premium a uživatelům sady Enterprise Mobility Suite
Teď, když byli vaši uživatelé synchronizováni do cloudu, musíte jim přiřadit licenci, aby se mohli dostat k cloudovým aplikacím, jako je Microsoft 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Přiřazení licence Azure AD Premium nebo sady Enterprise Mobility Suite

1. Přihlaste se k Azure Portal jako správce.
2. Na levé straně vyberte **Active Directory**.
3. Na stránce **služby Active Directory** dvakrát klikněte na adresář, který obsahuje uživatele, které chcete nastavit.
4. V horní části stránky adresáře vyberte možnost **Licence**.
5. Na stránce **licence** vyberte **Active Directory Premium** nebo **Enterprise Mobility Suite**a pak klikněte na **přiřadit**.
6. V dialogovém okně vyberte uživatele, kterým chcete přiřadit licence, a potom změny uložte kliknutím na ikonu zaškrtnutí.

## <a name="verify-the-scheduled-synchronization-task"></a>Ověřte naplánovanou úlohu synchronizace.
Ke kontrole stavu synchronizace použijte Azure Portal.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Ověření úlohy naplánované synchronizace
1. Přihlaste se k Azure Portal jako správce.
2. Na levé straně vyberte **Active Directory**.
3. Na levé straně vyberte **Azure AD Connect**
4. V horní části stránky si všimněte poslední synchronizace.

![Čas synchronizace adresářů](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Spustí naplánovanou úlohu synchronizace.
Pokud potřebujete spustit úlohu synchronizace, můžete to udělat takto:

1. Spusťte Průvodce dvojitým kliknutím na zástupce Azure AD Connect Desktop.
2. Klikněte na **Konfigurovat**.
3. Na obrazovce úlohy vyberte **Možnosti přizpůsobení synchronizace** a klikněte na **Další** .
4. Zadejte svoje přihlašovací údaje služby Azure AD.
5. Klikněte na **Next** (Další). Klikněte na **Next** (Další).  Klikněte na **Next** (Další).
5.  Na obrazovce **připraveno ke konfiguraci** zajistěte, aby bylo zaškrtnuto políčko **spustit proces synchronizace po dokončení konfigurace** .
6.  Klikněte na **Konfigurovat**.

Další informace o plánovači Azure AD Connect Sync najdete v tématu [Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Další úlohy, které jsou k dispozici v Azure AD Connect
Po počáteční instalaci Azure AD Connect můžete Průvodce kdykoli znovu spustit z Azure AD Connect úvodní stránky nebo na ploše.  Všimnete si, že průvodce bude znovu poskytovat některé nové možnosti ve formě dalších úloh.  

Následující tabulka poskytuje souhrn těchto úkolů a stručný popis jednotlivých úloh.

![Seznam dalších úloh](./media/how-to-connect-post-installation/addtasks2.png)

| Další úloha | Description |
| --- | --- |
|**Nastavení ochrany osobních údajů**|Podívejte se, jaká data telemetrie se s Microsoftem sdílí.|
|**Zobrazit aktuální konfiguraci**|Podívejte se na vaše aktuální řešení Azure AD Connect.  To zahrnuje obecná nastavení, synchronizované adresáře a nastavení synchronizace. |
| **Přizpůsobení možností synchronizace** |Změňte aktuální konfiguraci, třeba přidání dalších doménových struktur služby Active Directory do konfigurace, nebo povolení možností synchronizace, jako je například uživatel, skupina, zařízení nebo zápis hesla. |
|**Konfigurace možností zařízení**|Možnosti zařízení, které jsou k dispozici pro synchronizaci|
|**Aktualizovat schéma adresáře**|Umožňuje přidat nové místní adresářové objekty pro synchronizaci.|
|**Konfigurovat pracovní režim** |Informace o fázi, která není hned synchronizována a není exportována do služby Azure AD ani do místní služby Active Directory.  Pomocí této funkce můžete zobrazit náhled synchronizací dřív, než k nim dojde. |
|**Změna přihlášení uživatele**|Změna metody ověřování, kterou uživatelé používají k přihlášení|
|**Správa federace**|Správa infrastruktury AD FS, obnovení certifikátů a přidání serverů AD FS|
|**Řešení potíží**|Pomoc s řešením problémů s Azure AD Connect|

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [integraci místních identit s Azure Active Directory](whatis-hybrid-identity.md).
