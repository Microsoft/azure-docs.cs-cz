---
title: Postup spuštění kontrola přístupu | Microsoft Docs
description: Naučte se vytvářet kontrola přístupu pro privilegované identity s aplikací Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: a4ef91f7055db8f202e3a4e48375427c85964781
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020943"
---
# <a name="how-to-start-an-access-review-in-azure-ad-privileged-identity-management"></a>Spuštění kontrola přístupu v Azure AD Privileged Identity Management
Přiřazení rolí stát "zastaralé", když uživatelé mají privilegovaný přístup, které už nepotřebují. Chcete-li snížit riziko spojené s těchto zastaralých role přiřazení privilegované role by měl správci nebo globální správci pravidelné vytváření přístup recenze požádat správcům revizi rolí, které mají uživatelé. Tento dokument popisuje kroky pro spuštění kontrola přístupu v Azure AD Privileged Identity Management (PIM).

## <a name="start-an-access-review"></a>Spustit revizi přístupu
> [!NOTE]
> Pokud jste nepřidali aplikaci PIM do řídicího panelu portálu Azure, podívejte se na postup v [Začínáme s Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)
> 
> 

Na hlavní stránce aplikace PIM existují tři způsoby, jak začít kontrola přístupu:

* **Přístup k recenze** > **přidat**
* **Role** > **zkontrolujte** tlačítko
* Vyberte konkrétní roli, které mají být zkontrolovány ze seznamu rolí > **zkontrolujte** tlačítko

Po kliknutí na **zkontrolujte** tlačítko **spustit kontrola přístupu** otevře se okno. V tomto okně budete konfigurovat kontrola s názvem a časový limit, vyberte roli zkontrolovat a rozhodnout, který provede kontrola.

![Kontrola přístupu – snímek obrazovky Start][1]

### <a name="configure-the-review"></a>Kontrola konfigurace
Pokud chcete vytvořit kontrola přístupu, potřebujete název a nastavit počáteční a koncové datum.

![Nakonfigurujte kontrolní – snímek obrazovky][2]

Ujistěte se, délka dostatečně dlouhé, aby se pro uživatele dokončit, protože se kontrola. Pokud dokončíte před datem ukončení, můžete vždy zastavit kontrola již v rané fázi.

### <a name="choose-a-role-to-review"></a>Vyberte role ke kontrole
Každý revize se zaměřuje na jen jednu roli. Pokud jste začali kontrola přístupu v okně konkrétní roli, musíte teď zvolte roli.

1. Přejděte na **Zkontrolujte členství v roli**
   
    ![Zkontrolujte členství v rolích – snímek obrazovky][3]
2. Vyberte jednu roli ze seznamu.

### <a name="decide-who-will-perform-the-review"></a>Rozhodněte, který provede kontrola
Existují tři možnosti pro provádění kontrolu. Kontrola můžete přiřadit někomu jinému k dokončení, můžete provést sami nebo můžete mít každý uživatel, zkontrolujte své vlastní přístup.

1. Přejděte do **vyberte**
   
    ![Vyberte – snímek obrazovky][4]
2. Vyberte jednu z možností:
   
   * **Vyberte kontrolora**: tuto možnost použijte, pokud si nejste jisti, který potřebuje přístup. Pomocí této možnosti můžete přiřadit kontrola prostředků vlastníka nebo správce skupiny pro dokončení.
   * **Poslat mi**: užitečné, pokud si chcete prohlédnout, jak přístupu zkontroluje pracovní, nebo chcete zkontrolovat jménem osoby, které nelze.
   * **Členy zkontrolujte sami**: tuto možnost použijte, pokud chcete, aby uživatelé Zkontrolujte své vlastní přiřazení rolí.

### <a name="start-the-review"></a>Kontrola spuštění
Nakonec máte možnost, která vyžaduje, aby uživatelé zadali příslušný důvod. Pokud schválí jejich přístup. Pokud chcete přidat popis kontrola a vyberte **spustit**.

Zajistěte, aby dát uživatelům vědět, že je kontrola přístupu jim a zobrazit je [postup kontrola přístupu](active-directory-privileged-identity-management-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Spravovat kontrola přístupu
Průběh můžete sledovat, kontroloři dokončení jejich recenze v řídicím panelu Azure AD PIM, v části recenze přístup. Žádné oprávnění se změní v adresáři, dokud [kontrola dokončí](active-directory-privileged-identity-management-how-to-complete-review.md).

Dokud zkontrolujte doba je u konce, můžete připomenout uživatelům dokončení jejich kontrola nebo zastavit kontrola již v rané fázi z části recenze přístup.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="pim-table-of-contents"></a>Tabulka PIM obsahu
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png
