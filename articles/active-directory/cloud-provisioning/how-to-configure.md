---
title: Konfigurace nového agenta Azure AD Connect zřízení cloudu
description: Tento článek popisuje, jak nainstalovat cloudové zřizování.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628879"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Vytvoření nové konfigurace pro Azure AD Connect cloudové zřizování

Po nainstalování agenta se budete muset přihlásit do Azure Portal a nakonfigurovat Azure Active Directory (Azure AD) Connect Cloud Provisioning. Chcete-li povolit agenta, postupujte podle těchto kroků.

## <a name="configure-provisioning"></a>Konfigurace zřizování
Pokud chcete nakonfigurovat zřizování, postupujte podle těchto kroků.

 1. V Azure Portal vyberte **Azure Active Directory**
 2. Vyberte **Azure AD Connect**.
 3. Vyberte **Spravovat zřizování**.

 ![Spravovat zřizování](media/how-to-configure/manage1.png)
 
 4. Vyberte **Nová konfigurace**.
 5. Na obrazovce konfigurace vyberte doménu a určete, jestli se má povolit synchronizace hodnot hash hesel.  Klikněte na **vytvořit**.  
 
 ![Vytvořit novou konfiguraci](media/how-to-configure/configure1.png)


 6.  Otevře se obrazovka upravit konfiguraci zřizování.

   ![Upravit konfiguraci](media/how-to-configure/configure2.png)

 7. Zadejte **e-mailové oznámení**. Tento e-mail se upozorní, když zřizování není v pořádku.
 8. Přesuňte selektor, který chcete povolit, a vyberte Uložit.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Zřizování oboru pro konkrétní uživatele a skupiny
Můžete určit, že má agent synchronizovat konkrétní uživatele a skupiny pomocí místních skupin služby Active Directory nebo organizačních jednotek. V rámci konfigurace nemůžete konfigurovat skupiny a organizační jednotky. 

 1.  V Azure Portal vyberte **Azure Active Directory**.
 2. Vyberte **Azure AD Connect**.
 3. Vyberte **Spravovat zřizování (Preview)**.
 4. V části **Konfigurace**vyberte svou konfiguraci.

 ![Konfigurační oddíl](media/how-to-configure/scope1.png)
 
 5. V části **Konfigurovat**vyberte **upravit filtry oborů** a změňte rozsah pravidla konfigurace.
 ![Upravit obor](media/how-to-configure/scope3.png)
 7. Na pravé straně můžete změnit obor.  Po dokončení klikněte na **Hotovo**  a **Uložit** .
 8. Po změně oboru byste měli [restartovat zřizování](#restart-provisioning) a zahájit okamžitou synchronizaci změn.

## <a name="attribute-mapping"></a>Mapování atributů
Cloudové zřizování Azure AD Connect umožňuje snadno mapovat atributy mezi místními objekty uživatelů nebo skupin a objekty ve službě Azure AD.  Výchozí mapování atributů můžete přizpůsobit podle potřeb vaší firmy. Takže můžete změnit nebo odstranit existující mapování atributů nebo vytvořit nová mapování atributů.  Další informace najdete v tématu [mapování atributů](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Zřizování na vyžádání
Cloudové zřizování Azure AD Connect umožňuje testovat změny konfigurace tím, že se tyto změny použijí pro jednoho uživatele nebo skupinu.  Pomocí této služby můžete ověřit a ověřit, zda byly změny provedené v konfiguraci správně použity a zda jsou správně synchronizovány do Azure AD.  Další informace najdete v tématu [zřizování na vyžádání](how-to-on-demand-provision.md).

## <a name="restart-provisioning"></a>Restartovat zřizování 
Pokud nechcete čekat na další naplánované spuštění, aktivujte zřizování spuštěním pomocí tlačítka pro **restartování** . 
 1.  V Azure Portal vyberte **Azure Active Directory**.
 2. Vyberte **Azure AD Connect**.
 3.  Vyberte **Spravovat zřizování (Preview)**.
 4. V části **Konfigurace**vyberte svou konfiguraci.

   ![Výběr konfigurace pro restartování zřizování](media/how-to-configure/scope1.png)

 5. V horní části vyberte **restartovat zřizování**.

## <a name="remove-a-configuration"></a>Odebrat konfiguraci
Pokud chcete konfiguraci odstranit, postupujte podle těchto kroků.

 1.  V Azure Portal vyberte **Azure Active Directory**.
 2. Vyberte **Azure AD Connect**.
 3. Vyberte **Spravovat zřizování (Preview)**.
 4. V části **Konfigurace**vyberte svou konfiguraci.
   
   ![Výběr konfigurace pro odebrání konfigurace](media/how-to-configure/scope1.png)

 5. V horní části obrazovky konfigurace vyberte **Odstranit**.

>[!IMPORTANT]
>Před odstraněním konfigurace se nejedná o žádné potvrzení. Ujistěte se, že se jedná o akci, kterou chcete provést před výběrem možnosti **Odstranit**.


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
