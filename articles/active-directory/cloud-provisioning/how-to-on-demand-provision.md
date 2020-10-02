---
title: Zřizování Azure AD Connectho cloudu při zřizování na vyžádání
description: Tento článek popisuje funkci zřizování na vyžádání.
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
ms.openlocfilehash: 72623d69f9fb0cbc68758a362bb977e5e02d2c00
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637099"
---
# <a name="azure-ad-connect-cloud-provisioning-on-demand-provisioning"></a>Zřizování Azure AD Connectho cloudu při zřizování na vyžádání

Azure AD Connect zřízení cloudu zavedlo novou funkci, která vám umožní otestovat změny konfigurace, a to použitím těchto změn u jednoho uživatele.  Pomocí této služby můžete ověřit a ověřit, zda byly změny provedené v konfiguraci správně použity a zda jsou správně synchronizovány do Azure AD.  

> [!IMPORTANT] 
> Když použijete zřizování na vyžádání, nepoužijí se pro vybraného uživatele filtry oborů.  To znamená, že můžete použít zřizování na vyžádání pro uživatele, kteří jsou mimo určené organizační jednotky.


## <a name="using-on-demand-provisioning"></a>Použití zřizování na vyžádání
Chcete-li použít novou funkci, postupujte podle následujících kroků.


1.  V Azure Portal vyberte **Azure Active Directory**.
2.  Vyberte **Azure AD Connect**.
3.  Vyberte **Spravovat zřizování**.

    ![Spravovat zřizování](media/how-to-configure/manage1.png)
4. V části **Konfigurace**vyberte svou konfiguraci.
5. V části **ověřit** klikněte na tlačítko **zřídit uživatele** . 

 ![Zřídit uživatele](media/how-to-on-demand-provision/on-demand2.png)

6. Na obrazovce zřizování na vyžádání.  Zadejte **rozlišující jméno** uživatele a klikněte na tlačítko **zřídit** .  
 
 ![Zřizování na vyžádání](media/how-to-on-demand-provision/on-demand3.png)
7. Po dokončení by se měla zobrazit obrazovka úspěšná a 4 zelená zaškrtávací políčka, která označují úspěšné zřízení.  Vlevo se zobrazí všechny chyby.

  ![Success](media/how-to-on-demand-provision/on-demand4.png)

Nyní se můžete podívat na uživatele a zjistit, zda byly provedeny změny provedené v konfiguraci.  Zbývající část tohoto dokumentu popisuje jednotlivé oddíly, které se zobrazí v podrobnostech o úspěšné synchronizaci uživatele.

## <a name="import-user-details"></a>Importovat podrobnosti o uživateli
V této části najdete informace o uživateli, který se importoval ze služby Active Directory.  To znamená, že uživatel vypadá předtím, než se zřídí do Azure AD.  Kliknutím na odkaz **Zobrazit podrobnosti** zobrazíte tyto informace.

![Importovat uživatele](media/how-to-on-demand-provision/on-demand5.png)

Pomocí těchto informací můžete zobrazit různé atributy a jejich hodnoty, které byly naimportovány.  Pokud jste vytvořili mapování vlastního atributu, budete moct zobrazit jeho hodnotu.
![Importovat podrobnosti o uživateli](media/how-to-on-demand-provision/on-demand6.png)

## <a name="determine-if-user-is-in-scope-details"></a>Určení, jestli se uživatel nachází v podrobnostech o oboru
Tato část poskytuje informace o tom, jestli je uživatel, který se importoval do služby Azure AD, v oboru.  Kliknutím na odkaz **Zobrazit podrobnosti** zobrazíte tyto informace.

![Obor uživatele](media/how-to-on-demand-provision/on-demand7.png)

Pomocí těchto informací můžete zobrazit další informace o oboru uživatelů.

![Podrobnosti o oboru uživatele](media/how-to-on-demand-provision/on-demand10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>Porovnává uživatele mezi podrobnostmi zdrojového a cílového systému
Tato část poskytuje informace o tom, jestli uživatel ve službě Azure AD již existuje a že se k němu připojit místo zřízení nového uživatele.  Kliknutím na odkaz **Zobrazit podrobnosti** zobrazíte tyto informace.
![Zobrazení podrobností](media/how-to-on-demand-provision/on-demand8.png)

Pomocí těchto informací můžete zjistit, zda byla nalezena shoda nebo zda bude vytvořen nový uživatel.

![Údaje uživatele](media/how-to-on-demand-provision/on-demand11.png)

U odpovídajícího podrobností se zobrazí zpráva s jednou ze tří následujících operací.  Jsou to tyto:
- Vytvoření – ve službě Azure AD se vytvoří uživatel.
- Aktualizace – uživatel je aktualizovaný na základě změny provedené v konfiguraci.
- Odstranění – uživatel se odebere z Azure AD.

V závislosti na typu operace, kterou jste provedli, se tato zpráva bude lišit.

## <a name="perform-action-details"></a>Provést podrobnosti akce
V této části najdete informace o uživateli, který se zřídil nebo exportovali do služby Azure AD po použití konfigurace.  Uživatel to vypadá, když se zřídí do Azure AD.  Kliknutím na odkaz **Zobrazit podrobnosti** zobrazíte tyto informace.
![Provést podrobnosti akce](media/how-to-on-demand-provision/on-demand9.png)

Pomocí těchto informací můžete zobrazit hodnoty atributů po použití konfigurace.  Vypadají podobně jako při importu nebo se liší?  Platí konfigurace úspěšně?  

Tato operace vám umožní trasovat transformaci atributů při pohybu přes Cloud a do tenanta Azure AD.

![Trace – atribut](media/how-to-on-demand-provision/on-demand12.png)

## <a name="next-steps"></a>Další kroky 

- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
- [Jak nainstalovat Azure AD Connect zřizování cloudu](how-to-install.md)
 