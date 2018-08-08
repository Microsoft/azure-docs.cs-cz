---
title: Azure Privileged Identity Management schvalovacích pracovních postupů | Dokumentace Microsoftu
description: Další informace o pracovních postupů schvalování v Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 14d0cdc0bde1081f1a020c7039596a5b6880070f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619744"
---
# <a name="approvals"></a>Schvalování

## <a name="overview"></a>Přehled

Schvalování Privileged Identity Management můžete konfigurovat role, které vyžadují schválení pro aktivaci a zvolte jednu nebo více uživatelů nebo skupin jako delegovaný schvalovatele. Pokračujte ve čtení a zjistěte, jak nakonfigurovat role a vybrat schvalovatele.


## <a name="new-terminology"></a>Nový terminologií

*Oprávněný uživatel roli* – oprávněné role uživatele je uživateli ve vaší organizaci, který je přiřazený k roli Azure AD jako oprávněné (role vyžaduje aktivaci).

*Delegovaná schvalovatel* – delegovaný schvalovatel má na jeden nebo více jednotlivců nebo skupin v rámci služby Azure AD, který je zodpovědný za schvalování žádostí o aktivaci role.

## <a name="scenarios"></a>Scénáře

Privátní verze preview podporuje následující scénáře:

**Jako privilegované Role správce (PRA) můžete:**

