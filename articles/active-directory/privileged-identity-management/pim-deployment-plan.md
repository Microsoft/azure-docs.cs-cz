---
title: Nasazení správy privilegovaných identit (PIM) – Azure AD | Dokumenty společnosti Microsoft
description: Popisuje, jak naplánovat nasazení Azure AD Privilegované správy identit (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/04/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8c77b3454026aa309d979bd938674e7c3ae7b6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025992"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Nasazení správy privilegovaných identit Azure AD (PIM)

Tento podrobný průvodce popisuje, jak naplánovat nasazení privilegované správy identit (PIM) ve vaší organizaci Azure Active Directory (Azure AD).

> [!TIP]
> V tomto článku uvidíte položky označené jako:
> 
> :heavy_check_mark: **Microsoft doporučuje**
> 
> Jedná se o obecná doporučení a měli byste je implementovat pouze v případě, že se vztahují na vaše konkrétní podnikové potřeby.

## <a name="learn-about-privileged-identity-management"></a>Další informace o správě privilegovaných identit

Správa privilegovaných identit Azure AD vám pomůže spravovat privilegované role správy napříč Azure AD, prostředky Azure a dalšími službami Microsoft Online Services. Ve světě, kde jsou privilegované identity přiřazovány a zapomenuty, poskytuje správa privilegovaných identit řešení, jako je přístup za chvíli, pracovní postupy schvalování žádostí a plně integrované kontroly přístupu, takže můžete identifikovat, odhalit a zabránit škodlivým privilegovaných rolí v reálném čase. Nasazení správy privilegovaných identit ke správě privilegovaných rolí v celé organizaci výrazně sníží riziko a zároveň zobrazí cenné poznatky o aktivitách vašich privilegovaných rolí.

### <a name="business-value-of-privileged-identity-management"></a>Obchodní hodnota správy privilegovaných identit

**Správa rizik** – Zabezpečte svou organizaci vynucením principu [přístupu k nejnižším oprávněním](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) a přístupu za čase. Minimalizací počtu trvalých přiřazení uživatelů na privilegované role a vynucením schválení a vícefaktorové pomoci pro zvýšení oprávnění můžete výrazně snížit bezpečnostní rizika spojená s privilegovaným přístupem ve vaší organizaci. Vynucení nejnižších oprávnění a přístupu za čase vám také umožní zobrazit historii přístupu k privilegovaným rolím a sledovat problémy se zabezpečením.

**Adresa dodržování předpisů a zásad správného řízení** – nasazení správy privilegovaných identit vytváří prostředí pro každodenní zásady správného řízení identit. Zvýšení úrovně privilegovaných identit za chvíli poskytuje privilegované správě identit způsob, jak sledovat aktivity privilegovaného přístupu ve vaší organizaci. Budete také moci zobrazit a přijímat oznámení pro všechna přiřazení trvalých a způsobilých rolí v rámci organizace. Prostřednictvím kontroly přístupu můžete pravidelně auditovat a odebírat nepotřebné privilegované identity a zajistit, aby vaše organizace splňovala nejpřísnější standardy identity, přístupu a zabezpečení.

**Snižte náklady** – Snižte náklady odstraněním neefektivity, lidských chyb a problémů se zabezpečením správným nasazením správy privilegovaných identit. Čistým výsledkem je snížení kybernetických trestných činů spojených s privilegovanými identitami, z nichž se nákladná a obtížně se zotavuje. Správa privilegovaných identit také pomůže vaší organizaci snížit náklady spojené s auditováním přístupových informací, pokud jde o dodržování předpisů a standardů.

Další informace naleznete v tématu [Co je správa privilegovaných identit Azure AD?](pim-configure.md).

### <a name="licensing-requirements"></a>Licenční požadavky

Chcete-li používat službu Privileged Identity Management, musí mít adresář jednu z následujících placených nebo zkušebních licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Další informace naleznete v [tématu Licenční požadavky na používání správy privilegovaných identit](subscription-requirements.md).

### <a name="key-terminology"></a>Klíčová terminologie

| Termín nebo koncept | Popis |
| --- | --- |
| Způsobilé | Přiřazení role, které vyžaduje, aby uživatel provedl jednu nebo více akcí k použití role. Pokud uživatel byl učiněn nárok na roli, to znamená, že mohou aktivovat roli, když potřebují provádět privilegované úkoly. Neexistuje žádný rozdíl v přístupu k někomu, kdo má trvalé versus způsobilé přiřazení role. Jediný rozdíl je v tom, že někteří lidé nepotřebují, aby přístup po celou dobu. |
| aktivovat | Proces provádění jedné nebo více akcí za účelem použití role, pro kterou má uživatel nárok. Akce mohou zahrnovat provedení kontroly vícefaktorového ověřování (MFA), poskytnutí obchodního odůvodnění nebo vyžádání schválení od určených schvalovatelů. |
| přístup just-in-time (JIT) | Model, ve kterém uživatelé získají dočasná oprávnění k provádění privilegovaných úloh, což brání škodlivým nebo neoprávněným uživatelům v získání přístupu po vypršení platnosti oprávnění. Přístup je udělen pouze v případě, že jej uživatelé potřebují. |
| zásada přístupu k nejnižším oprávněním | Doporučený postup zabezpečení, ve kterém je každému uživateli poskytnuta pouze minimální oprávnění potřebná k provedení úkolů, k jejichž provádění je oprávněn. Tento postup minimalizuje počet globálních správců a místo toho používá konkrétní role správce pro určité scénáře. |

Další informace naleznete v [tématu Terminology](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Přehled na vysoké úrovni o tom, jak funguje správa privilegovaných identit

1. Správa privilegovaných identit je nastavena tak, aby uživatelé měli nárok na privilegované role.
1. Pokud oprávněný uživatel potřebuje použít svou privilegovanou roli, aktivuje roli ve správě privilegovaných identit.
1. V závislosti na nastavení správy privilegovaných identit nakonfigurovaných pro roli musí uživatel provést určité kroky (například provedení vícefaktorového ověřování, získání schválení nebo určení důvodu).)
1. Jakmile uživatel úspěšně aktivuje svou roli, získá roli pro předem nakonfigurované časové období.
1. Správci mohou zobrazit historii všech aktivit správy privilegovaných identit v protokolu auditu. Mohou také dále zabezpečit své organizace Azure AD a splňovat požadavky na dodržování předpisů pomocí funkcí správy privilegovaných identit, jako jsou kontroly přístupu a výstrahy.

Další informace naleznete v tématu [Co je správa privilegovaných identit Azure AD?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Role, které lze spravovat pomocí správy privilegovaných identit

**Role Azure AD** – všechny tyto role jsou ve službě Azure Active Directory (například globální správce, správce exchange a správce zabezpečení). Další informace o rolích a jejich funkcích najdete v [oprávněních rolí správce ve službě Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Nápovědu k určení rolí, které mají být přiřazeny správcům, naleznete v [tématu Nejméně privilegované role podle úkolů](../users-groups-roles/roles-delegate-by-task.md).

**Role prostředků Azure** – tyto role jsou propojené s prostředky Azure, skupinou prostředků, předplatným nebo skupinou pro správu. Správa privilegovaných identit poskytuje přístup za čas k předdefinovaným rolím, jako je vlastník, správce přístupu uživatelů a přispěvatel, a [také k vlastním rolím](../../role-based-access-control/custom-roles.md). Další informace o rolích prostředků Azure najdete v [tématu řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/overview.md).

Další informace naleznete [v tématu Role, které nelze spravovat ve správě privilegovaných identit](pim-roles.md).

## <a name="plan-your-deployment"></a>Plánování nasazení

Tato část se zaměřuje na to, co je třeba udělat před nasazením správy privilegovaných identit ve vaší organizaci. Je nezbytné postupovat podle pokynů a porozumět konceptům v této části, protože vás povedou k vytvoření nejlepšího plánu přizpůsobeného privilegovaným identitám vaší organizace.

### <a name="identify-your-stakeholders"></a>Identifikujte své zúčastněné strany

Následující část vám pomůže identifikovat všechny zúčastněné strany, které jsou zapojeny do projektu a je třeba se odhlásit, zkontrolovat nebo zůstat informováni. Obsahuje samostatné tabulky pro nasazení privilegované správy identit pro role Azure AD a privilegované správy identit pro role prostředků Azure. Podle potřeby vaší organizace přidejte zúčastněné strany do následující tabulky.

- SO = Odhlásit se na tomto projektu
- R = Zkontrolujte tento projekt a poskytněte vstup
- I = Informováni o tomto projektu

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Zúčastněné strany: Privilegované správy identit pro role Azure AD

| Name (Název) | Role | Akce |
| --- | --- | --- |
| Jméno a e-mail | **Architekt identity nebo globální správce Azure**<br/>Zástupce týmu pro správu identit, který má na starosti definování způsobu, jakým je tato změna zarovnána s hlavní infrastrukturou správy identit ve vaší organizaci. | SO/R/I |
| Jméno a e-mail | **Majitel služby / Line manager**<br/>Zástupce vlastníků IT služby nebo skupiny služeb. Jsou klíčem k rozhodování a pomáhá zavést privilegovanou správu identit pro svůj tým. | SO/R/I |
| Jméno a e-mail | **Vlastník zabezpečení**<br/>Zástupce týmu zabezpečení, který může podepsat, že plán splňuje požadavky na zabezpečení vaší organizace. | SO/R |
| Jméno a e-mail | **Manažer IT podpory / Helpdesk**<br/>Zástupce organizace podpory IT, který může poskytnout informace o podpoře této změny z hlediska helpdesku. | R/I |
| Jméno a e-mail pro pilotní uživatele | **Uživatelé privilegovaných rolí**<br/>Skupina uživatelů, pro které je implementována správa privilegovaných identit. Budou potřebovat vědět, jak aktivovat své role po implementaci správy privilegovaných identit. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Zúčastněné strany: Privilegovaná správa identit pro role prostředků Azure

| Name (Název) | Role | Akce |
| --- | --- | --- |
| Jméno a e-mail | **Předplatné / vlastník prostředku**<br/>Zástupce vlastníků IT každého předplatného nebo prostředku, pro který chcete nasadit správu privilegovaných identit | SO/R/I |
| Jméno a e-mail | **Vlastník zabezpečení**<br/>Zástupce týmu zabezpečení, který může podepsat, že plán splňuje požadavky na zabezpečení vaší organizace. | SO/R |
| Jméno a e-mail | **Manažer IT podpory / Helpdesk**<br/>Zástupce organizace podpory IT, který může poskytnout informace o podpoře této změny z hlediska helpdesku. | R/I |
| Jméno a e-mail pro pilotní uživatele | **Uživatelé rolí RBAC**<br/>Skupina uživatelů, pro které je implementována správa privilegovaných identit. Budou potřebovat vědět, jak aktivovat své role po implementaci správy privilegovaných identit. | I |

### <a name="enable-privileged-identity-management"></a>Povolit správu privilegovaných identit

V rámci procesu plánování musíte nejprve souhlasit se správou privilegovaných identit a povolit ji pomocí našeho [článku o řešení privilegovaných identit.](pim-getting-started.md) Povolení správy privilegovaných identit umožňuje přístup k některým funkcím, které jsou speciálně navrženy tak, aby vám pomohly s nasazením.

Pokud je vaším cílem nasadit privilegovanou správu identit pro prostředky Azure, měli byste sledovat naše [zjišťovací prostředky Azure ke správě v článku Správa privilegovaných identit.](pim-resource-roles-discover-resources.md) Pouze vlastníci předplatných a skupin pro správu mohou tyto prostředky zjišťovat a zařazovat do správy privilegovaných identit. Po jeho zahrnutí je funkce PIM k dispozici pro vlastníky na všech úrovních, včetně skupiny pro správu, předplatného, skupiny prostředků a prostředků. Pokud jste globální správce, který se pokouší nasadit správu privilegovaných identit pro vaše prostředky Azure, můžete [zvýšit přístup ke správě všech předplatných Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) a poskytnout vám přístup ke všem prostředkům Azure v adresáři pro zjišťování. Doporučujeme však, abyste před správou jejich prostředků pomocí správy privilegovaných identit získali souhlas od každého z vlastníků předplatného.

### <a name="enforce-principle-of-least-privilege"></a>Vynucovat zásadu nejnižšího oprávnění

Je důležité se ujistit, že jste vynutili princip nejnižších oprávnění ve vaší organizaci pro vaše role prostředků Azure a pro vaše role prostředků Azure.

#### <a name="azure-ad-roles"></a>Role Azure AD

Pro role Azure AD je běžné, že organizace přiřadí roli globálního správce významnému počtu správců, když většina správců potřebuje jenom jednu nebo dvě konkrétní role správce. Přiřazení privilegovaných rolí mají také tendenci zůstat nespravovaná.

> [!NOTE]
> Společné úskalí v delegování rolí:
>
> - Správce, který má na starosti server Exchange, má roli globálního správce, i když k provádění každodenní úlohy potřebuje pouze roli správce serveru Exchange.
> - Role globálního správce je přiřazena správci sady Office, protože správce potřebuje role správce zabezpečení i služby SharePoint a je snazší delegovat pouze jednu roli.
> - Správci byla přiřazena role správce zabezpečení k provedení úlohy již dávno, ale nikdy nebyl odebrán.

Postupujte podle těchto kroků k vynucení principu nejnižší oprávnění pro vaše role Azure AD.

1. Seznamte se s rozlišovací schopnostrolí čtením a pochopením [dostupných rolí správce služby Azure AD](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Vy a váš tým byste také měli odkazovat na [role správce podle úlohy identity ve službě Azure AD](../users-groups-roles/roles-delegate-by-task.md), což vysvětluje nejméně privilegovanou roli pro konkrétní úkoly.

1. Seznam, který má ve vaší organizaci privilegované role. Pomocí průvodce [správou privilegovaných identit](pim-security-wizard.md#run-the-wizard) se můžete dostat na následující stránku.

    ![Zjištění podokna privilegovaných rolí zobrazujícího, kdo má privilegované role](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Pro všechny globální správce ve vaší organizaci zjistěte, proč roli potřebují. Na základě přečtení předchozí dokumentace, pokud úlohu osoby lze provést pomocí jedné nebo více podrobných rolí správce, měli byste je odebrat z role globálního správce a odpovídajícím způsobem provést přiřazení ve službě Azure Active Directory (Jako odkaz: Společnost Microsoft má v současné době pouze asi 10 správců s rolí globálního správce. Další informace najdete v [tom, jak společnost Microsoft používá správu privilegovaných identit](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Pro všechny ostatní role Azure AD zkontrolujte seznam přiřazení, identifikujte správce, kteří už roli nepotřebují, a odeberte je z jejich přiřazení.

Chcete-li automatizovat poslední dva kroky, můžete použít kontroly přístupu v privilegované správě identit. Postupujte podle kroků při [spuštění kontroly přístupu pro role Azure AD ve službě Privileged Identity Management](pim-how-to-start-security-review.md), můžete nastavit kontrolu přístupu pro každou roli Azure AD, která má jeden nebo více členů.

![Vytvoření podokna kontroly přístupu pro role Azure AD](./media/pim-deployment-plan/create-access-review.png)

Recenzenti byste měli nastavit na **Členy (vlastní)**. Tím odešlete e-mail všem členům v roli, aby se potvrdilo, zda potřebují přístup. Měli byste také **zapnout Vyžadovat důvod schválení** v upřesňujícím nastavení, aby uživatelé mohli uvést, proč roli potřebují. Na základě těchto informací budete moci odebrat uživatele z nepotřebných rolí a delegovat podrobnější role správce v případě globálních správců.

Recenze přístupu spoléhají na e-maily, které uživatele upozorňují, aby zkontrolovali jejich přístup k rolím. Pokud máte privilegované účty, které nemají propojené e-maily, nezapomeňte na tyto účty vyplnit sekundární pole e-mailu. Další informace naleznete [v tématu proxyAddresses atribut ve službě Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Role prostředků Azure

U předplatných a prostředků Azure můžete nastavit podobný proces kontroly accessu ke kontrole rolí v každém předplatném nebo prostředku. Cílem tohoto procesu je minimalizovat přiřazení vlastníka a správce přístupu uživatelů připojená ke každému předplatnému nebo prostředku a také odebrat nepotřebná přiřazení. Organizace však často delegují tyto úkoly na vlastníka každého předplatného nebo prostředku, protože mají lepší pochopení konkrétních rolí (zejména vlastních rolí).

Pokud jste správce IT s rolí globálního správce, který se pokouší nasadit prostředky privileged identity management pro Azure ve vaší organizaci, můžete [zvýšit přístup ke správě všech předplatných Azure,](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) abyste získali přístup ke každému předplatnému. Potom můžete najít každého vlastníka předplatného a pracovat s nimi odebrat nepotřebná přiřazení a minimalizovat přiřazení role vlastníka.

Uživatelé s rolí Vlastník pro předplatné Azure můžete také využít [kontroly přístupu pro prostředky Azure](pim-resource-roles-start-access-review.md) k auditování a odebrání nepotřebných přiřazení rolí podobných procesu popsanému dříve pro role Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Rozhodněte, která přiřazení rolí by měla být chráněna správou privilegovaných identit

Po vyčištění přiřazení privilegovaných rolí ve vaší organizaci se budete muset rozhodnout, které role chcete chránit pomocí správy privilegovaných identit.

Pokud je role chráněna správou privilegovaných identit, oprávnění uživatelé, kteří jsou jí přiřazeni, musí zvýšit úroveň, aby mohli používat oprávnění udělená rolí. Proces zvýšení může také zahrnovat získání schválení, provedení vícefaktorového ověřování a/nebo poskytnutí důvodu, proč se aktivují. Správa privilegovaných identit můžete také sledovat zvýšení prostřednictvím oznámení a privileged Identity Management a protokoly událostí auditu Azure AD.

Výběr rolí, které chcete chránit pomocí správy privilegovaných identit, může být obtížný a pro každou organizaci se bude lišit. Tato část obsahuje naše rady pro osvědčené postupy pro role prostředků Azure AD a Azure.

#### <a name="azure-ad-roles"></a>Role Azure AD

Je důležité stanovit prioritu ochrany rolí Azure AD, které mají největší počet oprávnění. Na základě vzorců využití mezi všemi zákazníky správy privilegovaných identit je 10 hlavních rolí Azure AD spravovaných privilegovanou správou identit:

1. Globální správce
1. Správce zabezpečení
1. Správce uživatele
1. Správce Exchange
1. Správce SharePointu
1. Správce Intune
1. Čtenář zabezpečení
1. Správce služeb
1. Správce fakturace
1. Správce Skypu pro firmy

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** spravovat všechny globální správce a správce zabezpečení pomocí správy privilegovaných identit jako první krok, protože jsou to ti, kteří mohou způsobit největší škodu, když jsou ohroženi.

Je důležité zvážit, jaká data a oprávnění jsou pro vaši organizaci nejcitlivější. Některé organizace mohou například chtít chránit svou roli správce Power BI nebo roli správce týmů pomocí správy privilegovaných identit, protože mají možnost přistupovat k datům nebo měnit základní pracovní postupy.

Pokud jsou přiřazeny nějaké role s přiřazenými uživateli typu Host, jsou obzvláště zranitelní vůči útoku.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** spravovat všechny role s uživateli typu Host pomocí správy privilegovaných identit ke snížení rizika spojeného s kompromitovanými uživatelskými účty typu Host.

Role čtečky, jako je čtečka adresářů, čtečka zpráv a čtečka zabezpečení, jsou někdy považovány za méně důležité ve srovnání s jinými rolemi, protože nemají oprávnění k zápisu. Někteří zákazníci však také tyto role chrání, protože útočníci, kteří získali přístup k těmto účtům, mohou číst citlivá data, například osobní údaje. Měli byste to vzít v úvahu při rozhodování, zda je třeba spravovat role čtenáře ve vaší organizaci pomocí správy privilegovaných identit.

#### <a name="azure-resource-roles"></a>Role prostředků Azure

Při rozhodování o tom, která přiřazení rolí by měla být spravována pomocí prostředků Privileged Identity Management for Azure, musíte nejprve identifikovat předplatná nebo prostředky, které jsou pro vaši organizaci nejdůležitější. Příklady těchto předplatných/prostředků jsou:

- Prostředky, které hostují nejcitlivější data
- Prostředky, na kterých závisí základní aplikace orientované na zákazníky

Pokud jste globální správce, který má potíže s rozhodováním o tom, která předplatná nebo prostředky jsou nejdůležitější, měli byste kontaktovat vlastníky předplatného ve vaší organizaci a získat seznam prostředků spravovaných jednotlivými předplatnými. Potom byste měli pracovat s vlastníky předplatného seskupit prostředky na základě úrovně závažnosti v případě, že jsou ohroženy (nízká, střední, vysoká). Měli byste určit prioritu správy prostředků pomocí správy privilegovaných identit na základě této úrovně závažnosti.

> [!TIP]
> :heavy_check_mark: Společnost Microsoft doporučuje spolupracovat s **vlastníky** předplatného nebo prostředků kritických služeb a nastavit pracovní postup správy privilegovaných identit pro všechny role uvnitř citlivých předplatných nebo prostředků.

Privilegovaná správa identit pro prostředky Azure podporuje časově vázané účty služeb. Účty služeb byste měli považovat za přesně stejné jako s běžným uživatelským účtem.

U předplatných nebo prostředků, které nejsou tak důležité, nebudete muset nastavit správu privilegovaných identit pro všechny role. Role vlastníka a správce přístupu uživatelů byste však měli stále chránit pomocí správy privilegovaných identit.

> [!TIP]
> :heavy_check_mark: Společnost Microsoft doporučuje spravovat role **vlastníka** a role správce přístupu uživatelů všech předplatných/prostředků pomocí správy privilegovaných identit.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Rozhodnout, která přiřazení rolí by měla být trvalá nebo způsobilá

Jakmile se rozhodnete pro seznam rolí, které mají být spravovány službou Správa privilegovaných identit, musíte se rozhodnout, kteří uživatelé by měli získat způsobilou roli oproti trvale aktivní roli. Trvale aktivní role jsou normální role přiřazené prostřednictvím prostředků Azure Active Directory a Azure, zatímco způsobilé role lze přiřadit jenom ve správě privilegovaných identit.

> [!TIP]
> :heavy_check_mark: Microsoft doporučuje, abyste měli nulová trvale aktivní přiřazení pro role Azure AD i pro role prostředků Azure jiné než doporučené [dva účty nouzového přístupu pro rozbití skla](../users-groups-roles/directory-emergency-access.md), které by **měly** mít stálou roli globálního správce.

I když doporučujeme nulové stálého správce, je někdy obtížné pro organizace k dosažení tohoto hned. Při tomto rozhodování je třeba zvážit tyto věci:

- Frekvence zvýšení – Pokud uživatel potřebuje pouze privilegované přiřazení jednou, neměl by mít trvalé přiřazení. Na druhou stranu, pokud uživatel potřebuje roli pro své každodenní úlohy a pomocí privilegované správy identit by výrazně snížit jejich produktivitu, mohou být považovány za pro trvalou roli.
- Případy specifické pro vaši organizaci – Pokud osoba, která je dána způsobilá role je z velmi vzdáleného týmu nebo vysoce postavený výkonný do té míry, že komunikace a prosazování procesu zvýšení je obtížné, mohou být považovány za trvalé role.

> [!TIP]
> :heavy_check_mark: Společnost Microsoft doporučuje nastavit opakované **kontroly** přístupu pro uživatele se stálými přiřazeními rolí (pokud máte nějaké). Další informace o opakované kontrole přístupu naleznete v poslední části tohoto plánu nasazení.

### <a name="draft-your-privileged-identity-management-settings"></a>Nakreslení nastavení správy privilegovaných identit

Před implementací řešení správy privilegovaných identit je vhodné navrhnout nastavení správy privilegovaných identit pro každou privilegovanou roli, kterou vaše organizace používá. Tato část obsahuje některé příklady nastavení správy privilegovaných identit pro konkrétní role (jsou pouze orientační a mohou se lišit pro vaši organizaci). Každé z těchto nastavení je podrobně vysvětleno doporučeními společnosti Microsoft po tabulkách.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Nastavení správy privilegovaných identit pro role Azure AD

| Role | Vyžadování MFA | Oznámení | Lístek incidentu | Vyžadovat schválení | Schvalovatel | Doba trvání aktivace | Stálý správce |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Globální správce | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Další globální správci | 1 hodina | Účty nouzového přístupu |
| Správce serveru Exchange | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Žádný | 2 hodiny | Žádný |
| Správce technické podpory | :x: | :x: | :heavy_check_mark: | :x: | Žádný | 8 Hodina | Žádný |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Nastavení správy privilegovaných identit pro role prostředků Azure

| Role | Vyžadování MFA | Oznámení | Vyžadovat schválení | Schvalovatel | Doba trvání aktivace | Aktivní správce | Aktivní vypršení platnosti | Způsobilé vypršení platnosti |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Vlastník kritických předplatných | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Ostatní vlastníci předplatného | 1 hodina | Žádný | neuvedeno | 3 měsíce |
| Správce přístupu uživatelů s méně důležitými předplatnými | :heavy_check_mark: | :heavy_check_mark: | :x: | Žádný | 1 hodina | Žádný | neuvedeno | 3 měsíce |
| Přispěvatel virtuálních počítačů | :x: | :heavy_check_mark: | :x: | Žádný | 3 Hodiny | Žádný | neuvedeno | 6 měsíců |

Následující tabulka popisuje každé nastavení.

| Nastavení | Popis |
| --- | --- |
| Role | Název role, pro kterou definujete nastavení. |
| Vyžadování MFA | Určuje, zda oprávněný uživatel musí před aktivací role provést vícefaktorové ověřování.<br/><br/> :heavy_check_mark: **Společnost Microsoft doporučuje vynutit vícefaktorové** zabezpečení pro všechny role správce, zejména pokud role mají uživatele typu Host. |
| Oznámení | Pokud je nastavena hodnota true, globální správce, správce privilegovaných rolí a správce zabezpečení v organizaci obdrží e-mailové oznámení, když oprávněný uživatel aktivuje roli.<br/><br/>**Poznámka:** Některé organizace nemají e-mailovou adresu svázanou s účty správce, chcete-li získat tato e-mailová oznámení, měli byste jít nastavit alternativní e-mailovou adresu, aby správci obdrželi tyto e-maily. |
| Lístek incidentu | Určuje, zda oprávněný uživatel potřebuje při aktivaci své role zaznamenat číslo lístku incidentu. Toto nastavení pomáhá organizaci identifikovat každou aktivaci s interním číslem incidentu, aby se zmírnily nežádoucí aktivace.<br/><br/> :heavy_check_mark: **Společnost Microsoft doporučuje** využít čísla čísel vstupenek k incidentům a spojit správu privilegovaných identit do interního systému. To je užitečné zejména pro schvalovatele, kteří potřebují kontext pro aktivaci. |
| Vyžadovat schválení | Určuje, zda oprávněný uživatel potřebuje k aktivaci role získat schválení.<br/><br/> :heavy_check_mark: **Společnost Microsoft doporučuje** nastavit schválení rolí s největším i min. Na základě vzorců použití všech zákazníků správy privilegovaných identit jsou nejčastějšími rolemi s nastavením schválení globální správce, správce uživatelů, správce serveru Exchange, správce zabezpečení a správce hesel. |
| Schvalovatel | Pokud je k aktivaci způsobilé role vyžadováno schválení, uveďte osoby, které by měly žádost schválit. Ve výchozím nastavení nastaví správa privilegovaných identit schvalovatele tak, aby byli všichni uživatelé, kteří jsou správcem privilegované role, ať už jsou stálí nebo způsobilí.<br/><br/>**Poznámka:** Pokud uživatel má nárok na roli Azure AD a schvalovatel role, nebude moci schválit sami.<br/><br/> :heavy_check_mark: **Společnost Microsoft doporučuje** zvolit schvalovately jako ty, kteří mají největší znalosti o konkrétní roli a jejích častých uživatelích, nikoli o globálním správci. |
| Doba trvání aktivace | Doba, po kterou bude uživatel v roli aktivován, než vyprší jeho platnost. |
| Stálý správce | Seznam uživatelů, kteří budou stálým správcem role (nikdy nemusí aktivovat).<br/><br/> :heavy_check_mark: **Společnost Microsoft doporučuje,** abyste měli nulového stálého správce pro všechny role s výjimkou globálních správců. Přečtěte si více o tom, kdo by měl být způsobilý a kdo by měl být trvale aktivní část tohoto plánu. |
| Aktivní správce | Pro prostředky Azure je aktivní správce seznam uživatelů, kteří nikdy nebudou muset aktivovat, aby mohli roli používat. To se neoznačuje jako trvalý správce jako v rolích Azure AD, protože můžete nastavit dobu vypršení platnosti, kdy uživatel ztratí tuto roli. |
| Aktivní vypršení platnosti | Platnost aktivnírole pro role prostředků Azure vyprší po tomto nakonfigurovaném časovém období. Můžete si vybrat z 15 dnů, 1 měsíc, 3 měsíce, 6 měsíců, 1 rok nebo trvale aktivní. |
| Způsobilé vypršení platnosti | Platnost způsobilého přiřazení role pro role prostředků Azure vyprší po tomto nakonfigurovaném časovém období. Můžete si vybrat z 15 dnů, 1 měsíc, 3 měsíce, 6 měsíců, 1 rok nebo trvale způsobilých. |

## <a name="implement-your-solution"></a>Implementace řešení

Základem správného plánování je základ, na kterém můžete úspěšně nasadit aplikaci pomocí služby Azure Active Directory.  Poskytuje inteligentní zabezpečení a integraci, která zjednodušuje zapisování a zároveň zkrátí čas pro úspěšná nasazení.  Tato kombinace zajišťuje, že vaše aplikace je integrována s lehkostí a zároveň zmírňuje prostoje pro koncové uživatele.

### <a name="identify-test-users"></a>Identifikace testovacích uživatelů

Tato část slouží k identifikaci sady uživatelů nebo skupin uživatelů k ověření implementace. Na základě nastavení, které jste vybrali v části plánování, určete uživatele, které chcete testovat pro každou roli.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje,** aby vlastníky služeb každé role Azure AD být testovací uživatelé, aby se mohli seznámit s procesem a stát se interním advokatorem pro zavedení.

V této tabulce identifikujte testovací uživatele, kteří ověří, zda nastavení pro každou roli funguje.

| Název role | Testovat uživatele |
| --- | --- |
| &lt;Název role&gt; | &lt;Uživatelé otestovat roli&gt; |
| &lt;Název role&gt; | &lt;Uživatelé otestovat roli&gt; |

### <a name="test-implementation"></a>Implementace testu

Nyní, když jste identifikovali testovací uživatele, použijte tento krok ke konfiguraci správy privilegovaných identit pro testovací uživatele. Pokud vaše organizace chce začlenit pracovní postup správy privilegovaných identit do vlastní interní aplikace namísto použití správy privilegovaných identit na webu Azure Portal, všechny operace v privileged identity management jsou také podporovány prostřednictvím našeho [rozhraní API grafu](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Konfigurace správy privilegovaných identit pro role Azure AD

1. [Nakonfigurujte nastavení role Azure AD](pim-how-to-change-default-settings.md) na základě toho, co jste plánovali.

1. Přejděte na **role Azure AD**, klikněte na **Role**a vyberte roli, kterou jste právě nakonfigurovali.

1. Pro skupinu testovacích uživatelů, pokud jsou již trvalýsprávce, můžete je učinit způsobilými vyhledáním a jejich převedením z trvalých na způsobilé kliknutím na tři tečky na jejich řádku. Pokud ještě přiřazení rolí nemají, můžete [vytvořit nové způsobilé přiřazení](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Opakujte kroky 1-3 pro všechny role, které chcete testovat.

1. Po nastavení testovacích uživatelů byste jim měli poslat odkaz, jak [aktivovat jejich roli Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Konfigurace správy privilegovaných identit pro role prostředků Azure

1. [Nakonfigurujte nastavení role prostředků Azure](pim-resource-roles-configure-role-settings.md) pro roli uvnitř předplatného nebo prostředku, který chcete testovat.

1. Přejděte na **prostředky Azure** pro toto předplatné a klikněte na **Role**, vyberte roli, kterou jste právě nakonfigurovali.

1. Pokud jsou uživatelé testu již aktivním správcem, můžete pro skupinu testovacích uživatelů učinit způsobilými vyhledáním a [aktualizací jejich přiřazení role](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Pokud roli ještě nemají, můžete [přiřadit novou roli](pim-resource-roles-assign-roles.md#assign-a-role).

1. Opakujte kroky 1-3 pro všechny role, které chcete testovat.

1. Po nastavení testovacích uživatelů byste jim měli poslat odkaz, jak [aktivovat jejich roli prostředků Azure](pim-resource-roles-activate-your-roles.md).

Tuto fázi byste měli použít k ověření, zda všechny konfigurace, které jste nastavili pro role, fungují správně. Testy můžete dokumentovat v následující tabulce. Tuto fázi byste měli také použít k optimalizaci komunikace s postiženými uživateli.

| Role | Očekávané chování během aktivace | Skutečné výsledky |
| --- | --- | --- |
| Globální správce | (1) Vyžadovat vícefaktorové finanční finanční pomoc<br/>(2) Vyžadovat schválení<br/>(3) Schvalovatel obdrží oznámení a může schválit<br/>(4) Platnost role vyprší po uplynutí nastavené doby |  |
| Vlastník předplatného *X* | (1) Vyžadovat vícefaktorové finanční finanční pomoc<br/>(2) platnost způsobilého přiřazení vyprší po nakonfigurovaném časovém období |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Komunikovat s řízením privilegovaných identit postiženým zúčastněným stranám

Nasazení správy privilegovaných identit zavede další kroky pro uživatele privilegovaných rolí. Přestože správa privilegovaných identit výrazně snižuje problémy se zabezpečením spojené s privilegovanými identitami, změna musí být efektivně sdělena před nasazením v rámci celého klienta. V závislosti na počtu ovlivněných správců se organizace často rozhodnou vytvořit interní dokument, video nebo e-mail o změně. Často zahrnuty v těchto komunikacích patří:

- Co je PIM
- Jaký je přínos pro organizaci
- Kdo bude ovlivněn
- Kdy bude PIM zaveden
- Jaké další kroky budou vyžadovány pro uživatele k aktivaci jejich role
    - Měli byste poslat odkazy na naši dokumentaci:
    - [Aktivace rolí Azure AD](pim-how-to-activate-role.md)
    - [Aktivace rolí prostředků Azure](pim-resource-roles-activate-your-roles.md)
- Kontaktní informace nebo odkaz na helpdesk pro všechny problémy spojené s PIM

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** nastavit čas s týmem technické podpory, který je provede pracovním postupem Správa privilegovaných identit (pokud má vaše organizace interní tým podpory IT). Poskytněte jim příslušnou dokumentaci a kontaktní informace.

### <a name="move-to-production"></a>Přechod k produkčnímu prostředí

Po dokončení a úspěšném testování přesuňte správu privilegovaných identit do produkčního prostředí opakováním všech kroků ve fázích testování pro všechny uživatele každé role, kterou jste definovali v konfiguraci správy privilegovaných identit. Pro privilegovanou správu identit pro role Azure AD organizace často testují a zavádějí správu privilegovaných identit pro globální správce před testováním a zavedením správy privilegovaných identit pro jiné role. Mezitím pro prostředky Azure organizace obvykle testují a zavádějí privilegovanou správu identit po jednom předplatném.

### <a name="in-the-case-a-rollback-is-needed"></a>V případě, že je potřeba vrácení zpět

Pokud správa privilegovaných identit nefunguje v produkčním prostředí podle potřeby, mohou vám následující kroky vrácení zpět pomoci vrátit se zpět do známého dobrého stavu před nastavením správy privilegovaných identit:

#### <a name="azure-ad-roles"></a>Role Azure AD

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Otevřete **správu privilegovaných identit Azure AD**.
1. Klikněte na **role Azure AD** a potom klikněte na **Role**.
1. U každé role, kterou jste nakonfigurovali, klikněte na tři tečky (**...**) pro všechny uživatele se způsobilým přiřazením.
1. Kliknutím na možnost **Vytvořit trvalý,** chcete-li přiřazení role natrvalo.

#### <a name="azure-resource-roles"></a>Role prostředků Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Otevřete **správu privilegovaných identit Azure AD**.
1. Klikněte na **prostředky Azure** a potom klikněte na předplatné nebo prostředek, který chcete vrátit zpět.
1. Klepněte na **položku Role**.
1. U každé role, kterou jste nakonfigurovali, klikněte na tři tečky (**...**) pro všechny uživatele se způsobilým přiřazením.
1. Kliknutím na možnost **Vytvořit trvalý,** chcete-li přiřazení role natrvalo.

## <a name="next-steps-after-deploying"></a>Další kroky po nasazení

Úspěšné nasazení správy privilegovaných identit v produkčním prostředí je významným krokem vpřed, pokud jde o zabezpečení privilegovaných identit vaší organizace. S nasazením správy privilegovaných identit přichází další funkce správy privilegovaných identit, které byste měli používat pro zabezpečení a dodržování předpisů.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Použití upozornění na správu privilegovaných identit k ochraně privilegovaného přístupu

Měli byste využít integrované funkce upozornění privileged identity pro lepší ochranu vašeho tenanta. Další informace naleznete v [tématu výstrahy zabezpečení](pim-how-to-configure-security-alerts.md#security-alerts). Mezi tyto výstrahy patří: správci nepoužívají privilegované role, role jsou přiřazovány mimo správu privilegovaných identit, role jsou aktivovány příliš často a další. Chcete-li plně chránit svou organizaci, měli byste pravidelně procházet seznam výstrah a opravovat problémy. Výstrahy můžete zobrazit a opravit následujícím způsobem:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Otevřete **správu privilegovaných identit Azure AD**.
1. Klikněte na **role Azure AD** a potom klikněte na **Výstrahy**.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** okamžitě řešit všechna upozornění označená vysokou závažností. U výstrah se střední a nízkou závažností byste měli být informováni a provádět změny, pokud se domníváte, že existuje bezpečnostní hrozba.

Pokud některý z konkrétních výstrah nejsou užitečné nebo se nevztahuje na vaši organizaci, můžete vždy zavřít upozornění na stránce výstrahy. Toto odvolání můžete vždy vrátit později na stránce nastavení Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Nastavení opakovaných kontrol přístupu pro pravidelné auditování privilegovaných identit vaší organizace

Kontroly přístupu jsou nejlepším způsobem, jak se zeptat uživatelů přiřazených s privilegovanými rolemi nebo konkrétními recenzenty, zda každý uživatel potřebuje privilegovanou identitu. Recenze přístupu jsou skvělé, pokud chcete snížit prostor pro útok a zůstat v souladu. Další informace o spuštění kontroly přístupu najdete v [tématu kontroly přístupu k rolím Azure AD](pim-how-to-start-security-review.md) a [kontroly přístupu k rolím prostředků Azure](pim-resource-roles-start-access-review.md). U některých organizací je nutné provádět pravidelnou kontrolu přístupu, aby zůstali v souladu se zákony a předpisy, zatímco u jiných je kontrola přístupu nejlepším způsobem, jak vynutit hlavní oprávnění pro nejnižší oprávnění v celé organizaci.

> [!TIP]
> :heavy_check_mark: Microsoft doporučuje nastavit čtvrtletní **kontroly** přístupu pro všechny vaše role prostředků Azure AD a Azure.

Ve většině případů recenzent pro role Azure AD je samotní uživatelé, zatímco recenzent pro role prostředků Azure je vlastníkem předplatného, ve kterém je role. Často se však stává, že společnosti mají privilegované účty, které nejsou spojeny s e-mailovou adresou konkrétní osoby. V těchto případech nikdo nečte a nekontroluje přístup.

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje** přidat sekundární e-mailovou adresu pro všechny účty s přiřazeními privilegovaných rolí, které nejsou propojeny s pravidelně kontrolovou e-mailovou adresou.

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Vytěžte z protokolu auditu maximum a zlepšete tak zabezpečení a dodržování předpisů

Protokol auditu je místo, kde můžete zůstat aktuální a dodržovat předpisy. Správa privilegovaných identit v současné době ukládá 30denní historii veškeré historie vaší organizace do protokolu auditu, včetně:

- Aktivace/deaktivace způsobilých rolí
- Aktivity přiřazení rolí uvnitř i vně správy privilegované identity
- Změny v nastavení rolí
- Žádost/schválení/zamítnutí aktivit pro aktivaci role s nastavením schválení
- Aktualizace upozornění

K těmto protokolům auditu můžete přistupovat, pokud jste globální správce nebo správce privilegovaných rolí. Další informace najdete v [tématu historie auditu pro role Azure AD](pim-how-to-use-audit-log.md) a [historie auditu pro role prostředků Azure](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: **Společnost Microsoft doporučuje,** aby alespoň jeden správce každý týden přečetl všechny události auditu a každý měsíc exportoval události auditu.

Pokud chcete automaticky ukládat události auditu po delší dobu, protokol auditu správy privilegovaných identit se automaticky synchronizuje do [protokolů auditu azure ad](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: **Microsoft doporučuje** nastavit [monitorování protokolů Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) pro archivaci událostí auditu v účtu úložiště Azure pro potřebu zabezpečení a dodržování předpisů.
