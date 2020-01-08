---
title: Konfigurace nového agenta Azure AD Connect zřízení cloudu
description: Tento článek popisuje, jak nainstalovat cloudové zřizování.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 853950cf441007eac0170f32f28f2d9c16a71292
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549427"
---
# <a name="azure-ad-connect-cloud-provisioning-new-agent-configuration"></a>Konfigurace nového agenta Azure AD Connect zřízení cloudu

Po nainstalování agenta se budete muset přihlásit do Azure Portal a nakonfigurovat Azure Active Directory (Azure AD) Connect Cloud Provisioning. Chcete-li povolit agenta, postupujte podle těchto kroků.

## <a name="configure-provisioning"></a>Konfigurace zřizování
Pokud chcete nakonfigurovat zřizování, postupujte podle těchto kroků.

1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
1.  Vyberte **Azure AD Connect**.
1.  Vyberte **Spravovat zřizování (Preview)** .

    ![Spravovat zřizování (Preview)](media/how-to-configure/manage1.png)

1.  Vyberte **Nová konfigurace**.
1.  Na obrazovce konfigurace se předběžně vyplní místní doména.
1.  Zadejte **e-mailové oznámení**. Tento e-mail se upozorní, když zřizování není v pořádku.
1.  Přesuňte selektor, který chcete **Povolit**, a vyberte **Uložit**.

    ![Zřizování Azure AD (Preview)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Zřizování oboru pro konkrétní uživatele a skupiny
Můžete určit, že má agent synchronizovat konkrétní uživatele a skupiny pomocí místních skupin služby Active Directory nebo organizačních jednotek. V rámci konfigurace nemůžete konfigurovat skupiny a organizační jednotky. 

1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
1.  Vyberte **Azure AD Connect**.
1.  Vyberte **Spravovat zřizování (Preview)** .
1.  V části **Konfigurace**vyberte svou konfiguraci.

    ![Konfigurační oddíl](media/how-to-configure/scope1.png)

1.  V části **Konfigurovat**vyberte možnost **Všichni uživatelé** a změňte rozsah pravidla konfigurace.

    ![Možnost všichni uživatelé](media/how-to-configure/scope2.png)

1. Napravo můžete změnit obor tak, aby zahrnoval pouze skupiny zabezpečení. Zadejte rozlišující název skupiny a vyberte **Přidat**.

    ![Vybraná možnost skupin zabezpečení](media/how-to-configure/scope3.png)

1.  Můžete také změnit obor tak, aby zahrnoval pouze konkrétní organizační jednotky. Vyberte **Hotovo** a **Uložit**.

    ![Možnost vybraných organizačních jednotek](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Restartovat zřizování 
Pokud nechcete čekat na další naplánované spuštění, aktivujte zřizování spuštěním pomocí tlačítka pro **restartování** . 
1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
1.  Vyberte **Azure AD Connect**.
1.  Vyberte **Spravovat zřizování (Preview)** .
1.  V části **Konfigurace**vyberte svou konfiguraci.

    ![Výběr konfigurace pro restartování zřizování](media/how-to-configure/scope1.png)

1.  V horní části vyberte **restartovat zřizování**.

## <a name="remove-a-configuration"></a>Odebrat konfiguraci
Pokud chcete konfiguraci odstranit, postupujte podle těchto kroků.

1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
1.  Vyberte **Azure AD Connect**.
1.  Vyberte **Spravovat zřizování (Preview)** .
1.  V části **Konfigurace**vyberte svou konfiguraci.

    ![Výběr konfigurace pro odebrání konfigurace](media/how-to-configure/scope1.png)

1.  V horní části obrazovky konfigurace vyberte **Odstranit**.

    ![Tlačítko Odstranit](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Před odstraněním konfigurace se nejedná o žádné potvrzení. Ujistěte se, že se jedná o akci, kterou chcete provést před výběrem možnosti **Odstranit**.


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
