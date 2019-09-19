---
title: Registrace Azure s využitím účtu Office 365 | Microsoft Docs
description: Zjistěte, jak vytvořit předplatné Azure s využitím účtu Office 365.
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 44ff08c84b3e95a5b598ebf4279fc2ffc46313e3
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "60369479"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Registrace předplatného Azure s využitím účtu Office 365
Pokud máte předplatné Office 365, můžete k vytvoření předplatného Azure použít svůj účet Office 365. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého uživatelského jména a hesla Office 365. Pokud chcete nastavit virtuální počítače nebo využívat další služby Azure, musíte si zaregistrovat předplatné Azure. Své předplatné Azure můžete sdílet s ostatními a [pomocí řízení přístupu na základě role můžete spravovat přístup k vašemu předplatnému a prostředkům Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Pokud již máte účet Office 365 i předplatné Azure, přečtěte si téma [Přidružení tenanta Office 365 k předplatnému Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Získání předplatného Azure s využitím účtu Office 365

Ušetřete čas a zabraňte nárůstu počtu účtů tím, že si zaregistrujete Azure s využitím vašeho uživatelského jména a hesla Office 365. 

1. Zaregistrujte se na webu [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Přihlaste se pomocí svého uživatelského jména a hesla Office 365. Účet, který použijete, nemusí mít oprávnění správce. Pokud máte více účtů Office 365, nezapomeňte použít přihlašovací údaje k účtu Office 365, který chcete přidružit k vašemu předplatnému Azure. 

   ![Snímek obrazovky s přihlašovací stránkou](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Zadejte požadované informace a dokončete proces registrace. Pokud již máte účet Office 365, některé informace možná nebude potřeba zadávat.

    ![Snímek obrazovky s registračním formulářem](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Pokud k předplatnému Azure potřebujete přidat další lidi ve vaší organizaci, přečtěte si téma [Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md). 

## <a id="more-about-subs">Další informace o předplatných Azure a Office 365</a>
Office 365 i Azure ke správě uživatelů a předplatných využívají službu Azure AD. Adresář Azure je jako kontejner, ve kterém můžete seskupovat uživatele a předplatná. Pokud pro předplatná Azure a Office 365 chcete použít stejné uživatelské účty, musíte zajistit, aby se předplatná Azure vytvořila ve stejném adresáři jako předplatná Office 365. Mějte na paměti následující skutečnosti:

* Předplatné se vytvoří v rámci adresáře.
* Uživatelé patří do adresářů.
* Předplatné se umístí do adresáře uživatele, který předplatné vytvořil. Vaše předplatné Office 365 se tedy sváže se stejným účtem jako vaše předplatné Azure.
* Předplatná Azure vlastní jednotliví uživatelé v adresáři.
* Vlastníkem předplatných Office 365 je samotný adresář. Uživatelé s odpovídajícími oprávněními v rámci adresáře můžou tato předplatná spravovat.

![Snímek obrazovky znázorňující vztah mezi adresářem, uživateli a předplatnými](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Další informace najdete v článku [Přidružování předplatných Azure se službou Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).