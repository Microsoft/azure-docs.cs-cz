---
title: Nasazení Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
description: Popisuje, jak naplánovat nasazení Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b1d18982a4f2a9ee8ba585af56a5e9ded7c1c62
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036822"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Nasazení Azure AD Privileged Identity Management (PIM)

Tento článek popisuje podrobný návod, jak naplánovat nasazení Privileged Identity Management (PIM) ve vaší organizaci Azure Active Directory (Azure AD). Pokud je to možné, přeřaďte uživatele v rolích s vysokou úrovní oprávnění na méně výkonné předdefinované nebo vlastní role a naplánujte přiřazení rolí za běhu pro vaše nejvíc privilegované role. V tomto článku provedeme doporučení pro plánování a implementaci nasazení.

> [!TIP]
> V celém tomto článku se zobrazí položky označené jako:
>
> : heavy_check_mark: Společnost **Microsoft doporučuje**
>
> Jedná se o obecná doporučení a měli byste je implementovat jenom v případě, že se vztahují k vašim konkrétním podnikovým potřebám.

## <a name="licensing-requirements"></a>Licenční požadavky

Pokud chcete použít Privileged Identity Management, musí mít adresář jednu z následujících placených nebo zkušebních licencí. Další informace najdete v tématu [licenční požadavky pro použití Privileged Identity Management](subscription-requirements.md).

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 Education A5
- Microsoft 365 Enterprise E5

## <a name="how-pim-works"></a>Jak PIM funguje

V této části najdete přehled pro účely plánování relevantních částí procesu Privileged Identity Management. Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](pim-configure.md)

1. Začněte používat Privileged Identity Management, aby byli uživatelé oprávněni pro privilegované role.
1. Když oprávněný uživatel potřebuje použít svoji privilegovanou roli, aktivuje roli pomocí Privileged Identity Management.
1. Uživatel může být požadován v nastavení:

    - Použití Multi-Factor Authentication
    - Žádost o schválení pro aktivaci
    - Poskytnutí podnikového důvodu pro aktivaci

1. Jakmile uživatel úspěšně aktivuje svou roli, budou mít oprávnění role pro nastavenou dobu trvání.
1. Správci mohou zobrazit historii všech aktivit Privileged Identity Management v protokolu auditu. Můžou taky dál zabezpečit své organizace Azure AD a plnit dodržování předpisů pomocí Privileged Identity Managementch funkcí, jako jsou recenze a výstrahy přístupu.

## <a name="roles-that-can-be-managed-by-pim"></a>Role, které se dají spravovat pomocí PIM

**Role Azure AD** jsou v Azure Active Directory (například globální správce, správce Exchange a správce zabezpečení). Další informace o rolích a jejich funkci najdete v tématu [oprávnění role správce v Azure Active Directory](../roles/permissions-reference.md). Nápovědu k určení rolí pro přiřazení správců najdete v tématu [nejméně privilegované role podle úkolu](../roles/delegate-by-task.md).

**Role Azure** jsou role, které jsou propojené s prostředkem Azure, skupinou prostředků, předplatným nebo skupinou pro správu. PIM můžete použít k zajištění přístupu za běhu k předdefinovaným rolím Azure, jako je vlastník, správce přístupu uživatelů a přispěvatel, a také k [vlastním rolím](../../role-based-access-control/custom-roles.md). Další informace o rolích Azure najdete v tématu [řízení přístupu na základě role v Azure](../../role-based-access-control/overview.md).

Další informace najdete v tématu [role, které nemůžete spravovat v Privileged Identity Management](pim-roles.md).

## <a name="deployment-plan"></a>Plán nasazení

Než nasadíte Privileged Identity Management ve vaší organizaci, postupujte podle pokynů a seznamte se s koncepty v této části, které vám pomůžou vytvořit plán přizpůsobený požadavkům na privilegovaný identity vaší organizace.

### <a name="identify-your-stakeholders"></a>Identifikace zúčastněných stran

Následující část vám pomůže identifikovat všechny zúčastněné strany, které jsou součástí projektu, a je třeba se odhlásit, kontrolovat nebo si informovat. Zahrnuje samostatné tabulky pro nasazení rolí PIM pro role Azure AD a PIM pro role Azure. Přidejte zúčastněné strany do následující tabulky podle potřeby vaší organizace.

- SO = odhlásit se v tomto projektu
- R = zkontrolovat tento projekt a zadat vstup
- I = informovat tento projekt

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Zúčastněné strany: Privileged Identity Management pro role Azure AD

