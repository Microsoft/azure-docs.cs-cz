---
title: Azure Privileged Identity Management schválení pracovních | Microsoft Docs
description: Další informace o schválení pracovních postupů v Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 6e14d91eae367ef62d1ff2e4cb6ddeb47c9fbbea
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261011"
---
# <a name="approvals"></a>Schvalování

## <a name="overview"></a>Přehled

Schválení Privileged Identity Management můžete nakonfigurovat role za účelem vyžadovat schválení pro aktivaci a zvolte jednu nebo víc uživatelů nebo skupin jako delegovaný schvalovatelů. Zachovat čtení Další informace o konfiguraci rolí a vyberte schvalovatelů.


## <a name="new-terminology"></a>Nové terminologie

*Oprávněný uživatel Role* – oprávněné role uživatel je uživatelem v rámci vaší organizace, který je přiřazený k roli služby Azure AD jako způsobilých (role vyžaduje aktivaci).

*Delegovaná schvalovatel* – delegované schvalovatel je jeden nebo více jednotlivce nebo skupiny v rámci služby Azure AD, který je zodpovědný za schvalování žádostí k aktivaci role.

## <a name="scenarios"></a>Scénáře

Privátní preview podporuje následující scénáře:

**Jako privilegované Role správce (PRA) můžete:**

