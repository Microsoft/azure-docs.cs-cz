---
title: Účet Azure zaregistrovat pro Office 365 | Microsoft Docs
description: Naučte se vytvářet předplatné služeb Office 365 pomocí účtu Azure
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
ms.date: 11/03/2017
ms.author: cjiang
ms.openlocfilehash: bce4b58d4197eee34be43a4dc852c6f43e0e18b4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203370"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Zaregistrujte si předplatné služeb Office 365 s vaším účtem Azure
Pokud jste Azure odběratele, můžete si zaregistrovat pro předplatné služeb Office 365 účtu Azure. Pokud jste součástí v organizaci, která má předplatné Azure, můžete vytvořit předplatná Office 365 pro uživatele ve vaší stávající Azure Active Directory (Azure AD). Zaregistrovat se k Office 365 pomocí účtu, který má oprávnění globální správce nebo správce fakturace v klientovi služby Azure Active Directory. Další informace najdete v tématu [zkontrolujte oprávnění pro uživatelský účet ve službě Azure AD](#RoleInAzureAD) a [přiřazení rolí správce v Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

Pokud již máte účet Office 365 a předplatné Azure, můžete [přidružit klienta služby Office 365 k předplatnému Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Získat předplatné služeb Office 365 pomocí účtu Azure

1. Přejděte na [stránky produktu Office 365](https://products.office.com/business)a vyberte plán.
2. Klikněte na tlačítko **přihlášení** v pravém horním rohu stránky.

    ![snímek obrazovky stránky zkušební verzi Office 365](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Přihlaste se pomocí přihlašovacích údajů účtu Azure. Pokud vytváříte odběr pro vaši organizaci, použijte účet Azure, který je členem role Globální správce nebo správce fakturace adresáře v klientovi služby Azure Active Directory.

    ![Snímek obrazovky Office 365 přihlásit](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Klikněte na tlačítko **vyzkoušet**.

    ![Snímek obrazovky, který potvrzuje vaši objednávku pro Office 365.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. Na stránce potvrzení pořadí, klikněte na tlačítko **pokračovat**.

    ![Snímek obrazovky příjmu pořadí Office 365](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Nyní je vše připraveno. Pokud jste vytvořili předplatné služeb Office 365 pro vaši organizaci, pomocí následujících kroků zkontrolujte, jestli vaše uživatele Azure AD jsou nyní v Office 365.

1. Otevřete Centrum pro správu Office 365.
2. Rozbalte položku **uživatelé**a potom klikněte na **aktivní uživatelé**.

    ![Snímek obrazovky uživatele center Správce služeb Office 365](./media/billing-use-existing-azure-account-office-365-subscription/16-office-365-admin-center-users.png)

Po přihlášení, předplatné služeb Office 365 se přidá do stejnou instanci služby Azure Active Directory, který je součástí vašeho předplatného Azure. Další informace najdete v tématu [Další informace o předplatných Azure a Office 365](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) a [asociování předplatných Azure se službou Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Zkontrolujte oprávnění pro uživatelský účet ve službě Azure AD
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **všechny služby**a poté vyhledejte **služby Active Directory**.

    ![Snímek obrazovky Active Directory na portálu Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Klikněte na tlačítko **uživatelů a skupin** > **všichni uživatelé**.
4. Vyberte uživatelské jméno. 

    ![Snímek obrazovky, který zobrazuje uživatele Azure Active Directory](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Klikněte na tlačítko **Directory role**.
  
    ![Snímek obrazovky zobrazující roli adresáře portálu Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  Role **globálního správce** nebo **správce s omezeními** > **správce fakturace** je potřeba vytvořit předplatné služeb Office 365 pro uživatele v existující Azure Active Directory.

    ![Snímek obrazovky zobrazující role Azure directory portálu správce fakturace](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém. 
