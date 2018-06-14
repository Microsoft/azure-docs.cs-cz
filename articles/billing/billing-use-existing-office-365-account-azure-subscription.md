---
title: Registrace ke službě Azure s účtem Office 365 | Microsoft Docs
description: Naučte se vytvářet předplatné služby Azure pomocí účtu Office 365
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
ms.date: 11/03/2017
ms.author: cjiang
ms.openlocfilehash: dfb39b809f9a1082682d269d8bd4c180c8a264ce
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204560"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Zaregistrujte si předplatné Azure pomocí svého účtu Office 365
Pokud máte předplatné služeb Office 365, můžete předplatné Azure, vytvořte účtu služeb Office 365. Přihlaste se k [portál Azure](https://portal.azure.com/) pomocí Office 365 uživatelské jméno a heslo. Pokud chcete nastavit virtuální počítače nebo použijte jinými službami Azure, musíte se musí zaregistrovat pro předplatné Azure. Vaše předplatné Azure můžete sdílet s ostatními a [pomocí řízení přístupu na základě Role můžete spravovat přístup k vašemu předplatnému Azure a prostředky](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Pokud již máte účet Office 365 a předplatné Azure, najdete v části [přidružit klienta služby Office 365 k předplatnému Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Získat předplatné Azure pomocí svého účtu Office 365

Ušetřit čas a vyhnout se účet šíření nutná registrace do Azure pomocí Office 365 uživatelské jméno a heslo. 

1. Zaregistrovat na [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Přihlaste se pomocí Office 365 uživatelské jméno a heslo. Účet, který používáte nemusí mít oprávnění správce. Pokud máte více než jeden účet Office 365, ujistěte se, že použijete přihlašovací údaje pro účet Office 365, který chcete přidružit k předplatnému Azure. 

   ![Snímek obrazovky, který zobrazuje stránku přihlášení.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Zadejte požadované informace a dokončete proces registrace. Pokud již máte účet Office 365, nemusí být některé informace požadované.

    ![Snímek obrazovky zobrazující registračního formuláře.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Pokud potřebujete přidat jiní lidé ve vaší organizaci k předplatnému Azure, najdete v části [Začínáme se správou přístupu na portálu Azure](../role-based-access-control/overview.md). 

## <a id="more-about-subs">Další informace o předplatných Azure a Office 365</a>
Office 365 a Azure použít ke správě uživatelů a odběry služby Azure AD. Adresář Azure je jako kontejner, ve kterém můžete skupině uživatelů a odběry. Chcete-li používat stejné uživatelské účty pro vaše předplatná Azure a Office 365, ujistěte se, že předplatná Azure jsou vytvořeny ve stejném adresáři jako předplatná Office 365. Mějte na paměti následující body:

* Předplatné získá vytvořil v rámci adresáře
* Uživatelé patří do adresáře
* Předplatné pojmenováváme v adresáři uživatele, který vytvoří odběr. Předplatné služeb Office 365, je vázaný na stejný účet jako vašeho předplatného Azure.
* Předplatná Azure jsou vlastněny jednotlivé uživatele v adresáři
* Předplatná Office 365 jsou vlastněny daný adresář. Uživatelé s potřebnými oprávněními v adresáři můžete spravovat tyto odběry.

![Snímek obrazovky, který znázorňuje vztah adresáře, uživatelů a odběry.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Další informace najdete v tématu [asociování předplatných Azure se službou Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém. 
