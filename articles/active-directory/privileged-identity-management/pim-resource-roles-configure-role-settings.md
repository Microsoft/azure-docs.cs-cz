---
title: Konfigurace nastavení role prostředků Azure v PIM-Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat nastavení role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88645d07ff213b0b7a56f2b081f0aaed01fd2929
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804201"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Konfigurace nastavení role prostředků Azure v PIM

Při konfiguraci nastavení role prostředku Azure definujete výchozí nastavení, která se aplikují na přiřazení rolí prostředků Azure v Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Pomocí následujících postupů můžete nakonfigurovat pracovní postup schvalování a určit, kdo může žádosti schvalovat nebo zamítnout.

## <a name="open-role-settings"></a>Otevřít nastavení role

Pomocí těchto kroků otevřete nastavení pro roli prostředku Azure.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je členem role [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **prostředky Azure**.

1. Klikněte na prostředek, který chcete spravovat, jako je například předplatné nebo skupina pro správu.

    ![Prostředky Azure stránka výpisu prostředků, které se dají spravovat](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Klikněte na **Nastavení role**.

    ![Stránka nastavení role výpis rolí prostředků Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Klikněte na roli, jejíž nastavení chcete nakonfigurovat.

    ![Stránka s podrobnostmi nastavení role – seznam několika přiřazení a nastavení aktivace](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Kliknutím na **Upravit** otevřete podokno nastavení role.

    ![Stránka upravit nastavení role s možnostmi aktualizace nastavení přiřazení a aktivace](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    V podokně nastavení role pro každou roli existuje několik nastavení, která můžete konfigurovat.

## <a name="assignment-duration"></a>Doba trvání přiřazení

Při konfiguraci nastavení role můžete vybrat ze dvou možností trvání přiřazení pro každý typ přiřazení (způsobilé a aktivní). Tyto možnosti se stanou výchozím maximálním trváním, když je člen přiřazen k roli v PIM.

Můžete vybrat jednu z následujících možností trvání přiřazení s **nárokem** :

| | |
| --- | --- |
| **Povolení trvalého oprávněného přiřazení** | Správci prostředků mohou přiřadit trvalé oprávněné členství. |
| **Vypršení platnosti oprávněného přiřazení** | Správci prostředků mohou vyžadovat, aby u všech oprávněných přiřazení bylo zadané počáteční a koncové datum. |

A můžete zvolit jednu z těchto **aktivních** možností trvání přiřazení:

| | |
| --- | --- |
| **Povoluje trvalé aktivní přiřazení** | Správci prostředků mohou přiřadit trvalé aktivní členství. |
| **Po vypršení platnosti aktivního přiřazení** | Správci prostředků mohou vyžadovat, aby u všech aktivních přiřazení bylo zadané počáteční a koncové datum. |

> [!NOTE] 
> Všechna přiřazení, která mají zadané koncové datum, může obnovit správci prostředků. Členové mohou také zahájit samoobslužné žádosti o [prodloužení nebo obnovení přiřazení rolí](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Vyžadovat Multi-Factor Authentication

PIM poskytuje volitelnou vynucování Azure Multi-Factor Authentication (MFA) pro dva různé scénáře.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Vyžadovat Multi-Factor Authentication aktivnímu přiřazení

V některých případech můžete chtít přiřadit člena k roli po krátkou dobu (například jeden den). V takovém případě nepotřebují přiřazeným členům požádat o aktivaci. V tomto scénáři nemůže PIM vymáhat MFA, pokud člen používá přiřazení role, protože už jsou aktivní v roli od okamžiku, kdy jsou přiřazeni.

Chcete-li zajistit, že správce prostředků, který odpovídá přiřazení, říká, že jsou k němu přihlášeni, můžete ověřování MFA vynutit pro aktivní přiřazení zaškrtnutím políčka **vyžadovat Multi-Factor Authentication v aktivním přiřazení** .

### <a name="require-multi-factor-authentication-on-activation"></a>Vyžadovat Multi-Factor Authentication při aktivaci

Můžete vyžadovat, aby oprávnění členové role mohli spustit MFA, aby mohli provést aktivaci. Tento proces zajišťuje, že uživatel žádající o aktivaci je tím, kdo říkají, že má rozumnou jistotu. Vynucování této možnosti chrání kritické prostředky v situacích, kdy může dojít k ohrožení uživatelského účtu.

Pro vyžadování oprávněného člena pro spuštění vícefaktorového ověřování před aktivací zaškrtněte políčko **vyžadovat Multi-Factor Authentication při aktivaci** .

Další informace najdete v tématu [Multi-Factor Authentication (MFA) a PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximální doba trvání aktivace

Pomocí posuvníku **maximální doby trvání aktivace** můžete nastavit maximální dobu v hodinách, po kterou role zůstane aktivní, než vyprší její platnost. Tato hodnota může být v rozmezí od 1 do 24 hodin.

## <a name="require-justification"></a>Vyžadovat odůvodnění

Můžete požadovat, aby členové zadali odůvodnění aktivní přiřazení nebo když se aktivují. Chcete-li vyžadovat odůvodnění, zaškrtněte políčko **vyžadovat odůvodnění u aktivního přiřazení** nebo políčko **vyžadovat odůvodnění při aktivaci** .

## <a name="require-approval-to-activate"></a>K aktivaci vyžadovat schválení

Pokud chcete pro aktivaci role vyžadovat schválení, postupujte podle těchto kroků.

1. Zaškrtněte políčko **vyžadovat schválení k aktivaci** .

1. Kliknutím na **Vybrat schvalovatelé** otevřete podokno vybrat člena nebo skupinu.

    ![Vyberte člena nebo podokno skupiny pro výběr schvalovatelů.](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Vyberte aspoň jednoho člena nebo skupinu a potom klikněte na **Vybrat**. Můžete přidat libovolnou kombinaci členů a skupin. Musíte vybrat aspoň jednoho schvalovatele. Neexistují žádní výchozí schvalovatelé.

    Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.

1. Po zadání všech nastavení role uložte změny kliknutím na **aktualizovat** .

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
- [Konfigurace výstrah zabezpečení pro role prostředků Azure v PIM](pim-resource-roles-configure-alerts.md)
