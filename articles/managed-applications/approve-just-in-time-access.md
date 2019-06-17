---
title: Konfigurace a schvalovat přístup just-in-time pro spravované aplikace Azure
description: Popisuje, jak uživatele služby Azure Managed Applications schvalovat žádosti o just-in-time přístup ke spravované aplikaci.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 55fbf7292ab894cad3de3de9e96ddc96fe0b79b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481711"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Konfigurace a schvalovat přístup just-in-time pro spravované aplikace Azure

Jako příjemce spravované aplikace, nemusí být pohodlné poskytuje vydavatele permanentní přístup k spravovanou skupinu prostředků. Získáte větší kontrolu nad udělení přístupu ke spravovaným prostředkům, Azure Managed Applications poskytuje funkci s názvem přístup just-in-time (JIT), která je aktuálně ve verzi preview. Umožňuje schválit, kdy a jak dlouho vydavatel má přístup do skupiny prostředků. Požadovaná aktualizace během této doby můžete nastavit jako vydavatele, ale po za tu dobu vypršení platnosti přístupu vydavatele.

Pracovní postup pro udělení přístupu je:

1. Vydavatel přidá spravované aplikace na webu Marketplace a určuje, zda je k dispozici přístup JIT.

1. Během nasazení povolte přístup JIT pro vaši instanci spravované aplikace.

1. Po nasazení můžete změnit nastavení pro přístup JIT.

1. Vydavatel odešle žádost o přístup.

1. Tuto žádost schválit.

Tento článek se zaměřuje na akce, které pokud chcete povolit přístup JIT a schvalovat žádosti o provést příjemci. Další informace o publikování spravované aplikace s přístupem JIT, naleznete v tématu [žádat o přístup za běhu v Azure Managed Applications](request-just-in-time-access.md).

> [!NOTE]
> Použití just-in-time přístupu, musí mít [licence Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Povolit během nasazení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyhledejte položku marketplace pro spravované aplikace s JIT povoleno. Vyberte **Vytvořit**.

1. Při zadání hodnot pro nové spravované aplikace **konfigurace JIT** krok umožňuje povolit nebo zakázat přístup JIT pro spravované aplikace. Vyberte **Ano** pro **povolit přístup JIT**. Tato možnost je vybrána ve výchozím nastavení pro spravované aplikace, které jsou definované pomocí JIT povoleno na webu Marketplace.

   ![Konfigurace přístupu](./media/approve-just-in-time-access/configure-jit-access.png)

   Přístup JIT lze povolit pouze během nasazování. Pokud vyberete **ne**, vydavatel získá permanentní přístup pro spravovanou skupinu prostředků. Přístup JIT nelze povolit později.

1. Chcete-li změnit výchozí nastavení schvalování vyberte **přizpůsobení konfigurace JIT**.

   ![Přizpůsobení přístup](./media/approve-just-in-time-access/customize-jit-access.png)

   Spravované aplikace s JIT povoleno ve výchozím nastavení, má následující nastavení:

   * Režim schválení – automatické
   * Doba trvání maximální přístupu – 8 hodin
   * Schvalovatelé – none

   Když je režim schválení nastavený na **automatické**, schvalovatelů přijímat oznámení pro každý požadavek, ale jeho žádost se schválí automaticky. Pokud je nastavena na **ruční**schvalovatelů, dostanete oznámení pro každý požadavek a jeden z nich musí schválit.

   Maximální doba trvání aktivace určuje maximální množství času, kterou Vydavatel může požadovat pro přístup ke spravované skupině prostředků.

   Je schvalovatelů seznam uživatelů Azure Active Directory, které můžete schválit požadavků na přístup za běhu. Chcete-li přidat approver **přidejte schvalovatele** a vyhledejte uživatele.

   Po aktualizaci nastavení, vyberte **Uložit**.

## <a name="update-after-deployment"></a>Aktualizace po nasazení

Můžete změnit hodnoty jak žádosti o schválení. Ale pokud jste nepovolili přístup JIT během nasazování, nelze povolit ji později.

Chcete-li změnit nastavení pro nasazené spravované aplikace:

1. Na portálu vyberte aplikaci spravovat.

1. Vyberte **konfigurace JIT** a podle potřeby změňte nastavení.

   ![Změnit nastavení přístupu](./media/approve-just-in-time-access/change-settings.png)

1. Až budete hotovi, vyberte **Uložit**.

## <a name="approve-requests"></a>Schvalování žádostí

Pokud vydavatel požádá o přístup, budete upozorněni o žádosti. Můžete schvalovat žádosti o přístup JIT přímo prostřednictvím spravované aplikace nebo ve všech aplikacích spravovaných přes službu Azure AD Privileged Identity Management. Použití just-in-time přístupu, musí mít [licence Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

Schválit žádosti přes spravované aplikace:

1. Vyberte **přístup JIT** pro spravované aplikace a vyberte **schvalovat žádosti**.

   ![Schvalování žádostí](./media/approve-just-in-time-access/approve-requests.png)
 
1. Vyberte žádost o schválení.

   ![Vyberte žádost o](./media/approve-just-in-time-access/select-request.png)

1. Ve formuláři, zadejte důvod pro schválení a vyberte **schválit**.

Schválit žádosti přes Azure AD Privileged Identity Management:

1. Vyberte **všechny služby** a zahájit hledání **Azure AD Privileged Identity Management**. Vyberte ho z dostupných možností.

   ![Vyhledání služby](./media/approve-just-in-time-access/search.png)

1. Vyberte **schvalovat žádosti o**.

   ![Výběr možnosti schválit žádosti](./media/approve-just-in-time-access/select-approve-requests.png)

1. Vyberte **spravované aplikace Azure**a vyberte žádost o schválení.

   ![Vyberte žádosti](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Další postup

Další informace o publikování spravované aplikace s přístupem JIT, naleznete v tématu [žádat o přístup za běhu v Azure Managed Applications](request-just-in-time-access.md).
