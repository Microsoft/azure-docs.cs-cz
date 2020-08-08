---
title: Zjišťování rolí a přehledy rolí Azure AD (Preview) v Privileged Identity Management bývalého Průvodce zabezpečením – Azure Active Directory
description: Zjišťování a přehledy (dříve Průvodce zabezpečením) vám pomůžou převést trvalá přiřazení rolí Azure AD na přiřazení za běhu s Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/07/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1de7e98a9cb57f83b87589ceddedc3cdd80927
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005904"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Zjišťování a přehledy (Preview) pro role Azure AD (dřív Průvodce zabezpečením)

Pokud začínáte s Privileged Identity Management (PIM) v organizaci Azure Active Directory (Azure AD), můžete začít pomocí stránky **zjišťování a přehledy (Preview)** . Tato funkce ukazuje, kdo je přiřazený k privilegovaným rolím ve vaší organizaci, a jak pomocí PIM snadno měnit trvalá přiřazení rolí na přiřazení za běhu. U trvalých přiřazení privilegovaných rolí můžete v **zjišťování a přehledech**zobrazit nebo provádět změny (Preview). Jedná se o nástroj pro analýzu a nástroj akce.

## <a name="discovery-and-insights-preview"></a>Zjišťování a přehledy (Preview)

Předtím, než začne vaše organizace používat Privileged Identity Management, jsou všechna přiřazení rolí trvalá. Uživatelé jsou vždy ve svých přiřazených rolích, i když nepotřebují jejich oprávnění. Zjišťování a přehledy (ve verzi Preview), která nahrazuje dřívějšího Průvodce zabezpečením, zobrazuje seznam privilegovaných rolí a počet uživatelů, kteří jsou v těchto rolích aktuálně. Můžete vypsat přiřazení pro roli a získat další informace o přiřazených uživatelích, pokud některý z nich není známý.

: heavy_check_mark: **Společnost Microsoft doporučuje** , abyste zachovali 2 rozlomené účty, které jsou trvale přiřazeny k roli globálního správce, které při přihlašování nevyžadují službu Multi-Factor Authentication. Můžete je použít v jakémkoli scénáři rozbití nebo v případě, že žádný z nich není schopný zvýšit svou globální roli správce.

I když má uživatel účet Microsoft (jinými slovy, účet, který používá pro přihlášení ke službám Microsoftu, jako je Skype nebo Outlook.com), zachovat přiřazení rolí trvale. Pokud pro takovou roli požadujete, aby se aplikace Multi-Factor Authentication aktivovala, uživatel se zamkne.

## <a name="open-discovery-and-insights-preview"></a>Otevřete zjišťování a přehledy (Preview)

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD** a pak vyberte **zjišťování a přehledy (Preview)**. Otevřením stránky zahájíte proces zjišťování pro nalezení relevantních přiřazení rolí.

    ![Role Azure AD – stránka zjišťování a přehled znázorňující 3 možnosti](./media/pim-security-wizard/new-preview-link.png)

1. Vyberte možnost **snížit globální správce**.

    ![Omezení globálních správců – podokno role zobrazující všechny členy](./media/pim-security-wizard/new-preview-page.png)

1. Zkontrolujte seznam přiřazení rolí globálního správce.

    ![Omezení globálních správců – podokno role zobrazující všechny členy](./media/pim-security-wizard/new-global-administrator-list.png)

1. Výběrem **Další** vyberte uživatele nebo skupiny, které chcete nastavit jako oprávněné, a pak vyberte **nastavit jako oprávněné** nebo **odebrat přiřazení**.

    ![Převeďte členy na stránku s nárokem s možnostmi výběru členů, které chcete nastavit jako oprávněné pro role.](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. Můžete také vyžadovat, aby všichni globální správci zkontrolovali vlastní přístup.

    ![Stránka globální správci zobrazující oddíl kontrol přístupu](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. Po výběru některé z těchto změn se zobrazí oznámení Azure.

1. Pak můžete vybrat možnost **eliminovat stálý přístup** nebo **zkontrolovat instanční objekty** , aby se výše uvedené kroky opakovaly u jiných privilegovaných rolí a u přiřazení rolí instančního objektu. U přiřazení rolí instančního objektu můžete jenom odstranit přiřazení rolí.

    ![Další možnosti přehledů, které odstraňují stálý přístup a kontrolující objekty služby ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Udělte ostatním správcům přístup ke správě Privileged Identity Management](pim-how-to-give-access-to-pim.md)
