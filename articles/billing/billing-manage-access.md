---
title: Správa přístupu k Azure fakturace použití rolí | Microsoft Docs
description: ''
services: ''
documentationcenter: ''
author: vikramdesai01
manager: vikdesai
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: vikdesai
ms.openlocfilehash: ded7904c6cb855994d92e68c4a1af64ccdb2c3a0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Správa přístupu k fakturační informace pro Azure pomocí řízení přístupu na základě rolí

Můžete udělit přístup Azure fakturační informace pro členy týmu přiřazením jednu z následujících rolí uživatele k předplatnému: správce účtu, Správce služby, spolusprávcem, vlastník, Přispěvatel, Čtenář a fakturace čtečky. By mají přístup k fakturační informace v [portál Azure](https://portal.azure.com/), a mohou používat [fakturace rozhraní API](billing-usage-rate-card-overview.md) prostřednictvím kódu programu získat faktury (jednou přihlásí) a podrobnosti o použití. Další informace o, který můžete udělit role, a které role co naleznete na adrese [role v Azure RBAC](../role-based-access-control/built-in-roles.md).

## <a name="opt-in"></a> Povolíte dalším uživatelům přístup k faktury

Účet správce musí povolit pomocí [portál Azure](https://portal.azure.com/) povolit přístup k faktury pro ostatní uživatele nebo přes rozhraní API.

1. Jako správce účtu, vyberte své předplatné z [odběry okno](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na portálu Azure.

1. Vyberte **faktury** a potom **přístup k faktury**.

    ![Snímek obrazovky ukazuje, jak delegovat přístup k faktury](./media/billing-manage-access/AA-optin.png)

1. Zapnout **na** přístup, za nímž následuje uložením změn, aby uživatelé v rámci předplatného obor role ke stažení faktury.

    ![Snímek obrazovky ukazuje zapnutí a vypnutí delegovat přístup k fakturaci](./media/billing-manage-access/AA-optinAllow.png)

Výslovným souhlasem umožňuje služby správce, spolusprávce, vlastník, Přispěvatel, Čtenář a fakturace čtečky u předplatného ke stažení faktury PDF na portálu Azure. Faktury starší než prosinec 2016 jsou však k dispozici pouze pro účet správce teď.

Správce účtu můžete taky nakonfigurovat tak, aby měl faktury odesílaly e-mailem. Další informace najdete v tématu [získat faktury v e-mailu](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Přidání uživatelů do role Čtenář fakturace

Role čtenáře fakturace má přístup jen pro čtení k fakturační informace o odběru na portálu Azure a žádný přístup ke službám, jako je například virtuální počítače a účty úložiště. Přiřadíte role Čtenář fakturace jinému uživateli, který potřebuje přístup k fakturační informace o předplatném, ale není možnost spravovat služby Azure. Tato role je vhodný pro uživatele v organizaci, kteří pouze provádět správu finanční a nákladů pro předplatná Azure.

1. Vyberte své předplatné z [odběry okno](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na portálu Azure.

1. Vyberte **přístup k ovládacímu prvku (IAM)** a pak klikněte na **přidat**.

    ![Snímek obrazovky ukazuje IAM v okně předplatného](./media/billing-manage-access/select-iam.PNG)

1. Zvolte **fakturace čtečky** v **vyberte roli** stránky.

    ![Snímek obrazovky ukazuje fakturace čtečky v automaticky otevřeném okně zobrazení](./media/billing-manage-access/select-roles.PNG)

1. Zadejte e-mailu pro uživatele, které chcete pozvat a pak klikněte na **OK** odeslat pozvánky.

    ![Snímek obrazovky zobrazující k zadání e-mailu pozvání uživatele](./media/billing-manage-access/add-user.PNG)

1. Postupujte podle pokynů v e-mailu pozvání k přihlášení jako čtečku fakturace.

    ![Snímek obrazovky, který znázorňuje co čtečky fakturace můžete zobrazit na portálu Azure](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> Funkci fakturace čtečky je ve verzi preview a zatím nepodporuje předplatného typu enterprise (EA) nebo neglobální cloudy.

## <a name="adding-users-to-other-roles"></a>Přidání uživatelů k jiné role

Uživatelé v jiných rolí, například vlastníka nebo přispěvatele, můžou používat jenom fakturační informace, ale také služby Azure. Ke správě těchto rolí, najdete v části [přidání nebo změna role Správce služby Azure, které spravují předplatné nebo služby](billing-add-change-azure-subscription-administrator.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Kdo má přístup [centra účtů](https://account.windowsazure.com)?

Pouze správce účtu může Přihlaste se k centru účtů. Správce účtu je právní vlastník předplatného. Ve výchozím nastavení, osoba, která registraci aplikace nebo kód zakoupili předplatné Azure je účet správce, pokud [byl přenos vlastnictví předplatného](billing-subscription-transfer.md) pošlete někomu jinému. Správce účtu můžete vytvářet odběry, zrušit předplatné, změňte adresu fakturace předplatného a spravovat zásady přístupu pro předplatné.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
