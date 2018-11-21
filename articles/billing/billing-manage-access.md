---
title: Správa přístupu k fakturaci, pomocí role Azure | Dokumentace Microsoftu
description: ''
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: cwatson
ms.openlocfilehash: 0a8b5532f00d5feb964109710132816a191298e7
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274768"
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Správa přístupu k fakturačních údajů pro Azure pomocí řízení přístupu na základě rolí

Přístup k fakturačním údajům Azure můžete členům vašeho týmu poskytnout přiřazením jedné z následujících uživatelských rolí k vašemu předplatnému: Správce účtu, Správce služeb, Spolusprávce, Vlastník, Přispěvatel, Čtenář a Čtenář fakturace. By mají přístup k fakturační údaje v [webu Azure portal](https://portal.azure.com/), a můžete použít [rozhraní API pro fakturaci](billing-usage-rate-card-overview.md) prostřednictvím kódu programu získat faktury (jednou přihlásí) a podrobnosti o použití. Další informace o, kdo může udělit rolí a rolí můžete udělat, najdete v článku [role v Azure RBAC](../role-based-access-control/built-in-roles.md).

## <a name="opt-in"></a> Povolíte dalším uživatelům pro přístup k faktury

Správce účtu musí přihlásit pomocí [webu Azure portal](https://portal.azure.com/) povolit přístup k faktury pro ostatní uživatele a prostřednictvím rozhraní API.

1. Jako správce účtu, vyberte své předplatné z [okně předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure portal.

1. Vyberte **faktury** a potom **přístup na fakturách**.

    ![Snímek obrazovky ukazuje, jak delegovat přístup k faktury](./media/billing-manage-access/AA-optin.png)

1. Zapnout **na** přístup a ukládají se změny, umožňuje uživatelům v předplatném obor role stáhnout fakturu.

    ![Snímek obrazovky ukazuje zapnutí nebo vypnutí pro delegování přístupu k faktuře](./media/billing-manage-access/AA-optinAllow.png)

Vyjádření výslovného souhlasu umožňuje Správce služeb, spolusprávce, vlastník, Přispěvatel, Čtenář a čtenář fakturace na předplatné, které chcete stahovat PDF faktury na webu Azure Portal. Starší než prosince 2016 faktury jsou však k dispozici pouze pro účet správce teď.

Správce účtu může také nakonfigurovat odesílání faktur e-mailem. Další informace najdete v tématu věnovaném [dostávání faktur e-mailem](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Přidání uživatele k roli Čtenář fakturace

Role Čtenář fakturace má přístup jen pro čtení na fakturační informace o předplatném na webu Azure portal a žádné přístup ke službám, jako jsou virtuální počítače a účty úložiště. Přiřazení role Čtenář fakturace jinému uživateli, který potřebuje přístup k fakturační informace o předplatném, ale není možnost správy služeb Azure. Tato role je vhodný pro uživatele v organizaci, kteří pouze provádět správu finanční a poplatků za odběr služeb Azure.

1. Vyberte požadované předplatné v [okně Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal.

1. Vyberte **řízení přístupu (IAM)** a potom klikněte na tlačítko **přidat**.

    ![Snímek obrazovky ukazuje IAM v okně předplatného](./media/billing-manage-access/select-iam.PNG)

1. Zvolte **Čtenář fakturace** v **vybrat roli** stránky.

    ![Snímek obrazovky ukazuje Čtenář fakturace v zobrazení místní nabídky](./media/billing-manage-access/select-roles.PNG)

1. Zadejte e-mail uživatele, kterého chcete pozvat, a potom kliknutím na **OK** odešlete pozvánku.

    ![Snímek obrazovky zobrazující k zadání e-mailu na pozvání uživatele](./media/billing-manage-access/add-user.PNG)

1. Postupujte podle pokynů v e-mailové pozvánce a přihlaste se jako čtenář fakturace.

    ![Snímek obrazovky, který ukazuje, co všechno uvidí Čtenář fakturace na webu Azure portal](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> Čtenář fakturace funkce je ve verzi preview a zatím nepodporuje neglobální cloudy. Předplatné Enterprise můžete zobrazit náklady, pokud má správce rozlehlé sítě povolené zobrazit náklady.

## <a name="adding-users-to-other-roles"></a>Přidání uživatelů do jiné role

Uživatelé v jiných rolích, jako je například roli vlastníka nebo přispěvatele, můžou používat jenom fakturační údaje, ale také služby Azure. Ke správě těchto rolí, najdete v článku [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Kdo má přístup k [centra pro účty](https://account.windowsazure.com)?

Pouze správce účtu může přihlásit do centra pro účty. Správce účtu se jejím právoplatným vlastníkem předplatného. Ve výchozím nastavení, osoby, která registrovanou službu nebo zakoupili předplatné Azure je správcem účtu, pokud [bylo převedeno vlastnictví předplatného](billing-subscription-transfer.md) někomu jinému. Správce účtu můžete vytvářet odběry, zrušit předplatná, změnit fakturační adresu pro přihlášení k odběru a spravovat zásady přístupu pro předplatné.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
