---
title: Konfigurace nastavení role Azure AD v PIM – Azure AD | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat nastavení role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee99a8e75fe8da85b1cf82623ed110991db24b66
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021797"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Konfigurace nastavení role Azure AD v Privileged Identity Management

Správce privilegovaných rolí může přizpůsobovat Privileged Identity Management (PIM) ve své organizaci Azure Active Directory (Azure AD), a to včetně změny prostředí pro uživatele, který aktivuje přiřazení oprávněné role.

## <a name="open-role-settings"></a>Otevřít nastavení role

Pomocí těchto kroků otevřete nastavení role Azure AD.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **role Azure AD**.

1. Klikněte na **Nastavení**.

    ![Role Azure AD – nastavení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Klikněte na **role**.

1. Klikněte na roli, jejíž nastavení chcete nakonfigurovat.

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

- Uživatelé, kteří mají účty Microsoft pro své e-mailové adresy (obvykle @outlook.com, ale ne vždycky), se nemůžou zaregistrovat pro Azure Multi-Factor Authentication. Pokud chcete přiřadit role uživatelům s účty Microsoft, měli byste je buď zaměnit, nebo zakázat službu Multi-Factor Authentication pro danou roli.
- Nemůžete zakázat Azure Multi-Factor Authentication pro role s vysokou úrovní oprávnění pro Azure AD a Office 365. Tato bezpečnostní funkce pomáhá chránit následující role:  
  
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
  - Správce uživatele

Další informace naleznete v tématu [Multi-Factor Authentication a Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Vyžadovat schválení

Pokud chcete delegovat požadované schválení pro aktivaci role, postupujte podle těchto kroků.

1. Nastavte přepínač **vyžadovat schválení** na **povoleno**. Podokno se rozbalí s možnostmi pro výběr schvalovatelů.

    ![Role Azure AD – nastavení-vyžadovat schválení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Pokud nezadáte žádné schvalovatele, bude správce privilegovaných rolí výchozím schvalovatelem a pak bude vyžadován ke schválení všech žádostí o aktivaci této role.

1. Chcete-li zadat schvalovatele, klikněte na **Vybrat schvalovatele**.

    ![Role Azure AD – nastavení-vyžadovat schválení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Kromě správce privilegovaných rolí vyberte aspoň jednoho schvalovatele a potom klikněte na **Vybrat**. Můžete vybrat uživatele nebo skupiny. Doporučujeme aspoň dva schvalovatele. I v případě, že jako schvalovatele přidáte sebe sama, nemůžete sami schválit aktivaci role. Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.

1. Po zadání všech nastavení role uložte změny kliknutím na **Uložit** .

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Konfigurace výstrah zabezpečení pro role Azure AD v Privileged Identity Management](pim-how-to-configure-security-alerts.md)
