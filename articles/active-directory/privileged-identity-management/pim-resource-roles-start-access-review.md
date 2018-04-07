---
title: Spuštění kontrola přístupu v PIM prostředky Azure | Microsoft Docs
description: Vysvětluje, jak spustit kontrola přístupu v Privileged Identity Management pro prostředky Azure
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 61ed4e82e0b782b423668564dae6efb272967702
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---start-access-review"></a>Kontrola přístupu privileged Identity Management - prostředků role - Start
Přiřazení rolí stát "zastaralé", když uživatelé mají privilegovaný přístup, které už nepotřebují. Chcete-li snížit riziko spojené s přiřazení těchto zastaralých rolí, správci privilegované role by pravidelně zkontrolovat role, které mají uživatelé. Tento dokument popisuje kroky pro spuštění v Privileged Identity Management (PIM) kontrola přístupu k prostředkům Azure.

Na hlavní stránce aplikace PIM přejděte do:

* **Přístup k recenze** > **přidat**

![](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Po kliknutí na **přidat** tlačítko **vytvořit kontrola přístupu** otevře se okno. V tomto okně budete konfigurovat kontrola s názvem a časový limit, vyberte roli zkontrolovat a rozhodnout, který provede kontrola.

![](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Kontrola konfigurace
Pokud chcete vytvořit kontrola přístupu, potřebujete název a nastavit počáteční a koncové datum.

![Nakonfigurujte kontrolní – snímek obrazovky](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Ujistěte se, délka dostatečně dlouhé, aby se pro uživatele dokončit, protože se kontrola. Pokud dokončíte před datem ukončení, můžete vždy zastavit kontrola již v rané fázi.

### <a name="choose-a-role-to-review"></a>Vyberte role ke kontrole
Každý revize se zaměřuje na jen jednu roli. Pokud jste začali kontrola přístupu v okně konkrétní roli, musíte teď zvolte roli.

1. Přejděte na **Zkontrolujte členství v roli**
   
    ![Zkontrolujte členství v rolích – snímek obrazovky](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Vyberte jednu roli ze seznamu.

### <a name="decide-who-will-perform-the-review"></a>Rozhodněte, který provede kontrola
Existují tři možnosti pro provádění kontrolu. Kontrola můžete přiřadit někomu jinému k dokončení, můžete provést sami nebo můžete mít každý uživatel, zkontrolujte své vlastní přístup.

1. Vyberte jednu z možností:
   
   * **Vybraní uživatelé**: tuto možnost použijte, pokud si nejste jisti, který potřebuje přístup. Pomocí této možnosti můžete přiřadit kontrola prostředků vlastníka nebo správce skupiny pro dokončení.
   * **Přiřazené (vlastní)**: tuto možnost použijte, pokud chcete, aby uživatelé Zkontrolujte své vlastní přiřazení rolí.
   
2. Přejděte do **vyberte**
   
    ![Vyberte – snímek obrazovky](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Kontrola spuštění
Nakonec máte možnost, která vyžaduje, aby uživatelé zadali příslušný důvod. Pokud schválí jejich přístup. Pokud chcete přidat popis kontrola a vyberte **spustit**.

Zajistěte, aby dát uživatelům vědět, že je kontrola přístupu jim a zobrazit je [postup kontrola přístupu](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Spravovat kontrola přístupu
Průběh můžete sledovat, kontroloři dokončení jejich recenze v řídicím panelu PIM prostředky Azure, v části recenze přístup. Žádné oprávnění se změní v adresáři, dokud [kontrola dokončí](pim-resource-roles-complete-access-review.md).

Dokud zkontrolujte doba je u konce, můžete připomenout uživatelům dokončení jejich kontrola nebo zastavit kontrola již v rané fázi z části recenze přístup.

