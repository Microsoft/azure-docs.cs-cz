---
title: Nová konfigurace agenta Azure AD Connect synchronizace cloudu
description: Tento článek popisuje, jak nainstalovat cloudovou synchronizaci.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffdd27b67a122f82cc5fdb5568f11c85387955e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660792"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Vytvoří novou konfiguraci pro Azure AD Connect synchronizaci cloudu.

Po instalaci agenta pro zřizování Azure AD Connect se musíte přihlásit k Azure Portal a nakonfigurovat ho. Chcete-li povolit agenta, postupujte podle těchto kroků.

## <a name="configure-provisioning"></a>Konfigurace zřizování
Pokud chcete nakonfigurovat zřizování, postupujte podle těchto kroků.

 1. V Azure Portal vyberte **Azure Active Directory**
 2. Vyberte **Azure AD Connect**.
 3. Vyberte **Spravovat cloudovou synchronizaci**.

 ![Spravovat zřizování](media/how-to-install/install-6.png)
 
 4. Vyberte **Nová konfigurace**.
 5. Na obrazovce konfigurace vyberte doménu a určete, jestli se má povolit synchronizace hodnot hash hesel.  Klikněte na **vytvořit**.  
 
 ![Vytvořit novou konfiguraci](media/how-to-configure/configure-1.png)


 6.  Otevře se obrazovka upravit konfiguraci zřizování.

   ![Upravit konfiguraci](media/how-to-configure/con-1.png)

 7. Zadejte **e-mailové oznámení**. Tento e-mail se upozorní, když zřizování není v pořádku.  Doporučuje se **zabránit nechtěnému odstranění** a nastavit **prahovou hodnotu náhodného odstranění** na číslo, na které chcete být upozorňováni.  Další informace najdete v části [náhodné odstranění](#accidental-deletions) .
 8. Přesuňte selektor, který chcete povolit, a vyberte Uložit.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Zřizování oboru pro konkrétní uživatele a skupiny
Můžete určit, že má agent synchronizovat konkrétní uživatele a skupiny pomocí místních skupin služby Active Directory nebo organizačních jednotek. V rámci konfigurace nemůžete konfigurovat skupiny a organizační jednotky. 

 1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
 2. Vyberte **Azure AD Connect**.
 3. Vyberte **Spravovat cloudovou synchronizaci**.
 4. V části **Konfigurace** vyberte svou konfiguraci.

 ![Konfigurační oddíl](media/how-to-configure/scope-1.png)
 
 5. V části **Konfigurovat** vyberte **upravit filtry oborů** a změňte rozsah pravidla konfigurace.
 ![Upravit obor](media/how-to-configure/scope-3.png)
 7. Na pravé straně můžete změnit obor.  Po dokončení klikněte na **Hotovo**  a **Uložit** .
 8. Po změně oboru byste měli [restartovat zřizování](#restart-provisioning) a zahájit okamžitou synchronizaci změn.

## <a name="attribute-mapping"></a>Mapování atributů
Azure AD Connect Cloud Sync umožňuje snadno mapovat atributy mezi místními objekty uživatelů nebo skupin a objekty ve službě Azure AD.  Výchozí mapování atributů můžete přizpůsobit podle potřeb vaší firmy. Takže můžete změnit nebo odstranit existující mapování atributů nebo vytvořit nová mapování atributů.  Další informace najdete v tématu [mapování atributů](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Zřizování na vyžádání
Azure AD Connect synchronizace cloudu umožňuje testovat změny konfigurace tím, že se tyto změny použijí pro jednoho uživatele nebo skupinu.  Pomocí této služby můžete ověřit a ověřit, zda byly změny provedené v konfiguraci správně použity a zda jsou správně synchronizovány do Azure AD.  Další informace najdete v tématu [zřizování na vyžádání](how-to-on-demand-provision.md).

## <a name="accidental-deletions"></a>Náhodné odstranění
Funkce náhodného odstranění je navržená tak, aby vás chránila před nechtěnými změnami konfigurace a změnami v místním adresáři, které by mohly ovlivnit mnoho uživatelů a skupin.  Tato funkce umožňuje:

- Nakonfigurujte schopnost zabránit náhodným odstraněním automaticky. 
- Nastavit počet objektů (prahová hodnota), po jejichž uplynutí bude konfigurace platit 
- Nastavte e-mailovou adresu oznámení, aby se mohla zobrazit e-mailové oznámení, když se příslušná úloha synchronizace umístí do karantény pro tento scénář. 

Další informace najdete v tématu [náhodné odstranění](how-to-accidental-deletes.md) .

## <a name="quarantines"></a>Umístí
Synchronizace cloudu monitoruje stav konfigurace a umisťuje objekty, které nejsou v pořádku, do karantény. Pokud se většina nebo všechna volání prováděná na cílovém systému konzistentně nezdařila kvůli chybě, například neplatné přihlašovací údaje správce, úloha synchronizace je označena jako v karanténě.  Další informace najdete v části řešení potíží na [karanténách](how-to-troubleshoot.md#provisioning-quarantined-problems).

## <a name="restart-provisioning"></a>Restartovat zřizování 
Pokud nechcete čekat na další naplánované spuštění, aktivujte zřizování spuštěním pomocí tlačítka pro **restartování** . 
 1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
 2. Vyberte **Azure AD Connect**.
 3.  Vyberte **Spravovat cloudovou synchronizaci**.
 4. V části **Konfigurace** vyberte svou konfiguraci.

   ![Výběr konfigurace pro restartování zřizování](media/how-to-configure/scope-1.png)

 5. V horní části vyberte **restartovat zřizování**.

## <a name="remove-a-configuration"></a>Odebrat konfiguraci
Pokud chcete konfiguraci odstranit, postupujte podle těchto kroků.

 1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
 2. Vyberte **Azure AD Connect**.
 3. Vyberte **Spravovat cloudovou synchronizaci**.
 4. V části **Konfigurace** vyberte svou konfiguraci.
   
   ![Výběr konfigurace pro odebrání konfigurace](media/how-to-configure/scope-1.png)

 5. V horní části obrazovky konfigurace vyberte **Odstranit**.

>[!IMPORTANT]
>Před odstraněním konfigurace se nejedná o žádné potvrzení. Ujistěte se, že se jedná o akci, kterou chcete provést před výběrem možnosti **Odstranit**.


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)
