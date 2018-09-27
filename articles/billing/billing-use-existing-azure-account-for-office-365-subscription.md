---
title: Zaregistrovat pro Office 365 pomocí účtu Azure | Dokumentace Microsoftu
description: Naučte se vytvořit předplatné Office 365 pomocí účtu Azure
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: ''
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: cwatson
ms.openlocfilehash: d48b55af705eddb42853fb236e5eb6a214d1a0ff
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394043"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Zaregistrujte si předplatné Office 365 pomocí svého účtu Azure
Pokud jste předplatitelem Azure, můžete si zaregistrovat předplatné služeb Office 365 účtu Azure. Pokud jste součástí organizace, který má předplatné Azure, můžete vytvořit předplatných Office 365 pro uživatele ve vaší stávající Azure Active Directory (Azure AD). Zaregistrujte se k Office 365 pomocí účtu, který má oprávnění globálním správcem nebo správcem pro fakturaci ve vašem tenantovi Azure Active Directory. Další informace najdete v tématu [zkontrolujte Moje oprávnění účtu ve službě Azure AD](#RoleInAzureAD) a [přiřazení rolí správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Pokud už máte účet Office 365 a předplatné Azure, můžete si [přidružit tenanta Office 365 s předplatným Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Získat předplatné Office 365 pomocí svého účtu Azure

1. Přejděte [stránky produktu Office 365](https://products.office.com/business)a vyberte si plán.
2. Klikněte na tlačítko **přihlášení** v pravém horním rohu stránky.

    ![snímek obrazovky stránky zkušební verzi Office 365](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Přihlaste se pomocí přihlašovacích údajů k účtu Azure. Pokud vytváříte předplatné pro vaši organizaci, pomocí účtu Azure, který je členem role adresáře globálním správcem nebo správcem pro fakturaci ve vašem tenantovi Azure Active Directory.

    ![Snímek obrazovky s Office 365 přihlásit](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Klikněte na tlačítko **vyzkoušet**.

    ![Snímek obrazovky, který potvrdí vaši objednávku na Office 365.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. Na stránce potvrzení objednávky, klikněte na tlačítko **pokračovat**.

    ![Snímek obrazovky příjemce objednávky Office 365](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Teď všechno je nastavené. Pokud jste předplatné Office 365 vytvořili pro vaši organizaci, postupujte následovně Chcete-li zkontrolovat, že uživatele Azure AD jsou teď v Office 365.

1. Otevřete Centrum pro správu Office 365.
2. Rozbalte **uživatelé**a potom klikněte na tlačítko **aktivní uživatelé**.

    ![Snímek obrazovky uživatele služeb Office 365 admin System center](./media/billing-use-existing-azure-account-office-365-subscription/16-office-365-admin-center-users.png)

Když si zaregistrujete předplatné služeb Office 365 se přidá do stejné instance služby Azure Active Directory, které patří vaše předplatné Azure. Další informace najdete v tématu [Další informace o předplatných Azure a Office 365](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) a [předplatné Azure propojeno se službou Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Zkontrolujte oprávnění Můj účet ve službě Azure AD
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **všechny služby**a poté vyhledejte **služby Active Directory**.

    ![Snímek obrazovky s Active Directory na webu Azure Portal](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Klikněte na tlačítko **uživatelů a skupin** > **všichni uživatelé**.
4. Vyberte uživatelské jméno. 

    ![Snímek obrazovky zobrazující uživatele Azure Active Directory](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Klikněte na tlačítko **role adresáře**.
  
    ![Snímek obrazovky zobrazující role adresáře Azure portal](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  Roli **globálního správce** nebo **správce s omezením** > **správce fakturace** je potřebný k vytvoření předplatného Office 365 pro uživatele existující Azure Active Directory.

    ![Snímek obrazovky zobrazující role adresáře Azure portal správcem pro fakturaci](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému. 
