---
title: Jak přidat nebo odebrat skupiny z jiné skupiny ve službě Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak přidat nebo odebrat skupiny z jiné skupiny pomocí služby Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: c28fe5ef226fac993fde221b16bfa875ba4845ca
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579764"
---
# <a name="how-to-add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Postupy: Přidat nebo odebrat skupiny z jiné skupiny pomocí služby Azure Active Directory
Tento článek vám umožní přidat a odebrat skupiny z jiné skupiny pomocí služby Azure Active Directory.

>[!Note]
>Pokud se snažíte odstranit nadřazené skupiny, přečtěte si téma [aktualizace nebo odstranění skupiny a její členy](active-directory-groups-delete-group.md).

## <a name="add-a-group-as-a-member-to-another-group"></a>Přidání skupiny jako člen do jiné skupiny
Existující skupiny můžete přidat do jiné existující skupiny, vytvoření člena skupiny (podskupiny) a nadřazenou skupinu. Vlastnosti atributy a nadřazené skupiny, ušetříte čas konfigurační nastavení převezme skupina člena.

### <a name="to-add-a-group-as-a-member-to-another-group"></a>Přidání skupiny jako člen do jiné skupiny

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu globálního správce adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **skupiny**.

3. Na **skupiny – všechny skupiny** stránky, vyhledejte a vyberte skupinu, která se stane členem jiné skupiny. Pro toto cvičení používáme **zásady MDM - západní** skupiny.

    >[!Note]
    >Vaši skupinu můžete přidat jako člena pouze do jedné skupiny najednou. Kromě toho **vybrat skupinu** pole filtry pro zobrazení na základě porovnání svou položku do libovolné části názvu uživatele nebo zařízení. Zástupné znaky nejsou podporovány.

    ![Vybraná skupina skupiny – všechny skupiny stránka zásadám MDM. – západ](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Na **MDM zásad - západ - členství ve skupinách** stránce **členství ve skupinách**vyberte **přidat**, vyhledejte skupinu, kterou chcete, aby vaše skupina členem, a klikněte na tlačítko  **Vyberte**. Pro toto cvičení používáme **zásady MDM – všechny organizace** skupiny.

    **Zásady MDM - západní** skupiny je teď členem **zásady MDM – všechny organizace** skupina dědí všechny vlastnosti a konfigurace zásad MDM – všechny skupiny organizace.

    ![Členství ve skupině s vytvořením přidání skupiny do jiné skupiny](media/active-directory-groups-membership-azure-portal/add-group-membership.png)

5. Zkontrolujte **MDM zásad - západ - členství ve skupinách** stránku, abyste zobrazili skupina a člen relace.

    ![MDM - západ - zásad skupiny ve skupinách stránky zobrazující nadřazené skupiny](media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Podrobnější přehled vztahů skupiny a členů, vyberte název skupiny (**zásady MDM – všechny organizace**) a podívejte se na **zásady MDM - západní** stránce Podrobnosti.

    ![Skupiny členství stránky zobrazující člena a podrobnosti o skupině](media/active-directory-groups-membership-azure-portal/group-membership-review.png)

## <a name="remove-a-member-group-from-another-group"></a>Odebrání člena skupiny z jiné skupiny
Stávající člen skupiny můžete odebrat z jiné skupiny. Ale odebrání členství taky odebere všechny zděděné atributy a vlastnosti pro vaše uživatele.

### <a name="to-remove-a-member-group-from-another-group"></a>Odebrání člena skupiny z jiné skupiny
1. Na **skupiny – všechny skupiny** stránky, vyhledejte a vyberte skupinu, která se má odebrat jako člena jiné skupiny. Pro toto cvičení znovu používáme **zásady MDM - západní** skupiny.

2. Na **zásady MDM – přehled – západ** stránce **členství ve skupinách**.

    ![Zásady MDM – stránka s přehledem – západ](media/active-directory-groups-membership-azure-portal/group-membership-overview.png)

3. Vyberte **zásady MDM – všechny organizace** skupinu **MDM zásad - západ - členství ve skupinách** stránce a pak vyberte **odebrat** z **zásady MDM - západní** stránce Podrobnosti.

    ![Skupiny členství stránky zobrazující člena a podrobnosti o skupině](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)


## <a name="additional-information"></a>Další informace
Následující články poskytují další informace o službě Azure Active Directory.

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Přidat nebo odebrat členy ze skupiny](active-directory-groups-members-azure-portal.md)

- [Upravit nastavení skupiny](active-directory-groups-settings-azure-portal.md)

- [Přiřazení licencí uživatelům ve skupině](../users-groups-roles/licensing-groups-assign.md)
