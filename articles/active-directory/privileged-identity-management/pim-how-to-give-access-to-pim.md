---
title: Udělení přístupu na jiné správce ke správě PIM – Azure | Dokumentace Microsoftu
description: Zjistěte, jak udělit přístup k jiné správy pro správu Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: d6b2d9f43ce9bb86f4557c92887689c83beb49fa
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189551"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Udělení přístupu na jiné správce ke správě PIM
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

- [Povolení správy předplatného ve vašem tenantovi](pim-resource-roles-enable-subscription-management.md)