---
title: Registrace Office 365 s využitím účtu Azure | Microsoft Docs
description: Zjistěte, jak vytvořit předplatné Office 365 s využitím účtu Azure.
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: ''
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 034df0de027a50c61e5a7cab3d1e829446002c86
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75993593"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Registrace předplatného Office 365 s využitím účtu Azure
Pokud jste předplatitelem Azure, můžete k registraci předplatného Office 365 použít svůj účet Azure. Pokud jste součástí organizace, která má předplatné Azure, můžete pro uživatele ve vašem stávajícím tenantovi Azure Active Directory (Azure AD) vytvořit předplatná Office 365. Zaregistrujte si Office 365 pomocí účtu, který má ve vašem tenantovi Azure Active Directory oprávnění globálního správce nebo správce fakturace. Další informace najdete v tématech [Kontrola oprávnění účtu v Azure AD](#RoleInAzureAD) a [Přiřazování rolí správců v Azure Active Directory](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Pokud již máte účet Office 365 i předplatné Azure, můžete [přidružit tenanta Office 365 k předplatnému Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Získání předplatného Office 365 s využitím účtu Azure

1. Přejděte na [stránku produktu Office 365](https://products.office.com/business) a vyberte plán.
2. V pravém horním rohu stránky klikněte na **Přihlásit se**.

    ![Snímek obrazovky se stránkou zkušební verze Office 365](./media/azure-account-for-office-365-subscription/12-office-365-trial-page.png)
3. Přihlaste se pomocí přihlašovacích údajů k vašemu účtu Azure. Pokud vytváříte předplatné pro vaši organizaci, použijte účet Azure, který je ve vašem tenantovi Azure Active Directory členem role adresáře Globální správce nebo Správce fakturace.

    ![Snímek obrazovky s přihlašovací stránkou Office 365](./media/azure-account-for-office-365-subscription/13-office-365-sign-in.png)
4. Klikněte na **Vyzkoušet**.

    ![Snímek obrazovky s potvrzením objednávky Office 365](./media/azure-account-for-office-365-subscription/14-office-365-confirm-your-order.png)
5. Na stránce s potvrzením příjmu objednávky klikněte na **Pokračovat**.

    ![Snímek obrazovky s potvrzením příjmu objednávky Office 365](./media/azure-account-for-office-365-subscription/15-office-365-order-receipt.png)

Nyní je vše připraveno.
Pokud jste vytvořili předplatné Office 365 pro vaši organizaci, pomocí následujících kroků zkontrolujte, jestli jsou teď vaši uživatelé Azure AD v Office 365.

1. Otevřete Centrum pro správu Microsoftu 365.
2. Rozbalte nabídku **UŽIVATELÉ** a klikněte na **Aktivní uživatelé**.

    ![Snímek obrazovky s uživateli v Centru pro správu Microsoftu 365](./media/azure-account-for-office-365-subscription/16-microsoft-365-admin-center-users.png)

Po registraci se předplatné Office 365 přidá do stejné instance Azure Active Directory, do které patří vaše předplatné Azure. Další informace najdete v tématech [Další informace o předplatných Azure a Office 365](office-365-account-for-azure-subscription.md#more-about-subs) a [Přidružování předplatných Azure se službou Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Kontrola oprávnění účtu v Azure AD
1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
2. Klikněte na **Všechny služby** a pak vyhledejte **Active Directory**.

    ![Snímek obrazovky se službou Active Directory na webu Azure Portal](./media/azure-account-for-office-365-subscription/billing-more-services-active-directory.png)
3. Klikněte na **Uživatelé a skupiny** > **Všichni uživatelé**.
4. Vyberte uživatelské jméno.

    ![Snímek obrazovky s uživateli služby Azure Active Directory](./media/azure-account-for-office-365-subscription/billing-users-groups.png)

5. Klikněte na **Role adresáře**.

    ![Snímek obrazovky s rolí adresáře na webu Azure Portal](./media/azure-account-for-office-365-subscription/billing-user-directory-role.png)
6.  Pokud chcete vytvořit předplatné Office 365 pro uživatele ve vašem stávajícím tenantovi Azure Active Directory, potřebujete roli **Globální správce** nebo **Omezený správce** > **Správce fakturace**.

    ![Snímek obrazovky s rolí adresáře Správce fakturace na webu Azure Portal](./media/azure-account-for-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
