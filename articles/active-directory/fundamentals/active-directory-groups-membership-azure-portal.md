---
title: Přidání nebo odebrání skupiny z jiné skupiny – Azure AD
description: Pokyny k přidání nebo odebrání skupiny z jiné skupiny pomocí Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f3918016e35029ea6cc1b60d407ad475b35895b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565460"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Přidat nebo odebrat skupinu z jiné skupiny pomocí Azure Active Directory
Tento článek vám pomůže přidat a odebrat skupinu z jiné skupiny pomocí Azure Active Directory.

>[!Note]
>Pokud se pokoušíte odstranit nadřazenou skupinu, přečtěte si téma [jak aktualizovat nebo odstranit skupinu a její členy](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Přidat skupinu do jiné skupiny
Existující skupinu zabezpečení můžete přidat do jiné existující skupiny zabezpečení (označované také jako vnořené skupiny), vytvořením skupiny členů (podskupiny) a nadřazené skupiny. Skupina členů zdědí atributy a vlastnosti nadřazené skupiny a šetří čas konfigurace.

>[!Important]
>Momentálně nepodporujeme:<ul><li>Přidání skupin do skupiny synchronizované s místní službou Active Directory.</li><li>Přidávání skupin zabezpečení do skupin Microsoft 365.</li><li>Přidávání skupin Microsoft 365 do skupin zabezpečení nebo jiných skupin Microsoft 365.</li><li>Přiřazují se aplikace do vnořených skupin.</li><li>Použití licencí pro vnořené skupiny.</li><li>Přidávání distribučních skupin ve scénářích vnořování.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Přidání skupiny jako člena jiné skupiny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **skupiny**.

3. Na stránce **skupiny – všechny skupiny** vyhledejte a vyberte skupinu, která se stane členem jiné skupiny. Pro toto cvičení používáme skupinu **zásad MDM – západ** .

    >[!Note]
    >Tuto skupinu můžete přidat jako člena pouze do jedné skupiny v jednom okamžiku. Kromě toho **skupinový rámeček vybrat skupinu** filtruje zobrazení podle toho, jak vaše položka odpovídá libovolné části uživatele nebo názvu zařízení. Zástupné znaky se ale nepodporují.

    ![Skupina – stránka všech skupin se zásadami MDM – vybraná skupina západ](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Na stránce **zásady MDM – oblasti členství** v oblasti vyberte **členství ve skupině**, vyberte **Přidat**, vyhledejte skupinu, které chcete, aby byla skupina členem, a pak zvolte **Vybrat**. Pro toto cvičení používáme **zásadu MDM – všechny skupiny organizace** .

    **Zásada MDM – skupina – západ** je teď členem **zásady MDM – všechny skupiny organizace** a dědí všechny vlastnosti a konfiguraci zásad MDM – všechny skupiny organizace.

    ![Vytvoření členství ve skupině přidáním skupiny do jiné skupiny](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Projděte si stránku **zásady MDM – oblasti členství ve skupině** a zobrazte vztah skupiny a člena.

6. Podrobnější zobrazení vztahu skupiny a člena získáte tak, že vyberete název skupiny (**zásady MDM – všichni org**) a Prohlédněte si podrobnosti o stránce **zásady MDM – západ** .

## <a name="remove-a-group-from-another-group"></a>Odebrat skupinu z jiné skupiny
Existující skupinu zabezpečení můžete odebrat z jiné skupiny zabezpečení. Odebráním skupiny se ale také odstraní všechny zděděné atributy a vlastnosti pro její členy.

### <a name="to-remove-a-member-group-from-another-group"></a>Odebrání skupiny členů z jiné skupiny
1. Na stránce **skupiny – všechny skupiny** vyhledejte a vyberte skupinu, která se má odebrat jako člen jiné skupiny. Pro toto cvičení znovu použijeme skupinu **zásad MDM – západ** .

2. Na stránce **Přehled zásad MDM – západ** vyberte **členství ve skupině**.

3. Vyberte **zásadu MDM – všechny organizace** ze stránky zásady správy mobilních zařízení – **Západ-skupina** a pak v podrobnostech o **zásadách MDM – západ** vyberte **Odebrat** .

    ![Stránka členství ve skupině, která zobrazuje podrobnosti o členu i skupině](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Další informace
Následující články poskytují další informace o službě Azure Active Directory.

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Přidat nebo odebrat členy ze skupiny](active-directory-groups-members-azure-portal.md)

- [Úprava nastavení skupiny](active-directory-groups-settings-azure-portal.md)

- [Použití skupiny ke správě přístupu k aplikacím SaaS](../users-groups-roles/groups-saasapps.md)

- [Scénáře, omezení a známé problémy, které používají skupiny pro správu licencování v Azure Active Directory](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
