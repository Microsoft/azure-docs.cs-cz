---
title: Vyhledejte prostředky Azure ke správě v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak zjistit prostředky Azure pro správu v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9ca4862f129b2da23a1d1ad8bb0b1bd0a5078f
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476278"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Vyhledejte prostředky Azure ke správě v PIM

Pomocí Azure Active Directory (Azure AD) Privileged Identity Management (PIM), můžete zlepšit ochranu vašich prostředků Azure. To je užitečné pro organizace, které už používají k ochraně role Azure AD PIM a vlastníkům správu skupin a předplatné, kteří chtějí zabezpečit prostředky v produkčním prostředí.

Když poprvé nastavujete PIM pro prostředky Azure, budete muset zjistit a vyberte prostředky, které chcete chránit pomocí PIM. Neexistuje žádné omezení počtu prostředků, které můžete spravovat pomocí služby PIM. Však doporučujeme začít s prostředky těch nejdůležitějších (produkce).

## <a name="discover-resources"></a>Zjišťování prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **prostředky Azure**.

    Pokud je toto vaše první přihlášení pomocí PIM pro prostředky Azure, uvidíte podokno zjišťování prostředků.

    ![Zjišťovat prostředky podokno se žádné prostředky uvedené pro první zkušenosti čas](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Pokud jiný prostředek nebo adresáře správce ve vaší organizaci je už správu prostředků Azure v PIM, zobrazí se vám seznam prostředků, které jsou aktuálně spravován.

    ![Zjistit prostředky podokně výpis prostředků, které jsou aktuálně spravován](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Klikněte na tlačítko **zjistit prostředky** ke spuštění prostředí pro zjišťování.

    ![Podokno zjišťování výpisy seznamů prostředků, které jde spravovat jako jsou předplatná a skupiny pro správu](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. V podokně zjišťování pomocí **filtr stavu prostředků** a **vyberte typ prostředku** filtrovat správu skupiny nebo předplatného máte oprávnění k zápisu do. To je pravděpodobně nejjednodušším začít **všechny** původně.

    Je možné pouze vyhledejte a vyberte správu skupiny nebo předplatného prostředky spravovat pomocí PIM. Když spravujete skupinu pro správu nebo předplatného v PIM, můžete také spravovat její podřízené prostředky.

1. Přidáte značku zaškrtnutí vedle všech nespravovaných prostředků, které chcete spravovat.

1. Klikněte na tlačítko **prostředky spravovat** k zahájení správy vybrané prostředky.

    > [!NOTE]
    > Po nastavení skupiny pro správu nebo předplatného je spravovat, nemůže nespravované. Odebírá se nastavení PIM tomu jiný správce prostředků.

    ![Zjišťování podokno s vybraného zdroje a zvýrazněnou možností prostředků spravovat](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Pokud se zobrazí zpráva k potvrzení onboarding vybraného prostředku ke správě, klikněte na tlačítko **Ano**.

    ![Zpráva s potvrzením připojení vybrané prostředky pro správu](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)
- [Přiřazení role prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
