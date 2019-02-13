---
title: Konfigurace nastavení role prostředků Azure v PIM | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat nastavení role prostředků Azure v Azure AD Privileged Identity Management (PIM).
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
ms.date: 08/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8f793ee22796497b96a1acbd998397dbe2ac40d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162131"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Konfigurace nastavení role prostředků Azure v PIM

Při konfiguraci nastavení role prostředků Azure, můžete definovat výchozí nastavení, které se použijí na přiřazení role prostředků Azure v Azure AD Privileged Identity Management (PIM). Použijte následující postupy ke konfiguraci pracovního postupu schvalování a určit, kdo může schválit nebo zamítnout žádosti.

## <a name="open-role-settings"></a>Otevřete nastavení role

Postupujte podle těchto kroků a otevřete nastavení pro roli prostředků Azure.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako uživatel, který je členem skupiny [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) role.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **prostředky Azure**.

1. Klikněte na prostředek, který chcete spravovat, například předplatné nebo skupinu pro správu.

    ![Seznam prostředků Azure pro správu](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Klikněte na tlačítko **nastavení Role**.

    ![Nastavení role](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Klikněte na roli, jehož nastavení chcete konfigurovat.

    ![Podrobnosti nastavení role](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Klikněte na tlačítko **upravit** a otevřete tak podokno nastavení Role.

    ![Upravit nastavení rolí](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    V podokně nastavení Role pro každou roli existuje několik nastavení, která můžete konfigurovat.

## <a name="assignment-duration"></a>Doba trvání přiřazení

Můžete vybrat ze dvou možností doba trvání přiřazení pro každý typ přiřazení (oprávněných a aktivních), při konfiguraci nastavení pro roli. Tyto možnosti se výchozí maximální doba, kdy je člen přiřazeny k roli v PIM.

Můžete zvolit jednu z těchto **oprávněné** možnosti doba trvání přiřazení:

| | |
| --- | --- |
| **Povolit trvalé oprávněné přiřazení** | Správci prostředků můžete přiřadit trvalé oprávněné členství. |
| **Platnost oprávněných přiřazení po** | Správci prostředků můžete nutné, aby všechny oprávněné přiřazení zadaný počáteční a koncové datum. |

A můžete zvolit jednu z těchto **aktivní** možnosti doba trvání přiřazení:

| | |
| --- | --- |
| **Povolit trvalé aktivní přiřazení** | Správci prostředků můžete přiřadit trvalé aktivními členy. |
| **Platnost aktivních přiřazení po** | Správci prostředků můžete nutné, aby všechny aktivní přiřazení zadaný počáteční a koncové datum. |

> [!NOTE] 
> Všechna přiřazení, které mají zadaného koncového data můžete obnovit ve Správci prostředků. Navíc členy můžete zahájit samoobslužných žádostí do [rozšíření nebo obnovení přiřazení rolí](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Vyžadovat vícefaktorové ověřování

PIM umožňuje volitelné vynucování Azure Multi-Factor Authentication (MFA) pro dva různé scénáře.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Při aktivním přiřazení vyžadovat vícefaktorové ověřování

V některých případech můžete chtít přiřadit člena k roli na krátkou dobu (jeden den, například). V takovém případě nepotřebují přiřazených členů na žádost o aktivaci. V tomto scénáři nelze PIM vynucení vícefaktorového ověřování, když člen používá přiřazení role, protože je již aktivní v roli od okamžiku, kdy jsou přiřazeny.

Tak, aby byl správce prostředků splnění přiřazení kdo, Řekněme, že jsou, můžete vynutit vícefaktorové ověřování při aktivním přiřazení tak, že zkontrolujete **při aktivním přiřazení vyžadovat Vícefaktorové ověřování** pole.

### <a name="require-multi-factor-authentication-on-activation"></a>Vyžadovat Multi-Factor Authentication při aktivaci

Může vyžadovat oprávněné členy role ke spuštění MFA, před aktivací. Tento proces zajišťuje, že uživatel, který žádá o tom, že se aktivace, který říká, že jsou s jistotou přiměřené. Vynucování této možnosti chrání důležité prostředky v situacích, pokud uživatelský účet může být ohrožený.

Tak, aby vyžadovala oprávněného člena prováděný aktivace MFA, zkontrolujte, **vyžadovat Vícefaktorové ověřování při aktivaci** pole.

Další informace najdete v tématu [vícefaktorové ověřování (MFA) a PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximální doba trvání aktivace

Použití **maximální doba trvání aktivace** posuvník nastavit maximální dobu v hodinách, že role zůstane aktivní, než vyprší její platnost. Tato hodnota může být v rozmezí 1 až 24 hodin.

## <a name="require-justification"></a>Vyžadovat odůvodnění

Můžete vyžadovat, aby členové zadejte odůvodnění na aktivní přiřazení nebo při aktivaci. Chcete-li vyžadovat odůvodnění, zkontrolujte **při aktivním přiřazení vyžadovat odůvodnění** pole nebo **při aktivaci vyžadovat odůvodnění** pole.

## <a name="require-approval-to-activate"></a>K aktivaci vyžadovat schválení

Pokud chcete k aktivaci role vyžadovat schválení, postupujte podle těchto kroků.

1. Zkontrolujte, **k aktivaci vyžadovat schválení** zaškrtávací políčko.

1. Klikněte na tlačítko **vybrat schvalovatele** a otevřete tak podokno členů nebo skupin Select.

    ![Vyberte člena nebo skupinu.](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Vyberte alespoň jeden člen nebo skupinu a potom klikněte na tlačítko **vyberte**. Můžete přidat libovolnou kombinaci členové a skupiny. Musíte vybrat aspoň jeden schvalovatel. Neexistují žádné výchozí schvalovatele.

    Vyberte požadované možnosti se zobrazí v seznamu vybraných schvalovatelů.

1. Po zadání všech vašich nastavení role, klikněte na tlačítko **aktualizace** uložte provedené změny.

## <a name="next-steps"></a>Další postup

- [Přiřazení role prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
- [Konfigurace výstrah zabezpečení pro role prostředků Azure v PIM](pim-resource-roles-configure-alerts.md)
