---
title: Registrace pro Microsoft 365 pomocí účtu Azure
description: Zjistěte, jak vytvořit předplatné Microsoft 365 s využitím účtu Azure. Můžete k sobě také přidružit stávající účty Azure a Microsoft 365.
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: db9c8d18d975661edf4a120a2e82b073c884d2a9
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369671"
---
# <a name="sign-up-for-a-microsoft-365-subscription-with-your-azure-account"></a>Registrace předplatného Microsoft 365 s využitím účtu Azure

Pokud jste předplatitelem Azure, můžete k registraci předplatného Microsoftu 365 použít svůj účet Azure. Pokud jste součástí organizace, která má předplatné Azure, můžete předplatná Microsoftu 365 vytvořit pro uživatele ve vašem stávajícím tenantovi Azure Active Directory (Azure AD). Zaregistrujte si Microsoft 365 pomocí účtu, který má ve vašem tenantovi Azure Active Directory oprávnění globálního správce nebo správce fakturace. Další informace najdete v tématech [Kontrola oprávnění účtu v Azure AD](#RoleInAzureAD) a [Přiřazování rolí správců v Azure Active Directory](../../active-directory/roles/permissions-reference.md).

Pokud již máte účet Microsoft 365 i předplatné Azure, můžete [přidružit tenanta Microsoftu 365 k předplatnému Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-a-microsoft-365-subscription-by-using-your-azure-account"></a>Získání předplatného Microsoft 365 s využitím účtu Azure

1. Přejděte na [stránku produktu Microsoft 365](https://www.microsoft.com/microsoft-365/business/all-business) a vyberte plán.
2. V pravém horním rohu stránky klikněte na **Přihlásit se** .

    ![Screenshot se stránkou zkušební verze Microsoftu 365](./media/azure-account-for-microsoft-365-subscription/12-office-365-trial-page.png)
3. Přihlaste se pomocí přihlašovacích údajů k vašemu účtu Azure. Pokud vytváříte předplatné pro vaši organizaci, použijte účet Azure, který je ve vašem tenantovi Azure Active Directory členem role adresáře Globální správce nebo Správce fakturace.

    ![Screenshot s přihlášením Microsoftu](./media/azure-account-for-microsoft-365-subscription/13-office-365-sign-in.png)
4. Klikněte na **Vyzkoušet** .

    ![Screenshot s potvrzením objednávky Microsoftu 365](./media/azure-account-for-microsoft-365-subscription/14-office-365-confirm-your-order.png)
5. Na stránce s potvrzením příjmu objednávky klikněte na **Pokračovat** .

    ![Screenshot s potvrzením příjmu objednávky Microsoftu 365](./media/azure-account-for-microsoft-365-subscription/15-office-365-order-receipt.png)

Nyní je vše připraveno. Pokud jste vytvořili předplatné Microsoftu 365 pro vaši organizaci, pomocí následujících kroků zkontrolujte, jestli teď vaši uživatelé Azure AD jsou v Microsoftu 365.

1. Otevřete Centrum pro správu Microsoftu 365.
2. Rozbalte nabídku **UŽIVATELÉ** a klikněte na **Aktivní uživatelé** .

    ![Snímek obrazovky s uživateli v Centru pro správu Microsoftu 365](./media/azure-account-for-microsoft-365-subscription/16-microsoft-365-admin-center-users.png)

Po registraci se předplatné Microsoftu 365 přidá do stejné instance Azure Active Directory, do které patří vaše předplatné Azure. Další informace najdete v tématech [Další informace o předplatných Azure a Microsoftu 365](microsoft-365-account-for-azure-subscription.md#more-about-subs) a [Přidružování předplatných Azure se službou Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="check-my-account-permissions-in-azure-ad"></a><a id="RoleInAzureAD"></a>Kontrola oprávnění účtu v Azure AD
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na **Všechny služby** a pak vyhledejte **Active Directory** .

    ![Snímek obrazovky se službou Active Directory na webu Azure Portal](./media/azure-account-for-microsoft-365-subscription/billing-more-services-active-directory.png)
3. Klikněte na **Uživatelé a skupiny** > **Všichni uživatelé** .
4. Vyberte uživatelské jméno.

    ![Snímek obrazovky s uživateli služby Azure Active Directory](./media/azure-account-for-microsoft-365-subscription/billing-users-groups.png)

5. Klikněte na **Role adresáře** .

    ![Snímek obrazovky s rolí adresáře na webu Azure Portal](./media/azure-account-for-microsoft-365-subscription/billing-user-directory-role.png)
6.  Pokud chcete vytvořit předplatné Microsoftu 365 pro uživatele ve vašem stávajícím tenantovi Azure Active Directory, potřebujete roli **Globální správce** nebo **Omezený správce** > **Správce fakturace** .

    ![Snímek obrazovky s rolí adresáře Správce fakturace na webu Azure Portal](./media/azure-account-for-microsoft-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Přidružení tenanta Microsoftu 365 k předplatnému Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
