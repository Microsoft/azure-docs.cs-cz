---
title: Zahájení kontroly přístupu pro role adresáře Azure AD v PIM | Dokumentace Microsoftu
description: Zjistěte, jak spustit revizi přístupu pro role adresáře Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5c7216a419ba01c1b2df744e305bf059cf68104e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224377"
---
# <a name="start-an-access-review-for-azure-ad-directory-roles-in-pim"></a>Zahájení kontroly přístupu pro role adresáře Azure AD v PIM
Přiřazení rolí stát "zastaralých", když uživatelé privilegovaný přístup, které už nepotřebují. Pokud chcete snížit riziko spojené s tyto zastaralé role přiřazení privilegovaných rolí by správci nebo globální správci pravidelně vytvářet kontroly přístupu požádat správce ke kontrole role, které mají uživatelé. Tento dokument popisuje kroky pro spuštění kontroly přístupu v Azure AD Privileged Identity Management (PIM).

## <a name="start-an-access-review"></a>Zahájení kontroly přístupu
> [!NOTE]
> Pokud jste nepřidali aplikaci PIM do řídicího panelu na webu Azure Portal, podívejte se na postup v [Začínáme se službou Azure Privileged Identity Management](pim-getting-started.md)
> 
> 

Na hlavní stránce aplikace PIM existují tři způsoby, jak spustit revizi přístupu:

* **Kontroly přístupu** > **přidat**
* **Role** > **revize** tlačítko
* Vyberte konkrétní roli zkontrolovat ze seznamu rolí > **revize** tlačítko

Po kliknutí na **zkontrolujte** tlačítko, **zahájení kontroly přístupu** otevře se okno. V tomto okně budete konfigurace revizi pomocí názvu a časového limitu, vyberte roli, kterou chcete zkontrolovat a rozhodnout, který bude provádět revizi.

![Zahájení kontroly přístupu – snímek obrazovky](./media/pim-how-to-start-security-review/PIM_start_review.png)

### <a name="configure-the-review"></a>Konfigurace kontroly
Vytvoření kontroly přístupu, potřebujete název a nastavte počáteční a koncové datum.

![Konfigurace kontroly – snímek obrazovky](./media/pim-how-to-start-security-review/PIM_review_configure.png)

Ujistěte se, délka revize dostatečně dlouhé pro uživatele k jejímu dokončení. Pokud dokončíte dřívější než datum ukončení, můžete vždy ukončit revizi již v rané fázi.

### <a name="choose-a-role-to-review"></a>Vyberte role ke kontrole
Každou recenzi, zaměřuje na jenom jedné roli. Pokud nezahájíte kontroly přístupu v okně konkrétní roli, bude nutné zvolit roli teď.

1. Přejděte na **kontrola členství v rolích**
   
    ![Kontrola členství v rolích – snímek obrazovky](./media/pim-how-to-start-security-review/PIM_review_role.png)
2. V seznamu vyberte jednu roli.

### <a name="decide-who-will-perform-the-review"></a>Rozhodněte, kdo bude provádět revizi
Existují tři možnosti pro provádění kontrolu. Revize můžete přiřadit někomu jinému k dokončení, můžete to provést sami nebo může mít každý uživatel kontrolovat svůj vlastní přístup.

1. Přejděte na **vybrat revidující**
   
    ![Vybrat revidující – snímek obrazovky](./media/pim-how-to-start-security-review/PIM_review_reviewers.png)
2. Vyberte jednu z možností:
   
   * **Vyberte kontrolora**: tuto možnost použijte, pokud si nejste jisti, který potřebuje přístup. Pomocí této možnosti můžete přiřadit revize vlastník prostředku nebo správce skupiny pro dokončení.
   * **ME**: užitečné, pokud chcete zobrazit náhled, jak pracovní kontroly přístupu, nebo chcete zkontrolovat jménem uživatelů, kteří nemohou.
   * **Členové si měli přečíst sami**: pomocí této možnosti můžete mít uživatele, projděte si svoje vlastní přiřazení rolí.

### <a name="start-the-review"></a>Spustit revizi
A konečně máte možnost vyžadovat, aby uživatelé zadat příslušný důvod. Pokud jejich schválit přístup. Pokud chcete, můžete přidat popis kontroly a vyberte **Start**.

Ujistěte se, že umožníte uživatelům vědět, že je čekat na jejich kontroly přístupu a zobrazit je [jak k provádění kontroly přístupu](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Správa kontroly přístupu.
Průběh můžete sledovat, jak se revidující dokončení své recenze na řídicím panelu Azure AD PIM, v části kontroly přístupu. Žádné přístupová práva se změní v adresáři, dokud [kontrola dokončí](pim-how-to-complete-review.md).

Až skončí období kontroly, můžete jim Připomeňte dokončení jejich kontroly nebo zastavte kontrolu již v rané fázi z část kontroly přístupu.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup

- [Dokončení kontroly přístupu pro role adresáře Azure AD v PIM](pim-how-to-complete-review.md)
- [Provádění kontroly přístupu sady Moje role adresáře Azure AD v PIM](pim-how-to-perform-security-review.md)
- [Zahájení kontroly přístupu pro role prostředků Azure v PIM](pim-resource-roles-start-access-review.md)
