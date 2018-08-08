---
title: Provádění kontroly přístupu v prostředcích Azure s použitím Privileged Identity Management | Dokumentace Microsoftu
description: Vysvětluje, jak spustit revizi přístupu v Privileged Identity Management pro prostředky Azure
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f88c4a2f7e6eb569c9c0de33ab86e8b484a923e3
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622877"
---
# <a name="perform-access-reviews-in-azure-resources-by-using-privileged-identity-management"></a>Provádění kontroly přístupu v prostředcích Azure s použitím Privileged Identity Management
Přiřazení rolí stát "zastaralých", když uživatelé privilegovaný přístup, které už nepotřebují. Aby se snížilo riziko, který je spojen s přiřazení těchto zastaralých rolí, správci privilegovaných rolí by měl pravidelně zkontrolovat role. Tento dokument popisuje kroky pro zahájení kontroly přístupu v Privileged Identity Management (PIM) pro prostředky Azure.

Na hlavní stránce aplikace PIM přejdete na:

* **Kontroly přístupu** > **přidat**

![Přidání kontroly přístupu](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Když vyberete **přidat** tlačítko, **vytvoření kontroly přístupu** otevře se okno. V tomto okně Konfigurace revizi pomocí názvu a časového limitu, vyberte roli zkontrolovat a rozhodněte, kdo provádí revizi.

![Vytvořit kontrolu přístupu](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Konfigurace kontroly
Vytvoření kontroly přístupu, nejprve pojmenujte ji a pak nastavte počáteční a koncové datum.

![Konfigurace kontroly – snímek obrazovky](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Ujistěte se, délka revize dostatečně dlouhé pro uživatele k jejímu dokončení. Pokud končily dřívější než datum ukončení, můžete se vždycky již v rané fázi zastavte kontrolu.

### <a name="choose-a-role-to-review"></a>Vyberte role ke kontrole
Každou recenzi, zaměřuje na jenom jedné roli. Pokud nezahájíte kontroly přístupu v okně konkrétní roli, musíte zvolit roli teď.

1. Přejděte na **kontrola členství v rolích**
   
    ![Kontrola členství v rolích – snímek obrazovky](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. V seznamu vyberte jednu roli.

### <a name="decide-who-will-perform-the-review"></a>Rozhodněte, kdo bude provádět revizi
Existují tři možnosti pro provádění kontrolu. Revize můžete přiřadit někomu jinému k dokončení, můžete to provést sami nebo může každý uživatel kontrolovat svůj vlastní přístup.

1. Vyberte jednu z možností:
   
   * **Vybraní uživatelé**: tuto možnost použijte, pokud si nejste jisti, který potřebuje přístup. Pomocí této možnosti můžete přiřadit revize vlastník prostředku nebo správce skupiny pro dokončení.
   * **Přiřazené (vlastní)**: pomocí této možnosti můžete mít uživatele, projděte si svoje vlastní přiřazení rolí.
   
2. Přejděte na **vybrat revidující**.
   
    ![Vybrat revidující – snímek obrazovky](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Spustit revizi
Nakonec můžete vyžadovat, aby uživatelé zadat důvod schválení přístupu. Pokud chcete, můžete přidáte popis kontroly. Potom vyberte **Start**.

Ujistěte se, že umožníte uživatelům vědět, že je čekat na jejich kontroly přístupu a zobrazit je [jak k provádění kontroly přístupu](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Správa kontroly přístupu.
Na řídicím panelu PIM Azure prostředky můžete sledovat průběh revidující dokončení jejich revize. Žádné přístupová práva jsou změněny v adresáři, dokud [kontrola se dokončila](pim-resource-roles-complete-access-review.md).

Až skončí období kontroly, můžete jim Připomeňte dokončení jejich kontroly nebo zastavte kontrolu již v rané fázi z část kontroly přístupu.

