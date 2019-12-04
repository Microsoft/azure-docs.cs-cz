---
title: Konfigurace nového agenta Azure AD Connect zřízení cloudu
description: Toto téma popisuje, jak nainstalovat cloudové zřizování.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956962503dcb3a6355d455ff96744143c5965d1e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794299"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Azure AD Connect nová konfigurace zřizování cloudu

Jakmile agenta nainstalujete, musíte se přihlásit k Azure Portal a nakonfigurovat zřizování.  K povolení agenta použijte následující postup.

## <a name="configure-provisioning"></a>Konfigurace zřizování
Při konfiguraci zřizování použijte následující postup:

1.  Na portálu Azure AD klikněte na **Azure Active Directory**
2.  Klikněte na **Azure AD Connect**
3.  Vyberte **Spravovat zřizování (Preview)** 
![](media/how-to-configure/manage1.png)

4.  Klikněte na **Nová konfigurace**.
5.  Na obrazovce konfigurace se předběžně vyplní místní doména.
6. Zadejte **e-mailové oznámení**. Tento e-mail bude upozorněn. 
7. Když zřizování není v pořádku.  
8. Přesuňte selektor, který chcete **Povolit** , a klikněte na **Uložit**.
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>Zřizování oboru pro konkrétní uživatele a skupiny
Pokud chcete určit obor agenta tak, aby synchronizoval jenom konkrétní uživatele a skupiny, můžete to udělat. Můžete určit rozsah pomocí místních skupin AD nebo organizačních jednotek. V rámci konfigurace nemůžete konfigurovat skupiny a organizační jednotky. 

1.  Na portálu Azure AD klikněte na **Azure Active Directory**
2.  Klikněte na **Azure AD Connect**
3.  Vyberte **Spravovat zřizování (Preview)** .
4.  V části **Konfigurace** klikněte na konfiguraci.  
![](media/how-to-configure/scope1.png)

5.  V části **Konfigurovat**vyberte možnost **Všichni uživatelé** a změňte rozsah pravidla konfigurace.
![](media/how-to-configure/scope2.png)

6. Napravo můžete změnit obor tak, aby zahrnoval jenom skupiny zabezpečení, a to zadáním rozlišujícího názvu skupiny a kliknutím na **Přidat**.
![](media/how-to-configure/scope3.png)

7. Nebo ho změňte tak, aby zahrnoval pouze konkrétní organizační jednotky. Klikněte na **Hotovo** a **Uložit**.
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Restartovat zřizování 
Pokud nechcete čekat na další naplánované spuštění, můžete aktivovat spuštění pomocí tlačítka pro restartování. 
1.  Na portálu Azure AD klikněte na **Azure Active Directory**
2.  Klikněte na **Azure AD Connect**
3.  Vyberte **Spravovat zřizování (Preview)** .
4.  V části **Konfigurace** klikněte na konfiguraci.  
![](media/how-to-configure/scope1.png)

5.  V horní části klikněte na **restartovat zřizování**.

## <a name="removing-a-configuration"></a>Odebrání konfigurace
Pokud chcete odstranit konfiguraci, můžete to udělat pomocí následujících kroků.

1.  Na portálu Azure AD klikněte na **Azure Active Directory**
2.  Klikněte na **Azure AD Connect**
3.  Vyberte **Spravovat zřizování (Preview)** .
4.  V části **Konfigurace** klikněte na konfiguraci.  
![](media/how-to-configure/scope1.png)

5.  V horní části klikněte na **Odstranit**.
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Před odstraněním konfigurace se neprovádí žádné potvrzení, takže se ujistěte, že se jedná o akci, kterou chcete provést, než kliknete na **Odstranit**.


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
