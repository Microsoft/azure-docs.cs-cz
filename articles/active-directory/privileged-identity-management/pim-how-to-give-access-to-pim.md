---
title: Jak udělit přístup k Privileged Identity Management – Azure | Dokumentace Microsoftu
description: Zjistěte, jak přidat role pro uživatele s rozšířením Azure Active Directory Privileged Identity Management, tak můžou spravovat PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: ade3939057669e7f9979001db4376fe61c2f4ad9
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623064"
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Poskytuje přístup ke správě Azure AD Privileged Identity Management
Globální správce, který umožňuje Azure AD Privileged Identity Management (PIM) pro organizaci automaticky získat přiřazení rolí a přístupu k PIM. Nikdo jiný získá oprávnění k zápisu ve výchozím nastavení, i když, včetně další globální správce. Jiné globální správci, správci zabezpečení a čtenáři zabezpečení mají přístup jen pro čtení k Azure AD PIM. K poskytnutí přístupu k PIM, můžete přiřadit na prvního uživatele ostatním uživatelům **správce privilegovaných rolí** role.

> [!NOTE]
> Správa Azure AD PIM vyžaduje Azure MFA. Protože účty Microsoft nelze zaregistrovat pro Azure MFA, nemůže uživatel, který se přihlásí pomocí účtu Microsoft k Azure AD PIM.
> 
> 

Ujistěte se, že jsou vždy alespoň dva uživatelé v roli správce privilegovaných rolí, v případě, že jeden uživatel je uzamčen nebo se odstranil jeho účet.

## <a name="give-another-user-access-to-manage-pim"></a>Jiný uživatel přístup ke správě PIM
1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) a vyberte **Azure AD Privileged Identity Management** aplikace na řídicím panelu.
2. Vyberte **spravovat privilegované role** > **správce privilegovaných rolí** > **přidat**.
   
    ![Přidat správce privilegovaných rolí – snímek obrazovky](./media/pim-how-to-give-access-to-pim/PIM_add_PRA.png)
3. V okně Přidat spravovaných uživatelů je již kroku 1 dokončena. Vyberte krok 2, **vybraným uživatelům** a vyhledejte uživatele, které chcete přidat.
   
    ![Vyberte uživatele – snímek obrazovky](./media/pim-how-to-give-access-to-pim/PIM_select_users.png)
4. Vyberte uživatele ze seznamu výsledků a klikněte na tlačítko **provádí**.
5. Klikněte na tlačítko **OK** uložte svůj výběr. Uživatel, kterého jste vybrali se zobrazí v seznamu Správce privilegovaných rolí.
   
   * Pokaždé, když přiřadíte novou roli uživatele, jsou automaticky nastavené jako oprávněné aktivaci této role. Pokud chcete, aby se daly trvalé v roli, klikněte na uživatele v seznamu. Vyberte **nastavit jako trvalé** v nabídce uživatelské informace.
6. Poslat uživateli odkaz [Začínáme se službou Azure AD Privileged Identity Management](pim-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Odeberte jiné uživatelská přístupová práva pro správu PIM
Před odebráním uživatele z role správce privilegovaných rolí, vždy ujistěte se, že bude stále být přiřazené dva uživatele.

1. Na řídicím panelu PIM, klikněte na roli **správce privilegovaných rolí**.  Zobrazí se seznam uživatelů aktuálně v této roli.
2. Kliknutím na uživatele v seznamu uživatelů.
3. Klikněte na **odebrat**.  Zobrazí se potvrzovací zpráva.
4. Klikněte na tlačítko **Ano** k odebrání uživatele z role.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
