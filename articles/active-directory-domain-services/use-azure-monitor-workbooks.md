---
title: Použití Azure Monitorch sešitů s Azure AD Domain Services | Microsoft Docs
description: Naučte se používat sešity Azure Monitor ke kontrole auditů zabezpečení a pochopení problémů v Azure Active Directory Domain Services spravované doméně.
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: 5d19cddc82d9fb092887c5cdeba36b8ab7127f94
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175130"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>Kontrola událostí auditu zabezpečení v Azure Active Directory Domain Services používání sešitů Azure Monitor

Pokud chcete porozumět stavu vaší Azure Active Directory Domain Services (Azure služba AD DS) spravované domény, můžete povolit události auditu zabezpečení. Tyto události auditu zabezpečení se pak dají zkontrolovat pomocí Azure Monitor sešitů, které kombinují text, analytické dotazy a parametry do propracovaných interaktivních sestav. Azure služba AD DS obsahuje šablony sešitu pro přehled zabezpečení a aktivity účtu, které umožňují dig události auditu a spravovat vaše prostředí.

V tomto článku se dozvíte, jak pomocí Azure Monitor sešity zkontrolovat události auditu zabezpečení v Azure služba AD DS.

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby dokončete kurz a [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].
* Pro spravovanou doménu jsou povolené události auditu zabezpečení, které streamují data do pracovního prostoru Log Analytics.
    * V případě potřeby [Povolte audity zabezpečení pro Azure služba AD DS][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Přehled Azure Monitor sešitů

Když jsou v Azure služba AD DS zapnuté události auditu zabezpečení, může být obtížné analyzovat a identifikovat problémy ve spravované doméně. Azure Monitor umožňuje agregovat tyto události auditu zabezpečení a dotazovat se na data. Pomocí Azure Monitorch sešitů můžete vizualizovat tato data, aby bylo rychlejší a bylo snazší identifikovat problémy.

Šablony sešitu jsou sestavy, které jsou navrženy pro flexibilní opakované použití více uživatelů a týmů. Když otevřete šablonu sešitu, načtou se data z Azure Monitorho prostředí. Šablony můžete používat bez dopadu na jiné uživatele ve vaší organizaci a můžete ukládat vlastní sešity založené na šabloně.

Azure služba AD DS obsahuje následující dvě šablony sešitu:

* Sestava přehledu zabezpečení
* Sestava aktivity účtu

Další informace o tom, jak upravovat a spravovat sešity, najdete v tématu [přehled Azure Monitorch sešitů](../azure-monitor/platform/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>Použití sešitu se sestavou přehledu zabezpečení

Aby vám pomohli lépe pochopit využití a identifikovat potenciální bezpečnostní hrozby, sestaví Přehled zabezpečení Shrnutí přihlašovacích dat a identifikuje účty, které byste mohli chtít kontrolovat. Můžete zobrazit události v konkrétním rozsahu data a přejít k podrobnostem specifických přihlašovacích událostí, jako jsou například chybné pokusy o zadání hesla nebo místo, kde byl účet zakázán.

Pro přístup k šabloně sešitu pro sestavu přehledu zabezpečení proveďte následující kroky:

1. Vyhledejte a vyberte **Azure Active Directory Domain Services** v Azure Portal.
1. Vyberte spravovanou doménu, například *aaddscontoso.com* .
1. V nabídce na levé straně vyberte **monitorování > sešity** .

    ![Snímek obrazovky, který hightlights, kde se má vybrat Sestava Přehled zabezpečení a sestava aktivity účtu](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Vyberte **sestavu Přehled zabezpečení**.
1. V rozevíracích nabídkách v horní části sešitu vyberte své předplatné Azure a pak Azure Monitor pracovní prostor.

    Vyberte **časový rozsah**, například *posledních 7 dní*, jak je znázorněno na následujícím ukázkovém snímku obrazovky:

    ![V Azure Portal vyberte možnost nabídky sešity.](./media/use-azure-monitor-workbooks/select-query-filters.png)

    Možnosti **zobrazení dlaždic** a **zobrazení grafu** je také možné změnit tak, aby se data daly analyzovat a vizualizovat podle potřeby.

1. Chcete-li přejít k podrobnostem konkrétního typu události, vyberte jednu z karet **výsledku přihlášení** , například *účet uzamčeno*, jak je znázorněno v následujícím příkladu:

    ![Příklad přehledu zabezpečení data sestavy, která jsou vyvizuálů v Azure Monitor sešity](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. Dolní část sestavy přehledu zabezpečení pod grafem pak rozdělí vybraný typ aktivity. Můžete filtrovat podle uživatelských jmen, která jsou zapojená na pravé straně, jak je znázorněno v následujícím příkladu sestavy:

    [![Podrobnosti o uzamčení účtu v sešitech Azure Monitor.](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Použití sešitu sestavy aktivity účtu

Aby vám pomohl řešit problémy pro určitý uživatelský účet, vytvoří sestava aktivita účtu podrobné informace protokolu událostí auditu. Můžete zkontrolovat, kdy bylo během přihlašování zadáno chybné uživatelské jméno nebo heslo, a zdroj pokusu o přihlášení.

Chcete-li získat přístup k šabloně sešitu pro sestavu aktivita účtu, proveďte následující kroky:

1. Vyhledejte a vyberte **Azure Active Directory Domain Services** v Azure Portal.
1. Vyberte spravovanou doménu, například *aaddscontoso.com* .
1. V nabídce na levé straně vyberte **monitorování > sešity** .
1. Vyberte **sestavu aktivita účtu**.
1. V rozevíracích nabídkách v horní části sešitu vyberte své předplatné Azure a pak Azure Monitor pracovní prostor.

    Zvolte **časový rozsah**, například *posledních 30 dní*, a určete, jak chcete, aby **zobrazení dlaždice** představovalo data.

    Můžete filtrovat podle **uživatelského jména účtu**, jako je například *Felix*, jak je znázorněno v následujícím příkladu sestavy:

    [![Sestava aktivity účtu v sešitech Azure Monitor.](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    V oblasti pod grafem se zobrazují individuální přihlašovací události spolu s informacemi, jako je například výsledek aktivity a zdrojová pracovní stanice. Tyto informace vám pomohou určit opakované zdroje událostí přihlášení, které mohou způsobit uzamčení účtu nebo naznačovat potenciální útok.

Stejně jako v sestavě přehledu zabezpečení můžete přejít k podrobnostem v různých dlaždicích v horní části sestavy a vizualizovat a analyzovat data podle potřeby.

## <a name="save-and-edit-workbooks"></a>Ukládání a úpravy sešitů

Dva sešity šablon, které poskytuje Azure služba AD DS, jsou vhodným místem, kde můžete začít s vlastní analýzou dat. Pokud potřebujete podrobnější informace v dotazech a dotazech k datům, můžete uložit vlastní sešity a upravit dotazy.

1. Pokud chcete uložit kopii jedné ze šablon sešitu, vyberte **upravit > Uložit jako > sdílené sestavy** a potom zadejte název a uložte ho.
1. Z vlastní kopie šablony vyberte **Upravit** a zadejte režim úprav. Můžete zvolit modré tlačítko **Upravit** vedle libovolné části sestavy a změnit ji.

Všechny grafy a tabulky v Azure Monitor sešitech se generují pomocí dotazů Kusto. Další informace o vytváření vlastních dotazů najdete v kurzu [Azure monitor dotazy protokolů][azure-monitor-queries] a [dotazy Kusto][kusto-queries].

## <a name="next-steps"></a>Další kroky

Pokud potřebujete upravit zásady hesel a uzamčení, přečtěte si téma [zásady hesel a uzamčení účtů ve spravovaných doménách][password-policy].

V případě problémů s uživateli se dozvíte, jak řešit problémy s [přihlašováním k účtu][troubleshoot-sign-in] nebo [problémy s uzamčením účtu][troubleshoot-account-lockout].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: /azure/data-explorer/kusto/query/
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer