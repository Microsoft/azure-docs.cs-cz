---
title: Registrace Azure s využitím účtu Microsoft 365
description: Zjistěte, jak vytvořit předplatné Azure s využitím účtu Microsoft 365. Můžete k sobě také přidružit stávající účty Azure a Microsoft 365.
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 255fbe4036c83618dd0131b262d13709ab7f9c2f
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131883"
---
# <a name="sign-up-for-an-azure-subscription-with-your-microsoft-365-account"></a>Registrace předplatného Azure s využitím účtu Microsoft 365

Pokud máte předplatné Microsoftu 365, můžete k vytvoření předplatného Azure použít svůj účet Microsoft 365. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého uživatelského jména a hesla pro Microsoft 365. Pokud chcete nastavit virtuální počítače nebo využívat další služby Azure, musíte si zaregistrovat předplatné Azure. Své předplatné Azure můžete sdílet s ostatními a [pomocí řízení přístupu na základě role v Azure (Azure RBAC) můžete spravovat přístup k vašemu předplatnému a prostředkům Azure](../../role-based-access-control/role-assignments-portal.md).

Pokud již máte účet Microsoft 365 i předplatné Azure, projděte si [přidružení tenanta Microsoftu 365 k předplatnému Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-microsoft-365-account"></a>Získání předplatného Azure s využitím účtu Microsoft 365

Ušetřete čas a zabraňte nárůstu počtu účtů tím, že si zaregistrujete Azure s využitím vašeho uživatelského jména a hesla pro Microsoft 365.

1. Zaregistrujte se na webu [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Přihlaste se pomocí svého uživatelského jména a hesla pro Microsoft 365. Účet, který použijete, nemusí mít oprávnění správce. Pokud máte více účtů Microsoft 365, nezapomeňte použít přihlašovací údaje k tomu účtu Microsoft 365, který chcete přidružit k vašemu předplatnému Azure.

   ![Snímek obrazovky s přihlašovací stránkou](./media/microsoft-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Zadejte požadované informace a dokončete proces registrace. Pokud již máte účet Microsoft 365, některé informace možná nebude potřeba zadávat.

    ![Snímek obrazovky s registračním formulářem](./media/microsoft-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Pokud k předplatnému Azure potřebujete přidat další lidi ve vaší organizaci, přečtěte si téma [Začínáme se správou přístupu na webu Azure Portal](../../role-based-access-control/overview.md).

## <a name=""></a><a id="more-about-subs">Další informace o předplatných Azure a Microsoft 365</a>

Microsoft 365 i Azure ke správě uživatelů a předplatných využívají službu Azure AD. Adresář Azure je jako kontejner, ve kterém můžete seskupovat uživatele a předplatná. Pokud pro předplatná Azure a Microsoft 365 chcete použít stejné uživatelské účty, musíte zajistit, aby se předplatná Azure vytvořila ve stejném adresáři jako předplatná Microsoftu 365. Mějte na paměti následující skutečnosti:

* Předplatné se vytvoří v rámci adresáře.
* Uživatelé patří do adresářů.
* Předplatné se umístí do adresáře uživatele, který předplatné vytvořil. Vaše předplatné Microsoftu 365 se tedy sváže se stejným účtem jako vaše předplatné Azure.
* Předplatná Azure vlastní jednotliví uživatelé v adresáři.
* Vlastníkem předplatných Microsoftu 365 je samotný adresář. Uživatelé s odpovídajícími oprávněními v rámci adresáře můžou tato předplatná spravovat.

![Snímek obrazovky znázorňující vztah mezi adresářem, uživateli a předplatnými](./media/microsoft-365-account-for-azure-subscription/19-background-information.png)

Další informace najdete v článku [Přidružování předplatných Azure se službou Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- Své předplatné Azure můžete sdílet s ostatními a [pomocí řízení přístupu na základě role v Azure (Azure RBAC) můžete spravovat přístup k vašemu předplatnému a prostředkům Azure](../../role-based-access-control/role-assignments-portal.md).