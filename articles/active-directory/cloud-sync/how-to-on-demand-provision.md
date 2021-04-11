---
title: Zřizování na vyžádání v Azure AD Connect synchronizace cloudu
description: Tento článek popisuje, jak pomocí funkce synchronizace cloudu Azure AD Connect otestovat změny konfigurace.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554271"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Zřizování na vyžádání v Azure AD Connect synchronizace cloudu

Pomocí funkce Cloud Sync služby Azure Active Directory (Azure AD) Connect můžete testovat změny konfigurace tím, že tyto změny použijete u jednoho uživatele. Toto zřizování na vyžádání pomáhá ověřit a ověřit, zda byly změny provedené v konfiguraci správně aplikovány a zda jsou správně synchronizovány do Azure AD.  

> [!IMPORTANT] 
> Když použijete zřizování na vyžádání, nepoužijí se pro vybraného uživatele filtry oborů. Můžete použít zřizování na vyžádání pro uživatele, kteří jsou mimo zadané organizační jednotky.

## <a name="validate-a-user"></a>Ověření uživatele
Pokud chcete použít zřizování na vyžádání, postupujte podle těchto kroků:

1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
2.  Vyberte **Azure AD Connect**.
3.  Vyberte **Spravovat cloudovou synchronizaci**.

    ![Snímek obrazovky zobrazující odkaz pro správu synchronizace cloudu](media/how-to-install/install-6.png)
4. V části **Konfigurace** vyberte svou konfiguraci.
5. V části **ověřit** vyberte tlačítko **zřídit uživatele** . 

   ![Snímek obrazovky, který zobrazuje tlačítko pro zřízení uživatele.](media/how-to-on-demand-provision/on-demand-2.png)

6. Na obrazovce **zřizování na vyžádání** zadejte rozlišující jméno uživatele a klikněte na tlačítko **zřídit** .  
 
   ![Snímek obrazovky, který zobrazuje uživatelské jméno a tlačítko pro zřízení.](media/how-to-on-demand-provision/on-demand-3.png)
7. Po dokončení zřizování se zobrazí obrazovka po úspěchu se čtyřmi zelenými značkami zaškrtnutí. Všechny chyby jsou zobrazeny vlevo.

   ![Snímek obrazovky, který ukazuje úspěšné zřízení.](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>Získat podrobnosti o zřizování
Nyní se můžete podívat na informace o uživateli a zjistit, jestli byly provedeny změny provedené v konfiguraci. Zbývající část tohoto článku popisuje jednotlivé oddíly, které se zobrazí v podrobnostech o úspěšné synchronizaci uživatele.

### <a name="import-user"></a>Importovat uživatele
Část **Import uživatele** poskytuje informace o uživateli, který byl importován ze služby Active Directory. Tento uživatel vypadá před tím, než se zřídí do služby Azure AD. Chcete-li zobrazit tyto informace, vyberte odkaz **Zobrazit podrobnosti** .

![Snímek obrazovky s tlačítkem pro zobrazení podrobností o importovaném uživateli](media/how-to-on-demand-provision/on-demand-5.png)

Pomocí těchto informací můžete zobrazit různé atributy (a jejich hodnoty), které byly naimportovány. Pokud jste vytvořili mapování vlastního atributu, můžete tady zobrazit jeho hodnotu.

![Snímek obrazovky zobrazující podrobnosti o uživateli](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Zjistit, jestli je uživatel v oboru
Část určení toho, jestli **je uživatel v oboru** , poskytuje informace o tom, jestli je uživatel, který byl naimportovaný do Azure AD, v oboru. Chcete-li zobrazit tyto informace, vyberte odkaz **Zobrazit podrobnosti** .

![Snímek obrazovky s tlačítkem pro zobrazení podrobností o oboru uživatele](media/how-to-on-demand-provision/on-demand-7.png)

Pomocí těchto informací můžete zjistit, jestli je uživatel v oboru.

![Snímek obrazovky zobrazující podrobnosti o rozsahu uživatele](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Odpovídá uživateli mezi zdrojovým a cílovým systémem
Oddíl **Shoda uživatele mezi zdrojovým a cílovým systémem** poskytuje informace o tom, jestli uživatel už ve službě Azure AD existuje a jestli se k němu má připojit místo zřízení nového uživatele. Chcete-li zobrazit tyto informace, vyberte odkaz **Zobrazit podrobnosti** .

![Snímek obrazovky s tlačítkem pro zobrazení podrobností o odpovídajícím uživateli](media/how-to-on-demand-provision/on-demand-8.png)

Pomocí těchto informací můžete zjistit, zda byla nalezena shoda nebo zda bude vytvořen nový uživatel.

![Snímek obrazovky, který zobrazuje informace o uživateli.](media/how-to-on-demand-provision/on-demand-11.png)

Pro porovnání podrobností se zobrazí zpráva s jednou ze tří následujících operací:
- **Vytvořit**: ve službě Azure AD se vytvoří uživatel.
- **Aktualizace**: uživatel je aktualizovaný na základě změny provedené v konfiguraci.
- **Odstranit**: uživatel je odebraný z Azure AD.

V závislosti na typu operace, kterou jste provedli, se tato zpráva bude lišit.

### <a name="perform-action"></a>Provést akci
Oddíl **provést akci** poskytuje informace o uživateli, který se zřídil nebo exportovali do služby Azure AD po použití konfigurace. Uživatel to vypadá po zřízení do Azure AD. Chcete-li zobrazit tyto informace, vyberte odkaz **Zobrazit podrobnosti** .

![Snímek obrazovky s tlačítkem pro zobrazení podrobností o provedené akci](media/how-to-on-demand-provision/on-demand-9.png)

Pomocí těchto informací můžete zobrazit hodnoty atributů po použití konfigurace. Vypadají podobně jako při importu, nebo se liší? Byla konfigurace úspěšně použita?  

Tento proces umožňuje sledovat transformaci atributů při pohybu přes Cloud a do tenanta služby Azure AD.

![Snímek obrazovky zobrazující Podrobnosti trasovacího atributu](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Další kroky 

- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)
- [Instalace Azure AD Connect synchronizace cloudu](how-to-install.md)
 