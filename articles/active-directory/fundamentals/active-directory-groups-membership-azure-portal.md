---
title: Přidání nebo odebrání skupiny z jiné skupiny – Azure AD
description: Pokyny, jak přidat nebo odebrat skupinu z jiné skupiny pomocí služby Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75423039"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Přidání nebo odebrání skupiny z jiné skupiny pomocí služby Azure Active Directory
Tento článek vám pomůže přidat a odebrat skupinu z jiné skupiny pomocí služby Azure Active Directory.

>[!Note]
>Pokud se pokoušíte odstranit nadřazenou skupinu, přečtěte si postup [aktualizace nebo odstranění skupiny a jejích členů](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Přidání skupiny do jiné skupiny
Existující skupinu zabezpečení můžete přidat do jiné existující skupiny zabezpečení (označované také jako vnořené skupiny) a vytvořit skupinu členů (podskupinu) a nadřazenou skupinu. Skupina členů zdědí atributy a vlastnosti nadřazené skupiny, což vám ušetří čas konfigurace.

>[!Important]
>V současné době nepodporujeme:<ul><li>Přidání skupin do skupiny synchronizované s místní službou Active Directory.</li><li>Přidání skupin zabezpečení do skupin Office 365</li><li>Přidání skupin Office 365 do skupin zabezpečení nebo jiných skupin Office 365</li><li>Přiřazení aplikací do vnořených skupin.</li><li>Použití licencí pro vnořené skupiny.</li><li>Přidání distribučních skupin ve scénářích vnoření.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Přidání skupiny jako člena jiné skupiny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Službu Azure Active Directory**a pak vyberte **Skupiny**.

3. Na stránce **Skupiny – všechny skupiny** vyhledejte a vyberte skupinu, která se má stát členem jiné skupiny. Pro toto cvičení používáme **zásadu MDM - západní** skupinu.

    >[!Note]
    >Skupinu můžete přidat jako člena pouze do jedné skupiny najednou. Pole Vybrat **skupinu** navíc filtruje zobrazení na základě porovnání vaší položky s libovolnou částí jména uživatele nebo zařízení. Zástupné znaky však nejsou podporovány.

    ![Skupiny – stránka Všechny skupiny se zásadami MDM – vybraná skupina Západ](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Na stránce **Zásady MDM – Západ – Členství ve skupině** vyberte Členství ve **skupině**, vyberte **Přidat**, vyhledejte skupinu, jejíž členem má být vaše skupina, a pak zvolte **Vybrat**. Pro toto cvičení používáme **zásady MDM – všechny skupiny organizace.**

    Zásada **MDM - skupina West** je nyní členem **zásady MDM - Všechny skupiny organizace,** dědí všechny vlastnosti a konfiguraci zásadY MDM - Všechny skupiny organizace.

    ![Vytvoření členství ve skupině přidáním skupiny do jiné skupiny](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Zkontrolujte **zásadu MDM – Západ – Členství ve skupině,** abyste viděli vztah skupiny a člena.

6. Podrobnější zobrazení vztahu skupiny a člena vyberte název skupiny (**Zásady MDM - Všechny organizace)** a podívejte se na **zásady MDM -** podrobnosti stránky Západ.

## <a name="remove-a-group-from-another-group"></a>Odebrání skupiny z jiné skupiny
Existující skupinu zabezpečení můžete odebrat z jiné skupiny zabezpečení. Odebrání skupiny však také odebere všechny zděděné atributy a vlastnosti pro své členy.

### <a name="to-remove-a-member-group-from-another-group"></a>Odebrání skupiny členů z jiné skupiny
1. Na stránce **Skupiny – všechny skupiny** vyhledejte a vyberte skupinu, která má být odebrána jako člen jiné skupiny. Pro toto cvičení opět používáme **zásadu MDM - západní** skupinu.

2. Na stránce **Zásady MDM – Západní přehled** vyberte **členství ve skupinách**.

3. Vyberte **zásadu MDM – všechny** skupiny organizace ze stránky **Zásady MDM – Západ – Členství ve skupině** a pak vyberte **Odebrat** ze **zásad MDM –** podrobnosti o západní stránce.

    ![Stránka členství ve skupině zobrazující podrobnosti o členovi i skupině](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Další informace
Následující články poskytují další informace o službě Azure Active Directory.

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Přidání nebo odebrání členů ze skupiny](active-directory-groups-members-azure-portal.md)

- [Úprava nastavení skupiny](active-directory-groups-settings-azure-portal.md)

- [Použití skupiny ke správě přístupu k aplikacím SaaS](../users-groups-roles/groups-saasapps.md)

- [Scénáře, omezení a známé problémy pomocí skupin ke správě licencí ve službě Azure Active Directory](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
