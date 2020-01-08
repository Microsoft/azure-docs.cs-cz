---
title: Přidání nebo odebrání skupiny z jiné skupiny – Azure AD
description: Pokyny ohledně toho, jak přidat nebo odebrat skupiny z jiné skupiny pomocí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 830bf7134b3a8b0425c53673a1347dd77897a5bd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423039"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Přidat nebo odebrat skupiny z jiné skupiny pomocí služby Azure Active Directory
Tento článek vám umožní přidat a odebrat skupiny z jiné skupiny pomocí služby Azure Active Directory.

>[!Note]
>Pokud se snažíte odstranit nadřazené skupiny, přečtěte si téma [aktualizace nebo odstranění skupiny a její členy](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Přidání skupiny do jiné skupiny
Můžete přidat existující skupinu zabezpečení do jiné existující skupiny zabezpečení (označované také jako vnořené skupiny), vytváření členem skupiny (podskupiny) a nadřazenou skupinu. Vlastnosti atributy a nadřazené skupiny, ušetříte čas konfigurační nastavení převezme skupina člena.

>[!Important]
>Aktuálně nepodporujeme:<ul><li>Přidání skupin do skupiny synchronizované s místní službou Active Directory.</li><li>Přidání skupin zabezpečení do skupin Office 365.</li><li>Přidávání skupin Office 365 do skupin zabezpečení nebo jiných skupin Office 365.</li><li>Přiřazují se aplikace do vnořených skupin.</li><li>Použití licencí pro vnořené skupiny.</li><li>Přidávání distribučních skupin ve scénářích vnořování.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Přidání skupiny jako člena jiné skupiny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **skupiny**.

3. Na **skupiny – všechny skupiny** stránky, vyhledejte a vyberte skupinu, která se stane členem jiné skupiny. Pro toto cvičení používáme **zásady MDM - západní** skupiny.

    >[!Note]
    >Vaši skupinu můžete přidat jako člena pouze do jedné skupiny najednou. Kromě toho **vybrat skupinu** pole filtry pro zobrazení na základě porovnání svou položku do libovolné části názvu uživatele nebo zařízení. Zástupné znaky nejsou podporovány.

    ![Vybraná skupina skupiny – všechny skupiny stránka zásadám MDM. – západ](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Na **MDM zásad - západ - členství ve skupinách** stránce **členství ve skupinách**vyberte **přidat**, vyhledejte skupinu, kterou chcete, aby vaše skupina členem, a klikněte na tlačítko  **Vyberte**. Pro toto cvičení používáme **zásady MDM – všechny organizace** skupiny.

    **Zásady MDM - západní** skupiny je teď členem **zásady MDM – všechny organizace** skupina dědí všechny vlastnosti a konfigurace zásad MDM – všechny skupiny organizace.

    ![Členství ve skupině s vytvořením přidání skupiny do jiné skupiny](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Zkontrolujte **MDM zásad - západ - členství ve skupinách** stránku, abyste zobrazili skupina a člen relace.

6. Podrobnější přehled vztahů skupiny a členů, vyberte název skupiny (**zásady MDM – všechny organizace**) a podívejte se na **zásady MDM - západní** stránce Podrobnosti.

## <a name="remove-a-group-from-another-group"></a>Odebrat skupiny z jiné skupiny
Existující skupiny zabezpečení můžete odebrat z jiné skupiny zabezpečení. Ale odebrání skupině taky odebere všechny zděděné atributy a vlastnosti pro její členy.

### <a name="to-remove-a-member-group-from-another-group"></a>Odebrání člena skupiny z jiné skupiny
1. Na **skupiny – všechny skupiny** stránky, vyhledejte a vyberte skupinu, která se má odebrat jako člena jiné skupiny. Pro toto cvičení znovu používáme **zásady MDM - západní** skupiny.

2. Na **zásady MDM – přehled – západ** stránce **členství ve skupinách**.

3. Vyberte **zásady MDM – všechny organizace** skupinu **MDM zásad - západ - členství ve skupinách** stránce a pak vyberte **odebrat** z **zásady MDM - západní** stránce Podrobnosti.

    ![Skupiny členství stránky zobrazující člena a podrobnosti o skupině](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Další informace
Následující články poskytují další informace o službě Azure Active Directory.

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Přidat nebo odebrat členy ze skupiny](active-directory-groups-members-azure-portal.md)

- [Úprava nastavení skupiny](active-directory-groups-settings-azure-portal.md)

- [Použití skupiny ke správě přístupu k aplikacím SaaS](../users-groups-roles/groups-saasapps.md)

- [Scénáře, omezeních a známých problémech použití skupin pro správu licencování v Azure Active Directory](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
