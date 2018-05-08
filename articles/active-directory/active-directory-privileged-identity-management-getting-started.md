---
title: Začínáme s aplikací Azure AD Privileged Identity Management | Dokumentace Microsoftu
description: Přečtěte si, jak spravovat privilegované identity pomocí aplikace Azure Active Directory Privileged Identity Management na webu Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: get-started-article
ms.workload: identity
ms.date: 09/17/2017
ms.author: curtand
ms.custom: pim
ms.openlocfilehash: 7137d7ba53c055c869bd967a04085d0c65aec683
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Začátek práce s aplikací Azure AD Privileged Identity Management

Pomocí aplikace Azure Active Directory (AD) Privileged Identity Management můžete spravovat, řídit a sledovat přístup v rámci organizace. Tento obor zahrnuje přístup k prostředkům Azure, Azure AD a dalších online služeb Microsoftu jako Office 365 nebo Microsoft Intune.

Tento článek vysvětluje, jak přidat aplikaci Azure AD PIM do řídicího panelu webu Azure Portal.

## <a name="add-the-privileged-identity-management-application"></a>Přidání aplikace Privileged Identity Management

Před použitím aplikace Azure AD Privileged Identity Management musíte tuto aplikaci přidat do řídicího panelu webu Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce adresáře.
2. Pokud má vaše organizace víc než jeden adresář, vyberte své uživatelské jméno v pravém horním rohu webu Azure Portal. Vyberte adresář, kde chcete PIM používat.
3. Vyberte **Všechny služby** a pomocí textového pole Filtr najděte **Azure AD Privileged Identity Management**.
4. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

Pokud jste první, kdo ve vašem adresáři používá Azure AD Privileged Identity Management, automaticky se vám přiřadí role **Správce zabezpečení** a **Správce privilegovaných rolí** v daném adresáři. Pouze správci privilegovaných rolí můžou spravovat přiřazení rolí uživatelů adresáře Azure AD. Kromě toho můžete spustit [průvodce zabezpečením](active-directory-privileged-identity-management-security-wizard.md), který vás provede počátečním zjišťováním a přiřazováním.

## <a name="navigate-to-your-tasks"></a>Přechod k úkolům

Po nastavení Azure AD Privileged Identity Management se vždy, když aplikaci otevřete, zobrazí navigační okno. Pomocí tohoto okna provádějte úkoly správy identity.

![Úkoly nejvyšší úrovně v PIM – snímek obrazovky](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

- **Moje role** zobrazí seznam oprávněných a aktivních rolí přiřazených vám. Toto je místo, kde můžete aktivovat jakoukoli přiřazenou oprávněnou roli.
- **Schválit žádosti (Preview)** zobrazí seznam žádostí o aktivaci oprávněných rolí adresáře Azure AD od uživatelů ve vašem adresáři, které jsou vám určené ke schválení. [Další informace](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
- **Čekající žádosti (Preview)** zobrazí všechny vaše žádosti čekající na aktivaci přiřazení oprávněné role.
- **Zkontrolovat přístup** zobrazí seznam aktivních kontrol přístupu, které jsou vám přiřazeny k dokončení, ať už kontrolujete přístup sobě nebo někomu jinému.
- **Role adresáře služby Azure AD** v části správy levé navigační nabídky zobrazí řídicí panel pro správce privilegovaných rolí, odkud se dá spravovat přiřazení rolí, měnit nastavení aktivace rolí, začít kontrolu přístupu atd. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta.
- **Role prostředků Azure (Preview)** v části správy levé navigační nabídky zobrazí seznam prostředků předplatného, pro které máte možnost výběru přiřazení role. 

## <a name="next-steps"></a>Další kroky
[Přehled aplikace Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) obsahuje podrobné informace o tom, jak můžete spravovat přístup pro správu ve vaší organizaci.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
