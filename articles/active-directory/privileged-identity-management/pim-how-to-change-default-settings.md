---
title: Konfigurace nastavení role Azure AD v PIM-Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat nastavení role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 05/31/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9252e3bb8ccddb810074b485f6f073f1bda3f05
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804436"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>Konfigurace nastavení role Azure AD v PIM

Správce privilegovaných rolí může ve své organizaci přizpůsobit Azure Active Directory (Azure AD) Privileged Identity Management (PIM), včetně změny prostředí pro uživatele, který aktivuje přiřazení oprávněné role.

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

## <a name="activations"></a>Aktivace

Pomocí posuvníku **Aktivace** nastavte maximální dobu v hodinách, po kterou role zůstane aktivní, než vyprší její platnost. Tato hodnota může být v rozmezí od 1 do 72 hodin.

## <a name="notifications"></a>Oznámení

Pomocí přepínače **oznámení** určete, jestli budou správci dostávat e-mailová oznámení, když se role aktivují. To může být užitečné při detekci neautorizovaných nebo illegitimatech aktivací.

Když je nastavená možnost **Povolit**, odesílají se oznámení na:

- Správce privilegované role
- Správce zabezpečení
- Globální správce

Další informace najdete v tématu [e-mailová oznámení v PIM](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Lístek incidentu nebo žádosti

Pomocí přepínače **lístku incident/požadavek** určete, jestli se při aktivaci své role mají vyžadovat oprávnění správci, aby zahrnuli číslo lístku. To může být užitečné při provádění auditů přístupu k rolím.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Pomocí přepínače **Multi-Factor Authentication** určete, jestli se má vyžadovat, aby uživatelé před aktivací svých rolí vyžadovali ověření své identity pomocí vícefaktorového ověřování. Stačí je pouze ověřit pro každou relaci, nikoli při každém aktivaci role. Při povolování vícefaktorového ověřování je potřeba mít na paměti dvě tipy:

* Uživatelé, kteří mají účty Microsoft pro své e-mailové adresy (obvykle @outlook.comale ne vždycky), se nemůžou registrovat pro Azure MFA. Pokud chcete přiřadit role uživatelům s účty Microsoft, měli byste je buď nastavit jako trvalí správci, nebo pro tuto roli zakázat MFA.
* Nemůžete zakázat MFA pro role s vysokou úrovní oprávnění pro Azure AD a Office 365. Tato funkce je bezpečnostní, protože tyto role by se měly pečlivě chránit:  
  
  * Správce Azure Information Protection
  * Správce fakturace
  * Správce cloudových aplikací
  * Správce dodržování předpisů
  * Správce podmíněného přístupu
  * Správce služeb CRM
  * Schvalovatel přístupu ke Customer LockBoxu
  * Zapisovače do adresáře
  * Správce Exchange
  * Globální správce
  * Správce služby Intune
  * Správce služeb Power BI
  * Správce privilegované role
  * Správce zabezpečení
  * Správce služeb SharePointu
  * Správce Skypu pro firmy
  * Správce uživatelů

Další informace najdete v tématu [Multi-Factor Authentication (MFA) a PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Vyžadovat schválení

Pokud chcete pro aktivaci role vyžadovat schválení, postupujte podle těchto kroků.

1. Nastavte přepínač **vyžadovat schválení** na **povoleno**. Podokno se rozbalí s možnostmi pro výběr schvalovatelů.

    ![Role Azure AD – nastavení-vyžadovat schválení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Pokud **neurčíte** žádné schvalovatele, stanou se správci privilegovaných rolí výchozími schvalovateli. Správcům privilegovaných rolí by se vyžadovalo schválení **všech** žádostí o aktivaci této role.

1. Chcete-li zadat schvalovatele, klikněte na **Vybrat schvalovatele**.

    ![Role Azure AD – nastavení-vyžadovat schválení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Vyberte jednoho nebo více schvalovatelů a potom klikněte na **Vybrat**. Můžete vybrat uživatele nebo skupiny. Doporučuje se alespoň 2 schvalovatelé. Schválení držitele není povoleno.

    Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.

1. Po zadání všech nastavení role uložte změny kliknutím na **Uložit** .


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Další postup

- [Přiřazení rolí Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Konfigurace výstrah zabezpečení pro role Azure AD v PIM](pim-how-to-configure-security-alerts.md)
