---
title: Nasazení Azure AD Privileged Identity Management (PIM) | Dokumentace Microsoftu
description: Popisuje, jak naplánovat nasazení služby Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: c1ecca0bf8021fb1b97628a73eddbe7968a02548
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985152"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Nasazení Azure AD Privileged Identity Management (PIM)

Tento podrobný návod popisuje, jak naplánovat nasazení služby Azure AD Privileged Identity Management (PIM) ve vaší organizaci.

> [!TIP]
> V tomto dokumentu se zobrazí položky označené jako:
>
> :heavy_check_mark: **Společnost Microsoft doporučuje**
>
> Jedná se obecná doporučení, která by měla implementovat pouze pokud se vztahují k vašim potřebám konkrétní organizace.

## <a name="step-1-learn-about-pim"></a>Krok 1. Další informace o PIM

Azure AD Privileged Identity Management (PIM) vám umožní spravovat privilegované role správce v Azure AD, prostředky Azure a dalších Microsoft Online Services. Ve světě, kde privilegované identity nejsou přiřazeni a zapomenout PIM poskytuje řešení, jako jsou just-in-time, pracovní postupy schválení žádostí a plně integrované přístup kontroly, abyste mohli identifikovat odkrýt a zabránit škodlivých aktivit privilegovaný role v reálném čase. Nasazení PIM spravovat privilegované role v celé vaší organizaci výrazně snížit riziko během zpřístupnění cenné přehledy o aktivitách privilegovaných rolí.

### <a name="business-value-of-pim"></a>Obchodní hodnota PIM

**Řízení rizik** -umožňuje zabezpečit vaši organizaci tím, že vynucuje zásady [nejnižší oprávnění přístupu](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) a přístup v čase. Minimalizovat počet trvalých přiřazení uživatelů k privilegovaným rolím a vynucení schválení a vícefaktorové ověřování pro zvýšení oprávnění, můžete výrazně snížit bezpečnostní rizika související s privilegovaným přístupem ve vaší organizaci. Vynucování nejnižších oprávnění a just-in-time přístup také umožňuje zobrazit historii přístup k privilegovaným rolím a sledovat problémy se zabezpečením při jejich provádění.

**Dodržování předpisů a zásad správného řízení** – nasazení PIM vytvoří prostředí pro probíhající zásady správného řízení identit. Elevací za běhu privilegovaných identit umožňuje PIM ke sledování aktivit privilegovaného přístupu ve vaší organizaci. Také budete moct zobrazit a přijímat oznámení pro všechna přiřazení rolí trvalý a bude možné je uvnitř vaší organizace. Prostřednictvím kontroly přístupu můžete pravidelně auditu a odeberte nepotřebné privilegované identity a ujistěte se, že je v souladu s nejvíce přísné standardy identity, přístupu a zabezpečení vaší organizace.

**Snížení nákladů na** -snížení nákladů odstraněním neefektivity, lidské chyby a problémy se zabezpečením nasazením PIM správně. Net výsledkem je snížení kybernetických crimes spojené s privileged identity, které jsou nákladné a obtížně obnoven. PIM vám také pomůže vaší organizaci, snížit náklady spojené s auditování přístupu k informacím v případě, že jde o dodržování předpisů a standardů využívá.

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Licenční požadavky

Pokud chcete používat PIM, adresáři musí mít jeden z následujících placené nebo zkušební licence:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Další informace najdete v tématu [licenční požadavky pro použití PIM](subscription-requirements.md).

### <a name="key-pim-terminology"></a>Terminologie klíč PIM

| Ukončení nebo koncept | Popis |
| --- | --- |
| Oprávněné | Přiřazení role, které musí uživatel provést jeden nebo více akcí umožňujících využití roli. Pokud uživatel provedl oprávněné pro roli, znamená to, že se aktivovat roli, kdy potřebují k provedení privilegovaných úloh. Není žádný rozdíl v přístupu k někdo s trvalou oproti přiřazení oprávněné role. Jediným rozdílem je, že někteří uživatelé nepotřebují tento přístup neustále. |
| aktivovat | Proces provedení jedné nebo více akcí umožňujících využití roli, jejímž je uživatel nárok. Akce může zahrnovat provedení kontroly ověřování službou Multi-Factor Authentication (MFA), poskytuje obchodní odůvodnění, nebo z určených schvalovatelů odesílání žádostí o schválení. |
| přístup k just-in-time (JIT) | Model ve kterém uživatelé dostanou dočasné oprávnění k provedení privilegovaných úlohy, což zabrání zlými úmysly nebo neoprávněným uživatelům v získání přístupu po vypršení platnosti oprávnění. Přístup je udělen jenom v případě, že ji uživatelé potřebovat. |
| Princip nejnižších oprávnění přístupu | Doporučené zabezpečení praxe, ve kterém je každý uživatel součástí pouze minimální oprávnění jsou nutné k provedení úlohy, které mají oprávnění k provedení. Tento postup minimalizuje počet globálních správců a místo toho používá konkrétní správce role pro určité scénáře. |

