---
title: Začínáme s aplikací Azure AD Privileged Identity Management | Dokumentace Microsoftu
description: Přečtěte si, jak spravovat privilegované identity pomocí aplikace Azure Active Directory Privileged Identity Management na webu Azure Portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 4eeb58e6e7377c2c0ec7db850a84bf1e296500d2
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623047"
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

Pokud jste první, kdo ve vašem adresáři používá Azure AD Privileged Identity Management, automaticky se vám přiřadí role **Správce zabezpečení** a **Správce privilegovaných rolí** v daném adresáři. Pouze správci privilegovaných rolí můžou spravovat přiřazení rolí uživatelů adresáře Azure AD. Kromě toho můžete spustit [průvodce zabezpečením](pim-security-wizard.md), který vás provede počátečním zjišťováním a přiřazováním.

## <a name="navigate-to-your-tasks"></a>Přechod k úkolům

Po nastavení Azure AD Privileged Identity Management se vždy, když aplikaci otevřete, zobrazí navigační okno. Pomocí tohoto okna provádějte úkoly správy identity.

![Úkoly nejvyšší úrovně v PIM – snímek obrazovky](./media/pim-getting-started/PIM_Tasks_New.png)

- **Moje role** zobrazí seznam oprávněných a aktivních rolí přiřazených vám. Toto je místo, kde můžete aktivovat jakoukoli přiřazenou oprávněnou roli.
- **Schválit žádosti (Preview)** zobrazí seznam žádostí o aktivaci oprávněných rolí adresáře Azure AD od uživatelů ve vašem adresáři, které jsou vám určené ke schválení. [Další informace](./azure-ad-pim-approval-workflow.md)
- **Čekající žádosti (Preview)** zobrazí všechny vaše žádosti čekající na aktivaci přiřazení oprávněné role.
- **Zkontrolovat přístup** zobrazí seznam aktivních kontrol přístupu, které jsou vám přiřazeny k dokončení, ať už kontrolujete přístup sobě nebo někomu jinému.
- **Role adresáře služby Azure AD** v části správy levé navigační nabídky zobrazí řídicí panel pro správce privilegovaných rolí, odkud se dá spravovat přiřazení rolí, měnit nastavení aktivace rolí, začít kontrolu přístupu atd. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta.
- **Role prostředků Azure (Preview)** v části správy levé navigační nabídky zobrazí seznam prostředků předplatného, pro které máte možnost výběru přiřazení role. 

## <a name="next-steps"></a>Další postup
[Přehled aplikace Azure AD Privileged Identity Management](pim-configure.md) obsahuje podrobné informace o tom, jak můžete spravovat přístup pro správu ve vaší organizaci.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
