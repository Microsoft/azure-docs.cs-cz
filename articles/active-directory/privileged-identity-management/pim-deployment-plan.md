---
title: Nasazení Privileged Identity Management (PIM) – Azure Active Directory | Microsoft Docs
description: Popisuje, jak naplánovat nasazení Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ff46a693766ab12b53baba1ad8e4a56e174a076
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804483"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Nasazení Azure AD Privileged Identity Management (PIM)

Tento podrobný průvodce popisuje, jak naplánovat nasazení Azure Active Directory (Azure AD) Privileged Identity Management (PIM) ve vaší organizaci.

> [!TIP]
> V celém tomto dokumentu se zobrazí položky označené jako:
> 
> :heavy_check_mark: **Společnost Microsoft doporučuje**
> 
> Toto jsou obecná doporučení a měli byste je implementovat jenom v případě, že se vztahují na vaše konkrétní podnikové potřeby.

## <a name="step-1-learn-about-pim"></a>Krok 1. Další informace o PIM

Azure AD Privileged Identity Management (PIM) pomáhá spravovat privilegované administrativní role napříč službami Azure AD, prostředky Azure a dalšími online službami Microsoftu. V celém světě, kde jsou privilegované a zapomenuté privilegované identity, poskytuje řešení, jako je přístup za běhu, pracovní postupy schvalování žádostí a plně integrované kontroly přístupu, abyste mohli identifikovat, odhalit a zabránit škodlivým aktivitám privilegovaného přístupu. role v reálném čase. Nasazení PIM pro správu privilegovaných rolí v celé organizaci výrazně snižuje riziko a zpřístupnění cenné poznatky o aktivitách privilegovaných rolí.

### <a name="business-value-of-pim"></a>Obchodní hodnota PIM

**Řízení rizik** – Zabezpečte svou organizaci vynucováním principu [minimálního přístupu k oprávněním](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) a přístupu za běhu. Díky minimalizaci počtu trvalých přiřazení uživatelů k privilegovaným rolím a vynucenému schvalování a MFA ke zvýšení oprávnění můžete výrazně snížit rizika zabezpečení související s privilegovaným přístupem ve vaší organizaci. Vynucení minimálního oprávnění a přístupu za běhu vám také umožní zobrazit historii přístupu k privilegovaným rolím a sledovat problémy zabezpečení při jejich výskytu.

**Dodržování předpisů a zásady správného řízení** – nasazení PIM vytvoří prostředí pro řízení identit. Zvýšení úrovně oprávnění privilegovaných identit za běhu poskytuje způsob, jak PIM sledovat aktivity privilegovaného přístupu ve vaší organizaci. Budete také moci zobrazit a přijímat oznámení pro všechna přiřazení trvalých a oprávněných rolí v rámci vaší organizace. Díky kontrole přístupu můžete pravidelně auditovat a odebírat zbytečné privilegované identity a zajistit, aby vaše organizace vyhovovala nejpřísnějším standardům identity, přístupu a zabezpečení.

**Snižte náklady** – snižte náklady tím, že Eliminujte neefektivity, lidská chyba a problémy zabezpečení tím, že nasadíte PIM správně. Čistý výsledek představuje omezení síťových trestů přidružených k privilegovaným identitám, které jsou nákladné a obtížně se obnovují. PIM také pomůže vaší organizaci snížit náklady spojené s přístupem k auditování, pokud to bude v souladu s předpisy a standardy.

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Licenční požadavky

Aby bylo možné použít PIM, musí mít váš adresář jednu z následujících placených nebo zkušebních licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Další informace najdete v tématu [licenční požadavky pro použití PIM](subscription-requirements.md).

### <a name="key-pim-terminology"></a>Klíčová terminologie PIM

| Ukončení nebo koncept | Popis |
| --- | --- |
| způsobil | Přiřazení role, které vyžaduje, aby uživatel prováděl jednu nebo více akcí pro použití role. Pokud uživatel nastavil nárok na roli, to znamená, že může roli aktivovat, když potřebují provádět privilegované úlohy. Neexistuje žádný rozdíl v přístupu pro někoho, kdo má trvalé přiřazení oprávnění oproti oprávněné roli. Jediným rozdílem je, že někteří lidé nepotřebují přístup ke všemu času. |
| aktivovat | Proces provádění jedné nebo více akcí pro použití role, pro kterou má uživatel nárok. Akce můžou zahrnovat provádění kontroly vícefaktorového ověřování (MFA), poskytování obchodního odůvodnění nebo žádosti o schválení od určených schvalovatelů. |
| přístup JIT (just-in-time) | Model, ve kterém uživatelé obdrží dočasná oprávnění k provádění privilegovaných úloh, což zabrání škodlivým nebo neoprávněným uživatelům získat přístup po vypršení platnosti oprávnění. Přístup se udělí jenom v případě, že ho uživatelé potřebují. |
| Princip minimálního přístupu k oprávněním | Doporučený postup zabezpečení, při kterém je každý uživatel k dispozici pouze s minimálními oprávněními potřebnými k provádění úloh, které mají autorizaci provádět. Tento postup minimalizuje počet globálních správců a místo toho používá konkrétní role správce pro určité scénáře. |

