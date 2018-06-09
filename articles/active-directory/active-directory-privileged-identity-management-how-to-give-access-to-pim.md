---
title: Jak poskytnout přístup k Privileged Identity Management - Azure | Microsoft Docs
description: Informace o postupu přidání role pro uživatele s příponou Azure Active Directory Privileged Identity Management, tak můžou spravovat PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 150f994aedfc8d2847a0741792c32114e1b520fe
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233542"
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Udělení přístupu ke správě Azure AD Privileged Identity Management
Globální správce, který umožňuje Azure AD Privileged Identity Management (PIM) pro organizaci automaticky získat přiřazení rolí a přístup k PIM. Nikdo jiný získá přístup pro zápis ve výchozím nastavení, ale včetně další globální správce. Další globální správce, správce zabezpečení a zabezpečení čtečky mít přístup jen pro čtení k Azure AD PIM. Udělit přístup k PIM, můžete přiřadit první uživatel ostatním uživatelům **správce privilegovaných rolí** role. Toto přiřazení musí provádět v PIM sám a nelze změnit pomocí prostředí PowerShell nebo dalších portálech.

> [!NOTE]
> Správa Azure AD PIM vyžaduje Azure MFA. Vzhledem k tomu, že účty Microsoft nelze zaregistrovat pro Azure MFA, uživatel, který se přihlásí pomocí účtu Microsoft přístup k Azure AD PIM.
> 
> 

Zkontrolujte, že jsou vždy alespoň dva uživatelé v roli správce privilegovaných rolí v případě, že jeden uživatel je uzamčen nebo jejich účet je odstraněný.

## <a name="give-another-user-access-to-manage-pim"></a>Zadejte jiný uživatel přístup ke správě PIM
1. Přihlaste se k [portál Azure](https://portal.azure.com/) a vyberte **Azure AD Privileged Identity Management** aplikace na řídicím panelu.
2. Vyberte **spravovat privilegované role** > **správce privilegovaných rolí** > **přidat**.
   
    ![Přidání privilegované role správců – snímek obrazovky][1]
3. V okně Přidat spravované uživatele je krok 1 již dokončena. Vyberte krok 2, **vyberte uživatele,** a vyhledejte uživatele, který chcete přidat.
   
    ![Vyberte uživatele – snímek obrazovky][2]
4. Ve výsledcích hledání vyberte uživatele a klikněte na tlačítko **provádí**.
5. Klikněte na tlačítko **OK** uložte svůj výběr. Uživatel, který jste vybrali, se zobrazí v seznamu privilegované role správců.
   
   * Vždy, když přiřadíte novou roli uživatele, budou se automaticky nastavit jako způsobilých při aktivaci role. Pokud chcete převést na trvalé v roli, klikněte na uživatele v seznamu. Vyberte **zkontrolujte oprávnění** v nabídce uživatelské informace.
6. Odkaz na uživateli odeslat [Začínáme s Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Odeberte jiné uživatelská přístupová práva pro správu PIM
Před odebráním někdo z role správce privilegovaných rolí, vždy ujistěte se, že stále ještě dva uživatelé přiřazení k němu.

1. Na řídicím panelu PIM klikněte na roli **správce privilegovaných rolí**.  Zobrazí se seznam uživatelů aktuálně v dané roli.
2. Klikněte na uživatele v seznamu uživatelů.
3. Klikněte na **odebrat**.  Se zobrazí potvrzovací zpráva.
4. Klikněte na tlačítko **Ano** odebrat uživatele z role.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
