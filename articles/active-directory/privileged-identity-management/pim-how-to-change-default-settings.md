---
title: Konfigurace nastavení role Azure AD v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat nastavení role Azure AD v azure ad privilegované správy identit (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd525973a8c8ba5bb3103126b4ddb5b6973590a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205020"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Konfigurace nastavení role Azure AD ve správě privilegovaných identit

Správce privilegovaných rolí může přizpůsobit správu privilegovaných identit (PIM) ve své organizaci Azure Active Directory (Azure AD), včetně změny prostředí pro uživatele, který aktivuje způsobilé přiřazení rolí.

## <a name="determine-your-version-of-pim"></a>Určení verze PIM

Počínaje listopadem 2019 se část rolí Azure AD privileged identity Management aktualizuje na novou verzi, která odpovídá prostředí pro role prostředků Azure. Tím se vytvoří další funkce, stejně jako [změny existující rozhraní API](azure-ad-roles-features.md#api-changes). Při zavádění nové verze, které postupy, které budete postupovat v tomto článku, závisí na verzi správy privilegovaných identit, kterou aktuálně máte. Podle pokynů v této části určete, kterou verzi správy privilegovaných identit máte. Poté, co znáte verzi správy privilegovaných identit, můžete vybrat postupy v tomto článku, které odpovídají této verzi.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) s uživatelem, který je v roli [správce privilegované role.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otevřete **správu privilegovaných identit Azure AD**. Pokud máte v horní části stránky přehledu nápis, postupujte podle pokynů na kartě **Nová verze** tohoto článku. V opačném případě postupujte podle pokynů na kartě **Předchozí verze.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Podle kroků v tomto článku schválit nebo odepřít žádosti o role Azure AD.

# <a name="new-version"></a>[Nová verze](#tab/new)

## <a name="open-role-settings"></a>Otevřít nastavení role

Podle těchto kroků otevřete nastavení pro roli Azure AD.

1. Přihlaste se na [portál Azure](https://portal.azure.com/) s uživatelem v roli [Správce privilegovaných rolí.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
gt
1. Otevřete &gt; **nastavení rolí role**Azure **AD privilegované identity** &gt; **Azure AD** .

    ![Stránka nastavení role se seznamem rolí Azure AD](./media/pim-how-to-change-default-settings/role-settings.png)

1. Vyberte roli, jejíž nastavení chcete konfigurovat.

    ![Stránka podrobností o nastavení role se seznamem několika nastavení přiřazení a aktivace](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Vyberte **Upravit,** chcete-li otevřít stránku Nastavení role.

    ![Upravit stránku nastavení role s možnostmi aktualizace nastavení přiřazení a aktivace](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    V podokně nastavení rolí pro každou roli je k dispozici několik nastavení, která můžete nakonfigurovat.

## <a name="assignment-duration"></a>Doba trvání přiřazení

Při konfiguraci nastavení role můžete vybrat ze dvou možností doby trvání přiřazení pro každý typ přiřazení (způsobilý a aktivní). Tyto možnosti se stanou výchozí maximální dobou trvání, pokud je uživatel přiřazen k roli ve správě privilegovaných identit.

Můžete zvolit jednu z těchto **možností způsobilé** doby trvání přiřazení:

| | |
| --- | --- |
| **Povolit trvalé způsobilé přiřazení** | Globální správci a správci privilegovaných rolí mohou přiřadit trvalé způsobilé přiřazení. |
| **Vypršet platnost způsobilého přiřazení po** | Globální správci a správci privilegovaných rolí mohou vyžadovat, aby všechna způsobilá přiřazení měla zadané počáteční a koncové datum. |

A můžete zvolit jednu z těchto **aktivních** možností doby trvání přiřazení:

| | |
| --- | --- |
| **Povolit trvalé aktivní přiřazení** | Globální správci a správci privilegovaných rolí mohou přiřadit trvalé aktivní přiřazení. |
| **Vyprší platnost aktivního přiřazení po** | Globální správci a správci privilegovaných rolí mohou vyžadovat, aby všechna aktivní přiřazení měla zadané počáteční a koncové datum. |

> [!NOTE]
> Všechna přiřazení, která mají zadané koncové datum, mohou být obnovena globálními správci a správci privilegovaných rolí. Uživatelé mohou také iniciovat samoobslužné požadavky na [rozšíření nebo obnovení přiřazení rolí](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Vyžadovat vícefaktorové ověřování

Správa privilegovaných identit poskytuje volitelné vynucení vícefaktorového ověřování Azure pro dva odlišné scénáře.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Vyžadovat vícefaktorové ověřování při aktivním přiřazení

V některých případech můžete chtít přiřadit uživatele k roli na krátkou dobu (například jeden den). V takovém případě přiřazení uživatelé nemusí žádat o aktivaci. V tomto scénáři správa privilegovaných identit nemůže vynutit vícefaktorové ověřování, když uživatel používá přiřazení role, protože jsou již aktivní v roli od okamžiku, kdy je přiřazena.

Chcete-li zajistit, aby správce, který úkol plní, byl tím, kým se podle nich jedná, můžete vynutit vícefaktorové ověřování při aktivním přiřazení zaškrtnutím políčka **Vyžadovat vícefaktorové ověřování u aktivního přiřazení.**

### <a name="require-multi-factor-authentication-on-activation"></a>Vyžadovat vícefaktorové ověřování při aktivaci

Můžete vyžadovat, aby uživatelé, kteří mají nárok na roli, před aktivací prokázali, kdo používají Azure Multi-Factor Authentication. Vícefaktorové ověřování zajišťuje, že uživatel je tím, za koho se s přiměřenou jistotou jedná. Vynucení této možnosti chrání kritické prostředky v situacích, kdy uživatelský účet mohl být ohrožen.

Chcete-li před aktivací vyžadovat vícefaktorové ověřování, zaškrtněte políčko **Vyžadovat vícefaktorové ověřování při aktivaci** na kartě Přiřazení **nastavení role Úpravy**.

Další informace naleznete [v tématu Vícefaktorové ověřování a Správa privilegovaných identit](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximální doba trvání aktivace

Pomocí jezdce **Maximální doba trvání aktivace** nastavte maximální dobu v hodinách, po kterou zůstane role aktivní, než vyprší její platnost. Tato hodnota může být od jedné do 24 hodin.

## <a name="require-justification"></a>Vyžadovat odůvodnění

Můžete požadovat, aby uživatelé při aktivaci zadali obchodní odůvodnění. Chcete-li vyžadovat zarovnání, zaškrtněte políčko **Vyžadovat zarovnání aktivního přiřazení** nebo **Vyžadovat zarovnání při aktivaci.**

## <a name="require-approval-to-activate"></a>Vyžadovat schválení aktivace

Pokud nastavíte více schvalovatelů, schválení se dokončí, jakmile jeden z nich schválí nebo zamítne. Nemůžete vyžadovat schválení alespoň od dvou uživatelů. Chcete-li k aktivaci role vyžadovat schválení, postupujte takto.

1. Zaškrtněte políčko **Vyžadovat schválení pro aktivaci.**

1. Vyberte **Vybrat schvalovately**.

    ![Výběr emisi nebo podokna skupiny pro výběr schvalovatelů](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Vyberte alespoň jednoho uživatele a klepněte na tlačítko **Vybrat**. Je nutné vybrat alespoň jednoho schvalovatele. Neexistují žádní výchozí schvalovatelé.

    Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.

1. Jakmile zadáte všechna nastavení role, vyberte **Aktualizovat a** uložte změny.

# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

## <a name="open-role-settings"></a>Otevřít nastavení role

Podle těchto kroků otevřete nastavení pro roli Azure AD.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **role Azure AD**.

1. Vyberte **Nastavení**.

    ![Role Azure AD – nastavení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Vyberte **role**.

1. Vyberte roli, jejíž nastavení chcete konfigurovat.

    ![Role Azure AD – role nastavení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na stránce nastavení pro každou roli je několik nastavení, která můžete nakonfigurovat. Tato nastavení se týkají pouze uživatelů, kteří jsou **způsobilými** přiřazeními, nikoli **trvalými** přiřazeními.

## <a name="activations"></a>Aktivace

Pomocí posuvníku **Aktivace** nastavte maximální dobu v hodinách, po kterou zůstane role aktivní, než vyprší její platnost. Tato hodnota může být mezi 1 a 72 hodin.

## <a name="notifications"></a>Oznámení

Pomocí **přepínače Oznámení** můžete určit, zda budou správci při aktivaci rolí dostávat e-mailová oznámení. Toto oznámení může být užitečné pro detekci neoprávněných nebo nelegitimních aktivací.

Pokud je nastavena možnost **Povolit**, jsou oznámení odesílána na adrese:

- Správce privilegovaných rolí
- Správce zabezpečení
- Globální správce

Další informace naleznete [v tématu E-mailová oznámení v části Správa privilegovaných identit](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Lístek incidentu/požadavku

Přepínač **lístku Incident/Žádost** můžete vyžadovat, aby oprávnění správci při aktivaci své role uvadali číslo lístku. Tento postup může zefektivnit audity přístupu k rolím.

## <a name="multi-factor-authentication"></a>Multi-factor Authentication

Pomocí přepínače **vícefaktorového ověřování** určete, zda mají uživatelé před aktivací svých rolí vyžadovat ověření své identity pomocí vícefaktorového ověřování. Musí ověřit svou identitu pouze jednou za relaci, ne pokaždé, když aktivují roli. Při povolování vícefaktorové paměti je třeba mít na paměti dva tipy:

- Uživatelé, kteří mají účty Microsoft pro @outlook.comsvé e-mailové adresy (obvykle , ale ne vždy) se nemohou zaregistrovat pro Azure Multi-Factor Authentication. Pokud chcete přiřadit role uživatelům s účty Microsoft, měli byste je buď zařadit jako stálé správce, nebo zakázat vícefaktorové ověřování pro tuto roli.
- Vícefaktorové ověřování Azure nelze zakázat pro vysoce privilegované role pro Azure AD a Office 365. Tato bezpečnostní funkce pomáhá chránit následující role:  
  
  - Správce azure information protection
  - Správce fakturace
  - Správce cloudových aplikací
  - Správce dodržování předpisů
  - Správce podmíněného přístupu
  - Správce dynamics 365
  - Schvalovatel přístupu k zámku zákazníka
  - Adresář spisovatelů
  - Správce Exchange
  - Globální správce
  - Správce Intune
  - Správce Power BI
  - Správce privilegovaných rolí
  - Správce zabezpečení
  - Správce SharePointu
  - Správce Skypu pro firmy
  - Správce uživatele

Další informace naleznete [v tématu Vícefaktorové ověřování a Správa privilegovaných identit](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Vyžadovat schválení

Pokud chcete delegovat požadované schválení pro aktivaci role, postupujte takto.

1. Nastavte přepínač **Vyžadovat schválení** na **Povoleno**. Podokno se rozbalí s možnostmi výběru schvalovatelů.

    ![Role Azure AD – nastavení – vyžadují schválení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Pokud nezadáte žádné schvalovatele, správce privilegované role se stane výchozím schvalovatelem a je pak povinen schválit všechny žádosti o aktivaci pro tuto roli.

1. Chcete-li přidat schvalovatele, klepněte na **tlačítko Vybrat schvalovatelé**.

    ![Role Azure AD – nastavení – vyžadují schválení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Kromě správce privilegované role vyberte jednoho nebo více schvalovatelů a klepněte na tlačítko **Vybrat**. Doporučujeme přidat alespoň dva schvalovatele. I když se přidáte jako schvalovatel, nemůžete aktivaci role schválit sami. Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.

1. Po zadání všech nastavení role vyberte **Uložit** a uložte změny.

---

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD ve správě privilegovaných identit](pim-how-to-add-role-to-user.md)
- [Konfigurace výstrah zabezpečení pro role Azure AD ve správě privilegovaných identit](pim-how-to-configure-security-alerts.md)
