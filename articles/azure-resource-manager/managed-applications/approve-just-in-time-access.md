---
title: Schválení přístupu za běhu
description: Popisuje, jak spotřebitelé spravovaných aplikací Azure schvalují žádosti o přístup ke spravované aplikaci za čase.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651017"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Konfigurace a schválení přístupu za čas pro spravované aplikace Azure

Jako příjemce spravované aplikace nemusí být pohodlné poskytování trvalého přístupu vydavatele ke spravované skupině prostředků. Abychom vám poskytli větší kontrolu nad udělováním přístupu ke spravovaným prostředkům, azure spravované aplikace poskytuje funkci nazvanou přístup za čas (JIT), která je aktuálně ve verzi Preview. Umožňuje schválit, kdy a jak dlouho má vydavatel přístup ke skupině prostředků. Vydavatel může během této doby provádět požadované aktualizace, ale po uplynutí této doby vyprší platnost přístupu vydavatele.

Pracovní tok pro udělení přístupu je:

1. Vydavatel přidá spravovanou aplikaci na tržiště a určuje, že přístup JIT je k dispozici.

1. Během nasazení povolíte přístup JIT pro vaši instanci spravované aplikace.

1. Po nasazení můžete změnit nastavení přístupu JIT.

1. Vydavatel odešle žádost o přístup.

1. Vy schvalujete žádost.

Tento článek se zaměřuje na akce, které spotřebitelé přijmou k povolení přístupu JIT a schválení požadavků. Informace o publikování spravované aplikace s přístupem JIT najdete [v tématu Požadavek na přístup za čas ve spravovaných aplikacích Azure](request-just-in-time-access.md).

> [!NOTE]
> Chcete-li použít přístup just-in-time, musíte mít [licenci Azure Active Directory P2](../../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Povolit během nasazení

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Najděte položku tržiště pro spravovanou aplikaci s povolenou JIT. Vyberte **Vytvořit**.

1. Při poskytování hodnot pro novou spravovanou aplikaci umožňuje krok **Konfigurace JIT** povolit nebo zakázat přístup JIT pro spravovanou aplikaci. Chcete-li **povolit přístup JIT,** vyberte **možnost Ano** . Tato možnost je vybrána ve výchozím nastavení pro spravované aplikace, které jsou definovány s JIT povoleno na trhu.

   ![Konfigurace přístupu](./media/approve-just-in-time-access/configure-jit-access.png)

   Přístup JIT můžete povolit pouze během nasazení. Pokud vyberete **možnost Ne**, získá vydavatel trvalý přístup ke spravované skupině prostředků. Přístup JIT nelze povolit později.

1. Chcete-li změnit výchozí nastavení schválení, vyberte **možnost Přizpůsobit konfiguraci JIT**.

   ![Přizpůsobení přístupu](./media/approve-just-in-time-access/customize-jit-access.png)

   Ve výchozím nastavení má spravovaná aplikace s povolenou JIT následující nastavení:

   * Schvalovací režim – automatický
   * Maximální doba přístupu – 8 hodin
   * Schvalovatelé – žádné

   Pokud je režim schvalování nastaven na **automatický**, schvalovatelé obdrží oznámení pro každou žádost, ale žádost je automaticky schválena. Pokud je nastavena na **ruční**, schvalovatelé obdrží oznámení pro každou žádost a jeden z nich musí schválit.

   Maximální doba trvání aktivace určuje maximální dobu, po kterou může vydavatel požádat o přístup ke skupině spravovaných prostředků.

   Seznam schvalovatelů jsou uživatelé služby Azure Active Directory, kteří mohou schvalovat žádosti o přístup JIT. Pokud chcete přidat schvalovatele, vyberte **Přidat schvalovatele** a vyhledejte uživatele.

   Po aktualizaci nastavení vyberte **Uložit**.

## <a name="update-after-deployment"></a>Aktualizovat po nasazení

Můžete změnit hodnoty pro způsob, jakým jsou požadavky schváleny. Pokud jste však během nasazení nepovolili přístup JIT, nemůžete jej povolit později.

Změna nastavení nasazené spravované aplikace:

1. Na portálu vyberte spravovat aplikaci.

1. Vyberte **konfiguraci JIT** a podle potřeby změňte nastavení.

   ![Změna nastavení přístupu](./media/approve-just-in-time-access/change-settings.png)

1. Až budete hotovi, vyberte **Uložit**.

## <a name="approve-requests"></a>Schvalování žádostí

Když vydavatel požádá o přístup, budete na ni upozorněni. Žádosti o přístup JIT můžete schválit buď přímo prostřednictvím spravované aplikace, nebo napříč všemi spravovanými aplikacemi prostřednictvím služby Azure AD Privileged Identity Management. Chcete-li použít přístup just-in-time, musíte mít [licenci Azure Active Directory P2](../../active-directory/privileged-identity-management/subscription-requirements.md).

Schválení žádostí prostřednictvím spravované aplikace:

1. Vyberte **JIT Access** pro spravovanou aplikaci a vyberte **Schválit požadavky**.

   ![Schvalování žádostí](./media/approve-just-in-time-access/approve-requests.png)
 
1. Vyberte požadavek ke schválení.

   ![Vybrat požadavek](./media/approve-just-in-time-access/select-request.png)

1. Ve formuláři uveďte důvod schválení a vyberte **schválit**.

Schválení žádostí prostřednictvím správy privilegovaných identit Azure AD:

1. Vyberte **Všechny služby** a začněte vyhledávat **správu privilegovaných identit Azure AD**. Vyberte ji z dostupných možností.

   ![Hledat službu](./media/approve-just-in-time-access/search.png)

1. Vyberte **schválit požadavky**.

   ![Vybrat žádosti o schválení](./media/approve-just-in-time-access/select-approve-requests.png)

1. Vyberte **spravované aplikace Azure**a vyberte požadavek ke schválení.

   ![Vybrat požadavky](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Další kroky

Informace o publikování spravované aplikace s přístupem JIT najdete [v tématu Požadavek na přístup za čas ve spravovaných aplikacích Azure](request-just-in-time-access.md).
