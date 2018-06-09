---
title: Přehled prostředků Azure PIM RBAC | Microsoft Docs
description: Získat přehled o funkci RBAC v PIM včetně terminologie a oznámení
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 304a06dee04b405c7e7acb2c73b31af426591d1d
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233284"
---
# <a name="pim-for-azure-resources"></a>Pro prostředky Azure PIM

S Azure Active Directory Privileged Identity Management (PIM), teď můžete spravovat, řízení a monitorování přístupu k prostředkům Azure v rámci vaší organizace. To zahrnuje odběry, skupiny prostředků a i virtuálních počítačů. Jakémukoli prostředku, v rámci portálu Azure, který využívá funkce Azure na základě řízení přístupu Role (RBAC) můžete využít výhod všech skvělé zabezpečení a možnosti správy životního cyklu, které má Azure AD PIM nabízet a některé skvělé nové funkce plánujeme uvede Azure AD role brzy k dispozici. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>PIM pro prostředky Azure pomáhá správcům prostředků

- V tématu, kteří uživatelé a skupiny přiřazené role pro prostředky Azure, které spravujete
- Povolit přístup "právě v čase" ke správě prostředků, jako je například odběry, skupiny prostředků a informace na vyžádání
- Platnost přístupu k prostředkům přiřazené uživatele nebo skupiny automaticky pomocí nového nastavení časově vázaných přiřazení
- Přiřadit přístup dočasných prostředků pro Rychlé úlohy nebo plány na volání
- Vynutit ověřování Multi-Factor Authentication pro přístup k prostředkům na všechny vestavěná nebo vlastní role 
- Získat sestavy o aktivita prostředku přístup korelační prostředků během aktivní relace uživatele
- Zasílání upozornění, když nové uživatele nebo skupiny jsou přiřazeny přístup k prostředkům a aktivují oprávněné přiřazení

Azure AD PIM můžete spravovat předdefinované role prostředků Azure, jakož i vlastní role (RBAC), včetně (ale bez omezení):

- Vlastník
- Správce přístupu uživatelů
- Přispěvatel
- Správce zabezpečení
- Správce zabezpečení a další

>[!NOTE]
Uživatelé nebo skupiny, se přiřadila role vlastníka nebo správce přístupu uživatelů a globální správci, které umožňují správy předplatného ve službě Azure AD jsou správci prostředků. Tyto správce může přiřadit role, nakonfigurujte nastavení role a kontrolujte přístup pomocí PIM pro prostředky Azure. Zobrazení seznamu [předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

## <a name="tasks"></a>Úlohy

PIM poskytuje pohodlné přístup k aktivaci rolí, zobrazení čekajících aktivací nebo požadavků, čeká na schválení (pro [role adresář Azure AD](azure-ad-pim-approval-workflow.md)) a kontroluje čekající na vaši odpověď z části úlohy levé navigační nabídce.

Při přístupu k některou z položek nabídky úlohy ze vstupního bodu přehled, výsledné zobrazení obsahuje výsledky pro role adresář Azure AD i role prostředků Azure. 

![](media/azure-pim-resource-rbac/role-settings-details.png)

Moje rolí obsahuje seznam vaše aktivní a vhodné role přiřazení role adresář Azure AD, a rolí prostředků Azure.

## <a name="activate-roles"></a>Aktivace role

Aktivace role pro prostředky Azure zavádí nové prostředí, které umožňuje členům role vhodné naplánovat aktivace pro budoucí datum a čas a vyberte dobu trvání konkrétní aktivace v rámci maximální (nakonfiguroval správce). Další informace o [aktivace role Azure AD zde](../active-directory-privileged-identity-management-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

V nabídce aktivace zadejte požadované počáteční datum a čas, při aktivaci role. Volitelně můžete zkrátit dobu trvání aktivace (dobu role je aktivní) a zadejte odůvodnění, v případě potřeby; Klikněte na možnost aktivovat.

Pokud není změnit počáteční datum a čas, aktivuje se roli během několika sekund. Uvidíte, že role zařazených do fronty pro aktivační banner zpráva na stránce Moje role. Klikněte na tlačítko Aktualizovat zrušte tuto zprávu.

![](media/azure-pim-resource-rbac/my-roles.png)

Pokud se aktivace je naplánováno budoucí datum a čas, nevyřízené žádosti se zobrazí na kartě žádosti čekající na vyřízení levé navigační nabídce. V případě, že se už nevyžaduje aktivaci role, může uživatel zrušte žádost kliknutím na tlačítko Storno na pravé straně stránky.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Zjišťovat a spravovat prostředky Azure

Můžete najít a spravovat role pro prostředek služby Azure, vyberte na kartě Správa v levé navigační nabídce prostředky Azure. Použijte filtry nebo panelu Hledat v horní části stránky se najít prostředek.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Řídicí panely prostředků

Správce zobrazení řídicí panel obsahuje čtyři primární součásti. Grafické znázornění prostředků role aktivací za posledních 7 dnů. Tato data je vymezen na vybrané prostředky a zobrazí aktivací pro nejběžnější role (vlastník, Přispěvatel, správce přístupu uživatelů) a všechny role kombinaci.

Napravo od grafu aktivace jsou dva grafy zobrazují rozdělení přiřazení rolí podle typu přiřazení pro uživatele a skupiny. Výběr řez grafu se změní hodnota v procentech (nebo naopak).

![](media/azure-pim-resource-rbac/admin-view.png)

Pod grafy zobrazí počet uživatelů a skupin pomocí nové přiřazení role přes za posledních 30 dní (vlevo) a seznam rolí, které jsou seřazené podle celkové přiřazení (sestupně).

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Správa přiřazení rolí

Správci mohou spravovat přiřazení rolí výběrem role nebo členy z levé navigaci. Výběr rolí umožňuje správcům obor jejich úlohy správy pro určité role, zatímco členy zobrazí všechny uživatele a skupiny přiřazení rolí pro prostředek.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Pokud máte role čekající na vyřízení aktivace, banner oznámení se zobrazí v horní části stránky, při zobrazení členství.

## <a name="assign-roles"></a>Přiřazení rolí

Přiřadit k roli uživatele nebo skupinu, vyberte roli (Pokud je zobrazení rolí), nebo klikněte na tlačítko Přidat z panelu akcí (Pokud na zobrazení členů).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
-Li přidat uživatele nebo skupinu z karty členy, budete muset vybrat roli z nabídky přidat, abyste mohli vybrat uživatele nebo skupinu.

![](media/azure-pim-resource-rbac/select-role.png)

Vyberte uživatele nebo skupiny z adresáře.

![](media/azure-pim-resource-rbac/choose.png)

Typ odpovídající přiřazení vyberte z rozevírací nabídky. 

**Právě v čas přiřazení:** poskytuje členy uživatele nebo skupinu oprávněné, ale není trvalý přístup k roli v zadaném období času nebo po neomezenou dobu (Pokud je nakonfigurováno v nastavení role). 

**Přímé přiřazení:** nevyžaduje žádný uživatel nebo skupina členy aktivovat přiřazení role (označované jako trvalý přístup). Společnost Microsoft doporučuje pomocí přímé přiřazení pro krátkodobé použití, například na volání posuny nebo citlivé aktivity čas, kdy přístup nebude muset po dokončení úlohy.

![](media/azure-pim-resource-rbac/membership-settings.png)

Zaškrtávací políčko pod rozevíracím typ přiřazení umožňuje zadat, pokud má být trvalé přiřazení (vhodné trvale aktivovat pouze v čas přiřazení nebo trvale active pro přímé přiřazení). K určení doby trvání konkrétní přiřazení, zrušte výběr zaškrtnutím políčka a upravit spuštění nebo ukončení pole data a času.

>[!NOTE]
Zaškrtávací políčko může být unmodifiable, pokud jiný správce má zadanou dobu trvání maximální přiřazení pro každý typ přiřazení v nastavení role.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Zobrazení aktivace a aktivita prostředku Azure

V případě, že potřebujete najdete v části které akce trvala konkrétního uživatele na různých prostředků, můžete zkontrolovat prostředků Azure aktivity spojené s danou aktivační období (pro oprávněné uživatele). Začněte výběrem uživatele ze zobrazení členů, nebo v seznamu členů v konkrétní roli. Výsledek zobrazí grafické zobrazení akcí uživatele na prostředky Azure podle data a poslední aktivace role v tomto stejné období.

![](media/azure-pim-resource-rbac/user-details.png)

Výběr specifické role aktivace se zobrazí podrobnosti o aktivaci role a odpovídající aktivity prostředků Azure, které došlo k chybě tento uživatel byl aktivní.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Upravte existující přiřazení

Pokud chcete upravit existující přiřazení ze zobrazení podrobností uživatele nebo skupiny, vyberte z panelu Akce v horní části stránky změnit nastavení. Změňte typ přiřazení na právě v přiřazení čas nebo přímé přiřazení.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Kontrola, kdo má přístup v předplatném.

Chcete-li zkontrolovat přiřazení rolí v rámci vašeho předplatného, vyberte kartu členy z levé navigaci, nebo vybrat role a vyberte, chcete-li zkontrolovat členy určité role. 

Zkontrolujte vyberte z panelu akcí k zobrazení existujících recenze přístup a vyberte Přidat k vytvoření nové revize.

![](media/azure-pim-resource-rbac/owner.png)

[Další informace o přístupu recenze](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
Recenze jsou podporovány pouze pro typy prostředků předplatného v tuto chvíli.

## <a name="configure-role-settings"></a>Konfigurace nastavení role

Konfigurace nastavení role definovat výchozí hodnoty použít pro přiřazení v prostředí PIM. Pokud chcete definovat tyto prostředku, vyberte kartu Nastavení Role z levé navigaci, nebo na tlačítko Nastavení role z panelu akcí v žádné role, chcete-li zobrazit aktuální možnosti.

Kliknutím na Upravit z panelu Akce v horní části stránky umožňuje upravovat jednotlivých nastavení.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Změny nastavení se protokolují na stránce nastavení role, včetně poslední datum aktualizace a správce, který změnil nastavení.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Audit prostředků

Audit prostředků nabízí zobrazení všech aktivit role pro prostředek. Můžete filtrovat pomocí předdefinovaných datum nebo rozsah vlastní informace.
![](media/azure-pim-resource-rbac/last-day.png) Audit prostředků taky poskytuje rychlý přístup k zobrazení podrobností aktivity uživatele. V zobrazení všechny akce "Aktivovat roli" jsou odkazy na konkrétní žadatele aktivita prostředku.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Akorát správy

Pomocí vaše přiřazení role prostředků akorát osvědčené postupy správy (JEA) je jednoduchý PIM pro prostředky Azure. Uživatelé a členy skupiny s přiřazením v Azure nebo skupiny prostředků můžete aktivovat jejich stávající přiřazení role v menší oboru. 

Na stránce hledání najít podřízené prostředek, který potřebujete spravovat.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Z nabídky na levém navigačním vyberete Moje role a vyberte příslušnou roli aktivovat. Všimněte si typ přiřazení zděděna, vzhledem k tomu, že role se přiřadila na předplatné, nikoli na skupinu prostředků, jak je uvedeno níže.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Další postup

- [Předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md)
- Další informace o [aktivace zde role Azure AD](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [Pracovní postupy schválení PIM](azure-ad-pim-approval-workflow.md)
