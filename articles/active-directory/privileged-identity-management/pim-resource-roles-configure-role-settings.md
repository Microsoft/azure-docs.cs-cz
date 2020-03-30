---
title: Konfigurace nastavení role prostředků Azure v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat nastavení role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db330a875b8241b642bcbc71fb0866c9833ee7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638676"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Konfigurace nastavení role prostředků Azure ve správě privilegovaných identit

Při konfiguraci nastavení role prostředků Azure definujete výchozí nastavení, která se použijí na přiřazení rolí prostředků Azure ve službě Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Pomocí následujících postupů nakonfigurujte pracovní postup schválení a určete, kdo může schválit nebo odepřít žádosti.

## <a name="open-role-settings"></a>Otevřít nastavení role

Podle těchto kroků otevřete nastavení role prostředků Azure.

1. Přihlaste se na [portál Azure](https://portal.azure.com/) s uživatelem v roli [Správce privilegovaných rolí.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **prostředky Azure**.

1. Vyberte prostředek, který chcete spravovat, například předplatné nebo skupinu pro správu.

    ![Stránka prostředků Azure se seznamem prostředků, které se dá spravovat](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Vyberte **nastavení role**.

    ![Stránka nastavení role se seznamem rolí prostředků Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Vyberte roli, jejíž nastavení chcete konfigurovat.

    ![Stránka podrobností o nastavení role se seznamem několika nastavení přiřazení a aktivace](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Vyberte **Upravit,** chcete-li otevřít podokno **Nastavení role.** První karta umožňuje aktualizovat konfiguraci pro aktivaci role v privilegované správě identit.

    ![Upravit stránku nastavení role s otevřenou kartou Aktivace](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Vyberte kartu **Přiřazení** nebo **Tlačítko Další: Přiřazení** v dolní části stránky a otevřete kartu nastavení úlohy. Tato nastavení řídí přiřazení rolí provedená v rozhraní Správy privilegovaných identit.

    ![Karta Přiřazení role na stránce nastavení role](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Pomocí karty **Oznámení** nebo **Tlačítka Další: Aktivace** v dolní části stránky se dostanete na kartu nastavení oznámení pro tuto roli. Tato nastavení řídí všechna e-mailová oznámení související s touto rolí.

    ![Karta Oznámení rolí na stránce Nastavení rolí](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    Na kartě **Oznámení** na stránce Nastavení rolí umožňuje správa privilegovaných identit podrobnou kontrolu nad tím, kdo přijímá oznámení a jaká oznámení obdrží.

    - **Vypnutí e-mailu**<br>Konkrétní e-maily můžete vypnout zrušením zaškrtnutí výchozího políčka příjemce a odstraněním dalších příjemců.  

    - **Omezení e-mailů na zadané e-mailové adresy**<br>E-maily odeslané výchozím příjemcům můžete vypnout zrušením zaškrtnutí políčka výchozího příjemce. Jako další příjemce pak můžete přidat další e-mailové adresy. Pokud chcete přidat více než jednu e-mailovou adresu, oddělte je pomocí středníku (;).

    - **Odesílání e-mailů výchozím i dalším příjemcům**<br>E-maily můžete odesílat jak výchozímu příjemci, tak dalšímu příjemci, zaškrtnutím políčka výchozího příjemce a přidáním e-mailových adres pro další příjemce.

    - **Pouze kritické e-maily**<br>U každého typu e-mailu můžete zaškrtnout políčko pro příjem pouze kritických e-mailů. To znamená, že správa privilegovaných identit bude nadále odesílat e-maily nakonfigurovaným příjemcům pouze v případě, že e-mail vyžaduje okamžitou akci. Například e-maily s žádostí o rozšíření přiřazení role se nespustí, zatímco se spustí e-maily vyžadující, aby správci schválili žádost o rozšíření.

1. Kdykoli vyberte tlačítko **Aktualizovat** a aktualizujte nastavení role.

## <a name="assignment-duration"></a>Doba trvání přiřazení

Při konfiguraci nastavení role můžete vybrat ze dvou možností doby trvání přiřazení pro každý typ přiřazení (způsobilý a aktivní). Tyto možnosti se stanou výchozí maximální dobou trvání, pokud je uživatel přiřazen k roli ve správě privilegovaných identit.

Můžete zvolit jednu z těchto **možností způsobilé** doby trvání přiřazení:

| | |
| --- | --- |
| **Povolit trvalé způsobilé přiřazení** | Správci prostředků mohou přiřadit trvalé způsobilé přiřazení. |
| **Vypršet platnost způsobilého přiřazení po** | Správci prostředků mohou vyžadovat, aby všechna způsobilá přiřazení měla zadané počáteční a koncové datum. |

A můžete zvolit jednu z těchto **aktivních** možností doby trvání přiřazení:

| | |
| --- | --- |
| **Povolit trvalé aktivní přiřazení** | Správci prostředků mohou přiřadit trvalé aktivní přiřazení. |
| **Vyprší platnost aktivního přiřazení po** | Správci prostředků mohou vyžadovat, aby všechna aktivní přiřazení měla zadané počáteční a koncové datum. |

> [!NOTE]
> Všechna přiřazení, která mají zadané koncové datum, mohou být obnovena správci prostředků. Uživatelé mohou také iniciovat samoobslužné požadavky na [rozšíření nebo obnovení přiřazení rolí](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Vyžadovat vícefaktorové ověřování

Správa privilegovaných identit poskytuje volitelné vynucení vícefaktorového ověřování Azure pro dva odlišné scénáře.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Vyžadovat vícefaktorové ověřování při aktivním přiřazení

V některých případech můžete chtít přiřadit uživatele nebo skupinu k roli na krátkou dobu (například jeden den). V takovém případě přiřazení uživatelé nemusí žádat o aktivaci. V tomto scénáři správa privilegovaných identit nemůže vynutit vícefaktorové ověřování, když uživatel používá přiřazení role, protože jsou již aktivní v roli od okamžiku, kdy je přiřazena.

Chcete-li zajistit, aby správce prostředků, který úkol plní, byl tím, kým se podle nich je, můžete vynutit vícefaktorové ověřování při aktivním přiřazení zaškrtnutím políčka **Vyžadovat vícefaktorové ověřování u aktivního přiřazení.**

### <a name="require-multi-factor-authentication-on-activation"></a>Vyžadovat vícefaktorové ověřování při aktivaci

Můžete vyžadovat, aby uživatelé, kteří mají nárok na roli, před aktivací prokázali, kdo používají Azure Multi-Factor Authentication. Vícefaktorové ověřování zajišťuje, že uživatel je tím, za koho se s přiměřenou jistotou jedná. Vynucení této možnosti chrání kritické prostředky v situacích, kdy uživatelský účet mohl být ohrožen.

Chcete-li před aktivací vyžadovat vícefaktorové ověřování, zaškrtněte políčko **Vyžadovat vícefaktorové ověřování při aktivaci.**

Další informace naleznete [v tématu Vícefaktorové ověřování a Správa privilegovaných identit](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximální doba trvání aktivace

Pomocí jezdce **Maximální doba trvání aktivace** nastavte maximální dobu v hodinách, po kterou zůstane role aktivní, než vyprší její platnost. Tato hodnota může být od jedné do 24 hodin.

## <a name="require-justification"></a>Vyžadovat odůvodnění

Můžete požadovat, aby uživatelé při aktivaci zadali obchodní odůvodnění. Chcete-li vyžadovat zarovnání, zaškrtněte políčko **Vyžadovat zarovnání aktivního přiřazení** nebo **Vyžadovat zarovnání při aktivaci.**

## <a name="require-approval-to-activate"></a>Vyžadovat schválení aktivace

Pokud chcete k aktivaci role vyžadovat schválení, postupujte takto.

1. Zaškrtněte políčko **Vyžadovat schválení pro aktivaci.**

1. Výběrem **možnosti Vybrat schvalovatele** otevřete **stránku Vybrat člena nebo skupinu.**

    ![Výběr emisi nebo podokna skupiny pro výběr schvalovatelů](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Vyberte alespoň jednoho uživatele nebo skupinu a klepněte na tlačítko **Vybrat**. Můžete přidat libovolnou kombinaci uživatelů a skupin. Je nutné vybrat alespoň jednoho schvalovatele. Neexistují žádní výchozí schvalovatelé.

    Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.

1. Jakmile zadáte všechna nastavení role, vyberte **Aktualizovat a** uložte změny.

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí prostředků Azure ve správě privilegovaných identit](pim-resource-roles-assign-roles.md)
- [Konfigurace výstrah zabezpečení pro role prostředků Azure ve správě privilegovaných identit](pim-resource-roles-configure-alerts.md)
