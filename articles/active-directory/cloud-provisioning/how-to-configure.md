---
title: Azure AD Connect zřazování nové konfigurace agenta
description: Tento článek popisuje, jak nainstalovat zřizování cloudu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620978"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Vytvoření nové konfigurace pro cloudové zřizování Azure AD Connect

Po instalaci agenta se musíte přihlásit k portálu Azure a nakonfigurovat azure active directory (Azure AD) connect cloud zřizování. Chcete-li agenta povolit, postupujte takto.

## <a name="configure-provisioning"></a>Konfigurace zřizování
Chcete-li konfigurovat zřizování, postupujte takto.

1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
1.  Vyberte **Azure AD Connect**.
1.  Vyberte **Spravovat zřizování (náhled).**

    ![Správa zřizování (náhled)](media/how-to-configure/manage1.png)

1.  Vyberte **nová konfigurace**.
1.  Na konfigurační obrazovce je předem vyplněná místní doména.
1.  Zadejte **e-mail s oznámením**. Tento e-mail bude upozorněn, když zřizování není v pořádku.
1.  Přesuňte volič na **Povolit**a vyberte **Uložit**.

    ![Zřizování Azure AD (preview)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Zřizování rozsahu pro konkrétní uživatele a skupiny
Agenta můžete určit tak, aby synchronizoval konkrétní uživatele a skupiny pomocí místních skupin služby Active Directory nebo organizačních jednotek. Skupiny a organizační jednotky nelze konfigurovat v rámci konfigurace. 

1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
1.  Vyberte **Azure AD Connect**.
1.  Vyberte **Spravovat zřizování (náhled).**
1.  V části **Konfigurace**vyberte konfiguraci.

    ![Oddíl Konfigurace](media/how-to-configure/scope1.png)

1.  V části **Konfigurovat**vyberte **Možnost Všichni uživatelé,** chcete-li změnit rozsah pravidla konfigurace.

    ![Možnost Všichni uživatelé](media/how-to-configure/scope2.png)

1. Na pravé straně můžete změnit obor tak, aby zahrnoval pouze skupiny zabezpečení. Zadejte rozlišující název skupiny a vyberte **Přidat**.

    ![Vybraná možnost skupin zabezpečení](media/how-to-configure/scope3.png)

1.  Nebo můžete změnit obor tak, aby zahrnoval pouze určité organizační jednotky. Vyberte **Hotovo** a **Uložit**.  
2.  Po změně oboru byste měli [restartovat zřizování](#restart-provisioning) a zahájit okamžitou synchronizaci změn.

    ![Možnost vybraných organizačních jednotek](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Restartovat zřizování 
Pokud nechcete čekat na další naplánované spuštění, aktivujte spuštění zřizování pomocí tlačítka **Restartzování.** 
1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
1.  Vyberte **Azure AD Connect**.
1.  Vyberte **Spravovat zřizování (náhled).**
1.  V části **Konfigurace**vyberte konfiguraci.

    ![Výběr konfigurace pro restartování zřizování](media/how-to-configure/scope1.png)

1.  V horní části vyberte **Restartovat zřizování**.

## <a name="remove-a-configuration"></a>Odebrání konfigurace
Chcete-li odstranit konfiguraci, postupujte takto.

1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
1.  Vyberte **Azure AD Connect**.
1.  Vyberte **Spravovat zřizování (náhled).**
1.  V části **Konfigurace**vyberte konfiguraci.

    ![Výběr konfigurace pro odebrání konfigurace](media/how-to-configure/scope1.png)

1.  V horní části obrazovky konfigurace vyberte **Odstranit**.

    ![Tlačítko Odstranit](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Před odstraněním konfigurace není žádné potvrzení. Před výběrem možnosti **Odstranit**se ujistěte, že se jedná o akci, kterou chcete provést.


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