Další informace najdete v tématu [terminologie](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-pim-works"></a>Základní přehled o fungování PIM

1. PIM je nastavený tak, že uživatelé jsou způsobilé pro privilegované role.
1. Oprávněný uživatel potřebuje používat svou privilegovanou roli, aktivují role v PIM.
1. V závislosti na nastavení PIM nakonfigurované pro roli musí uživatel provést některé kroky (jako je ověřování službou Multi-Factor Authentication, získávání schválení nebo zadáte důvod.)
1. Jakmile se uživatel úspěšně tuto roli aktivuje, dostanou roli pro předem nakonfigurované časové období.
1. Správci mohou zobrazit historii všechny aktivity PIM v protokolu auditu. Můžete také dále zabezpečení svým klientům a nesplní dodržování předpisů pomocí PIM funkce, jako jsou výstrahy a kontroly přístupu.

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-pim"></a>Role, které je možné spravovat pomocí PIM

**Role Azure AD** – tyto role jsou všechny role adresáře v Azure Active Directory (například globální správce, správce Exchange a správce zabezpečení). Další informace o rolích a jejich funkce v [oprávnění role správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Nápovědu k zjištění rolí přiřadit správce najdete v tématu [nejméně privilegované role úlohou](../users-groups-roles/roles-delegate-by-task.md).

**Role prostředků Azure** – tyto role jsou spojeny s prostředky Azure, skupinu prostředků, předplatné nebo skupinu pro správu. PIM poskytuje just-in-time přístup k oběma předdefinované role jako vlastník správce přístupu uživatelů a přispěvatelů, stejně jako [vlastní role](../../role-based-access-control/custom-roles.md). Další informace o role prostředků Azure najdete v tématu [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md).

Další informace najdete v tématu [role nemůže spravovat v PIM](pim-roles.md).

## <a name="step-2-plan-your-deployment"></a>Krok 2. Plánování nasazení

Tato část se zaměřuje na co je potřeba udělat před nasazením PIM ve vaší organizaci. Je nutné postupovat podle pokynů a pochopit koncepty v této části, jak se vám pomohou vytvořit si nejvhodnější plán navržených pro privilegované identity vaší organizace.

### <a name="identify-your-stakeholders"></a>Identifikovat účastníky

Následující část vám pomůže Identifikujte všechny zúčastněné strany, které jsou součástí projektu a nutné schválení, projděte si nebo Udržujte si přehled. Zahrnuje samostatných tabulek pro nasazení PIM pro role Azure AD a PIM pro role prostředků Azure. Přidání účastníků v následující tabulce v závislosti na vaší organizaci.

- TAKŽE = přihlašování vypnout na tomto projektu
- R = Zkontrolujte tento projekt a zadání
- Můžu = informovaný tohoto projektu.

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>Zúčastněné strany: Role PIM pro Azure AD

| Název | Role | Akce |
| --- | --- | --- |
| Jméno a e-mailu | **Architekt identity nebo globální správce Azure**<br/>Zástupce z týmu správy identit pro definování, jak je tuto změnu v souladu s základní infrastruktury správy identit ve vaší organizaci. | SO/R/I |
| Jméno a e-mailu | **Vlastník služby / řádek správce**<br/>Zástupce od vlastníků IT služby nebo skupiny služeb. Jsou to klíč v rozhodování a pomáhá zavést PIM pro jejich tým. | SO/R/I |
| Jméno a e-mailu | **Vlastník zabezpečení**<br/>Zástupce od bezpečnostního týmu, který může PM, plán splňuje požadavky na zabezpečení vaší organizace. | SO/R |
| Jméno a e-mailu | **IT správce podpory / helpdesku**<br/>Zástupce z podpory IT v organizaci, který může poskytnout vstup na možnosti podpory této změny z hlediska technickou podporu. | R/I |
| Jméno a e-mailu pro uživatele pilotního nasazení | **Privilegovaných rolí uživatelů**<br/>Skupiny uživatelů, pro které je implementováno privileged identity management. Bude musí vědět, jak aktivovat svoje role po implementaci PIM. | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>Zúčastněné strany: Role prostředků PIM pro Azure

| Název | Role | Akce |
| --- | --- | --- |
| Jméno a e-mailu | **Předplatné / vlastníka prostředku**<br/>Zástupce z každé předplatné nebo prostředek, který chcete nasadit PIM pro vlastníky IT | SO/R/I |
| Jméno a e-mailu | **Vlastník zabezpečení**<br/>Zástupce od bezpečnostního týmu, který může PM, plán splňuje požadavky na zabezpečení vaší organizace. | SO/R |
| Jméno a e-mailu | **IT správce podpory / helpdesku**<br/>Zástupce z podpory IT v organizaci, který může poskytnout vstup na možnosti podpory této změny z hlediska technickou podporu. | R/I |
| Jméno a e-mailu pro uživatele pilotního nasazení | **Uživatelé v roli RBAC**<br/>Skupiny uživatelů, pro které je implementováno privileged identity management. Bude musí vědět, jak aktivovat svoje role po implementaci PIM. | I |

### <a name="enable-pim"></a>Povolení PIM

Jako součást procesu plánování, musíte nejdřív vyjádřit souhlas a povolení PIM podle našich [začít používat PIM dokumentu](pim-getting-started.md). Povolení PIM umožňuje přístup k některým funkcím, které jsou vytvořené speciálně pro pomoct s nasazením.

Pokud je váš cíl nasazení PIM pro prostředky Azure, měli byste postupovat podle našich [vyhledejte prostředky Azure ke správě v PIM dokumentu](pim-resource-roles-discover-resources.md). Pouze vlastníci každý prostředek, skupinu prostředků a předplatné budete moct vyhledat v PIM. Pokud jste globální správce pokusu o nasazení PIM pro prostředky Azure, můžete [zvýšení úrovně přístupu ke správě všech předplatných Azure ](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , abyste měli přístup ke všem prostředkům Azure v adresáři pro zjišťování. Doporučujeme však získat schválení ze všech vašich na vlastníky těchto předplatných než začnete spravovat své prostředky pomocí služby PIM.

### <a name="enforce-principle-of-least-privilege"></a>Vynutit principu nejnižších možných oprávnění

Je důležité, abyste měli jistotu, že máte vynucena principu nejnižších možných oprávnění ve vaší organizaci pro obě služby Azure AD a vaší role prostředků Azure.

#### <a name="azure-ad-roles"></a>Role Azure AD

Pro role Azure AD je běžné, že organizace přiřadit roli globálního správce pro velký počet správců při Většina správců stačí jeden nebo dva konkrétní správce rolí. Nespravované privilegovaných rolí, které se také často zůstat přiřazení.

> [!NOTE]
> Běžné nástrahy delegování role:
>
> - Se správce systému Exchange je uveden v roli globálního správce, i když pouze potřebují roli správce serveru Exchange k provedení své každodenní práce.
> - V roli globálního správce je přiřazené správcem Office, protože správce musí správce rolí zabezpečení a Sharepointu a je snadněji delegují právě jednu roli.
> - Správce byla přiřazena role správce zabezpečení k provedení úkolu dávno, ale nikdy se odebrala.

Použijte následující postup Princip nejnižších oprávnění pro role Azure AD.

1. Čtení a pochopení pochopit členitosti role [dostupné role správce Azure AD](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Vy a váš tým by měl také odkazovat [role správce úlohou identity ve službě Azure AD](../users-groups-roles/roles-delegate-by-task.md), která vysvětluje nejméně privilegovaných rolí pro konkrétní úlohy.

1. Seznam, který má privilegované role ve vaší organizaci. Můžete použít [PIM průvodce](pim-security-wizard.md#run-the-wizard) zobrazíte stránku následujícím postupem.

    ![Zjistit privilegované role](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Pro všechny globální správce ve vaší organizaci přečtěte si, proč potřebují roli. Založené na přečtení dokumentace k předchozím, pokud jeden nebo více rolí detailní správce může provádět úlohy osoby, by měly odebrat z role Globální správce a odpovídajícím způsobem vytvoření přiřazení v Azure Active Directory (jako referenci: Microsoft aktuálně obsahuje pouze asi 10 správců s rolí globálního správce. Další informace najdete na [jak Microsoft využívá PIM](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Pro všechny ostatní role Azure AD projděte si seznam přiřazení, identifikovat správci, kteří už nepotřebují roli a odeberte jejich přiřazení.

Pokud chcete automatizovat kroky 3 a 4, můžou využívat funkci kontroly přístupu v PIM. Proveďte kroky v [zahájení kontroly přístupu pro role Azure AD v PIM](pim-how-to-start-security-review.md), můžete nastavit kontroly přístupu pro každou roli Azure AD, která má jeden nebo více členů.

![Vytvoření kontroly přístupu](./media/pim-deployment-plan/create-access-review.png)

Revidující byste měli nastavit na **členové (vlastní)**. To se rozešle e-mail všem členům role získat, aby ověřil, zda potřebují přístup. Měli byste také zapnout **vyžadovat důvod při schválení** v upřesňujících nastaveních tak, aby uživatelé můžou stát proč potřebují roli. Na základě těchto informací, budete moct odebrat uživatele z nepotřebné role a delegování podrobnější rolí správce v případě globální správci.

Kontroly přístupu závisí na e-mailů, aby bylo uživatelům zkontrolovat přístup k rolím. Pokud mají privilegovaný účty, které nemají e-mailů propojený, je nutné k vyplnění pole e-mailu sekundárním u těchto účtů. Další informace najdete v tématu [atributu proxyAddresses v Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Role prostředků Azure

Pro předplatná Azure a prostředky můžete nastavit podobným kontroly přístupu ke kontrole role v každé předplatné nebo prostředek. Cílem tohoto procesu je minimalizovat přiřazení vlastníka a správce uživatelských přístupů připojeny k každé předplatné nebo prostředek stejně jako odebrat nepotřebné přiřazení. Organizace ale často delegovat takových úloh vlastníkovi každé předplatné nebo prostředek, protože mají lépe pochopit specifické role (zejména vlastní role).

Pokud jste správcem IT s rolí globálního správce při nasazení PIM pro prostředky Azure ve vaší organizaci, můžete [zvýšení úrovně přístupu ke správě všech předplatných Azure ](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) získat přístup ke každé předplatné. Můžete najít každý vlastník předplatného a pracovat s nimi odebrat nepotřebné přiřazení a minimalizovat přiřazení role vlastníka.

Uživatelé s rolí vlastník předplatného Azure můžete také využít [kontroly pro prostředky Azure přístupu](pim-resource-roles-start-access-review.md) auditu a odeberte nepotřebné role přiřazení podobný procesu pro role Azure AD je popsáno výše.

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>Rozhodněte, které přiřazení rolí by měly být chráněny pomocí PIM

Po vyčištění přiřazení privilegovaných rolí ve vaší organizaci, musíte rozhodnout, jaké role pro ochranu pomocí služby PIM.

Pokud role se nechrání pomocí PIM, oprávněným uživatelům přiřazena musí zvýšit na oprávněních udělených systémem roli použít. Získání schválení, provádí se ověřování službou Multi-Factor Authentication a/nebo poskytování důvod, proč se aktivuje, může také obsahovat proces zvýšení oprávnění. PIM můžete také sledovat bezpečnostně oznámeními a protokoly událostí auditu PIM a Azure AD.

Výběr rolí chránit pomocí služby PIM, může být obtížné a se bude lišit pro každou organizaci. Tato část obsahuje naše Rady založené na osvědčených pro službu Azure AD a role prostředků Azure.

#### <a name="azure-ad-roles"></a>Role Azure AD

Je důležité k určení priority ochrana role Azure AD, které mají nejvíce oprávnění. Na základě způsobů využití mezi všechny zákazníky PIM, role Azure AD prvních 10 spravovaných pomocí PIM jsou:

1. Globální správce
1. Správce zabezpečení
1. Správce uživatelských účtů
1. Správce Exchange
1. Správce Sharepointu
1. Správce služby Intune
1. Čtecí zařízení pro zabezpečení
1. Správce služeb
1. Správce fakturace
1. Správce Skypu pro firmy

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** spravujete všichni globální správci a správci zabezpečení pomocí PIM jako první krok, jako jsou ty, které můžete provést na maximum poškození, když dojde k ohrožení bezpečnosti.

Je důležité vzít v úvahu, jaká data a oprávnění jsou nejcitlivější pro vaši organizaci. Například některé organizace chtít chránit jejich roli správce Power BI nebo jejich roli správce týmy mají možnost získat přístup k datům a/nebo změnit pracovní postupy core pomocí PIM.

Pokud neexistují žádné role s přiřazenými uživateli typu Host, jsou zvláště snadno napadnutelný.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** můžete spravovat všechny role s uživatelům typu Host pomocí PIM pro snížení rizika spojená s ohroženými hosta uživatelskými účty.

Role Čtenář jako čtečky adresáře, čtenář Centra zpráv a čtenář zabezpečení jsou někdy považována za méně důležité ve srovnání s další role, protože nemají oprávnění k zápisu. Nicméně se objevuje někteří zákazníci taky chránit tyto role, protože útočníci, kteří získali přístup k tyto účty mohou být schopni číst citlivá data, jako jsou identifikovatelné osobní údaje (PII). Je třeba vzít v úvahu při rozhodování o tom, jestli je potřeba čtečky role ve vaší organizaci se spravují pomocí PIM.

#### <a name="azure-resource-roles"></a>Role prostředků Azure

Při rozhodování, která přiřazení role se mají spravovat pomocí PIM pro prostředky Azure, musíte nejdřív určit předplatná a prostředky, které jsou nejvíce důležité pro vaši organizaci. Příkladem takové předplatná a prostředky jsou:

- Prostředky, které jsou hostiteli nejcitlivější data
- Prostředky, které jádro, zákaznických aplikací závisí na

Pokud jste globální správce máte potíže při rozhodování o tom, které předplatná a prostředky jsou nejdůležitější, obraťte se vlastníkům předplatného ve vaší organizaci k získání seznamu prostředků spravuje každé předplatné. Měli byste pak pracovat s vlastníky předplatného do skupiny prostředků podle úrovně závažnosti v případě, že dojde k ohrožení (nízká, střední, vysoká). Měli byste prioritně prostředků, které spravují pomocí PIM podle tato úroveň závažnosti.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** práce s vlastníky předplatného nebo prostředek důležitých služeb nastavit pracovní postup PIM pro všechny role v citlivých prostředků nebo předplatných.

PIM pro prostředky Azure podporuje účty časově omezenou službu. Účty služby by měly zpracovávat stejně jako způsob, jakým by považovat běžný uživatelský účet.

Pro předplatná a prostředky, které nejsou tak kritické nemusíte nastavení PIM u všech rolí. Ale měli stále chránit role vlastníka a správci přístupu uživatelů pomocí služby PIM.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** spravovat role vlastníka a role správce uživatelských přístupů všechny odběry a prostředky s využitím PIM.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Rozhodněte, které přiřazení rolí by měl být trvalé nebo oprávněné

Až si ověříte seznam rolí jej lze spravovat pomocí PIM, musíte se rozhodnout, které uživatelé měli získat oprávněné role a trvale aktivní roli. Trvale aktivní role jsou přiřazené prostřednictvím Azure Active Directory a prostředky Azure, zatímco oprávněnou roli je možné přiřadit pouze v PIM normální role.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** mají nulovou trvale aktivní přiřazení role Azure AD a role prostředků Azure než doporučenou [dva účty pro nouzový přístup skleněné](../users-groups-roles/directory-emergency-access.md), které by měly mít trvalé roli globálního správce.

Přestože doporučujeme žádný stávající správce, je někdy složité pro organizace, jak tohoto dosáhnout okamžitě. Tady jsou věci k uvážení při tomto rozhodnutí:

- Frekvence zvýšení – uživatel potřebuje pouze jednou, privilegovaných přiřazení, by neměla mít trvalé přiřazení. Na druhé straně Pokud uživatel potřebuje roli pro své každodenní práce a pomocí PIM by výrazně omezit jejich produktivitu, mohly být uvažovány pro trvalé roli.
- Případy, které jsou specifické pro vaši organizaci – Pokud uživatel obdrží oprávněné role od velmi vzdálených týmu nebo vyššího vedení do bodu, který komunikace a vynucování procesů ke zvýšení úrovně oprávnění je obtížné, mohly být uvažovány pro trvalé roli.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** můžete nastavit přístup opakované kontroly pro uživatele s rolí trvalé přiřazení (Pokud máte některé). Další informace o opakování kontroly přístupu v poslední části Tento plán nasazení

### <a name="draft-your-pim-settings"></a>Koncept nastavení PIM

Před implementací řešení PIM je dobrým zvykem koncept nastavení PIM pro každý privilegovaných rolí, které vaše organizace používá. Tato část obsahuje příklady nastavení PIM pro konkrétní role (jsou pouze pro referenci a můžou být různé pro organizaci). Za tabulky každé z těchto nastavení je podrobně popsána pomocí doporučení společnosti Microsoft.

#### <a name="pim-settings-for-azure-ad-roles"></a>Nastavení PIM pro role Azure AD

| Role | Vyžadování MFA | Oznámení | Lístek incidentu | Vyžadovat schválení | Schvalovatel | Doba trvání aktivace | Trvalého správce |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Globální správce | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Další globální správce | 1 hodina | Účty pro nouzový přístup |
| Správce Exchange | :heavy_check_mark: | :heavy_check_mark: | : x:. | : x:. | Žádný | 2 Hour | Žádný |
| Správce technické podpory | : x:. | : x:. | :heavy_check_mark: | : x:. | Žádný | 8 Hour | Žádný |

#### <a name="pim-settings-for-azure-resource-roles"></a>Nastavení PIM pro role prostředků Azure

| Role | Vyžadování MFA | Oznámení | Vyžadovat schválení | Schvalovatel | Doba trvání aktivace | Správce Active | Aktivní vypršení platnosti | Vypršení platnosti oprávnění |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Vlastník kritické předplatných | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Další vlastníky předplatného | 1 hodina | Žádný | neuvedeno | 3 měsíce |
| Správce uživatelských přístupů méně kritické předplatných | :heavy_check_mark: | :heavy_check_mark: | : x:. | Žádný | 1 hodina | Žádný | neuvedeno | 3 měsíce |
| Přispěvatel virtuálních počítačů | : x:. | :heavy_check_mark: | : x:. | Žádný | 3 Hour | Žádný | neuvedeno | 6 měsíců |

Následující tabulka popisuje jednotlivé nastavení.

| Nastavení | Popis |
| --- | --- |
| Role | Název role, kterou definujete nastavení. |
| Vyžadování MFA | Určuje, zda oprávněný uživatel potřebuje provést MFA před aktivací roli.<br/><br/>:heavy_check_mark: **Společnost Microsoft doporučuje** vynucení vícefaktorového ověřování pro všechny role správce, zejména v případě, že mají uživatelé typu Host. |
| Oznámení | Pokud nastavena na hodnotu true, globálního správce, správce privilegovaných rolí, správce zabezpečení v organizaci dostanou oznámení e-mailem když oprávněný uživatel aktivuje roli.<br/><br/>**Poznámka:** Některé organizace nemají e-mailovou adresu, vázané na svých účtů správce, chcete-li získat tyto e-mailová oznámení, měli byste nastavit alternativní e-mailovou adresu, takže správci budou dostávat tyto e-maily. |
| Lístek incidentu | Určuje, zda oprávněný uživatel potřebuje k zaznamenání číslo lístku incidentu při aktivaci jejich role. Toto nastavení pomáhá organizacím identifikovat každou aktivaci s interní číslo incidentu ke zmírnění aktivace nežádoucí serverům.<br/><br/>:heavy_check_mark: **Společnost Microsoft doporučuje** výhod čísla incidentů a jejich zapojení PIM s váš interní systém. To je užitečné hlavně pro schvalovatelů, kteří potřebují kontext pro aktivaci. |
| Vyžadovat schválení | Určuje, zda oprávněný uživatel potřebuje získat schválení aktivaci této role.<br/><br/>:heavy_check_mark: **Společnost Microsoft doporučuje** můžete nastavit schválení pro role s nejvíce oprávnění. Na základě způsobů využití u všech zákazníků PIM, globální správce, Správce uživatelů, správce Exchange, správce zabezpečení a správce hesel je nejběžnější role s instalačním programem schválení. |
| Schvalovatel | Pokud se vyžaduje k aktivaci oprávněných rolí seznam uživatelů, kteří by měla schválit žádost o schválení. Ve výchozím nastavení nastaví PIM schvalovatel být všichni uživatelé, kteří jsou správce privilegovaných rolí, ať už jsou trvalé nebo oprávněné.<br/><br/>**Poznámka:** Pokud je uživatel i oprávněné pro roli Azure AD a schvalovatele role, nebudou moct schvalovat sami.<br/><br/>:heavy_check_mark: **Společnost Microsoft doporučuje** , abyste zvolili schvalovatele, které chcete být těmi, kdo jsou nejvíc informovanosti ohledně konkrétní roli a jeho časté uživatelům, nikoli jako globální správce. |
| Doba trvání aktivace | Dlouhá doba, kterou uživatel aktivuje se v roli předtím, než vyprší platnost. |
| Trvalého správce | Seznam uživatelů, kteří se budou pro roli správce je trvalý (nikdy muset aktivovat).<br/><br/>:heavy_check_mark: **Společnost Microsoft doporučuje** mít žádný stávající správce u všech rolí s výjimkou globální správci. Přečtěte si více o řezu kdo je třeba oprávnění a trvale aktivní části tohoto plánu, která má být. |
| Správce Active | Správce active pro prostředky Azure, je seznam uživatelů, kteří se už nikdy nemusíte použít roli aktivovat. To se označuje jako správce je trvalý stejně jako v Azure AD role vzhledem k tomu, že můžete nastavit dobu vypršení platnosti pro Pokud uživatel ztratí této role. |
| Aktivní vypršení platnosti | Po dokončení konfigurace toto časové období vypršení platnosti aktivní přiřazení role pro role prostředků Azure. Můžete si vybrat z 15 dnů, 1 měsíc, 3 měsíce, 6 měsíců, 1 rok nebo trvale aktivní. |
| Vypršení platnosti oprávnění | Po dokončení konfigurace toto časové období vypršení platnosti přiřazení oprávněné role pro role prostředků Azure. Můžete si vybrat z 15 dnů, 1 měsíc, 3 měsíce, 6 měsíců, 1 rok nebo trvalá způsobilost. |

## <a name="step-3-implement-your-solution"></a>Krok 3. Implementovat řešení

Základ pro důkladném plánování se základ, na kterém nasadíte aplikace s Azure Active Directory úspěšně.  Poskytuje inteligentní zabezpečení a integraci, která zjednodušuje registrace při současném snížení času pro úspěšné nasazení.  Tato kombinace se zajistí, že je aplikace integrovaná s lehkostí a elegancí přičemž současně zmírňuje časové prodlevy pro koncové uživatele.

### <a name="identify-test-users"></a>Identifikace testovacích uživatelů

V této části použijte k identifikaci sady uživatelé a skupiny uživatelů k provedení ověření. Na základě nastavení, které jste vybrali v části věnované plánování, identifikujte, které chcete testovat pro každou roli uživatele.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** provedete vlastníci služby každé role Azure AD jako testovací uživatele, takže se můžete seznámit se s procesem a stát interní advocator pro zavedení.

V této tabulce identifikace testovacích uživatelů, které ověří, zda je funkční nastavení pro každou roli.

| Název role | Testování uživatelů |
| --- | --- |
| &lt;Název role&gt; | &lt;Uživateli a otestovat role&gt; |
| &lt;Název role&gt; | &lt;Uživateli a otestovat role&gt; |

### <a name="test-implementation"></a>Provádění testů

Teď, když jste identifikovali testovacích uživatelů, pomocí tohoto kroku můžete konfigurovat PIM pro testovací uživatele. Pokud chce vaše organizace pracovní postup PIM začlenit do vlastní interní aplikace namísto použití uživatelského rozhraní PIM na portálu Azure Portal, všechny operace v PIM jsou také podporovány prostřednictvím našich [graph API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-pim-for-azure-ad-roles"></a>Konfigurovat PIM pro role Azure AD

1. [Konfigurace nastavení role adresáře Azure AD](pim-how-to-change-default-settings.md) podle jste naplánovali.

1. Přejděte do **role Azure AD**, klikněte na tlačítko **role**a potom vyberte roli, kterou jste právě nakonfigurovali.

1. Pro skupinu testovacích uživatelů pokud jsou již správce je trvalý, můžete vytvořit je vhodné tak, že vyhledáte a jejich konverze z trvalé na oprávněné. po kliknutí na tři tečky na jejich řádku. Pokud nemají přiřazení rolí, ale můžete [vytvořit nové přiřazení oprávněné](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Opakujte kroky 1-3 pro všechny role, kterou chcete testovat.

1. Jakmile nastavíte testovacích uživatelů, byste měli zaslat odkaz Jak [aktivovat svoje role Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-pim-for-azure-resource-roles"></a>Konfigurovat PIM pro role prostředků Azure

1. [Konfigurace nastavení role prostředků Azure](pim-resource-roles-configure-role-settings.md) pro roli v předplatném nebo prostředek, který chcete testovat.

1. Přejděte do **prostředky Azure** u tohoto předplatného a klikněte na **role**, vyberte roli, kterou jste právě nakonfigurovali.

1. Pro skupinu testovacích uživatelů, pokud je již aktivní správce, můžete vytvořit je oprávnění tak, že je a [aktualizovat přiřazení role](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Pokud nemají roli, ale můžete [přiřazení nové role](pim-resource-roles-assign-roles.md#assign-a-role).

1. Opakujte kroky 1-3 pro všechny role, kterou chcete testovat.

1. Jakmile nastavíte testovacích uživatelů, byste měli zaslat odkaz Jak [aktivovat svoje role prostředků Azure](pim-resource-roles-activate-your-roles.md).

Chcete-li ověřit, jestli všechny konfigurace nastaveny pro role funguje správně, měli byste použít tuto fázi. Pomocí následující tabulky dokumentu testy. Tuto fázi byste měli použít také k optimalizaci komunikaci s ovlivnění uživatelé.

| Role | Očekávané chování při aktivaci | Skutečné výsledky |
| --- | --- | --- |
| Globální správce | (1) vyžadovat vícefaktorové ověřování<br/>(2) vyžadují schválení<br/>(3) schvalovatel obdrží oznámení a může schválit<br/>(4) role vyprší po zadaném časovém |  |
| Vlastník předplatného *X* | (1) vyžadovat vícefaktorové ověřování<br/>(2) platnost vyprší po nakonfigurované časové období oprávněného přiřazení |  |

### <a name="communicate-pim-to-affected-stakeholders"></a>Ovlivněné zúčastněným stranám komunikovat PIM

Nasazení PIM představí další kroky pro privilegované role uživatele. I když PIM výrazně snižuje problémy se zabezpečením související s privilegované identity, změnu potřeba efektivně předávat před nasazením celého tenanta. V závislosti na počtu ovlivněných správci organizace často rozhodnout, jestli k vytvoření interní dokumentu, video nebo e-mailu o změnu. Často součástí sdělení tohoto typu zahrnují:

- Co je PIM
- Jaké jsou výhody pro organizaci
- Kdo bude mít vliv
- Když PIM se začne distribuovat
- Jaké další kroky se bude vyžadovat uživatelům aktivovat svoje role
    - By měl odeslání odkazů na dokumentaci:
    - [Aktivovat role Azure AD](pim-how-to-activate-role.md)
    - [Aktivovat role prostředků Azure](pim-resource-roles-activate-your-roles.md)
- Kontaktní informace nebo odkaz na technickou podporu pro všechny problémy související s PIM

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** můžete nastavit čas, který nabízí váš tým technické podpory a podpory pro provede pracovní postup PIM (Pokud vaše organizace má interní podpory IT týmu). Jim poskytnout příslušné dokumentace, jakož i vaše kontaktní informace.

### <a name="move-to-production"></a>Přejít do produkčního prostředí

Po testování je kompletní a úspěšné, přesuňte opakováním všechny kroky v testovací fáze pro všechny uživatele každou roli, kterou jste definovali v konfiguraci PIM PIM do produkčního prostředí. PIM pro role Azure AD organizace často testovat a zaveďte PIM pro globální správce před testováním a zavádí PIM pro jiné role. Mezitím pro prostředky Azure, které organizace obvykle testování a zavedení PIM jedno předplatné Azure, najednou.

### <a name="in-the-case-a-rollback-is-needed"></a>V případě, je potřeba vrácení zpět.

Pokud pro práci podle potřeby v produkčním prostředí se nepodařilo PIM, následující kroky odvolání vám můžou pomoct s vrátit zpět do známého funkčního stavu před nastavením PIM:

#### <a name="azure-ad-roles"></a>Role Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Otevřít **Azure AD Privileged Identity Management**.
1. Klikněte na tlačítko **role Azure AD** a potom klikněte na tlačítko **role**.
1. Pro každou roli, kterou jste nakonfigurovali, klikněte na tlačítko se třemi tečkami (**...** ) pro všechny uživatele s oprávněného přiřazení.
1. Klikněte na tlačítko **trvalé** možnost trvalé přiřazení role.

#### <a name="azure-resource-roles"></a>Role prostředků Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Otevřít **Azure AD Privileged Identity Management**.
1. Klikněte na tlačítko **prostředky Azure** a potom klikněte na předplatné nebo prostředek, které chcete vrátit zpět.
1. Klikněte na tlačítko **role**.
1. Pro každou roli, kterou jste nakonfigurovali, klikněte na tlačítko se třemi tečkami (**...** ) pro všechny uživatele s oprávněného přiřazení.
1. Klikněte na tlačítko **trvalé** možnost trvalé přiřazení role.

## <a name="step-4-next-steps-after-deploying-pim"></a>Krok 4. Další kroky po nasazení PIM

Úspěšné nasazení PIM v produkčním prostředí je významný krok vpřed z hlediska zabezpečení vaší organizace v privilegovaných identit. S nasazením PIM obsahuje další funkce PIM, které byste měli použít pro zabezpečení a dodržování předpisů.

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>Použití PIM upozornění k zabezpečení privilegovaného přístupu

PIM pro integrované funkce výstrah k lepší ochrana by měla využívat vašeho tenanta. Další informace najdete v tématu [výstrahy zabezpečení](pim-how-to-configure-security-alerts.md#security-alerts). Tyto výstrahy obsahují: Správci nepoužívají privilegované role, role se přiřazení mimo PIM, role se aktivuje příliš často a provádění dalších akcí. Plně ochrany vaší organizace, by měl pravidelně projít seznam výstrah a opravte tyto problémy. Můžete zobrazit a vyřešit upozornění tímto způsobem:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Otevřít **Azure AD Privileged Identity Management**.
1. Klikněte na tlačítko **role Azure AD** a potom klikněte na tlačítko **výstrahy**.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** řešit všechny výstrahy s vysokou závažností okamžitě označeny. Pro střední nebo nízké závažnost výstrahy by měl Udržujte si přehled a provést změny, pokud se domníváte, že existuje ohrožení zabezpečení.

Pokud některý z konkrétní výstrahy nejsou užitečné nebo se nedá použít pro vaši organizaci, můžete vždy zavřít upozornění na stránky s upozorněními. Můžete se vždycky vrátit tuto propuštění později na stránce nastavení služby Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Upravit opakování kontroly přístupu tak, aby pravidelně auditujte privileged identity ve vaší organizaci

Kontroly přístupu jsou nejlepší způsob, kde můžete pokládat uživatelé přiřazené privilegované role nebo konkrétní kontrolory, zda každý uživatel musí privilegovaných identit. Kontroly přístupu se skvěle hodí, pokud chcete zmenšit prostor pro napadení a zůstane kompatibilní. Další informace o spuštění kontroly přístupu najdete v tématu [kontrol přístupu Azure AD role](pim-how-to-start-security-review.md) a [kontrol přístupu pro role prostředků Azure](pim-resource-roles-start-access-review.md). Pro některé organizace provádí kontroly pravidelné přístupu je potřeba zajistěte dodržování předpisů zákonům a předpisům při pro ostatní uživatele, kontroly přístupu je nejlepší způsob, jak vynutit zabezpečení nejnižší úrovně oprávnění v rámci vaší organizace.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** nastavíte čtvrtletně kontroly přístupu pro službu Azure AD a role prostředků Azure.

Ve většině případů je kontrolor pro role Azure AD samotných uživatelů, zatímco kontrolor pro role prostředků Azure je vlastníkem předplatného, který se nachází v roli. Je však často případ, ve kterém společnosti privilegovaný účty, které nejsou spojeny s konkrétní osoba e-mailovou adresu. V těchto případech se nikdo přečte kontrol a přístup.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** přidat sekundární e-mailovou adresu pro všechny účty s přiřazení privilegovaných rolí, které nejsou připojeny pravidelně checked e-mailovou adresu

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Využijte naplno protokolů auditu pro zlepšení zabezpečení a dodržování předpisů

Protokolu auditu je místo, kde byly vždycky aktuální a musí splňovat předpisy. PIM ukládá 30 dní historie všechna firemní historie uvnitř jeho včetně protokolu auditu:

- Aktivace nebo deaktivace oprávněné role
- Aktivity přiřazení role uvnitř i vně PIM
- Změny v nastavení role
- Požadavek nebo schválit nebo zamítnout aktivity pro aktivaci role s instalačním programem schválení
- Aktualizace výstrahy

Jsou přístupné protokoly auditu, pokud jste globálním správcem nebo správcem privilegovaných rolí. Další informace najdete v tématu [historie pro role Azure AD auditu](pim-how-to-use-audit-log.md) a [historie pro role prostředků Azure auditu](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** mít aspoň jednoho správce přečíst všechny auditovat události každý týden a export událostí auditu každý měsíc.

Pokud chcete automaticky ukládat události auditu pro delší časové období, se automaticky synchronizuje protokolu auditu v PIM [protokoly auditu Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** nastavíte [monitorování protokolů Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) k archivaci událostech auditování v účtu úložiště Azure pro potřeby zabezpečení a dodržování předpisů.
