---
title: Přehled prostředků Azure PIM RBAC | Dokumentace Microsoftu
description: Získejte přehled o funkci RBAC v PIM, včetně oznámení a terminologie
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 7cf628495a79fe775528080ae6ec31df8e9a0f37
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447573"
---
# <a name="pim-for-azure-resources"></a>PIM pro prostředky Azure

S Azure Active Directory Privileged Identity Management (PIM), teď můžete spravovat, řídit a sledovat přístup k prostředkům Azure v rámci vaší organizace. To zahrnuje předplatná, skupiny prostředků a dokonce i virtuální počítače. Prostředek na portálu Azure portal, který využívá funkce Azure na základě řízení přístupu Role (RBAC) můžete využít všechny skvělé aktualizace zabezpečení a možnosti správy životního cyklu, které Azure AD PIM nabízí, a několik skvělých nových funkcí plánujeme do Role Azure AD brzy. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>PIM pro prostředky Azure pomáhá správcům prostředků

- Zobrazit, kteří uživatelé a skupiny jsou přiřazené role prostředků Azure, které spravujete
- Povolit přístup "just in time" ke správě prostředků, jako jsou předplatná, skupiny prostředků a další na vyžádání
- Vypršení platnosti přístupu k prostředkům přiřazené uživatele nebo skupiny automaticky s novými nastaveními časově omezenou přiřazení
- Přiřazení přístupu k prostředkům dočasný pro rychlé úkoly nebo plány na volání
- Vynutit Vícefaktorové ověřování pro přístup k prostředkům v libovolné vestavěné i vlastní role 
- Získání sestav o aktivita prostředku korelační přístup k prostředku během aktivní uživatelské relace
- Dostávat upozornění, když nové uživatele nebo skupiny jsou přiřazeny přístup k prostředkům, a při aktivaci oprávněnými přiřazeními

Azure AD PIM můžete spravovat předdefinované role prostředků Azure, jakož i vlastní role (RBAC), včetně (ale nikoli výhradně):

- Vlastník
- Správce přístupu uživatelů
- Přispěvatel
- Správce zabezpečení
- Správce zabezpečení a další