-   [Povolit schválení pro konkrétní role](#enable-approval-for-specific-roles)

-   [Zadejte uživatele schvalovatele a/nebo skupiny schválit žádosti](#specify-approver-users-and/or-groups-to-approve-requests)

-   [Zobrazit historii žádosti a schválení pro všechny privilegované role](#view-request-and-approval-history-for-all-privileged-roles)

**Jako určeného schvalovatele můžete:**

-   [Zobrazení čekajících na schválení (požadavků)](#view-pending-approvals-requests)

-   [schválení nebo odmítnutí žádostí o zvýšení oprávnění role (jeden nebo hromadně)](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [Uveďte její odůvodnění moje schválení nebo zamítnutí](#provide-justification-for-my-approval/rejection) 

**Jako oprávněný uživatel roli můžete:**

-   [žádost o aktivaci role, která vyžaduje schválení](#request-activation-of-a-role-that-requires-approval)

-   [Zobrazení stavu vaši žádost o aktivaci](#view-the-status-of-your-request-to-activate)

-   [Dokončení úlohy ve službě Azure AD, pokud aktivace byla schválena](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Navigace

Aktualizovali jsme navigace pro podporu schválení

![](media/azure-ad-pim-approval-workflow/image001.png)

Výchozí úvodní stránka poskytuje pohodlný přístup k informacím o PIM a novou dokumentaci schválení.

![](media/azure-ad-pim-approval-workflow/image002.png)

Přidali jsme také nový oddíl pro všechny uživatele PIM, "Moje historie auditů". Tady najdete všechny informace relevantní pro vaši identitu. To zahrnuje všechny vaše žádosti čekající na vyřízení a dokončený, rozhodnutí, které jste udělali o žádosti, na které můžete vyřešit a všechny vaše poslední aktivace rolí na jediném místě.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Povolit schválení pro konkrétní role

Pokud chcete povolit schvalování pro určitou roli, vyberte nejprve role adresáře v levém navigačním panelu.

![](media/azure-ad-pim-approval-workflow/image004.png)

Vyhledejte a vyberte nastavení v levém navigačním panelu role adresáře

![](media/azure-ad-pim-approval-workflow/image006.png)

Vyberte privilegované role:

![](media/azure-ad-pim-approval-workflow/image009.png)

Vyberte možnost "Povolit" v vyžadují schválení části:

![](media/azure-ad-pim-approval-workflow/image011.png)

Po povolení okna se rozbalí a zobrazí následující podrobnosti:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Neměňte při zadání jakékoli schvalovatelé, PRA(s) stane výchozí schvalovateli. PRA(s) by vyžadoval schválit všechny žádosti o aktivaci pro tuto roli.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Zadejte uživatele schvalovatele a/nebo skupiny schválit žádosti

Na delegáta schválení, klikněte na možnost "Vybrat schvalovatelům":

![](media/azure-ad-pim-approval-workflow/image015.png)

Při načtení okna vyberte schvalovatelé, může vyhledat konkrétního uživatele nebo skupiny pomocí panelu hledání v horní části nebo výběrem ze seznamu předem vyplněná a pak klikněte na tlačítko "Vyberte" po dokončení:

![](media/azure-ad-pim-approval-workflow/image017.png)

Poznámka: Můžete vybrat víc uživatelů nebo skupin po jednom.

Váš výběr se zobrazí v seznamu Vybraní schvalovatelé, jak je znázorněno níže:

![](media/azure-ad-pim-approval-workflow/image019.png)

Odebrání schvalovatele, stačí klikněte na tlačítko Odebrat vedle jejich názvu.

Pokud chcete přidat další schvalovatele, opakujte proces.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Zobrazit historii žádosti a schválení pro všechny privilegované role

Chcete-li zobrazit historii žádosti a schválení všech privilegovaných rolí, vyberte historie auditu z řídicího panelu:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Řazení dat podle akce a vyhledejte "Schválení aktivace"

### <a name="view-pending-approvals-requests"></a>Zobrazení čekajících na schválení (požadavků)

Jako delegovaný schvalovatel obdržíte e-mailová oznámení, když je požadavek čekající na vaše schválení. Chcete-li zobrazit tyto požadavky na portálu PIM, z řídicího panelu (v nové navigace) vyberte v levém navigačním panelu kartu "Čekající na vyřízení žádosti o schválení".

![](media/azure-ad-pim-approval-workflow/image023.png)

Tady se zobrazí seznam žádostí o schválení:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>schválení nebo odmítnutí žádostí o zvýšení oprávnění role (jeden nebo hromadně)

Vyberte žádosti, na které chcete schválit nebo zamítnout a klikněte na tlačítko na panelu akcí, která odpovídá vaší rozhodnutí:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>uveďte její odůvodnění moje schválení nebo zamítnutí

Otevře se nové okno Schválit nebo zamítnout žádosti více najednou. Zadejte odůvodnění pro své rozhodnutí, a klikněte na tlačítko Schválit (nebo zakázat) v dolní části nebo v okně:

![](media/azure-ad-pim-approval-workflow/image029.png)

Po dokončení zpracování žádosti o symbolu stavu bude odrážet provedené rozhodnutí (v tomto příkladu rozhodnutí je schválit):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>žádost o aktivaci role, která vyžaduje schválení

Žádá se o aktivaci role, která vyžaduje schválení může být zahájen ze staré PIM navigace nebo novou navigaci jako proces na aktivaci role zůstává stejná. Stačí vyberte roli ze seznamu rolí k aktivaci:

![](media/azure-ad-pim-approval-workflow/image033.png)

Pokud privilegovaných rolí vyžaduje Vícefaktorové ověřování, budete vyzváni k dokončení této úlohy nejprve:

![](media/azure-ad-pim-approval-workflow/image035.png)

Po dokončení, klikněte na tlačítko aktivovat a zadejte odůvodnění (v případě potřeby):

![](media/azure-ad-pim-approval-workflow/image037.png)

Žadateli se zobrazí oznámení, že žádost čeká na schválení:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Zobrazení stavu vaši žádost o aktivaci

Zobrazení stavu čeká na požadavek na aktivaci musí přistupovat z novou navigaci. V levém navigačním panelu vyberte na kartě "Moje žádosti o":

![](media/azure-ad-pim-approval-workflow/image041.png)

Stav žádosti o výchozí nastavení "Čekající na vyřízení", ale můžete přepínat zobrazíte všechny nebo odepření požadavků.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>Dokončení úlohy ve službě Azure AD, pokud aktivace byla schválena

Jakmile se žádost schválí, role je aktivní a žádnou práci, kterou vyžaduje tato role může pokračovat.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>Další postup

Vaše zpětná vazba je pro nás důležité. Můžete tady sdílet vaše připomínky či názory s námi!
