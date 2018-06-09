---
title: Proveďte recenze přístup v prostředků Azure pomocí Privileged Identity managementu | Microsoft Docs
description: Vysvětluje, jak spustit kontrola přístupu v Privileged Identity Management pro prostředky Azure
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b5c2f13386a996a6c7895bd4755b6cf609a5df72
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233250"
---
# <a name="perform-access-reviews-in-azure-resources-by-using-privileged-identity-management"></a>Proveďte recenze přístup v prostředků Azure pomocí Privileged Identity Management
Přiřazení rolí stát "zastaralé", když uživatelé mají privilegovaný přístup, které už nepotřebují. Aby se snížilo riziko, že je spojen s přiřazení těchto zastaralých rolí, správci privilegované role by pravidelně zkontrolovat role. Tento dokument popisuje kroky pro spuštění v Privileged Identity Management (PIM) kontrola přístupu k prostředkům Azure.

Na hlavní stránce aplikace PIM přejdete na:

* **Přístup k recenze** > **přidat**

![Přidat přístup recenze](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Když vyberete **přidat** tlačítko **vytvořit kontrola přístupu** otevře se okno. V tomto okně nakonfigurovat kontrola s názvem a časový limit, vyberte roli zkontrolovat a potom se rozhodnete, který nemá kontrola.

![Vytvořit kontrolu přístupu](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Kontrola konfigurace
Pokud chcete vytvořit kontrola přístupu, nejprve pojmenujte ji a nastavte počáteční a koncové datum.

![Nakonfigurujte kontrolní – snímek obrazovky](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Ujistěte se, délka dostatečně dlouhé, aby se pro uživatele dokončit, protože se kontrola. Pokud se dokončila před datem ukončení, můžete se vždy již v rané fázi zastavit kontrola.

### <a name="choose-a-role-to-review"></a>Vyberte role ke kontrole
Každý revize se zaměřuje na jen jednu roli. Pokud jste začali kontrola přístupu v okně konkrétní roli, musíte teď zvolte roli.

1. Přejděte na **Zkontrolujte členství v roli**
   
    ![Zkontrolujte členství v rolích – snímek obrazovky](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Vyberte jednu roli ze seznamu.

### <a name="decide-who-will-perform-the-review"></a>Rozhodněte, který provede kontrola
Existují tři možnosti pro provádění kontrolu. Kontrola můžete přiřadit někomu jinému k dokončení, můžete provést sami nebo každého uživatele, můžete zkontrolovat svůj vlastní přístup.

1. Vyberte jednu z možností:
   
   * **Vybraní uživatelé**: tuto možnost použijte, pokud si nejste jisti, který potřebuje přístup. Pomocí této možnosti můžete přiřadit kontrola prostředků vlastníka nebo správce skupiny pro dokončení.
   * **Přiřazené (vlastní)**: tuto možnost použijte, pokud chcete, aby uživatelé Zkontrolujte své vlastní přiřazení rolí.
   
2. Přejděte na **vyberte**.
   
    ![Vyberte – snímek obrazovky](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Kontrola spuštění
Nakonec můžete vyžadovat, aby uživatelé zadali důvod pro schvalování přístupu. Pokud chcete přidáte popis kontrola. Potom vyberte **spustit**.

Zajistěte, aby dát uživatelům vědět, že je kontrola přístupu jim a zobrazit je [postup kontrola přístupu](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Spravovat kontrola přístupu
Na řídicím panelu PIM Azure prostředků můžete sledovat průběh kontroloři dokončení jejich recenze. Žádné oprávnění došlo ke změně v adresáři, dokud [byla dokončena kontrola](pim-resource-roles-complete-access-review.md).

Dokud zkontrolujte doba je u konce, můžete připomenout uživatelům dokončení jejich kontrola nebo zastavit kontrola již v rané fázi z části recenze přístup.

