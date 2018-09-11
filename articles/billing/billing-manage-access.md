---
title: Správa přístupu k fakturaci, pomocí role Azure | Dokumentace Microsoftu
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
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: vikdesai
ms.openlocfilehash: 38702fde344bb5fb831f7c26177438456035beae
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294455"
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Správa přístupu k fakturačních údajů pro Azure pomocí řízení přístupu na základě rolí

Můžete udělit přístup pro Azure fakturační údaje členům vašeho týmu tak, že jeden z následujících rolí uživatele přiřadíte ke svému předplatnému: účet správce, Správce služeb, spolusprávce, vlastník, Přispěvatel, Čtenář a čtenář fakturace. By mají přístup k fakturační údaje v [webu Azure portal](https://portal.azure.com/), a můžete použít [rozhraní API pro fakturaci](billing-usage-rate-card-overview.md) prostřednictvím kódu programu získat faktury (jednou přihlásí) a podrobnosti o použití. Další informace o, kdo může udělit rolí a rolí můžete udělat, najdete v článku [role v Azure RBAC](../role-based-access-control/built-in-roles.md).

## <a name="opt-in"></a> Povolíte dalším uživatelům pro přístup k faktury

Správce účtu musí přihlásit pomocí [webu Azure portal](https://portal.azure.com/) povolit přístup k faktury pro ostatní uživatele a prostřednictvím rozhraní API.

1. Jako správce účtu, vyberte své předplatné z [okně předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure portal.

1. Vyberte **faktury** a potom **přístup na fakturách**.

    ![Snímek obrazovky ukazuje, jak delegovat přístup k faktury](./media/billing-manage-access/AA-optin.png)

1. Zapnout **na** přístup a ukládají se změny, umožňuje uživatelům v předplatném obor role stáhnout fakturu.

    ![Snímek obrazovky ukazuje zapnutí nebo vypnutí pro delegování přístupu k faktuře](./media/billing-manage-access/AA-optinAllow.png)

Vyjádření výslovného souhlasu umožňuje Správce služeb, spolusprávce, vlastník, Přispěvatel, Čtenář a čtenář fakturace na předplatné, které chcete stahovat PDF faktury na webu Azure Portal. Starší než prosince 2016 faktury jsou však k dispozici pouze pro účet správce teď.

Správce účtu můžete nakonfigurovat také mít faktury posílat e-mailem. Další informace najdete v tématu [získat faktury e-mailem](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Přidání uživatele k roli Čtenář fakturace

Role Čtenář fakturace má přístup jen pro čtení na fakturační informace o předplatném na webu Azure portal a žádné přístup ke službám, jako jsou virtuální počítače a účty úložiště. Přiřazení role Čtenář fakturace jinému uživateli, který potřebuje přístup k fakturační informace o předplatném, ale není možnost správy služeb Azure. Tato role je vhodný pro uživatele v organizaci, kteří pouze provádět správu finanční a poplatků za odběr služeb Azure.

1. Vyberte své předplatné z [okně předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure portal.

1. Vyberte **řízení přístupu (IAM)** a potom klikněte na tlačítko **přidat**.

    ![Snímek obrazovky ukazuje IAM v okně předplatného](./media/billing-manage-access/select-iam.PNG)

1. Zvolte **Čtenář fakturace** v **vybrat roli** stránky.

    ![Snímek obrazovky ukazuje Čtenář fakturace v zobrazení místní nabídky](./media/billing-manage-access/select-roles.PNG)

1. Zadejte e-mailu pro daného uživatele chcete pozvat a potom klikněte na **OK** k odesílání e-mailové pozvánce.

    ![Snímek obrazovky zobrazující k zadání e-mailu na pozvání uživatele](./media/billing-manage-access/add-user.PNG)

1. Postupujte podle pokynů v e-mailovou pozvánku a přihlaste se jako čtenář fakturace.

    ![Snímek obrazovky, který ukazuje, co všechno uvidí Čtenář fakturace na webu Azure portal](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> Čtenář fakturace funkce je ve verzi preview a zatím nepodporuje předplatné enterprise (EA) nebo platformou cloudy.

## <a name="adding-users-to-other-roles"></a>Přidání uživatelů do jiné role

Uživatelé v jiných rolích, jako je například roli vlastníka nebo přispěvatele, můžou používat jenom fakturační údaje, ale také služby Azure. Ke správě těchto rolí, najdete v článku [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Kdo má přístup k [centra pro účty](https://account.windowsazure.com)?

Pouze správce účtu může přihlásit do centra pro účty. Správce účtu se jejím právoplatným vlastníkem předplatného. Ve výchozím nastavení, osoby, která registrovanou službu nebo zakoupili předplatné Azure je správcem účtu, pokud [bylo převedeno vlastnictví předplatného](billing-subscription-transfer.md) někomu jinému. Správce účtu můžete vytvářet odběry, zrušit předplatná, změnit fakturační adresu pro přihlášení k odběru a spravovat zásady přístupu pro předplatné.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