>[!NOTE]
Uživatele nebo členy skupiny přiřadit role vlastník nebo správce přístupu uživatelů a globální správci, který povolení správy předplatného ve službě Azure AD jsou správci prostředků. Tyto správce může přiřadit role, konfigurace nastavení role a kontrolovat přístup pomocí PIM pro prostředky Azure. Zobrazí se seznam [předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

## <a name="tasks"></a>Úlohy

PIM umožňuje pohodlný přístup k aktivovat role, zobrazit počet aktivací/žádosti čekající na schválení čekající na vyřízení (pro [role adresáře Azure AD](azure-ad-pim-approval-workflow.md)) a revize čekající na odpověď z části úkoly v levé navigační nabídce.

Při přístupu k některé z položek nabídky úlohy ze vstupního bodu přehled, obsahuje výsledný zobrazení výsledků pro role adresáře Azure AD a role prostředků Azure. 

![](media/azure-pim-resource-rbac/role-settings-details.png)

Moje role obsahují seznam přiřazení aktivní a oprávnění rolí pro role adresáře Azure AD a role prostředků Azure.

## <a name="activate-roles"></a>Aktivace role

Aktivace rolí za prostředky Azure, zavádí nové prostředí, která umožňuje členům oprávněné role k plánování aktivace pro budoucí datum/čas a vyberte dobu trvání konkrétní aktivace v rámci maximální (ve Správci nakonfigurované je). Další informace o [aktivace role Azure AD zde](../active-directory-privileged-identity-management-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

Z nabídky aktivace zadejte požadovanou počáteční datum a čas, aktivaci této role. Volitelně můžete snížit dobu trvání aktivace (dobu roli je aktivní) a zadejte odůvodnění v případě potřeby; Klikněte na možnost aktivovat.

Pokud není změnili datum a čas, aktivuje se role během několika sekund. Uvidíte, že role zařazených do fronty pro aktivační zprávě na stránce Moje role. Klikněte na tlačítko Aktualizovat, zrušte tuto zprávu.

![](media/azure-pim-resource-rbac/my-roles.png)

Pokud aktivace je naplánováno na dobu datum v budoucnosti, čekající žádost se zobrazí v levé navigační nabídce na kartě žádosti čekající na vyřízení. V případě, že se už nevyžaduje aktivaci role, může uživatel zrušit žádost kliknutím na tlačítko Storno na pravé straně stránky.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Zjišťovat a spravovat prostředky Azure

A hledá a spravuje role pro prostředky Azure, vyberte v levé navigační nabídce na kartě Správa prostředků Azure. Použití filtrů nebo panelu hledání v horní části stránky se najít prostředek.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Řídicí panely prostředků

Správce zobrazit řídicí panel má čtyři primární součásti. Grafická reprezentace prostředku Aktivace rolí za posledních sedm dnů. Tato data působí na vybraný prostředek a zobrazuje počet aktivací pro nejběžnější role (vlastník, Přispěvatel, správce uživatelských přístupů) a všechny role v kombinaci.

Napravo od grafu aktivací jsou dva grafy zobrazující rozdělení přiřazení role podle přiřazení typu pro uživatele a skupiny. Výběrem řezu grafu změní hodnotu v procentech (nebo naopak).

![](media/azure-pim-resource-rbac/admin-view.png)

Pod grafy se zobrazí počet uživatelů a skupin pomocí nové přiřazení rolí za posledních 30 dní (vlevo) a seznam rolí seřazené podle celkem přiřazení (sestupně).

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Správa přiřazení rolí

Správci můžou Spravovat přiřazení rolí výběrem role nebo členy v levém navigačním panelu. Výběr rolí umožňuje správcům k určení rozsahu jejich úlohy správy pro konkrétní role členy zobrazuje všechny uživatele a skupiny přiřazení rolí pro prostředek.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Pokud máte roli čekající na aktivaci, zobrazí se banner s oznámením v horní části stránky při prohlížení členství.

## <a name="assign-roles"></a>Přiřazení rolí

Pokud chcete přiřadit k roli uživatele nebo skupiny, vyberte roli (Pokud je zobrazení role) nebo klikněte na tlačítko Přidat na panelu akcí (Pokud na zobrazení členů).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Pokud přidáváte uživatele nebo skupiny z karty členy, bude nutné vybrat roli z nabídky přidat, abyste mohli vybrat uživatele nebo skupinu.

![](media/azure-pim-resource-rbac/select-role.png)

Zvolte uživatele nebo skupiny z adresáře.

![](media/azure-pim-resource-rbac/choose.png)

Zvolte typ odpovídající přiřazení z rozevírací nabídky. 

**Pouze v době přiřazení:** poskytuje členy uživatele nebo skupinu oprávnění, ale není trvalý přístup k roli během zadaného období doby nebo po neomezenou dobu (Pokud je nakonfigurovaný v nastavení role). 

**Přímého přiřazení:** nevyžaduje členy uživatele nebo skupiny k aktivaci přiřazení role (označuje se jako trvalý přístup). Microsoft doporučuje používat přímého přiřazení pro krátkodobé použití například staffhubu na volání nebo čas citlivé aktivity, ve kterém nesmí být nutný přístup k po dokončení úkolu.

![](media/azure-pim-resource-rbac/membership-settings.png)

Zaškrtávací políčko dole rozevírací seznam typu přiřazení můžete zadat, pokud má být trvalé přiřazení (trvalá způsobilost aktivace podle potřeby čas přiřazení/trvale aktivní pro přímého přiřazení). Chcete-li určit dobu trvání konkrétní přiřazení, zrušte výběr zaškrtávacího políčka a změna start a/nebo datum a čas ukončení.

>[!NOTE]
Zaškrtávací políčko může neupravitelných, pokud jiný správce má zadanou přiřazení maximální doba trvání pro každý typ přiřazení v nastavení role.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Zobrazit aktivace a aktivita prostředku Azure

V případě, že budete muset které akce trvala konkrétního uživatele na různé prostředky naleznete v tématu, můžete zkontrolovat aktivita prostředku Azure spojené s danou aktivační období (pro oprávněné uživatele). Začněte tím, že výběr uživatele ze zobrazení členů nebo ze seznamu členů v konkrétní roli. Výsledek zobrazí grafické zobrazení akcí uživatele pro prostředky Azure podle data a poslední aktivace rolí za stejné období čas.

![](media/azure-pim-resource-rbac/user-details.png)

Výběr aktivaci konkrétní role se zobrazí podrobnosti o aktivaci role a odpovídající aktivity prostředků Azure, ke které došlo během tento uživatel byl aktivní.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Upravte existující přiřazení

Pokud chcete upravit existující přiřazení ze zobrazení podrobností uživatele nebo skupiny, vyberte změnit nastavení na panelu akcí v horní části stránky. Změňte typ přiřazení jenom v přiřazení čas nebo přímého přiřazení.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Přezkoumejte, kdo má přístup v rámci předplatného

O kontrolu přiřazení role v rámci vašeho předplatného, vyberte kartu členy z levého navigačního panelu, nebo vybrat role a zvolte konkrétní role ke kontrole členy. 

Kontrola vyberte z panelu akcí vyberte možnost přidat k vytvoření nové kontroly a zobrazovat existující kontrolu přístupu.

![](media/azure-pim-resource-rbac/owner.png)

[Další informace o kontrolách přístupu](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
Kontroly jsou podporovány pouze pro typy prostředků předplatného v tuto chvíli.

## <a name="configure-role-settings"></a>Konfigurace nastavení role

Konfigurace nastavení role definovat výchozí hodnoty použít pro přiřazení v PIM prostředí. Pokud chcete definovat tyto pro prostředek, vyberte na kartě nastavení Role ze na levém navigačním nebo tlačítko Nastavení role z panelu akcí v jakékoli role, chcete-li zobrazit aktuální možnosti.

Na panelu akcí v horní části stránky klepněte na tlačítko Upravit umožňuje změnit jednotlivých nastavení.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Na stránce nastavení role, včetně doby poslední datum aktualizace a správce, která se změnila nastavení jsou protokolovány změny v nastavení.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Audit prostředků

Audit prostředků poskytuje přehled všech aktivit role pro prostředek. Můžete filtrovat informace pomocí předdefinované datové nebo vlastní rozsah.
![](media/azure-pim-resource-rbac/last-day.png) Audit prostředků také poskytuje rychlý přístup, chcete-li zobrazit podrobnosti o aktivitě uživatele. V okně všechny akce "Aktivace role" jsou odkazy na konkrétní žadatele aktivita prostředku.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Funkce just enough administration

Přiřazení role prostředků pomocí osvědčených postupů jenom dostatek administration (JEA) je jednoduchý s PIM pro prostředky Azure. Uživatelé a skupiny Členové s přiřazením předplatného Azure nebo skupiny prostředků můžete aktivovat jejich stávající přiřazení rolí v nižší oboru. 

Ze stránky hledání najdete podřízený prostředek, které potřebujete ke správě.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Vyberte Moje role z levé navigační nabídce a vyberte vhodnou roli aktivovat. Všimněte si, že typ přiřazení se dědí, protože role byla přiřazena na předplatné, nikoli na skupinu prostředků, jak je znázorněno níže.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Další postup

- [Předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md)
- Další informace o [aktivace zde role Azure AD](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [Pracovní postupy schvalování PIM](azure-ad-pim-approval-workflow.md)
