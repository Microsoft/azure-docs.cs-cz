---
title: Zaregistrovat do Azure s použitím účtu Office 365 | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet předplatné Azure s použitím účtu Office 365
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
ms.author: cjiang
ms.openlocfilehash: dd7b9609495cacd324ef5509f57db0384775e0a7
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35901650"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Zaregistrujte si předplatné Azure pomocí účtu Office 365
Pokud máte předplatné Office 365, můžete použít svůj účet Office 365 k vytvoření předplatného Azure. Přihlaste se k [webu Azure portal](https://portal.azure.com/) pomocí Office 365 uživatelské jméno a heslo. Pokud chcete nastavení virtuálních počítačů nebo využívat další služby Azure, které musíte zaregistrovat předplatné Azure. Vaše předplatné Azure můžete sdílet s ostatními a [použít řízení přístupu na základě Role můžete spravovat přístup k vašemu předplatnému Azure a prostředky](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Pokud už máte účet Office 365 a předplatné Azure, přečtěte si téma [přidružit tenanta Office 365 s předplatným Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Získání předplatného Azure pomocí účtu Office 365

Šetří čas a vyhněte se účet narůstá registrací do Azure s využitím Office 365 uživatelské jméno a heslo. 

1. Zaregistrujte se na [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Přihlaste se pomocí Office 365 uživatelské jméno a heslo. Účet, který používáte nemusí mít oprávnění správce. Pokud máte více než jeden účet Office 365, ujistěte se, že použijete přihlašovací údaje pro účet Office 365, který chcete přidružit k vašemu předplatnému Azure. 

   ![Snímek obrazovky zobrazující přihlašovací stránku.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Zadejte požadované informace a dokončení procesu registrace. Pokud už máte účet Office 365, nemusí být některé informace požadované.

    ![Snímek obrazovky zobrazující registračního formuláře.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Pokud potřebujete přidat jiní lidé ve vaší organizaci k předplatnému Azure, přečtěte si téma [Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md). 

## <a id="more-about-subs">Další informace o předplatných Azure a Office 365</a>
Office 365 a Azure pomocí služby Azure AD pro správu uživatelů a předplatných. Adresáře Azure je jako kontejner, ve kterém můžete skupině uživatelů a předplatných. Chcete-li používat stejné uživatelské účty pro vaše předplatná Azure a Office 365, ujistěte se, že předplatná Azure se vytvoří ve stejném adresáři jako předplatných Office 365. Mějte na paměti následující body:

* Předplatné se vytvoří v adresáři
* Uživatelé patří do adresáře
* Přihlášení k odběru jsou v adresáři uživatele, který vytvoří předplatné. Takže předplatné služeb Office 365 je vázán na stejný účet jako vaše předplatné Azure.
* Předplatná Azure jsou vlastněny jednotliví uživatelé v adresáři
* Předplatná Office 365 jsou vlastněny adresář samotný. Uživatelé s příslušným oprávněním v rámci adresáře mohou spravovat tato předplatná.

![Snímek obrazovky zobrazující vztah adresáře, uživatelů a předplatných.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Další informace najdete v tématu [předplatné Azure propojeno se službou Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému. 