| Název | Role | Akce |
| --- | --- | --- |
| Jméno a e-mail | **Architekt identity nebo globální správce Azure**<br/>Zástupce z týmu pro správu identit, který je pověřený definováním způsobu, jakým se tato změna zarovnává s hlavní infrastrukturou správy identit ve vaší organizaci. | TAKŽE/R/I |
| Jméno a e-mail | **Vlastník služby/manažer line**<br/>Zástupce od vlastníků IT služby nebo skupiny služeb. Jsou v rámci rozhodování a pomáhají při zavádění Privileged Identity Management pro svůj tým. | TAKŽE/R/I |
| Jméno a e-mail | **Vlastník zabezpečení**<br/>Zástupce z bezpečnostního týmu, který se může odhlásit, pokud plán splňuje požadavky vaší organizace na zabezpečení. | SO/R |
| Jméno a e-mail | **Správce podpory IT/Helpdesk**<br/>Zástupce oddělení IT, který poskytuje zpětnou vazbu k podpoře této změny z perspektivy technické podpory. | R/I |
| Jména a e-maily pro uživatele pilotního nasazení | **Uživatelé privilegovaných rolí**<br/>Skupina uživatelů, pro kterou je implementována Privileged Identity Management. Po implementaci Privileged Identity Management budou muset znát, jak aktivovat své role. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>Zúčastněné strany: Privileged Identity Management pro role Azure

| Název | Role | Akce |
| --- | --- | --- |
| Jméno a e-mail | **Předplatné/vlastník prostředku**<br/>Zástupce od vlastníků IT každého předplatného nebo prostředku, který chcete nasadit Privileged Identity Management pro | TAKŽE/R/I |
| Jméno a e-mail | **Vlastník zabezpečení**<br/>Zástupce bezpečnostního týmu, který se může odhlásit, že plán splňuje požadavky vaší organizace na zabezpečení. | SO/R |
| Jméno a e-mail | **Správce podpory IT/Helpdesk**<br/>Zástupce oddělení IT, který poskytuje zpětnou vazbu k podpoře této změny z perspektivy technické podpory. | R/I |
| Jména a e-maily pro uživatele pilotního nasazení | **Uživatelé role Azure**<br/>Skupina uživatelů, pro kterou je implementována Privileged Identity Management. Po implementaci Privileged Identity Management budou muset znát, jak aktivovat své role. | I |

### <a name="start-using-privileged-identity-management"></a>Zahájení využití technologie Privileged Identity Management

V rámci procesu plánování byste měli připravit Privileged Identity Management podle článku [Začínáme s Privileged Identity Management](pim-getting-started.md) . Privileged Identity Management vám poskytne přístup k některým funkcím, které jsou navržené tak, aby vám pomohly s vaším nasazením.

