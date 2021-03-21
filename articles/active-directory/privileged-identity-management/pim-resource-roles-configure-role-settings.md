---
title: Konfigurace nastavení role prostředků Azure v PIM – Azure AD | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat nastavení role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ded81389fae5954c0e488f634945b4f1c1fe4c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009037"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Konfigurace nastavení role prostředků Azure v Privileged Identity Management

Při konfiguraci nastavení role prostředku Azure definujete výchozí nastavení, která se aplikují na přiřazení rolí prostředků Azure v Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Pomocí následujících postupů můžete nakonfigurovat pracovní postup schvalování a určit, kdo může žádosti schvalovat nebo zamítnout.

## <a name="open-role-settings"></a>Otevřít nastavení role

Pomocí těchto kroků otevřete nastavení pro roli prostředku Azure.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem v roli [správce privilegované role](../roles/permissions-reference.md#privileged-role-administrator) .

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **prostředky Azure**.

1. Vyberte prostředek, který chcete spravovat, jako je například předplatné nebo skupina pro správu.

    ![Prostředky Azure stránka výpisu prostředků, které se dají spravovat](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Vyberte **Nastavení role**.

    ![Stránka nastavení role výpis rolí prostředků Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Vyberte roli, jejíž nastavení chcete nakonfigurovat.

    ![Stránka s podrobnostmi nastavení role – seznam několika přiřazení a nastavení aktivace](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Výběrem možnosti **Upravit** otevřete podokno **Nastavení role** . První karta vám umožní aktualizovat konfiguraci pro aktivaci rolí v Privileged Identity Management.

    ![Stránka upravit nastavení role s otevřenou kartou aktivace](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Vyberte kartu **přiřazení** nebo tlačítko **Další: přiřazení** v dolní části stránky a otevřete tak kartu Nastavení přiřazení. Tato nastavení řídí přiřazení rolí v rozhraní Privileged Identity Management.

    ![Karta přiřazení role na stránce nastavení role](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Použijte kartu **oznámení** nebo tlačítko **Další: Aktivace** v dolní části stránky, abyste se dostali na kartu nastavení oznámení pro tuto roli. Tato nastavení řídí všechna e-mailová oznámení týkající se této role.

    ![Karta oznámení role na stránce nastavení role](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    Na kartě **oznámení** na stránce nastavení role Privileged Identity Management umožňuje detailní kontrolu nad tím, kdo obdrží oznámení a která oznámení obdrží.

    - **Vypnutí e-mailu**<br>Konkrétní e-maily můžete vypnout zrušením zaškrtnutí políčka Výchozí příjemce a odstraněním dalších příjemců.  

    - **Omezit e-maily na zadané e-mailové adresy**<br>E-maily odeslané výchozím příjemcům můžete vypnout zrušením zaškrtnutí políčka Výchozí příjemce. Pak můžete přidat další e-mailové adresy jako další příjemce. Chcete-li přidat více než jednu e-mailovou adresu, oddělte je středníkem (;).

    - **Posílat e-maily oběma výchozím příjemcům a dalším příjemcům**<br>E-maily můžete odesílat jak výchozímu příjemci, tak i dalšímu příjemci. stačí zaškrtnout políčko výchozí příjemce a přidat e-mailové adresy pro další příjemce.

    - **Jenom kritické e-maily**<br>Pro každý typ e-mailu můžete zaškrtnout políčko pro příjem pouze důležitých e-mailů. To znamená, že Privileged Identity Management bude dál posílat e-maily nakonfigurovaným příjemcům jenom v případě, že e-mail vyžaduje okamžitou akci. Například e-maily požadující uživatele, aby rozšířili přiřazení role, se nebudou aktivovat, zatímco budou aktivovány e-maily vyžadující správcům schválení žádosti o rozšíření.

1. Pokud chcete aktualizovat nastavení role, vyberte tlačítko **aktualizovat** .

## <a name="assignment-duration"></a>Doba trvání přiřazení

Při konfiguraci nastavení role můžete vybrat ze dvou možností trvání přiřazení pro každý typ přiřazení (způsobilé a aktivní). Tyto možnosti se stanou výchozím maximálním trváním, když je uživatel přiřazen k roli v Privileged Identity Management.

Můžete vybrat jednu z následujících možností trvání přiřazení s **nárokem** :

| | |
| --- | --- |
| **Povolení trvalého oprávněného přiřazení** | Správci prostředků mohou přiřadit trvalé oprávněné přiřazení. |
| **Vypršení platnosti oprávněného přiřazení** | Správci prostředků mohou vyžadovat, aby u všech oprávněných přiřazení bylo zadané počáteční a koncové datum. |

A můžete zvolit jednu z těchto **aktivních** možností trvání přiřazení:

| | |
| --- | --- |
| **Povoluje trvalé aktivní přiřazení** | Správci prostředků mohou přiřadit trvalé aktivní přiřazení. |
| **Po vypršení platnosti aktivního přiřazení** | Správci prostředků mohou vyžadovat, aby u všech aktivních přiřazení bylo zadané počáteční a koncové datum. |

> [!NOTE]
> Všechna přiřazení, která mají zadané koncové datum, může obnovit správci prostředků. Uživatelé můžou taky iniciovat žádosti samoobslužné služby a [prodloužit nebo prodloužit přiřazení rolí](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Vyžadovat Multi-Factor Authentication

Privileged Identity Management poskytuje volitelnou vynucování Multi-Factor Authentication Azure AD pro dva různé scénáře.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Vyžadovat Multi-Factor Authentication aktivnímu přiřazení

V některých případech můžete chtít přiřadit uživatele nebo skupinu do role po krátkou dobu (například jeden den). V takovém případě nemusíte přiřazeným uživatelům požádat o aktivaci. V tomto scénáři Privileged Identity Management nemůžou vymáhat ověřování službou Multi-Factor Authentication, pokud uživatel použije přiřazení role, protože už je v roli aktivní od okamžiku, kdy je přiřazený.

Chcete-li zajistit, že správce prostředků, který odpovídá přiřazení, říká, že jsou k němu přihlášeni, můžete vynutit vícefaktorové ověřování u aktivního přiřazení zaškrtnutím políčka **vyžadovat Multi-Factor Authentication v aktivním přiřazení** .

### <a name="require-multi-factor-authentication-on-activation"></a>Vyžadovat Multi-Factor Authentication při aktivaci

Můžete vyžadovat, aby uživatelé, kteří mají nárok na roli, prokázali, kdo používají Multi-Factor Authentication Azure AD, než se můžou aktivovat. Multi-Factor Authentication zajišťuje uživatelům, kteří říkají, že mají rozumnou jistotu. Vynucování této možnosti chrání kritické prostředky v situacích, kdy může dojít k ohrožení uživatelského účtu.

Chcete-li před aktivací vyžadovat službu Multi-Factor Authentication, zaškrtněte políčko **vyžadovat Multi-Factor Authentication při aktivaci** .

Další informace naleznete v tématu [Multi-Factor Authentication a Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximální doba trvání aktivace

Pomocí posuvníku **maximální doby trvání aktivace** můžete nastavit maximální dobu v hodinách, po kterou role zůstane aktivní, než vyprší její platnost. Tato hodnota může být od 1 do 24 hodin.

## <a name="require-justification"></a>Vyžadovat odůvodnění

Můžete vyžadovat, aby uživatelé při aktivaci zadali obchodní odůvodnění. Chcete-li vyžadovat odůvodnění, zaškrtněte políčko **vyžadovat odůvodnění u aktivního přiřazení** nebo políčko **vyžadovat odůvodnění při aktivaci** .

## <a name="require-approval-to-activate"></a>Vyžadovat schválení pro aktivaci

Pokud chcete pro aktivaci role vyžadovat schválení, postupujte podle těchto kroků.

1. Zaškrtněte políčko **vyžadovat schválení k aktivaci** .

1. Vyberte **Vybrat schvalovatelé** a otevřete stránku **Vybrat člen nebo skupinu** .

    ![Vyberte uživatele nebo podokno skupiny pro výběr schvalovatelů.](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Vyberte aspoň jednoho uživatele nebo skupinu a potom klikněte na **Vybrat**. Můžete přidat libovolnou kombinaci uživatelů a skupin. Musíte vybrat aspoň jednoho schvalovatele. Neexistují žádní výchozí schvalovatelé.

    Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.

1. Po zadání všech nastavení role vyberte **aktualizovat** , aby se změny uložily.

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Konfigurace výstrah zabezpečení pro role prostředků Azure v Privileged Identity Management](pim-resource-roles-configure-alerts.md)
