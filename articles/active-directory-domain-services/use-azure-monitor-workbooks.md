---
title: Použití sešitů Azure Monitor se službami Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak pomocí sešitů Azure Monitor ukezovat audity zabezpečení a porozumět problémům ve spravované doméně služby Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: bdfc7d37d99dc5511f47e33d1848c3f142a9693e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654459"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>Kontrola událostí auditu zabezpečení ve službě Azure AD Domain Services pomocí sešitů Azure Monitor

Chcete-li lépe porozumět stavu spravované domény služby Azure Active Directory Domain Services (Azure AD DS), můžete povolit události auditování zabezpečení. Tyto události auditu zabezpečení pak můžete zkontrolovat pomocí sešitů Azure Monitor, které kombinují text, analytické dotazy a parametry do bohatých interaktivních sestav. Azure AD DS obsahuje šablony sešitu pro přehled zabezpečení a aktivity účtu, které vám umožní prohledat události auditu a spravovat vaše prostředí.

Tento článek ukazuje, jak používat Sešity Azure Monitor ke kontrole událostí auditování zabezpečení ve službě Azure AD DS.

## <a name="before-you-begin"></a>Než začnete

Chcete-li tento článek dokončit, potřebujete následující zdroje a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby proveďte kurz [a vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Události auditu zabezpečení povolené pro spravovanou doménu služby Azure Active Directory Domain Services, které streamují data do pracovního prostoru Analýzy protokolů.
    * V případě potřeby [povolte audity zabezpečení služby Azure Active Directory Domain Services][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Přehled sešitů Azure Monitor

Když jsou ve službě Azure AD DS zapnuté události auditu zabezpečení, může být obtížné analyzovat a identifikovat problémy ve spravované doméně. Azure Monitor umožňuje agregovat tyto události auditování zabezpečení a dotaz ovat data. Pomocí sešitů Azure Monitor můžete tato data vizualizovat tak, aby bylo rychlejší a snadnější identifikovat problémy.

Šablony sešitů jsou kurátorské sestavy, které jsou navrženy pro flexibilní opakované použití více uživateli a týmy. Když otevřete šablonu sešitu, načtou se data z prostředí Azure Monitoru. Šablony můžete používat bez dopadu na ostatní uživatele ve vaší organizaci a můžete uložit vlastní sešity založené na šabloně.

Azure AD DS obsahuje následující dvě šablony sešitu:

* Sestava přehledu zabezpečení
* Sestava aktivity účtu

Další informace o úpravách a správě sešitů najdete v [tématu Přehled sešitů Azure Monitor](../azure-monitor/platform/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>Použití sešitu sestavy přehledu zabezpečení

Abychom lépe porozuměli využití a identifikovali potenciální ohrožení zabezpečení, přehled zabezpečení shrnuje přihlašovací data a identifikuje účty, které budete chtít zkontrolovat. Můžete zobrazit události v určitém období a přejít k podrobnostem konkrétních událostí přihlášení, jako jsou pokusy o chybné heslo nebo místo, kde byl účet zakázán.

Chcete-li získat přístup k šabloně sešitu pro sestavu přehledu zabezpečení, proveďte následující kroky:

1. Vyhledejte a vyberte **službu Azure Active Directory Domain Services** na webu Azure Portal.
1. Vyberte spravovanou doménu, například *aaddscontoso.com*
1. V nabídce na levé straně zvolte **Sledování > sešitů**

    ![Výběr nabídky Sešity na webu Azure Portal](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Zvolte **přehled zabezpečení .**
1. Z rozevíracích nabídek v horní části sešitu vyberte předplatné Azure a pak pracovní prostor Azure Monitor. Zvolte **časový rozsah**, například Posledních 7 *dní*.

    ![Výběr nabídky Sešity na webu Azure Portal](./media/use-azure-monitor-workbooks/select-query-filters.png)

    Možnosti **zobrazení dlaždic** a **grafu** lze také změnit tak, aby analyzovaly a vizualizovaly data podle potřeby.

1. Chcete-li přejít k podrobnostem konkrétního typu události, vyberte jednu z karet **výsledků přihlášení,** například *Uzamčení účtu*, jak je znázorněno v následujícím příkladu:

    ![Příklad přehled zabezpečení Sestava dat vizualizovaných v sešitech Azure Monitor](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. Dolní část sestavy přehledu zabezpečení pod grafem pak rozdělí vybraný typ aktivity. Můžete filtrovat podle uživatelských jmen zapojených na pravé straně, jak je znázorněno v následujícím příkladu sestavy:

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Použití sešitu sestavy aktivity účtu

Chcete-li vyřešit problémy s konkrétním uživatelským účtem, sestava aktivit účtu rozdělí podrobné informace protokolu událostí auditu. Můžete zkontrolovat, kdy bylo při přihlášení zadáno chybné uživatelské jméno nebo heslo, a zdroj pokusu o přihlášení.

Chcete-li získat přístup k šabloně sešitu pro sestavu aktivity účtu, proveďte následující kroky:

1. Vyhledejte a vyberte **službu Azure Active Directory Domain Services** na webu Azure Portal.
1. Vyberte spravovanou doménu, například *aaddscontoso.com*
1. V nabídce na levé straně zvolte **Sledování > sešitů**
1. Zvolte **sestavu aktivity účtu**.
1. Z rozevíracích nabídek v horní části sešitu vyberte předplatné Azure a pak pracovní prostor Azure Monitor. Zvolte **časový rozsah**, například Posledních *30 dní*, a pak, jak má zobrazení **Dlaždice** představovat data. Můžete filtrovat podle **uživatelského jména účtu**, například *felix*, jak je znázorněno v následujícím příkladu sestavy:

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    Oblast pod grafem zobrazuje jednotlivé události přihlášení spolu s informacemi, jako je výsledek aktivity a zdrojová pracovní stanice. Tyto informace mohou pomoci určit opakované zdroje událostí přihlášení, které mohou způsobit uzamčení účtu nebo indikovat potenciální útok.

Stejně jako v přehledu zabezpečení můžete přejít k podrobnostem různých dlaždic v horní části sestavy a podle potřeby je vizualizovat a analyzovat.

## <a name="save-and-edit-workbooks"></a>Uložení a úpravy sešitů

Dvě šablony sešity poskytované Azure AD DS jsou vhodné místo pro spuštění s vlastní analýzu dat. Pokud potřebujete získat podrobnější v dotazech a šetření dat, můžete uložit vlastní sešity a upravit dotazy.

1. Pokud chcete uložit kopii jedné ze šablon sešitu, vyberte **Upravit > Uložit jako > sdílené sestavy**, zadejte název a uložte ho.
1. Ve své vlastní kopii šablony vyberte **Upravit,** abyste vstoupili do režimu úprav. Vedle libovolné části sestavy můžete zvolit modré tlačítko **Upravit** a změnit ho.

Všechny grafy a tabulky v sešitech Azure Monitor uvolní pomocí dotazů Kusto. Další informace o vytváření vlastních dotazů naleznete v tématu [dotazy protokolu Azure Monitor][azure-monitor-queries] a [kusto dotazy kurzu][kusto-queries].

## <a name="next-steps"></a>Další kroky

Pokud potřebujete upravit zásady pro hesla a uzamčení, přečtěte si informace [o zásadách uzamčení hesel a účtů ve spravovaných doménách][password-policy].

Pokud jde o problémy s uživateli, přečtěte si, jak řešit problémy s [přihlášením k účtu][troubleshoot-sign-in] nebo [problémy se uzamčením účtu][troubleshoot-account-lockout].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: https://docs.microsoft.com/azure/kusto/query/tutorial?pivots=azuredataexplorer