Pokud je vaším cílem nasazení Privileged Identity Management pro prostředky Azure, měli byste postupovat podle našich [prostředků zjišťování Azure pro správu v Privileged Identity Management](pim-resource-roles-discover-resources.md) článku. Spravovat tyto prostředky pomocí Privileged Identity Management mohou pouze vlastníci předplatných a skupin pro správu. Po správě je funkce PIM k dispozici pro vlastníky na všech úrovních, včetně skupiny pro správu, předplatného, skupiny prostředků a prostředku. Pokud jste globálním správcem, který se pokouší nasadit Privileged Identity Management vašich prostředků Azure, můžete [zvýšit přístup ke správě všech předplatných Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , abyste měli přístup ke všem prostředkům Azure v adresáři pro zjišťování. Doporučuje se ale od každého vlastního vlastníka předplatného získat schválení před správou svých prostředků pomocí Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Vymáhat princip nejnižších oprávnění

Je důležité se ujistit, že jste vynutili princip nejnižších oprávnění ve vaší organizaci pro Azure AD i vaše role Azure.

#### <a name="plan-least-privilege-delegation"></a>Plánování minimálního delegování oprávnění

Pro role Azure AD je běžné, že organizace přiřadí roli globálního správce k několika správcům, když většina správců potřebuje jenom jednu nebo dvě konkrétní a méně výkonné role správce. Díky velkému počtu globálních správců nebo jiných rolí s vysokou úrovní oprávnění je obtížné sledovat přiřazení privilegovaných rolí dostatečně pečlivě.

Pomocí těchto kroků můžete implementovat princip nejnižších oprávnění pro vaše role Azure AD.

1. Pochopte členitosti rolí, a to čtením a pochopením dostupných [předdefinovaných rolí Azure AD](../roles/permissions-reference.md). Vy a váš tým by měl také odkazovat na [role správců podle identity ve službě Azure AD](../roles/delegate-by-task.md), která vysvětluje nejnižší privilegovaný roli pro konkrétní úkoly.

1. Seznam obsahující privilegované role ve vaší organizaci. Ke snížení rizika můžete využít Privileged Identity Management [zjišťování a přehledy (Preview)](pim-security-wizard.md) .

    ![Stránka zjišťování a přehled (Preview), která umožňuje omezit expozici pomocí privilegovaných rolí](./media/pim-deployment-plan/new-preview-page.png)

1. U všech globálních správců ve vaší organizaci Zjistěte, proč potřebují roli. Pak je odeberte z role globálního správce a přiřaďte předdefinované role nebo vlastní role s nižšími oprávněními v rámci Azure Active Directory. Společnost Microsoft v současné době má pouze zhruba 10 správců s rolí globálního správce. Přečtěte si další informace [o tom, jak Microsoft používá Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

1. U všech ostatních rolí Azure AD zkontrolujte seznam přiřazení, identifikujte správce, kteří už roli nepotřebují, a odeberte je ze svých přiřazení.

K automatizaci posledních dvou kroků můžete použít kontroly přístupu v Privileged Identity Management. Podle postupu v části [zahájení kontroly přístupu pro role Azure AD v Privileged Identity Management](pim-how-to-start-security-review.md)můžete nastavit kontrolu přístupu pro každou roli Azure AD, která má jednoho nebo více členů.

![Vytvoření podokna kontroly přístupu pro role Azure AD](./media/pim-deployment-plan/create-access-review.png)

Nastavte kontrolory na **členy (samy)**. Všichni uživatelé v roli obdrží e-mail s žádostí o potvrzení, že potřebují přístup. Také zapněte **vyžadovat důvod na schválení** v upřesňujících nastaveních, aby uživatelé museli uvést, proč potřebují roli. Na základě těchto informací můžete odebrat uživatele z nepotřebných rolí nebo je delegovat na podrobnější role správce.

Kontroly přístupu využívají e-maily, které lidem upozorňují na jejich přístup k rolím. Pokud máte privilegované účty, které nemají připojené e-maily, nezapomeňte na tyto účty naplnit sekundární pole e-mailu. Další informace najdete v tématu [atribut proxyAddresses ve službě Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="plan-azure-resource-role-delegation"></a>Plánování delegování role prostředků Azure

U předplatných a prostředků Azure můžete nastavit podobný proces kontroly přístupu ke kontrole rolí v každém předplatném nebo prostředku. Cílem tohoto procesu je minimalizovat přiřazení vlastníků a správců přístupu uživatelů ke každému předplatnému nebo prostředku a odebrat nepotřebná přiřazení. Organizace ale často tyto úkoly předávají vlastníkovi každého předplatného nebo prostředku, protože mají lepší znalosti o konkrétních rolích (zejména vlastní role).

Pokud jste v roli globálního správce, která se pokouší nasadit role PIM pro Azure ve vaší organizaci, můžete [zvýšit přístup pro správu všech předplatných Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , abyste získali přístup ke každému předplatnému. Pak můžete najít každého vlastníka předplatného a pracovat s nimi, abyste odebrali nepotřebná přiřazení a minimalizovali přiřazení role vlastníka.

Uživatelé s rolí vlastníka pro předplatné Azure můžou také pomocí kontrol [přístupu k prostředkům Azure](pim-resource-roles-start-access-review.md) auditovat a odebírat nepotřebná přiřazení rolí podobně jako dříve popsané procesy pro role Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Rozhodněte, která přiřazení rolí by se měla chránit pomocí Privileged Identity Management

Po vyčištění přiřazení privilegovaných rolí ve vaší organizaci budete muset určit, které role se mají chránit pomocí Privileged Identity Management.

Pokud je role chráněná Privileged Identity Management, musí se oprávněným uživatelům, kteří jsou přiřazeni, zvýšit oprávnění k používání oprávnění udělených rolí. Proces zvýšení oprávnění může také zahrnovat získání schválení pomocí vícefaktorového ověřování a poskytnutí odůvodnění, proč se aktivují. Privileged Identity Management může také sledovat zvýšení oprávnění prostřednictvím oznámení a protokolů událostí služby Privileged Identity Management a auditu služby Azure AD.

Volba rolí, které se mají chránit pomocí Privileged Identity Management může být obtížná a bude pro každou organizaci odlišná. V této části najdete osvědčené Rady k osvědčeným postupům pro Azure AD a role Azure.

#### <a name="azure-ad-roles"></a>Role Azure AD

Je důležité upřednostnit ochranu rolí Azure AD, které mají nejvíce oprávnění. V závislosti na vzorcích využití mezi všemi Privileged Identity Management zákazníky, hlavních 10 rolí Azure AD spravovaných pomocí Privileged Identity Management jsou:

1. Globální správce
1. Správce zabezpečení
1. Správce uživatelů
1. Správce Exchange
1. Správce SharePointu
1. Správce Intune
1. Čtenář zabezpečení
1. Správce služeb
1. Správce fakturace
1. Správce Skypu pro firmy

> [!TIP]
> : heavy_check_mark: **Společnost Microsoft doporučuje** spravovat všechny globální správce a správce zabezpečení pomocí Privileged Identity Management jako první krok, protože se jedná o uživatele, kteří můžou při ohrožení zabezpečení provést nejvíce poškození.

Je důležité vzít v úvahu, jaká data a oprávnění jsou pro vaši organizaci nejvíc citlivá. Například některé organizace mohou chtít chránit svou roli správce Power BI nebo jejich roli správce týmů pomocí Privileged Identity Management, protože mají přístup k datům a mění základní pracovní postupy.

Pokud jsou k dispozici nějaké role s přiřazenými uživateli typu Host, jsou zranitelnější vůči útokům.

> [!TIP]
> : heavy_check_mark: **Společnost Microsoft doporučuje** spravovat všechny role s uživateli typu host pomocí Privileged Identity Management k omezení rizik spojených s ohroženými uživatelskými účty hostů.

Role čtenářů, jako je čtečka adresářů, čtenář centra zpráv a čtecí modul zabezpečení, se někdy považují za méně důležité než jiné role, protože nemají oprávnění k zápisu. Máme ale taky nějaké zákazníky, kteří tyto role chrání, protože útočníci s přístupem k těmto účtům můžou číst citlivá data, jako jsou osobní data. Toto riziko Vezměte v úvahu při rozhodování, jestli chcete, aby byly role čtenářů ve vaší organizaci spravované pomocí Privileged Identity Management.

#### <a name="azure-roles"></a>Role Azure

Při rozhodování o tom, která přiřazení rolí by se měla spravovat pomocí Privileged Identity Management pro prostředek Azure, musíte nejdřív určit předplatné nebo prostředky, které jsou pro vaši organizaci nejdůležitější. Příklady těchto předplatných a prostředků:

- Prostředky, které hostují nejvíc citlivá data
- Prostředky, na kterých závisí základní aplikace pro zákazníky

Pokud jste globální správce a máte potíže s rozhodováním o tom, která předplatná a prostředky jsou nejdůležitější, měli byste ve vaší organizaci kontaktovat vlastníky předplatného, abyste mohli shromažďovat seznam prostředků spravovaných jednotlivými předplatnými. Pak můžete pomocí vlastníků předplatného seskupit prostředky na základě úrovně závažnosti v případě ohrožení zabezpečení (nízká, střední, vysoká). Určete prioritu správy prostředků pomocí Privileged Identity Management na základě této úrovně závažnosti.

> [!TIP]
> : heavy_check_mark: **Společnost Microsoft doporučuje** pracovat s vlastníky předplatného a prostředků důležitých služeb a nastavit Privileged Identity Management pracovní postup pro všechny role v citlivých předplatných a prostředcích.

Privileged Identity Management pro prostředky Azure podporuje účty služeb s časovou vazbou. Účty služeb byste měli považovat přesně za to, jak byste pocházeli s běžným uživatelským účtem.

U předplatných a prostředků, které nejsou tak kritické, nebudete muset nastavit Privileged Identity Management pro všechny role. Měli byste ale i nadále chránit role správců vlastník a přístup uživatelů pomocí Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **Společnost Microsoft doporučuje** spravovat role vlastníků a role správců přístupu uživatelů pro všechna předplatná a prostředky pomocí Privileged Identity Management.

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>Rozhodnutí, jestli se má použít skupina pro přiřazení rolí

Určuje, jestli se má skupina přiřadit roli místo pro jednotlivé uživatele, což je strategické rozhodnutí. Při plánování zvažte přiřazení role ke skupině pro správu přiřazení rolí v těchto případech:

- K roli je přiřazený velký počet uživatelů.
- Chcete delegovat přiřazení role

#### <a name="many-users-are-assigned-to-a-role"></a>K roli je přiřazený velký počet uživatelů.

Udržování přehledu o tom, kdo je přiřazen k roli a spravuje jejich přiřazení na základě toho, kdy je potřeba, může trvat až po ručním provedení. Chcete-li přiřadit skupinu k roli, nejprve [vytvořte skupinu s přiřazením role](../roles/groups-create-eligible.md) a pak ji přiřaďte jako způsobilou pro roli. Tato akce seskupí všechny skupiny ve skupině na stejný proces aktivace jako jednotliví uživatelé, kteří mají nárok na zvýšení role. Členové skupiny aktivují svá přiřazení do skupiny jednotlivě pomocí žádosti o aktivaci Privileged Identity Management a procesu schválení. Skupina není aktivovaná, stačí členství ve skupině uživatele.

#### <a name="you-want-to-delegate-assigning-the-role"></a>Chcete delegovat přiřazení role

Vlastník skupiny může spravovat členství ve skupině. Pro role Azure AD – přiřaditelné skupiny, může spravovat členství ve skupině jenom správce privilegovaných rolí, globální správce a vlastníci skupiny. Přidáním nových členů do skupiny získá člen přístup k rolím, ke kterým je přiřazena skupina, zda je přiřazení oprávněné nebo aktivní. Vlastníky skupin můžete použít k delegování správy členství ve skupině pro přiřazenou roli, aby se snížila šířka požadovaných oprávnění. Další informace o přiřazení vlastníka ke skupině při vytváření skupiny najdete v tématu [Vytvoření skupiny přiřazení role v Azure AD](../roles/groups-create-eligible.md).

> [!TIP]
> : heavy_check_mark: **Microsoft doporučuje** , abyste roli Azure AD přiřadili skupinám v části správa pomocí Privileged Identity Management. Když je skupina, která je přiřazena rolí, pod správou PIM, nazývá se přístupná skupina privilegovaného přístupu. Před tím, než bude moct členové skupiny spravovat členství ve skupině, použijte PIM k tomu, aby před tím, než mohli spravovat členství ve skupinách Další informace o tom, jak přenést skupiny pod správu PIM, najdete v tématu zařazení [privilegovaných přístupových skupin (Preview) do Privileged Identity Management](groups-discover-groups.md).

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Rozhodnutí o tom, která přiřazení role by měla být trvalá nebo způsobilá

Jakmile se rozhodnete, že seznam rolí, které se mají spravovat pomocí Privileged Identity Management, musíte rozhodnout, kteří uživatelé by měli mít k trvalé aktivní roli oprávnění. Trvalé aktivní role jsou běžné role přiřazené prostřednictvím Azure Active Directory a prostředků Azure, zatímco oprávněné role se dají přiřadit jenom v Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **Microsoft doporučuje** , abyste měli žádná trvale aktivní přiřazení pro role Azure AD i role Azure, kromě doporučených [dvou účtů pro nouzový přístup k zábrusu](../roles/security-emergency-access.md), které by měly mít trvalou roli globálního správce.

I když nedoporučujeme žádného stálého správce, je někdy obtížné zajistit, aby si organizace tuto situaci dosáhli okamžitě. Tady jsou věci, které je potřeba vzít v úvahu při provádění tohoto rozhodnutí:

- Frekvence zvýšení oprávnění – Pokud uživatel potřebuje privilegované přiřazení jenom jednou, neměl by mít trvalé přiřazení. Na druhé straně platí, že pokud uživatel potřebuje roli pro svou každodenní úlohu a využití Privileged Identity Management by významně snížilo svou produktivitu, je možné je zvážit pro trvalou roli.
- Případy, které jsou specifické pro vaši organizaci – Pokud je osoba, která je udělena oprávněným rolím, ze vzdáleného týmu nebo vedoucího s vysokým hodnocením pro bod, který komunikuje a vynucování procesu zvýšení oprávnění obtížná, je možné je zvážit pro trvalou roli.

> [!TIP]
> : heavy_check_mark: **Microsoft doporučuje** , abyste pro uživatele, kteří mají trvalé přiřazení rolí, nastavili opakované kontroly přístupu (měli byste je). Přečtěte si další informace o opakovaném přezkoumání přístupu v závěrečném oddílu tohoto plánu nasazení.

### <a name="draft-your-privileged-identity-management-settings"></a>Koncepty nastavení Privileged Identity Management

Před implementací řešení Privileged Identity Management je vhodné koncepty Privileged Identity Management nastavení pro každou privilegovanou roli, kterou vaše organizace používá. V této části najdete několik příkladů nastavení Privileged Identity Management pro konkrétní role (jsou jenom pro referenci a můžou se pro vaši organizaci lišit). Každé z těchto nastavení je vysvětleno podrobněji s doporučeními Microsoftu po tabulkách.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Nastavení Privileged Identity Management pro role Azure AD

| Role | Vyžadování MFA | Notification (Oznámení) | Lístek incidentu | Vyžadovat schválení | Schvalovatel | Doba trvání aktivace | Trvalý správce |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Globální správce | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Jiní globální správci | 1 hodina | Účty pro nouzový přístup |
| Správce Exchange | :heavy_check_mark: | :heavy_check_mark: | znak | znak | Žádné | 2 hodiny | Žádné |
| Správce helpdesku | znak | znak | :heavy_check_mark: | znak | Žádné | 8 hodin | Žádné |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Nastavení Privileged Identity Management pro role Azure

| Role | Vyžadování MFA | Notification (Oznámení) | Vyžadovat schválení | Schvalovatel | Doba trvání aktivace | Aktivní správce | Aktivní doba platnosti | Nárok na vypršení platnosti |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Vlastník kritických předplatných | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Další vlastníci předplatného | 1 hodina | Žádné | Není k dispozici | 3 měsíce |
| Správce přístupu uživatele s méně důležitými předplatnými | :heavy_check_mark: | :heavy_check_mark: | znak | Žádné | 1 hodina | Žádné | Není k dispozici | 3 měsíce |
| Přispěvatel virtuálních počítačů | znak | :heavy_check_mark: | znak | Žádné | 3 hodiny | Žádné | Není k dispozici | 6 měsíců |

V následující tabulce jsou popsána jednotlivá nastavení.

| Nastavení | Popis |
| --- | --- |
| Role | Název role, pro kterou definujete nastavení. |
| Vyžadování MFA | Určuje, zda oprávněný uživatel musí před aktivací role provést MFA.<br/><br/> : heavy_check_mark: **Společnost Microsoft doporučuje** vymáhat MFA pro všechny role správce, zejména v případě, že role mají uživatele typu Host. |
| Notification (Oznámení) | Pokud je nastavená hodnota true, globální správce, správce privilegovaných rolí a správce zabezpečení v organizaci dostane e-mailové oznámení, když je role oprávněným uživatelem aktivována.<br/><br/>**Poznámka:** Některé organizace nemají e-mailovou adresu vázanou na účty správců. Pokud chcete získat tato e-mailová oznámení, měli byste nastavit alternativní e-mailovou adresu, aby správci dostali tyto e-maily. |
| Lístek incidentu | Zda oprávněný uživatel musí při aktivaci své role zaznamenat číslo lístku incidentu. Toto nastavení pomáhá organizaci identifikovat každou aktivaci pomocí interního čísla incidentu a zmírnit tak nechtěné aktivace.<br/><br/> : heavy_check_mark: **Společnost Microsoft doporučuje** využít výhod čísel lístků incidentů k propojení Privileged Identity Management do interního systému. Tato metoda může být užitečná pro schvalovatele, kteří potřebují kontext pro aktivaci. |
| Vyžadovat schválení | Zda oprávněný uživatel potřebuje získat souhlas s aktivací role.<br/><br/> : heavy_check_mark: **Microsoft doporučuje** , abyste si nastavili schválení pro role s nejvyšším oprávněním. V závislosti na vzorcích použití všech zákazníků Privileged Identity Management, globální správce, Správce uživatelů, správce Exchange, správce zabezpečení a správce hesel jsou nejběžnějšími rolemi s nastavením schvalování. |
| Schvalovatel | Pokud se schválení vyžaduje k aktivaci oprávněné role, uveďte uživatele, kteří by měli žádost schválit. Ve výchozím nastavení Privileged Identity Management nastaví schvalovatele na všechny uživatele, kteří jsou správce privilegované role, jestli jsou trvalé nebo oprávněné.<br/><br/>**Poznámka:** Pokud má uživatel nárok na roli Azure AD a na schvalovatele této role, nebude možné schválit samy sebe.<br/><br/> : heavy_check_mark: **Společnost Microsoft doporučuje** , abyste zvolili schvalovatele pro uživatele, kteří mají nejvíc znalostí o roli a nejčastějších uživatelů, nikoli globální správce. |
| Doba trvání aktivace | Doba, po kterou bude uživatel v roli aktivován, než vyprší jeho platnost. |
| Trvalý správce | Seznam uživatelů, kteří budou mít pro roli trvalého správce (nikdy se nemusí aktivovat)<br/><br/> : heavy_check_mark: **Společnost Microsoft doporučuje** , abyste měli žádného stálého správce pro všechny role s výjimkou globálních správců. Přečtěte si další informace o tom, kdo by měl mít nárok a kdo by měl být trvale aktivní oddíl tohoto plánu. |
| Aktivní správce | U prostředků Azure je aktivním správcem seznam uživatelů, kteří se nikdy nebudou muset k použití této role aktivovat. Tento seznam se neodkazuje jako na trvalého správce jako v rolích Azure AD, protože můžete nastavit dobu vypršení platnosti, kdy uživatel ztratí tuto roli. |
| Aktivní doba platnosti | Aktivní přiřazení rolí pro role Azure vyprší po nakonfigurované době trvání. Můžete zvolit 15 dní, 1 měsíc, 3 měsíc, 6 měsíců, 1 rok nebo trvale aktivní. |
| Nárok na vypršení platnosti | Po tuto dobu vyprší platnost přiřazení rolí pro role Azure. Můžete zvolit 15 dní, 1 měsíc, 3 měsíc, 6 měsíců, 1 rok nebo trvale opravňující. |

## <a name="implementation-plan"></a>Implementační plán

Základem správného plánování je základ, na kterém můžete aplikaci úspěšně nasadit pomocí Azure Active Directory.  Poskytuje inteligentní zabezpečení a integraci, které zjednodušují registraci při zkrácení doby úspěšného nasazení.  Tato kombinace zajišťuje, že vaše aplikace je snadno integrovaná a zároveň snižuje čas pro koncové uživatele.

### <a name="identify-test-users"></a>Identifikace testovacích uživatelů

Pomocí této části můžete identifikovat skupinu uživatelů a skupin uživatelů, kteří budou tuto implementaci ověřovat. V závislosti na nastaveních, která jste vybrali v části plánování, identifikujte uživatele, které chcete pro každou roli otestovat.

> [!TIP]
> : heavy_check_mark: **Společnost Microsoft doporučuje** , aby vlastníci služeb pro každou roli Azure AD byli testovacími uživateli, aby se mohli seznámit s procesem a stát se jeho interním poradcem.

V této tabulce Identifikujte testovací uživatele, kteří ověří, že nastavení rolí funguje.

| Název role | Testovací uživatelé |
| --- | --- |
| &lt;Název role&gt; | &lt;Uživatelé testují roli&gt; |
| &lt;Název role&gt; | &lt;Uživatelé testují roli&gt; |

### <a name="test-implementation"></a>Implementace testu

Teď, když jste identifikovali testovací uživatele, použijte tento krok ke konfiguraci Privileged Identity Management pro vaše testovací uživatele. Pokud chce vaše organizace začlenit Privileged Identity Management pracovní postup do vlastní interní aplikace namísto použití Privileged Identity Management v Azure Portal, jsou všechny operace v Privileged Identity Management podporované i prostřednictvím [rozhraní Graph API](/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Konfigurace Privileged Identity Management pro role Azure AD

1. [Nakonfigurujte nastavení role Azure AD](pim-how-to-change-default-settings.md) na základě toho, co jste naplánovali.

1. Přejděte na **role Azure AD**, vyberte **role** a pak vyberte roli, kterou jste nakonfigurovali.

1. Pro skupinu testovacích uživatelů, pokud už jsou trvalého správce, můžete je nastavit tak, že je vyhledáte a převedete je z trvalého na oprávněnou tím, že vyberete tři tečky na jejich řádku. Pokud ještě nemají přiřazení rolí, můžete [vytvořit nové opravňující přiřazení](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Opakujte kroky 1-3 pro všechny role, které chcete testovat.

1. Po nastavení testovacích uživatelů byste jim měli poslat odkaz, jak [aktivovat jejich roli Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>Konfigurace Privileged Identity Management pro role Azure

1. [Nakonfigurujte nastavení role prostředku Azure](pim-resource-roles-configure-role-settings.md) pro roli v rámci předplatného nebo prostředku, který chcete testovat.

1. Přejděte na **prostředky Azure** pro toto předplatné a vyberte **role**, vyberte roli, kterou jste nakonfigurovali.

1. Pro skupinu testovacích uživatelů, pokud už jsou aktivním správcem, můžete je nastavit tak, že je vyhledáte a [aktualizujete jejich přiřazení role](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Pokud tato role ještě nemáte, můžete [přiřadit novou roli](pim-resource-roles-assign-roles.md#assign-a-role).

1. Opakujte kroky 1-3 pro všechny role, které chcete testovat.

1. Po nastavení testovacích uživatelů byste jim měli poslat odkaz, jak [aktivovat svoji roli prostředků Azure](pim-resource-roles-activate-your-roles.md).

Tuto fázi byste měli použít k ověření, zda veškerá konfigurace, kterou jste pro role nastavili, funguje správně. K dokumentaci testů použijte následující tabulku. Tuto fázi byste měli použít také k optimalizaci komunikace s ovlivněnými uživateli.

| Role | Očekávané chování během aktivace | Skutečné výsledky |
| --- | --- | --- |
| Globální správce | (1) vyžadovat MFA<br/>(2) vyžadovat schválení<br/>(3) schvalovatel obdrží oznámení a může schválit.<br/>(4) platnost role vyprší po přednastaveném čase. |  |
| Vlastník předplatného *X* | (1) vyžadovat MFA<br/>(2) po nakonfigurovaném časovém období vyprší platnost oprávněného přiřazení |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Komunikace Privileged Identity Management ovlivněným zúčastněným stranám

Nasazení Privileged Identity Management zavádí další kroky pro uživatele privilegovaných rolí. I když Privileged Identity Management významně snižuje problémy zabezpečení spojené s privilegovanými identitami, musí být tato změna účinně oznámena před nasazením v rámci organizace. V závislosti na počtu ovlivněných správců se organizace často rozhodnou vytvořit interní dokument, video nebo e-mailovou zprávu o změně. Mezi často zahrnuté do těchto komunikací patří:

- Co je PIM
- Jaká je výhoda pro organizaci
- Komu bude ovlivněn
- Kdy bude PIM nasazený
- Jaké další kroky budou vyžadovat, aby mohli uživatelé aktivovat jejich roli.
    - Měli byste poslat odkazy na naši dokumentaci:
    - [Aktivovat role Azure AD](pim-how-to-activate-role.md)
    - [Aktivovat role Azure](pim-resource-roles-activate-your-roles.md)
- Kontaktní údaje nebo informace o helpdesku pro všechny problémy spojené s PIM

> [!TIP]
> : heavy_check_mark: **Společnost Microsoft doporučuje** nastavit čas pomocí týmu Helpdesk/support, aby je provedl prostřednictvím pracovního postupu Privileged Identity Management (Pokud má vaše organizace interní tým podpory IT). Poskytněte jim vhodné dokumenty a kontaktní údaje.

### <a name="move-to-production"></a>Přechod k produkčnímu prostředí

Až se vaše testování dokončí a proběhne úspěšně, přesuňte Privileged Identity Management do produkčního prostředí tak, že zopakujete všechny kroky v testovacích fázích pro všechny uživatele v každé roli, kterou jste definovali v konfiguraci Privileged Identity Management. Pro Privileged Identity Management pro role Azure AD se organizace často testují a navádějí Privileged Identity Management pro globální správce před testováním a zaváděním Privileged Identity Management pro jiné role. U prostředků Azure se organizace obvykle testují a navádějí Privileged Identity Management jedno předplatné Azure v jednom okamžiku.

### <a name="if-a-rollback-is-needed"></a>Pokud je nutné vrácení zpět

Pokud se Privileged Identity Management nepovedlo v produkčním prostředí fungovat podle potřeby, může vám následující postup vrácení změn vrátit zpět do známého stavu před nastavením Privileged Identity Management:

#### <a name="azure-ad-roles"></a>Role Azure AD

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Otevřete **Azure AD Privileged Identity Management**.
1. Vyberte **role Azure AD** a pak vyberte **role**.
1. U každé role, kterou jste nakonfigurovali, vyberte tři tečky (**...**) pro všechny uživatele s oprávněným přiřazením.
1. Vyberte možnost **nastavit** jako trvalo, aby bylo přiřazení role trvalé.

#### <a name="azure-roles"></a>Role Azure

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Otevřete **Azure AD Privileged Identity Management**.
1. Vyberte **prostředky Azure** a pak vyberte předplatné nebo prostředek, který chcete vrátit zpět.
1. Vyberte **role**.
1. U každé role, kterou jste nakonfigurovali, vyberte tři tečky (**...**) pro všechny uživatele s oprávněným přiřazením.
1. Vyberte možnost **nastavit** jako trvalo, aby bylo přiřazení role trvalé.

## <a name="next-steps-after-deploying"></a>Další kroky po nasazení

Úspěšné nasazení Privileged Identity Management v produkčním prostředí představuje významný krok předaný v souvislosti s zabezpečením privilegovaných identit vaší organizace. Nasazení Privileged Identity Management přináší další funkce Privileged Identity Management, které byste měli použít pro zabezpečení a dodržování předpisů.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Použití výstrah Privileged Identity Management k ochraně privilegovaného přístupu

Další informace o používání integrované funkce upozorňování Privileged Identity Management k ochraně vaší organizace najdete v tématu [výstrahy zabezpečení](pim-how-to-configure-security-alerts.md#security-alerts). Mezi tyto výstrahy patří: Správci nepoužívají privilegované role, role se přiřazují mimo Privileged Identity Management a role se aktivují příliš často a častěji. K zajištění plné ochrany vaší organizace byste měli pravidelně procházet seznam výstrah a opravovat problémy. Výstrahy můžete zobrazit a opravit následujícím způsobem:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Otevřete **Azure AD Privileged Identity Management**.
1. Vyberte **role Azure AD** a pak vyberte **výstrahy**.

> [!TIP]
> : heavy_check_mark: **Společnost Microsoft doporučuje** řešit všechny výstrahy označené s vysokou závažností okamžitě. U upozornění na střední a nízkou závažnost byste měli zůstat informováni a dělat změny, pokud se domníváte, že došlo k ohrožení zabezpečení.

Pokud některé z konkrétních výstrah nejsou užitečné nebo se nevztahují na vaši organizaci, můžete oznámení vždycky zavřít na stránce s výstrahami. Tuto možnost můžete kdykoli později vrátit zpět na stránce nastavení Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Nastavte kontroly opakovaného přístupu, aby pravidelně ověřovaly privilegované identity vaší organizace.

Kontroly přístupu jsou nejlepším způsobem, jak požádat uživatele přiřazené k privilegovaným rolím nebo konkrétním kontrolorům, aby každý uživatel potřeboval privilegovanou identitu. Kontroly přístupu jsou skvělé, pokud chcete snížit plochu pro útoky a zachovat vyhovující předpisy. Další informace o spuštění kontroly přístupu najdete v tématu recenze [přístupu k rolím Azure AD](pim-how-to-start-security-review.md) a kontrolám [přístupu k rolím Azure](pim-resource-roles-start-access-review.md). U některých organizací se vyžaduje pravidelné kontroly přístupu, aby bylo zajištěno dodržování zákonů a předpisů, zatímco jiné jsou, a proto je kontrola přístupu nejlepším způsobem, jak vyhodnotit zabezpečení nejnižších oprávnění v celé organizaci.

> [!TIP]
> : heavy_check_mark: **Microsoft doporučuje** nastavit čtvrtletní recenze přístupu pro všechny vaše role Azure AD a Azure.

Ve většině případů je kontrolor pro role Azure AD vlastními uživateli, zatímco kontrolor pro role Azure je vlastníkem předplatného, které je role v. Často se ale jedná o případ, kdy společnosti mají privilegované účty, které nejsou propojené s jinou e-mailovou adresou konkrétní osoby. V těchto případech nikdo nepřečte a nekontroluje přístup.

> [!TIP]
> : heavy_check_mark: **Microsoft doporučuje** přidat sekundární e-mailovou adresu pro všechny účty s privilegovanými přiřazeními rolí, která nejsou propojená s pravidelně kontrolovaným e-mailovou adresou.

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Získejte na maximum svůj protokol auditu, abyste vylepšili zabezpečení a dodržování předpisů.

Protokol auditu je místo, kde můžete zůstat v aktuálním stavu a musí být v souladu s předpisy. Privileged Identity Management v současné době ukládá 30denní historii všech historií vaší organizace do svého protokolu auditu, včetně těchto:

- Aktivace nebo deaktivace oprávněných rolí
- Aktivity přiřazení rolí uvnitř a mimo Privileged Identity Management
- Změny v nastavení role
- Žádosti, schválení nebo zamítnutí aktivit pro aktivaci rolí s nastavením schválení
- Aktualizace na výstrahy

Pokud jste globální správce nebo správce privilegovaných rolí, můžete získat přístup k protokolům auditu. Další informace najdete v tématu [Historie auditu pro role Azure AD](pim-how-to-use-audit-log.md) a [historii auditu pro role Azure](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: **Společnost Microsoft doporučuje** , abyste měli alespoň jednoho správce, aby každý týden pročetl všechny události auditu a vyexportovali události auditu každý měsíc.

Pokud chcete události auditu ukládat automaticky po delší dobu, protokol auditu Privileged Identity Management se automaticky synchronizuje do [protokolů auditu služby Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: **Společnost Microsoft doporučuje** , abyste nastavili [monitorování protokolů Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) pro archivaci událostí auditu v účtu úložiště Azure pro zajištění vyššího zabezpečení a dodržování předpisů.
