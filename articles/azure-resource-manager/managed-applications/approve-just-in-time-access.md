---
title: Schválení přístupu za běhu
description: Popisuje způsob, jakým zákazníci Azure Managed Applications schvalovat žádosti o přístup za běhu ke spravované aplikaci.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75651017"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Konfigurace a schválení přístupu za běhu pro Azure Managed Applications

Jako spotřebitel spravované aplikace možná nebudete mít jistotu, že vydavatel má trvalý přístup ke spravované skupině prostředků. Aby vám poskytoval větší kontrolu nad udělením přístupu ke spravovaným prostředkům, Azure Managed Applications poskytuje funkci s názvem přístup JIT (just-in-time), která je aktuálně ve verzi Preview. Umožňuje schválit, kdy a jak dlouho má Vydavatel přístup ke skupině prostředků. Vydavatel může během této doby provádět požadované aktualizace, ale po uplynutí této doby vyprší platnost přístupu vydavatele.

Pracovní postup pro udělení přístupu:

1. Vydavatel přidá do Marketplace spravovanou aplikaci a určí, že je k dispozici přístup JIT.

1. Během nasazení povolíte přístup JIT pro vaši instanci spravované aplikace.

1. Po nasazení můžete změnit nastavení přístupu JIT.

1. Vydavatel pošle žádost o přístup.

1. Žádost schválíte.

Tento článek se zaměřuje na akce, které příjemci převezmou, aby mohli povolit přístup JIT a schvalovat žádosti. Další informace o publikování spravované aplikace s přístupem JIT najdete v tématu [žádost o přístup za běhu v Azure Managed Applications](request-just-in-time-access.md).

> [!NOTE]
> Pokud chcete použít přístup za běhu, musíte mít [licenci Azure Active Directory P2](../../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Povolit během nasazení

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Najde položku Marketplace pro spravovanou aplikaci s povoleným JIT. Vyberte **Vytvořit**.

1. Když poskytujete hodnoty pro novou spravovanou aplikaci, krok **Konfigurace JIT** vám umožní povolit nebo zakázat přístup JIT pro spravovanou aplikaci. Pro **Povolení přístupu JIT** vyberte **Ano** . Tato možnost je ve výchozím nastavení vybraná pro spravované aplikace, které jsou definované s povolenou podporou JIT na webu Marketplace.

   ![Konfigurace přístupu](./media/approve-just-in-time-access/configure-jit-access.png)

   Přístup JIT můžete povolit jenom během nasazování. Pokud vyberete **ne**, vydavatel získá trvalý přístup ke spravované skupině prostředků. Nemůžete povolit přístup JIT později.

1. Chcete-li změnit výchozí nastavení schválení, vyberte **přizpůsobit konfiguraci JIT**.

   ![Přizpůsobení přístupu](./media/approve-just-in-time-access/customize-jit-access.png)

   Ve výchozím nastavení má spravovaná aplikace s povolenou hodnotou JIT následující nastavení:

   * Režim schválení – automatické
   * Maximální doba trvání přístupu – 8 hodin
   * Schvalovatelé – žádné

   Pokud je režim schválení nastaven na hodnotu **automaticky**, schvalovatelé obdrží oznámení pro každý požadavek, ale požadavek se automaticky schválí. Pokud je nastaveno na **Ruční**, schvalovatelé obdrží oznámení pro každý požadavek a jeden z nich musí schválit.

   Maximální doba trvání aktivace určuje maximální dobu, po kterou může vydavatel vyžadovat přístup ke spravované skupině prostředků.

   Seznam schvalovatelé je Azure Active Directory uživatelům, kteří mohou schvalovat požadavky na přístup JIT. Chcete-li přidat schvalovatele, vyberte **Přidat schvalovatele** a vyhledejte uživatele.

   Po aktualizaci nastavení vyberte **Uložit**.

## <a name="update-after-deployment"></a>Aktualizace po nasazení

Můžete změnit hodnoty pro způsob, jakým jsou požadavky schváleny. Pokud jste ale nepovolili přístup JIT během nasazení, nemůžete ho později povolit.

Změna nastavení nasazené spravované aplikace:

1. Na portálu vyberte Spravovat aplikaci.

1. Vyberte **Konfigurace JIT** a podle potřeby změňte nastavení.

   ![Změnit nastavení přístupu](./media/approve-just-in-time-access/change-settings.png)

1. Až budete hotovi, vyberte **Uložit**.

## <a name="approve-requests"></a>Schvalování žádostí

Když Vydavatel požaduje přístup, budete upozorněni na žádost. Žádosti o přístup JIT můžete schvalovat buď přímo prostřednictvím spravované aplikace, nebo ve všech spravovaných aplikacích prostřednictvím služby Azure AD Privileged Identity Management. Pokud chcete použít přístup za běhu, musíte mít [licenci Azure Active Directory P2](../../active-directory/privileged-identity-management/subscription-requirements.md).

Postup schválení žádostí prostřednictvím spravované aplikace:

1. Vyberte pro spravovanou aplikaci **přístup JIT** a vyberte **schvalovat žádosti**.

   ![Schvalování žádostí](./media/approve-just-in-time-access/approve-requests.png)
 
1. Vyberte žádost ke schválení.

   ![Vybrat požadavek](./media/approve-just-in-time-access/select-request.png)

1. Ve formuláři zadejte důvod schválení a vyberte **schválit**.

Postup při schvalování žádostí prostřednictvím Azure AD Privileged Identity Management:

1. Vyberte **všechny služby** a spusťte hledání **Azure AD Privileged Identity Management**. Vyberte ho z dostupných možností.

   ![Hledání služby](./media/approve-just-in-time-access/search.png)

1. Vyberte **schvalovat žádosti**.

   ![Vybrat schvalovat žádosti](./media/approve-just-in-time-access/select-approve-requests.png)

1. Vyberte **spravované aplikace Azure** a vyberte žádost ke schválení.

   ![Vybrat žádosti](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Další kroky

Další informace o publikování spravované aplikace s přístupem JIT najdete v tématu [žádost o přístup za běhu v Azure Managed Applications](request-just-in-time-access.md).