-   [Povolit schválení pro konkrétní role](#enable-approval-for-specific-roles)

-   [Zadejte schvalovatel uživatelé nebo skupiny ke schválení žádostí](#specify-approver-users-and/or-groups-to-approve-requests)

-   [Zobrazit historii požadavku a schválení všech privilegovaných rolí](#view-request-and-approval-history-for-all-privileged-roles)

**Jako určeného schvalovatele můžete:**

-   [Zobrazit čeká na schválení (počet požadavků)](#view-pending-approvals-requests)

-   [Schválit nebo odmítnout žádosti o zvýšení oprávnění role (jeden nebo hromadně)](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [Uveďte její odůvodnění moje schválení nebo zamítnutí](#provide-justification-for-my-approval/rejection) 

**Jako oprávněný uživatel roli můžete:**

-   [Žádost o aktivaci role, který vyžaduje schválení](#request-activation-of-a-role-that-requires-approval)

-   [Zobrazení stavu vaši žádost o aktivaci](#view-the-status-of-your-request-to-activate)

-   [Dokončení úkolu ve službě Azure AD, pokud byla schválena aktivace](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Navigace

Aktualizovali jsme navigace pro podporu schválení

![](media/azure-ad-pim-approval-workflow/image001.png)

Výchozí úvodní stránka poskytuje pohodlné přístup k informacím o PIM a nová dokumentace schválení.

![](media/azure-ad-pim-approval-workflow/image002.png)

Přidali jsme také novou část pro všechny uživatele PIM, historie Moje auditu. Tady najdete všechny informace relevantní pro vaši identitu. To zahrnuje všechny vaše požadavky na vyřízení a dokončený, všechny rozhodnutí, které jste udělali týkající se požadavků, které můžete vyřešit a všechny vaše posledních aktivace role z jednoho místa.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Povolit schválení pro konkrétní role

Pokud chcete povolit schválení pro určité role, nejprve vyberte role Directory z levé navigaci.

![](media/azure-ad-pim-approval-workflow/image004.png)

Najděte a vyberte nastavení v levé navigaci Directory role

![](media/azure-ad-pim-approval-workflow/image006.png)

Vyberte privilegované role:

![](media/azure-ad-pim-approval-workflow/image009.png)

Vyberte možnost "Povolit" v vyžadují části:

![](media/azure-ad-pim-approval-workflow/image011.png)

Jakmile bude povoleno, v okně se rozbalí a zobrazí následující podrobnosti:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Pokud je nesmí zadat všechny schvalovatelů, stanou se PRA(s) schvalovatelům výchozí. PRA(s) by byly zapotřebí k schválit všechny žádosti o aktivaci pro tuto roli.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Zadejte schvalovatel uživatelé nebo skupiny ke schválení žádostí

Delegovat schválení, klikněte na možnost "Vyberte schvalovatelů":

![](media/azure-ad-pim-approval-workflow/image015.png)

Až se načte v okně vyberte schvalovatelů, pravděpodobně vyhledávání pro konkrétního uživatele nebo skupiny pomocí panelu Hledat v horní části, nebo výběrem ze seznamu předem vyplněná a pak klikněte na tlačítko "Vyberte" po dokončení:

![](media/azure-ad-pim-approval-workflow/image017.png)

Poznámka: Může vybrat více uživatelů nebo skupin současně.

Výběr se objeví v seznamu vybraný schvalovatelů, jak vidíte níže:

![](media/azure-ad-pim-approval-workflow/image019.png)

Odebrání schvalovatele, jednoduše kliknutím na tlačítko Odebrat vedle jména.

Pokud chcete přidat další schvalovatele, proces opakujte.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Zobrazit historii požadavku a schválení všech privilegovaných rolí

Chcete-li zobrazit historii požadavku a schválení všech privilegovaných rolí, vyberte historie auditu na řídicím panelu:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Můžete řadit data akcí a vyhledejte "Aktivace schválené"

### <a name="view-pending-approvals-requests"></a>Zobrazit čeká na schválení (počet požadavků)

Jako delegovaný schvalovatel obdržíte e-mailová oznámení při žádost čeká na schválení. Chcete-li zobrazit tyto požadavky na portálu PIM, na řídicím panelu (v nové navigační) vyberte kartu "Čekající žádosti o schválení" v levém navigačním panelu.

![](media/azure-ad-pim-approval-workflow/image023.png)

Z tohoto místa se zobrazí seznam žádosti čekající na schválení:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>Schválit nebo odmítnout žádosti o zvýšení oprávnění role (jeden nebo hromadně)

Vyberte požadavky, které chcete schválit nebo zamítnout a klikněte na tlačítko na panelu akcí, která odpovídá vaše rozhodnutí:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Uveďte její odůvodnění moje schválení nebo zamítnutí

Otevře se nové okno schválí nebo zamítne více požadavků najednou. Zadejte odůvodnění pro vaše rozhodnutí, a klikněte na tlačítko Schválit (nebo odepřít) v dolní části nebo v okně:

![](media/azure-ad-pim-approval-workflow/image029.png)

Po dokončení zpracování žádosti o symbolu stavu bude odrážet rozhodnutí, které jste nastavili (v tomto příkladu rozhodnutí je schválit):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>Žádost o aktivaci role, který vyžaduje schválení

Žádají o aktivaci role, který vyžaduje schválení lze zahájit z původního navigační PIM nebo nové navigační jako proces pro aktivaci role zůstává stejná. Jednoduše vyberte v seznamu rolí k aktivaci role:

![](media/azure-ad-pim-approval-workflow/image033.png)

Pokud privilegovaných rolí vyžaduje Vícefaktorové ověřování, budete vyzváni k dokončení této úlohy nejdřív:

![](media/azure-ad-pim-approval-workflow/image035.png)

Po dokončení, kliknout na aktivovat a zadejte odůvodnění (v případě potřeby):

![](media/azure-ad-pim-approval-workflow/image037.png)

Žadatel se zobrazí oznámení, že žádosti čekající na schválení:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Zobrazení stavu vaši žádost o aktivaci

Zobrazení stavu čekající žádost o aktivaci, musí se přístup z nové navigace. V levém navigačním panelu vyberte kartu "Moje žádosti o":

![](media/azure-ad-pim-approval-workflow/image041.png)

Stav žádosti bude jako výchozí nastavena na "Čekající na vyřízení", ale můžete přepínat zobrazíte všechny nebo odepření požadavků.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>Dokončení úkolu ve službě Azure AD, pokud byla schválena aktivace

Po schválení žádosti role je aktivní a veškerou práci, kterou vyžaduje tato role může pokračovat.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>Další postup

Je vhodné nám svůj názor. Prosím Nebojte se zde sdílet komentáře nebo zpětnou vazbu s námi!