Další informace najdete v tématu [terminologie](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-pim-works"></a>Podrobný přehled o tom, jak PIM funguje

1. PIM je nastavené tak, aby uživatelé byli oprávněni pro privilegované role.
1. Když oprávněný uživatel potřebuje použít svoji privilegovanou roli, aktivuje roli v PIM.
1. V závislosti na nastavení PIM nakonfigurovaném pro roli musí uživatel provést některé kroky (například provést vícefaktorové ověřování, získat schválení nebo zadat důvod).
1. Jakmile uživatel úspěšně aktivuje svou roli, obdrží roli pro předem nakonfigurované časové období.
1. Správci mohou zobrazit historii všech aktivit PIM v protokolu auditu. Můžou taky dál zabezpečit své klienty a splňovat dodržování předpisů pomocí funkcí PIM, jako jsou kontroly přístupu a výstrahy.

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-pim"></a>Role, které se dají spravovat pomocí PIM

**Role Azure AD** – tyto role jsou všechny v Azure Active Directory (například globální správce, správce Exchange a správce zabezpečení). Další informace o rolích a jejich funkci najdete v tématu [oprávnění role správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Nápovědu k určení rolí pro přiřazení správců najdete v tématu [nejméně privilegované role podle úkolu](../users-groups-roles/roles-delegate-by-task.md).

**Role prostředků Azure** – tyto role jsou propojené s prostředkem Azure, skupinou prostředků, předplatným nebo skupinou pro správu. PIM poskytuje přístup za běhu k předdefinovaným rolím, jako je vlastník, správce přístupu uživatelů a přispěvatel, i k [vlastním rolím](../../role-based-access-control/custom-roles.md). Další informace o rolích prostředků Azure najdete v tématu [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md).

Další informace najdete v tématu [role, které nemůžete spravovat v PIM](pim-roles.md).

## <a name="step-2-plan-your-deployment"></a>Krok 2. Plánování nasazení

Tato část se zaměřuje na to, co je potřeba udělat před nasazením PIM ve vaší organizaci. Je důležité postupovat podle pokynů a pochopit koncepty v této části, které vám pomohou vytvořit nejlepší plán přizpůsobený privilegovaným identitám vaší organizace.

### <a name="identify-your-stakeholders"></a>Identifikace zúčastněných stran

Následující část vám pomůže identifikovat všechny zúčastněné strany, které jsou součástí projektu, a musí se odhlásit, revidovat nebo si informovat. Zahrnuje samostatné tabulky pro nasazení rolí PIM pro role Azure AD a PIM pro role prostředků Azure. Přidejte zúčastněné strany do následující tabulky podle potřeby vaší organizace.

- SO = odhlásit se v tomto projektu
- R = zkontrolovat tento projekt a zadat vstup
- I = informovat tento projekt

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>Držitel PIM pro role Azure AD

| Name | Role | Action |
| --- | --- | --- |
| Jméno a e-mail | **Architekt identity nebo globální správce Azure**<br/>Zástupce z týmu pro správu identit, který má za následek definování způsobu, jakým se tato změna rovná, s hlavní infrastrukturou správy identit ve vaší organizaci. | SO/R/I |
| Jméno a e-mail | **Vlastník služby/manažer line**<br/>Zástupce od vlastníků IT služby nebo skupiny služeb. Představují klíč k rozhodování a pomáhají se zavádět PIM pro svůj tým. | SO/R/I |
| Jméno a e-mail | **Vlastník zabezpečení**<br/>Zástupce bezpečnostního týmu, který se může odhlásit, že plán splňuje požadavky vaší organizace na zabezpečení. | SO/R |
| Jméno a e-mail | **Správce podpory IT/Helpdesk**<br/>Zástupce oddělení IT, který může poskytnout vstup na základě podpory této změny z perspektivy technické podpory. | R/I |
| Jména a e-maily pro uživatele pilotního nasazení | **Uživatelé privilegovaných rolí**<br/>Skupina uživatelů, pro kterou je implementována Privileged Identity Management. Budou potřebovat informace o tom, jak aktivovat své role, jakmile bude PIM implementováno. | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>Držitel PIM pro role prostředků Azure

| Name | Role | Action |
| --- | --- | --- |
| Jméno a e-mail | **Předplatné/vlastník prostředku**<br/>Zástupce od vlastníků IT každého předplatného nebo prostředku, pro který chcete nasadit PIM | SO/R/I |
| Jméno a e-mail | **Vlastník zabezpečení**<br/>Zástupce bezpečnostního týmu, který se může odhlásit, že plán splňuje požadavky vaší organizace na zabezpečení. | SO/R |
| Jméno a e-mail | **Správce podpory IT/Helpdesk**<br/>Zástupce oddělení IT, který může poskytnout vstup na základě podpory této změny z perspektivy technické podpory. | R/I |
| Jména a e-maily pro uživatele pilotního nasazení | **Uživatelé role RBAC**<br/>Skupina uživatelů, pro kterou je implementována Privileged Identity Management. Budou potřebovat informace o tom, jak aktivovat své role, jakmile bude PIM implementováno. | I |

### <a name="enable-pim"></a>Povolit PIM

V rámci procesu plánování musíte nejdřív odsouhlasit a povolit PIM pomocí našeho [úvodního dokumentu](pim-getting-started.md)s PIM. Povolení PIM vám umožní přístup k některým funkcím, které jsou navržené speciálně pro pomoc s vaším nasazením.

Pokud je vaším cílem nasadit PIM pro prostředky Azure, měli byste postupovat podle našich [prostředků zjišťování Azure pro správu v dokumentu PIM](pim-resource-roles-discover-resources.md). Pouze vlastníci každého prostředku, skupiny prostředků a předplatného budou schopni je vyhledat v rámci PIM. Pokud jste globálním správcem, který se pokouší nasadit PIM pro prostředky Azure, můžete [zvýšit přístup ke správě všech předplatných Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , abyste měli přístup ke všem prostředkům Azure v adresáři pro zjišťování. Doporučuje se ale od každého vlastního vlastníka předplatného získat schválení před správou svých prostředků s PIM.

### <a name="enforce-principle-of-least-privilege"></a>Vymáhat princip nejnižších oprávnění

Je důležité se ujistit, že jste vynutili princip nejnižších oprávnění ve vaší organizaci pro Azure AD i vaše role prostředků Azure.

#### <a name="azure-ad-roles"></a>Role Azure AD

Pro role Azure AD je běžné, že organizace přiřadí roli globálního správce k velkému počtu správců, když většina správců potřebuje jenom jednu nebo dvě konkrétní role správce. Přiřazení privilegovaných rolí je také obvykle ponecháno nespravované.

> [!NOTE]
> Běžné nástrah v delegování role:
>
> - Správce, který se účtuje za Exchange, má roli globálního správce, a to i v případě, že potřebují jenom roli správce serveru Exchange, aby mohl provádět každodenní úlohu.
> - Role globálního správce je přiřazena správce sady Office, protože správce potřebuje role zabezpečení i správce služby SharePoint a je snazší pouze delegovat jednu roli.
> - Správce má přiřazenou roli správce zabezpečení, aby mohl provést dlouhou úlohu, ale nikdy se neodebral.

Postupujte podle těchto kroků, abyste vynutili princip nejnižších oprávnění pro vaše role Azure AD.

1. Přečtěte si o [dostupných rolích správce Azure AD](../users-groups-roles/directory-assign-admin-roles.md#available-roles)a pochopte si členitost rolí. Vy a váš tým by měl také odkazovat na [role správců podle identity ve službě Azure AD](../users-groups-roles/roles-delegate-by-task.md), která vysvětluje nejnižší privilegovaný roli pro konkrétní úkoly.

1. Seznam obsahující privilegované role ve vaší organizaci. [Průvodce PIM](pim-security-wizard.md#run-the-wizard) můžete použít k získání stránky podobné následujícímu.

    ![Podokno zjistit privilegované role zobrazující, kdo má privilegované role](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. U všech globálních správců ve vaší organizaci Zjistěte, proč potřebují roli. V závislosti na tom, jestli je možné úlohu uživatele provést pomocí jedné nebo více podrobných rolí správce, je potřeba, abyste je odebrali z role globálního správce a v rámci Azure Active Directory (jako referenci) byly odpovídajícím způsobem přiřazení. V současné době má společnost Microsoft přibližně 10 správců s rolí globálního správce. Přečtěte si další informace [o tom, jak Microsoft používá PIM](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

1. U všech ostatních rolí Azure AD zkontrolujte seznam přiřazení, identifikujte správce, kteří už roli nepotřebují, a odeberte je ze svých přiřazení.

K automatizaci kroků 3 a 4 můžete využít funkci kontroly přístupu v rámci PIM. Podle postupu v části [spuštění kontroly přístupu pro role Azure AD v PIM](pim-how-to-start-security-review.md)můžete nastavit kontrolu přístupu pro každou roli Azure AD, která má jednoho nebo více členů.

![Vytvoření podokna kontroly přístupu pro role Azure AD](./media/pim-deployment-plan/create-access-review.png)

Měli byste nastavit kontrolory na **členy (samy)** . Tím se pošle e-mail všem členům v roli, aby se ověřilo, jestli potřebují přístup. V rozšířených nastaveních byste měli také zapnout **důvod vyžadovat schválení** , aby uživatelé mohli stanovit, proč potřebují roli. Na základě těchto informací budete moct odebrat uživatele z zbytečných rolí a v případě globálních správců delegovat přesnější role správců.

Kontroly přístupu využívají e-maily, které lidem upozorňují na jejich přístup k rolím. Pokud máte privilegované účty, které nemají připojené e-maily, nezapomeňte na tyto účty naplnit sekundární pole e-mailu. Další informace najdete v tématu [atribut proxyAddresses ve službě Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Role prostředků Azure

U předplatných a prostředků Azure můžete nastavit podobný proces kontroly přístupu ke kontrole rolí v každém předplatném nebo prostředku. Cílem tohoto procesu je minimalizovat přiřazení vlastníků a správců přístupu uživatelů ke každému předplatnému nebo prostředku a odebrat nepotřebná přiřazení. Organizace ale často tyto úkoly předávají vlastníkovi každého předplatného nebo prostředku, protože mají lepší znalosti o konkrétních rolích (zejména vlastní role).

Pokud jste správcem IT s rolí globálního správce, která se pokouší nasadit prostředky PIM pro prostředky Azure ve vaší organizaci, můžete [zvýšit přístup ke správě všech předplatných Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , abyste získali přístup ke každému předplatnému. Pak můžete najít každého vlastníka předplatného a pracovat s nimi, abyste odebrali nepotřebná přiřazení a minimalizovali přiřazení role vlastníka.

Uživatelé s rolí vlastníka pro předplatné Azure můžou také využít kontroly přístupu k prostředkům Azure k tomu, aby [prozkoumali](pim-resource-roles-start-access-review.md) a odebrali nepotřebná přiřazení rolí podobně jako proces popsaný dříve pro role Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>Rozhodněte, která přiřazení rolí by se měla chránit pomocí PIM.

Po vyčištění přiřazení privilegovaných rolí ve vaší organizaci se musíte rozhodnout, jaké role chránit pomocí PIM.

Pokud je role chráněná PIM, musí se oprávněným uživatelům přiřadit oprávnění k používání oprávnění udělených rolí. Proces zvýšení oprávnění může také zahrnovat získání schválení, provádění vícefaktorového ověřování a/nebo poskytnutí odůvodnění, proč se aktivují. PIM může také sledovat zvýšení oprávnění prostřednictvím oznámení a protokolů událostí auditu PIM a Azure AD.

Volba rolí, které se mají chránit pomocí PIM, může být obtížná a bude pro každou organizaci odlišná. Tato část poskytuje naše osvědčené postupy pro Azure AD a role prostředků Azure.

#### <a name="azure-ad-roles"></a>Role Azure AD

Je důležité upřednostnit ochranu rolí Azure AD, které mají nejvyšší počet oprávnění. Na základě vzorů využití mezi všemi zákazníky PIM jsou hlavní 10 role Azure AD spravované pomocí PIM:

1. Globální správce
1. Správce zabezpečení
1. Správce uživatelů
1. Správce Exchange
1. Správce SharePointu
1. Správce Intune
1. Čtenář zabezpečení
1. Správce služby
1. Správce fakturace
1. Správce Skypu pro firmy

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** spravovat všechny globální správce a správce zabezpečení pomocí PIM jako první krok, protože se jedná o ty, které při ohrožení bezpečnosti můžou dělat nejvíce škod.

Je důležité zvážit, jaká data a oprávnění jsou pro vaši organizaci nejvíc citlivá. Například některé organizace mohou chtít chránit svou roli správce Power BI nebo jejich roli správce týmů pomocí PIM, protože mají možnost přistupovat k datům nebo měnit klíčové pracovní postupy.

Pokud jsou k dispozici nějaké role s přiřazenými uživateli typu Host, jsou obzvláště zranitelní vůči útokům.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** spravovat všechny role s uživateli typu Host pomocí PIM k omezení rizik spojených s ohroženými uživatelskými účty hostů.

Role čtenářů, jako je čtečka adresářů, čtenář centra zpráv a čtecí modul zabezpečení, se někdy považují za méně důležité ve srovnání s jinými rolemi, protože nemají oprávnění k zápisu. Někteří zákazníci ale také tyto role chránili, protože útočníci, kteří získali přístup k těmto účtům, můžou číst citlivá data, jako jsou identifikovatelné osobní údaje (PII). Tuto možnost byste měli vzít v úvahu při rozhodování, zda je potřeba spravovat role čtenářů ve vaší organizaci pomocí PIM.

#### <a name="azure-resource-roles"></a>Role prostředků Azure

Při rozhodování o tom, která přiřazení rolí by se měla spravovat pomocí prostředků PIM pro Azure, musíte nejdřív určit předplatné nebo prostředky, které jsou pro vaši organizaci nejdůležitější. Příklady těchto předplatných a prostředků:

- Prostředky, které hostují nejvíc citlivá data
- Prostředky, na kterých závisí základní aplikace pro zákazníky

Pokud jste globální správce a máte potíže s rozhodováním o tom, které předplatné/prostředky jsou nejdůležitější, měli byste se obrátit na vlastníky předplatného ve vaší organizaci, abyste mohli shromažďovat seznam prostředků spravovaných jednotlivými předplatnými. Pak byste měli spolupracovat s vlastníky předplatného a seskupit prostředky na základě úrovně závažnosti v případě ohrožení bezpečnosti (nízká, střední, vysoká). Měli byste nastavit prioritu správy prostředků s PIM na základě této úrovně závažnosti.

> [!TIP]
> :heavy_check_mark: **Microsoft doporučuje** pracovat s vlastníky předplatného a prostředků důležitých služeb a nastavit pracovní postup PIM pro všechny role v citlivých předplatných a prostředcích.

PIM pro prostředky Azure podporuje účty služeb s časovou vazbou. Účty služeb byste měli považovat přesně za to, jak byste pocházeli s běžným uživatelským účtem.

U předplatných a prostředků, které nejsou tak kritické, nebudete muset pro všechny role nastavovat PIM. Měli byste ale i nadále chránit role správců vlastník a přístup uživatelů pomocí PIM.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** spravovat role vlastníka a správce přístupu uživatelů pro všechna předplatná a prostředky pomocí PIM.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Rozhodnutí o tom, která přiřazení role by měla být trvalá nebo způsobilá

Jakmile se rozhodnete, že seznam rolí, které se mají spravovat pomocí PIM, musíte rozhodnout, kteří uživatelé by měli mít k trvalé aktivní roli oprávnění. Trvalé aktivní role jsou běžné role přiřazené prostřednictvím Azure Active Directory a prostředků Azure, zatímco oprávněné role se dají přiřadit jenom v PIM.

> [!TIP]
> :heavy_check_mark: **Microsoft doporučuje** , abyste měli žádná trvale aktivní přiřazení pro role Azure AD i pro role prostředků Azure, kromě doporučených [dvou účtů pro nouzový přístup k zábrusu](../users-groups-roles/directory-emergency-access.md), které by měly mít trvalého globálního správce. role.

I když nedoporučujeme žádného stálého správce, je někdy obtížné zajistit, aby si organizace tuto situaci dosáhli okamžitě. Tady jsou věci, které je potřeba vzít v úvahu při provádění tohoto rozhodnutí:

- Frekvence zvýšení oprávnění – Pokud uživatel potřebuje privilegované přiřazení jenom jednou, neměl by mít trvalé přiřazení. Na druhé straně platí, že pokud uživatel potřebuje roli pro svou každodenní úlohu a pomocí PIM by významně snížil svou produktivitu, je možné ji zvážit pro trvalou roli.
- Konkrétní případy, které jsou specifické pro vaši organizaci – Pokud je osoba, která je udělena oprávněným rolím, od velmi vzdáleného týmu nebo vedoucího s vysokým hodnocením pro bod, který komunikuje a vynucování procesu zvýšení oprávnění obtížná, je možné je zvážit pro trvalou roli.

> [!TIP]
> :heavy_check_mark: **Microsoft doporučuje** nastavit opakované kontroly přístupu pro uživatele, kteří mají trvalá přiřazení rolí (máte nějaké). Přečtěte si další informace o opakovaném přezkoumání přístupu v závěrečném oddílu tohoto plánu nasazení.

### <a name="draft-your-pim-settings"></a>Koncept nastavení PIM

Před implementací vašeho řešení PIM doporučujeme, abyste si vypravili nastavení PIM pro každou privilegovanou roli, kterou vaše organizace používá. V této části najdete některé příklady nastavení PIM pro konkrétní role (jsou jenom pro referenci a můžou se pro vaši organizaci lišit). Každé z těchto nastavení je vysvětleno podrobněji s doporučeními Microsoftu po tabulkách.

#### <a name="pim-settings-for-azure-ad-roles"></a>Nastavení PIM pro role Azure AD

| Role | Vyžadování MFA | Oznámení | Lístek incidentu | Vyžadovat schválení | Schvalovatel | Doba trvání aktivace | Trvalý správce |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Globální správce | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Jiní globální správci | 1 hodina | Účty pro nouzový přístup |
| Správce Exchange | :heavy_check_mark: | :heavy_check_mark: | znak | znak | Žádné | 2 Hour | Žádné |
| Správce technické podpory | znak | znak | :heavy_check_mark: | znak | Žádné | 8 Hour | Žádné |

#### <a name="pim-settings-for-azure-resource-roles"></a>Nastavení PIM pro role prostředků Azure

| Role | Vyžadování MFA | Oznámení | Vyžadovat schválení | Schvalovatel | Doba trvání aktivace | Aktivní správce | Aktivní doba platnosti | Nárok na vypršení platnosti |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Vlastník kritických předplatných | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Další vlastníci předplatného | 1 hodina | Žádné | neuvedeno | 3 měsíce |
| Správce přístupu uživatele s méně důležitými předplatnými | :heavy_check_mark: | :heavy_check_mark: | znak | Žádné | 1 hodina | Žádné | neuvedeno | 3 měsíce |
| Přispěvatel virtuálních počítačů | znak | :heavy_check_mark: | znak | Žádné | 3 Hour | Žádné | neuvedeno | 6 měsíců |

V následující tabulce jsou popsána jednotlivá nastavení.

| Nastavení | Popis |
| --- | --- |
| Role | Název role, pro kterou definujete nastavení. |
| Vyžadování MFA | Určuje, zda oprávněný uživatel musí před aktivací role provést MFA.<br/><br/> :heavy_check_mark: **Microsoft doporučuje** vymáhat MFA pro všechny role správce, zejména pokud role mají uživatele typu Host. |
| Oznámení | Pokud je nastavená hodnota true, globální správce, správce privilegovaných rolí a správce zabezpečení v organizaci dostane e-mailové oznámení, když je role oprávněným uživatelem aktivována.<br/><br/>**Poznámka:** Některé organizace nemají e-mailovou adresu vázanou na účty správců. Pokud chcete získat tato e-mailová oznámení, měli byste nastavit alternativní e-mailovou adresu, aby správci dostali tyto e-maily. |
| Lístek incidentu | Zda oprávněný uživatel musí při aktivaci své role zaznamenat číslo lístku incidentu. Toto nastavení pomáhá organizaci identifikovat každou aktivaci pomocí interního čísla incidentu a zmírnit tak nechtěné aktivace.<br/><br/> :heavy_check_mark: **Microsoft doporučuje** využít výhod čísel lístků incidentů k propojení PIM s vaším interním systémem. To je užitečné hlavně pro schvalovatele, kteří potřebují kontext pro aktivaci. |
| Vyžadovat schválení | Zda oprávněný uživatel potřebuje získat souhlas s aktivací role.<br/><br/> :heavy_check_mark: **Microsoft doporučuje** , abyste si nastavili schválení pro role s nejvyšším oprávněním. V závislosti na vzorcích použití všech zákazníků PIM, globální správce, Správce uživatelů, správce Exchange, správce zabezpečení a správce hesel jsou nejběžnějšími rolemi s nastavením schvalování. |
| Schvalovatel | Pokud se schválení vyžaduje k aktivaci oprávněné role, uveďte uživatele, kteří by měli žádost schválit. Ve výchozím nastavení PIM nastaví schvalovatele na všechny uživatele, kteří jsou správce privilegované role, jestli jsou trvalé nebo oprávněné.<br/><br/>**Poznámka:** Pokud má uživatel nárok na roli Azure AD a na schvalovatele této role, nebude možné schválit samy sebe.<br/><br/> :heavy_check_mark: **Společnost Microsoft doporučuje** , abyste zvolili schvalovatele jako ty, kteří jsou nejvíc znalost konkrétní role a Nejčastějšími uživateli, nikoli globální správce. |
| Doba trvání aktivace | Doba, po kterou bude uživatel v roli aktivován, než vyprší jeho platnost. |
| Trvalý správce | Seznam uživatelů, kteří budou mít pro roli trvalého správce (nikdy se nemusí aktivovat)<br/><br/> :heavy_check_mark: **Microsoft doporučuje** , abyste měli k dispozici žádný stálý správce pro všechny role s výjimkou globálních správců. Přečtěte si další informace o tom, kdo by měl mít nárok a kdo by měl být trvale aktivní oddíl tohoto plánu. |
| Aktivní správce | U prostředků Azure je aktivním správcem seznam uživatelů, kteří se nikdy nebudou muset k použití této role aktivovat. Nejedná se o trvalého správce jako v rolích služby Azure AD, protože můžete nastavit dobu vypršení platnosti, kdy uživatel ztratí tuto roli. |
| Aktivní doba platnosti | Po tomto nakonfigurovaném časovém období vyprší aktivní přiřazení role pro role prostředků Azure. Můžete zvolit 15 dní, 1 měsíc, 3 měsíc, 6 měsíců, 1 rok nebo trvale aktivní. |
| Nárok na vypršení platnosti | Po tomto nakonfigurovaném časovém období vyprší přiřazení oprávněné role pro role prostředků Azure. Můžete zvolit 15 dní, 1 měsíc, 3 měsíc, 6 měsíců, 1 rok nebo trvale opravňující. |

## <a name="step-3-implement-your-solution"></a>Krok 3. Implementace řešení

Základem správného plánování je základ, na kterém můžete aplikaci úspěšně nasadit pomocí Azure Active Directory.  Poskytuje inteligentní zabezpečení a integraci, které zjednodušují registraci při zkrácení doby úspěšného nasazení.  Tato kombinace zajišťuje, že vaše aplikace je snadno integrovaná a zároveň snižuje čas pro koncové uživatele.

### <a name="identify-test-users"></a>Identifikace testovacích uživatelů

Pomocí této části můžete identifikovat skupinu uživatelů a skupin uživatelů, kteří budou tuto implementaci ověřovat. V závislosti na nastaveních, která jste vybrali v části plánování, identifikujte uživatele, které chcete pro každou roli otestovat.

> [!TIP]
> :heavy_check_mark: **Microsoft doporučuje** , aby vlastníci služby pro každou roli Azure AD byli testovacími uživateli, aby se mohli seznámit s procesem a stát se jeho interním poradcem.

V této tabulce Identifikujte testovací uživatele, kteří budou ověřovat, že nastavení pro jednotlivé role fungují.

| Název role | Testovací uživatelé |
| --- | --- |
| &lt;Název role&gt; | &lt;Uživatelé testují roli&gt; |
| &lt;Název role&gt; | &lt;Uživatelé testují roli&gt; |

### <a name="test-implementation"></a>Implementace testu

Teď, když jste identifikovali testovací uživatele, použijte tento krok ke konfiguraci PIM pro vaše testovací uživatele. Pokud chce vaše organizace začlenit pracovní postup PIM do vaší vlastní interní aplikace namísto použití uživatelského rozhraní PIM v rámci Azure Portal, jsou všechny operace v PIM podporované i prostřednictvím [rozhraní Graph API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-pim-for-azure-ad-roles"></a>Konfigurace PIM pro role Azure AD

1. [Nakonfigurujte nastavení role Azure AD](pim-how-to-change-default-settings.md) na základě toho, co jste naplánovali.

1. Přejděte na **role Azure AD**, klikněte na **role**a potom vyberte roli, kterou jste právě nakonfigurovali.

1. Pro skupinu testovacích uživatelů, pokud už jsou trvalého správce, můžete je nastavit tak, že je vyhledáte a převedete je z trvalého na nárok kliknutím na tři tečky na jejich řádku. Pokud ještě nemají přiřazení rolí, můžete [vytvořit nové opravňující přiřazení](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Opakujte kroky 1-3 pro všechny role, které chcete testovat.

1. Po nastavení testovacích uživatelů byste jim měli poslat odkaz, jak [aktivovat jejich roli Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-pim-for-azure-resource-roles"></a>Konfigurace PIM pro role prostředků Azure

1. [Nakonfigurujte nastavení role prostředku Azure](pim-resource-roles-configure-role-settings.md) pro roli v rámci předplatného nebo prostředku, který chcete testovat.

1. Přejděte na **prostředky Azure** pro toto předplatné a klikněte na **role**, vyberte roli, kterou jste právě nakonfigurovali.

1. Pro skupinu testovacích uživatelů, pokud už jsou aktivním správcem, můžete je nastavit tak, že je vyhledáte a [aktualizujete jejich přiřazení role](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Pokud tato role ještě nemáte, můžete [přiřadit novou roli](pim-resource-roles-assign-roles.md#assign-a-role).

1. Opakujte kroky 1-3 pro všechny role, které chcete testovat.

1. Po nastavení testovacích uživatelů byste jim měli poslat odkaz, jak [aktivovat svoji roli prostředků Azure](pim-resource-roles-activate-your-roles.md).

Tuto fázi byste měli použít k ověření, zda veškerá konfigurace, kterou jste pro role nastavili, funguje správně. K dokumentaci testů použijte následující tabulku. Tuto fázi byste měli použít také k optimalizaci komunikace s ovlivněnými uživateli.

| Role | Očekávané chování během aktivace | Skutečné výsledky |
| --- | --- | --- |
| Globální správce | (1) vyžadovat MFA<br/>(2) vyžadovat schválení<br/>(3) schvalovatel obdrží oznámení a může schválit.<br/>(4) platnost role vyprší po přednastaveném čase. |  |
| Vlastník předplatného *X* | (1) vyžadovat MFA<br/>(2) po nakonfigurovaném časovém období vyprší platnost oprávněného přiřazení |  |

### <a name="communicate-pim-to-affected-stakeholders"></a>Komunikace PIM ovlivněným zúčastněným stranám

Nasazení PIM zavede další kroky pro uživatele privilegovaných rolí. I když PIM významně snižuje problémy zabezpečení spojené s privilegovanými identitami, musí být tato změna účinně oznámena před nasazením v rámci tenanta. V závislosti na počtu ovlivněných správců se organizace často rozhodnou vytvořit interní dokument, video nebo e-mailovou zprávu o změně. Mezi často zahrnuté do těchto komunikací patří:

- Co je PIM
- Jaká je výhoda pro organizaci
- Komu bude ovlivněn
- Kdy bude PIM nasazený
- Jaké další kroky budou vyžadovat, aby mohli uživatelé aktivovat jejich roli.
    - Měli byste poslat odkazy na naši dokumentaci:
    - [Aktivovat role Azure AD](pim-how-to-activate-role.md)
    - [Aktivovat role prostředků Azure](pim-resource-roles-activate-your-roles.md)
- Kontaktní údaje nebo informace o helpdesku pro všechny problémy spojené s PIM

> [!TIP]
> :heavy_check_mark: **Microsoft doporučuje** nastavit čas v rámci týmu helpdesku/podpory, aby je provedl prostřednictvím pracovního postupu PIM (Pokud má vaše organizace interní tým podpory IT). Poskytněte jim vhodné dokumenty a kontaktní údaje.

### <a name="move-to-production"></a>Přechod k produkčnímu prostředí

Až se vaše testování dokončí a bude úspěšné, přesuňte PIM do produkčního prostředí tak, že zopakujete všechny kroky v testovacích fázích pro všechny uživatele v každé roli, kterou jste definovali v konfiguraci PIM. Pro správce PIM pro role Azure AD se organizace často testují a zavedly pro globální správce před testováním a zavedení PIM pro jiné role. V současné době se v případě prostředku Azure organizace obvykle testují a zavádějí do služby PIM jedno předplatné Azure.

### <a name="in-the-case-a-rollback-is-needed"></a>V případě potřeby vrácení zpět

Pokud PIM nefunguje podle potřeby v produkčním prostředí, můžou vám tyto kroky vrácení zpět vrátit zpátky do známého stavu, než se nastavují PIM:

#### <a name="azure-ad-roles"></a>Role Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Otevřete **Azure AD Privileged Identity Management**.
1. Klikněte na **role Azure AD** a potom klikněte na **role**.
1. U každé role, kterou jste nakonfigurovali, klikněte na tři tečky ( **...** ) pro všechny uživatele s oprávněným přiřazením.
1. Klikněte na možnost **nastavit trvalo** , aby bylo přiřazení role trvalé.

#### <a name="azure-resource-roles"></a>Role prostředků Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Otevřete **Azure AD Privileged Identity Management**.
1. Klikněte na **prostředky Azure** a pak klikněte na předplatné nebo prostředek, který chcete vrátit zpět.
1. Klikněte na **role**.
1. U každé role, kterou jste nakonfigurovali, klikněte na tři tečky ( **...** ) pro všechny uživatele s oprávněným přiřazením.
1. Klikněte na možnost **nastavit trvalo** , aby bylo přiřazení role trvalé.

## <a name="step-4-next-steps-after-deploying-pim"></a>Krok 4. Další kroky po nasazení PIM

Úspěšné nasazení PIM v produkčním prostředí je významný krok předaný v souvislosti s zabezpečením privilegovaných identit vaší organizace. Nasazení PIM přináší další funkce PIM, které byste měli použít pro zabezpečení a dodržování předpisů.

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>Použití výstrah PIM k ochraně privilegovaného přístupu

K lepší ochraně vašeho tenanta byste měli využívat funkci integrovaného upozorňování PIM. Další informace najdete v tématu [výstrahy zabezpečení](pim-how-to-configure-security-alerts.md#security-alerts). Mezi tyto výstrahy patří: Správci nepoužívají privilegované role a role se přiřazují mimo PIM, role se aktivují příliš často a ještě více. K zajištění plné ochrany vaší organizace byste měli pravidelně procházet seznam výstrah a opravovat problémy. Výstrahy můžete zobrazit a opravit následujícím způsobem:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Otevřete **Azure AD Privileged Identity Management**.
1. Klikněte na **role Azure AD** a pak na **výstrahy**.

> [!TIP]
> :heavy_check_mark: **Microsoft doporučuje** , abyste se rozhodli, že budete okamžitě řešit všechny výstrahy označené vysokou závažností. U upozornění na střední a nízkou závažnost byste měli zůstat informováni a dělat změny, pokud se domníváte, že došlo k ohrožení zabezpečení.

Pokud některé z konkrétních výstrah nejsou užitečné nebo se nevztahují na vaši organizaci, můžete oznámení vždycky zavřít na stránce s výstrahami. Tuto možnost můžete kdykoli později vrátit zpět na stránce nastavení Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Nastavte kontroly opakovaného přístupu, aby pravidelně ověřovaly privilegované identity vaší organizace.

Kontroly přístupu jsou nejlepším způsobem, jak požádat uživatele přiřazené k privilegovaným rolím nebo konkrétním kontrolorům, aby každý uživatel potřeboval privilegovanou identitu. Kontroly přístupu jsou skvělé, pokud chcete snížit plochu pro útoky a zachovat vyhovující předpisy. Další informace o spuštění kontroly přístupu najdete v tématu kontroly přístupu [k rolím Azure AD](pim-how-to-start-security-review.md) a [přezkoumání přístupu k rolím prostředků Azure](pim-resource-roles-start-access-review.md). U některých organizací se vyžaduje pravidelné kontroly přístupu, aby bylo zajištěno dodržování zákonů a předpisů, zatímco jiné jsou, a proto je kontrola přístupu nejlepším způsobem, jak vyhodnotit zabezpečení nejnižších oprávnění v celé organizaci.

> [!TIP]
> :heavy_check_mark: **Microsoft doporučuje** nastavit čtvrtletní recenze přístupu pro všechny vaše role prostředků Azure AD a Azure.

Ve většině případů je kontrolor pro role Azure AD vlastními uživateli, zatímco kontrolor pro role prostředků Azure je vlastníkem předplatného, které je role v. Často se ale jedná o případ, kdy společnosti mají privilegované účty, které nejsou propojené s jinou e-mailovou adresou konkrétní osoby. V těchto případech nikdo nepřečte a nekontroluje přístup.

> [!TIP]
> :heavy_check_mark: **Microsoft doporučuje** přidat sekundární e-mailovou adresu pro všechny účty s privilegovanými přiřazeními rolí, která nejsou propojená s pravidelně kontrolovaným e-mailovou adresou.

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Získejte na maximum svůj protokol auditu, abyste vylepšili zabezpečení a dodržování předpisů.

Protokol auditu je místo, kde můžete zůstat v aktuálním stavu a musí být v souladu s předpisy. PIM v současné době ukládá 30denní historii všech historií vaší organizace do svého protokolu auditu, včetně těchto:

- Aktivace nebo deaktivace oprávněných rolí
- Aktivity přiřazení rolí uvnitř a mimo PIM
- Změny v nastavení role
- Žádosti, schválení nebo zamítnutí aktivit pro aktivaci rolí s nastavením schválení
- Aktualizace na výstrahy

Pokud jste globální správce nebo správce privilegovaných rolí, můžete k těmto protokolům auditu přistupovat. Další informace najdete v tématu [Historie auditu pro role Azure AD](pim-how-to-use-audit-log.md) a [historii auditu pro role prostředků Azure](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: **Microsoft doporučuje** , abyste měli aspoň jednoho správce, aby každý týden pročetl všechny události auditu a vyexportovali události auditu každý měsíc.

Pokud chcete události auditu ukládat automaticky po delší dobu, protokol auditu PIM se automaticky synchronizuje do [protokolů auditu služby Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: **Microsoft doporučuje** , abyste si nastavili [monitorování protokolů Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) pro archivaci událostí auditu v účtu Azure Storage, aby bylo potřeba zabezpečení a dodržování předpisů.
