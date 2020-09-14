---
title: Konfigurace nastavení role Azure AD v PIM – Azure AD | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat nastavení role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cedd6d019005a4c55bbabf6070c6d864da90f40a
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055482"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Konfigurace nastavení role Azure AD v Privileged Identity Management

Správce privilegovaných rolí může přizpůsobovat Privileged Identity Management (PIM) ve své organizaci Azure Active Directory (Azure AD), a to včetně změny prostředí pro uživatele, který aktivuje přiřazení oprávněné role.

## <a name="determine-your-version-of-pim"></a>Určení vaší verze PIM

Od listopadu 2019 se v části Privileged Identity Management role Azure AD aktualizuje na novou verzi, která se shoduje s prostředími pro role prostředků Azure. Tím se vytvoří další funkce a také [změny stávajícího rozhraní API](azure-ad-roles-features.md#api-changes). I když je nová verze zahrnuta, postupy, které provedete v tomto článku, závisí na verzi Privileged Identity Management, kterou máte v současnosti k dispozici. Podle pokynů v této části určete, kterou verzi Privileged Identity Management máte. Po zjištění vaší verze Privileged Identity Management můžete vybrat postupy v tomto článku, které odpovídají této verzi.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí uživatele, který je v roli [správce privilegované role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Otevřete **Azure AD Privileged Identity Management**. Pokud máte banner v horní části stránky s přehledem, postupujte podle pokynů na kartě **Nová verze** v tomto článku. Jinak postupujte podle pokynů na kartě **předchozí verze** .

  [![Vyberte Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Podle kroků v tomto článku můžete schvalovat nebo odmítat žádosti pro role Azure AD.

# <a name="new-version"></a>[Nová verze](#tab/new)

## <a name="open-role-settings"></a>Otevřít nastavení role

Pomocí těchto kroků otevřete nastavení role Azure AD.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem v roli [správce privilegované role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
gt
1. Otevřete **Azure AD Privileged Identity Management** &gt; Nastavení role **role Azure AD** &gt; **Role settings**.

    ![Stránka nastavení role se seznamem rolí Azure AD](./media/pim-how-to-change-default-settings/role-settings.png)

1. Vyberte roli, jejíž nastavení chcete nakonfigurovat.

    ![Stránka s podrobnostmi nastavení role – seznam několika přiřazení a nastavení aktivace](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Výběrem možnosti **Upravit** otevřete stránku nastavení rolí.

    ![Stránka upravit nastavení role s možnostmi aktualizace nastavení přiřazení a aktivace](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    V podokně nastavení role pro každou roli existuje několik nastavení, která můžete konfigurovat.

## <a name="assignment-duration"></a>Doba trvání přiřazení

Při konfiguraci nastavení role můžete vybrat ze dvou možností trvání přiřazení pro každý typ přiřazení (způsobilé a aktivní). Tyto možnosti se stanou výchozím maximálním trváním, když je uživatel přiřazen k roli v Privileged Identity Management.

Můžete vybrat jednu z následujících možností trvání přiřazení s **nárokem** :

| | |
| --- | --- |
| **Povolení trvalého oprávněného přiřazení** | Správci globálních správců a privilegovaných rolí můžou přiřadit trvalé oprávněné přiřazení. |
| **Vypršení platnosti oprávněného přiřazení** | Správci globálních správců a privilegovaných rolí můžou vyžadovat, aby u všech oprávněných přiřazení bylo zadané počáteční a koncové datum. |

A můžete zvolit jednu z těchto **aktivních** možností trvání přiřazení:

| | |
| --- | --- |
| **Povoluje trvalé aktivní přiřazení** | Globální správci a správci privilegovaných rolí můžou přiřadit trvalé aktivní přiřazení. |
| **Po vypršení platnosti aktivního přiřazení** | Správci globálních správců a privilegovaných rolí můžou vyžadovat, aby u všech aktivních přiřazení bylo zadané počáteční a koncové datum. |

> [!NOTE]
> Všechna přiřazení, která mají zadané koncové datum, můžou obnovit globální správci a správci privilegovaných rolí. Uživatelé můžou taky iniciovat žádosti samoobslužné služby a [prodloužit nebo prodloužit přiřazení rolí](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Vyžadovat Multi-Factor Authentication

Privileged Identity Management poskytuje volitelnou vynucování Multi-Factor Authentication Azure pro dva různé scénáře.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Vyžadovat Multi-Factor Authentication aktivnímu přiřazení

V některých případech můžete chtít přiřadit uživatele k roli po krátkou dobu (například jeden den). V takovém případě nemusíte přiřazeným uživatelům požádat o aktivaci. V tomto scénáři Privileged Identity Management nemůžou vymáhat ověřování službou Multi-Factor Authentication, pokud uživatel použije přiřazení role, protože už je v roli aktivní od okamžiku, kdy je přiřazený.

Aby se zajistilo, že správce naplní přiřazení, říká, že jsou k němu přihlášeni, můžete vynutit vícefaktorové ověřování u aktivního přiřazení zaškrtnutím políčka **vyžadovat Multi-Factor Authentication v aktivním přiřazení** .

### <a name="require-multi-factor-authentication-on-activation"></a>Vyžadovat Multi-Factor Authentication při aktivaci

Můžete vyžadovat, aby uživatelé, kteří mají nárok na roli, prokázali, že používají Multi-Factor Authentication Azure, než se můžou aktivovat. Multi-Factor Authentication zajišťuje uživatelům, kteří říkají, že mají rozumnou jistotu. Vynucování této možnosti chrání kritické prostředky v situacích, kdy může dojít k ohrožení uživatelského účtu.

Chcete-li před aktivací vyžadovat službu Multi-Factor Authentication, zaškrtněte políčko **vyžadovat Multi-Factor Authentication při aktivaci** na kartě přiřazení v **nastavení upravit roli**.

Další informace naleznete v tématu [Multi-Factor Authentication a Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximální doba trvání aktivace

Pomocí posuvníku **maximální doby trvání aktivace** můžete nastavit maximální dobu v hodinách, po kterou role zůstane aktivní, než vyprší její platnost. Tato hodnota může být od 1 do 24 hodin.

## <a name="require-justification"></a>Vyžadovat odůvodnění

Můžete vyžadovat, aby uživatelé při aktivaci zadali obchodní odůvodnění. Chcete-li vyžadovat odůvodnění, zaškrtněte políčko **vyžadovat odůvodnění u aktivního přiřazení** nebo políčko **vyžadovat odůvodnění při aktivaci** .

## <a name="require-approval-to-activate"></a>Vyžadovat schválení pro aktivaci

Pokud nastavíte více schvalovatelů, schválení se dokončí, jakmile jeden z nich schválí nebo zamítne. Nemůžete vyžadovat schválení alespoň pro dva uživatele. Pokud chcete pro aktivaci role vyžadovat schválení, postupujte podle těchto kroků.

1. Zaškrtněte políčko **vyžadovat schválení k aktivaci** .

1. Vyberte **Vybrat schvalovatelé**.

    ![Vyberte uživatele nebo podokno skupiny pro výběr schvalovatelů.](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Vyberte aspoň jednoho uživatele a pak klikněte na **Vybrat**. Musíte vybrat aspoň jednoho schvalovatele. Neexistují žádní výchozí schvalovatelé.

    Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.

1. Po zadání všech nastavení role vyberte **aktualizovat** , aby se změny uložily.

# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

## <a name="open-role-settings-previous-version"></a>Otevřít nastavení role (předchozí verze)

Pomocí těchto kroků otevřete nastavení role Azure AD.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. Vyberte **Nastavení**.

    ![Role Azure AD – nastavení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Vyberte **role**.

1. Vyberte roli, jejíž nastavení chcete nakonfigurovat.

    ![Role Azure AD – role nastavení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na stránce nastavení pro každou roli existuje několik nastavení, která můžete konfigurovat. Tato nastavení mají vliv jenom na uživatele, kteří jsou **oprávněná** přiřazení, ne na **trvalá** přiřazení.

## <a name="activations"></a>Aktivací

Pomocí posuvníku **Aktivace** nastavte maximální dobu v hodinách, po kterou role zůstane aktivní, než vyprší její platnost. Tato hodnota může být v rozmezí od 1 do 72 hodin.

## <a name="notifications"></a>Oznámení

Pomocí přepínače **oznámení** určete, jestli budou správci dostávat e-mailová oznámení, když se role aktivují. Toto oznámení může být užitečné při zjišťování neautorizovaných nebo illegitimatech aktivací.

Když je nastavená možnost **Povolit**, odesílají se oznámení na:

- Správce privilegovaných rolí
- Správce zabezpečení
- Globální správce

Další informace najdete v tématu [e-mailová oznámení v Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Lístek incidentu nebo žádosti

Použijte přepínač **lístku incident/požadavek** , který vyžádá oprávněným správcům, aby při aktivaci jejich role zahrnuli číslo lístku. Tento postup může zvýšit efektivitu přístupu k rolím.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Pomocí přepínače **Multi-Factor Authentication** určete, jestli se má vyžadovat, aby uživatelé před aktivací svých rolí vyžadovali ověření své identity pomocí vícefaktorového ověřování. Musí jenom ověřit identitu jenom jednou pro každou relaci, a ne pokaždé, když se role aktivuje. Při povolování vícefaktorového ověřování je potřeba mít na paměti dvě tipy:

- Uživatelé, kteří mají účty Microsoft pro své e-mailové adresy (obvykle @outlook.com ale ne vždycky), se nemůžou zaregistrovat pro Azure Multi-Factor Authentication. Pokud chcete přiřadit role uživatelům s účty Microsoft, měli byste je buď zaměnit, nebo zakázat službu Multi-Factor Authentication pro danou roli.
- Nemůžete zakázat Azure Multi-Factor Authentication pro role s vysokou úrovní oprávnění pro Azure AD a Microsoft 365. Tato bezpečnostní funkce pomáhá chránit následující role:  
  
  - Správce Azure Information Protection
  - Správce fakturace
  - Správce cloudové aplikace
  - Správce dodržování předpisů
  - Správce podmíněného přístupu
  - Správce Dynamics 365
  - Schvalovatel přístupu k bezpečnostnímu modulu zákazníka
  - Zapisovače adresářů
  - Správce Exchange
  - Globální správce
  - Správce Intune
  - Správce Power BI
  - Správce privilegovaných rolí
  - Správce zabezpečení
  - Správce SharePointu
  - Správce Skypu pro firmy
  - Správce uživatelů

Další informace naleznete v tématu [Multi-Factor Authentication a Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Vyžadovat schválení

Pokud chcete delegovat požadované schválení pro aktivaci role, postupujte podle těchto kroků.

1. Nastavte přepínač **vyžadovat schválení** na **povoleno**. Podokno se rozbalí s možnostmi pro výběr schvalovatelů.

    ![Role Azure AD – nastavení-vyžadovat schválení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Pokud nezadáte žádné schvalovatele, bude správce privilegovaných rolí výchozím schvalovatelem a pak bude vyžadován ke schválení všech žádostí o aktivaci této role.

1. Chcete-li přidat schvalovatele, klikněte na **Vybrat schvalovatele**.

    ![Role Azure AD – nastavení-vyžadovat schválení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Kromě správce privilegovaných rolí vyberte aspoň jednoho schvalovatele a potom klikněte na **Vybrat**. Doporučujeme přidat alespoň dva schvalovatele. I v případě, že jako schvalovatele přidáte sebe sama, nemůžete sami schválit aktivaci role. Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.

1. Po zadání všech nastavení role uložte změny kliknutím na **Uložit** .

---

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Konfigurace výstrah zabezpečení pro role Azure AD v Privileged Identity Management](pim-how-to-configure-security-alerts.md)
