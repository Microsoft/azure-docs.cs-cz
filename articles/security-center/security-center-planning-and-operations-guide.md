---
title: Průvodce plánováním a provozem Security Center
description: Tento dokument vám pomůže s plánováním před přijetím řešení Azure Security Center a s vyřešením aspektů každodenního provozu.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 0868c99a29b8da4376b639a54988fc821433e26d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576972"
---
# <a name="planning-and-operations-guide"></a>Průvodce plánováním a provozem
Tato příručka je určena odborníkům v oblasti informačních technologií (IT), architektům v oblasti IT, analytikům zabezpečení informací a správcům cloudu plánuje používat Azure Security Center.


## <a name="planning-guide"></a>Průvodce plánováním
Tato příručka popisuje úlohy, které můžete provést při optimalizaci používání Security Center v závislosti na požadavcích zabezpečení vaší organizace a modelu správy cloudu. Pokud chcete využívat všech výhod služby Security Center, je důležité pochopit, jak různí jednotlivci nebo týmy ve vaší organizaci používají tuto službu k tomu, aby vyhověli požadavkům na zabezpečený vývoj a provoz, sledování, řízení a reakce na incidenty. Při plánování použití služby Security Center je potřeba zvážit tyto klíčové oblasti:

* Role zabezpečení a řízení přístupu
* Zásady a doporučení zabezpečení
* Shromažďování dat a úložiště
* Připojení prostředků mimo Azure
* Průběžné sledování zabezpečení
* Reakce na incidenty

V další části se dozvíte, jak každou z těchto oblastí naplánovat a jak tato doporučení uplatnit v kombinaci s vašimi požadavky.


> [!NOTE]
> V tématu [Azure Security Center – nejčastější dotazy](faq-general.md) najdete seznam běžných dotazů, které vám můžou pomoct i ve fázi návrhu a plánování.

## <a name="security-roles-and-access-controls"></a>Role zabezpečení a řízení přístupu
V závislosti na velikosti a struktuře vaší organizace můžou službu Security Center používat různí jednotlivci a týmy a provádět v ní různé úkony týkající se zabezpečení. Následující diagram ukazuje příklad zahrnující fiktivní osoby a jejich příslušné role a povinnosti v oblasti zabezpečení:

![Role](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Security Center umožňuje těmto osobám tyto různé povinnosti plnit. Příklad:

**Jeff (vlastník úloh)**

* Spravuje úlohy v cloudu a související prostředky
* Odpovídá za implementace a správu bezpečnostních mechanismů v souladu se zásadami zabezpečení společnosti

**Ellen (ředitelka zabezpečení informací / ředitelka IT)**

* Odpovídá za všechny aspekty zabezpečení společnosti
* Odpovídá za jednotný postoj společnosti k zabezpečení napříč všemi úlohami v cloudu
* Musí být informována o hlavních útocích a rizicích

**David (zabezpečení IT)**

* Nastavuje zásady zabezpečení společnosti, aby zajistil funkčnost požadovaných bezpečnostních mechanismů
* Dohlíží na dodržování zásad
* Generuje sestavy pro vedení a auditory

**Judy (pracovnice oddělení zabezpečení)**

* Nepřetržitě sleduje výstrahy zabezpečení a reaguje na ně
* Problémy předává vlastníkovi úloh v cloudu nebo analytikovi zabezpečení IT

**Sam (analytik zabezpečení)**

* Zkoumá útoky
* Pracuje na nápravě s vlastníkem úlohy v cloudu

Security Center používá [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), které poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md) , které je možné přiřadit uživatelům, skupinám a službám v Azure. Když uživatel otevře službu Security Center, uvidí jenom informace související s prostředky, ke kterým má přístup. To znamená, že uživateli je přiřazena role Vlastníka, Přispěvatele nebo Čtenáře předplatného nebo skupiny prostředků, do které prostředek patří. Kromě těchto rolí existují ve službě Security Center dvě specifické role:

- **Čtenář zabezpečení:** uživatel patřící do této role může zobrazovat konfigurace služby Security Center jen pro čtení, což zahrnuje doporučení, výstrahy, zásady a stav, ale nemůže provádět změny.
- **Správce zabezpečení:** stejné jako čtenář zabezpečení, ale uživatel může také aktualizovat zásady zabezpečení a rušit doporučení a výstrahy.

Výše popsané role služby Security Center nemají přístup k jiným oblastem služeb Azure, jako je služba Storage, web a mobilní zařízení nebo Internet věcí.

Pomocí osoby vysvětlené v předchozím diagramu by se vyžadovalo následující Azure RBAC:

**Jeff (vlastník úloh)**

* Vlastník/Přispěvatel skupiny prostředků

**Ellen (ředitelka zabezpečení informací / ředitelka IT)**

* Vlastník/Přispěvatel předplatného nebo správce zabezpečení

**David (zabezpečení IT)**

* Vlastník/Přispěvatel předplatného nebo správce zabezpečení

**Judy (pracovnice oddělení zabezpečení)**

* Čtenář předplatného nebo čtenář zabezpečení s oprávněním zobrazovat výstrahy
* Vlastník/Přispěvatel předplatného nebo správce zabezpečení nutný k zavření výstrah

**Sam (analytik zabezpečení)**

* Čtenář předplatného s oprávněním zobrazovat výstrahy
* Vlastník/Přispěvatel předplatného nutný k zavření výstrahy
* Může být vyžadován přístup k pracovnímu prostoru

Další informace, které je potřeba vzít v úvahu:

* Zásady zabezpečení můžou upravovat jenom vlastníci/přispěvatelé předplatného a správci zabezpečení.
* Doporučení zabezpečení pro určitý prostředek můžou uplatňovat jenom vlastníci a přispěvatelé předplatného a skupiny prostředků.

Při plánování řízení přístupu pomocí Azure RBAC pro Security Center nezapomeňte pochopit, kdo ve vaší organizaci bude používat Security Center. Také typy úloh, které budou provádět, a následně nakonfigurujte službu Azure RBAC podle potřeby.

> [!NOTE]
> Doporučujeme přiřadit uživatelům tu nejvíc omezenou roli, kterou ke své práci potřebují. Třeba uživatelům, kteří potřebují jenom zobrazovat informace o stavu zabezpečení prostředků, ale nemusí provádět žádné kroky, třeba uplatňovat doporučení nebo upravovat zásady, by se měla přiřadit role Čtenář.
>
>

## <a name="security-policies-and-recommendations"></a>Zásady a doporučení zabezpečení
Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení. Ve službě Security Center můžete pro předplatná Azure definovat zásady upravené pro příslušný typ úlohy nebo citlivost dat.

Zásady Security Center obsahují následující součásti:
- [Shromažďování dat:](security-center-enable-data-collection.md) Zřizování agentů a nastavení shromažďování dat.
- [Zásady zabezpečení](tutorial-security-policy.md): [Azure Policy](../governance/policy/overview.md) , které určují, které ovládací prvky se monitorují a doporučují Security Center, nebo pomocí Azure Policy vytvořit nové definice, definovat další zásady a přiřazovat zásady napříč skupinami pro správu.
- [E-mailová oznámení:](security-center-provide-security-contact-details.md) Kontakty zabezpečení a nastavení oznámení.
- [Cenová úroveň](security-center-pricing.md): s Azure Defenderem nebo bez něj určíte, které Security Center funkce jsou dostupné pro prostředky v oboru (dá se zadat pro předplatná a pracovní prostory nebo skupiny prostředků pomocí rozhraní API).

> [!NOTE]
> Zadáním smlouvy o zabezpečení zajistíte, že Azure v případě incidentu zabezpečení bude moci kontaktovat správného člověka ve vaší organizaci. Další informace o povolení tohoto doporučení najdete v článku [Zadání podrobností o kontaktu zabezpečení do Azure Security Center](security-center-provide-security-contact-details.md).

### <a name="security-policies-definitions-and-recommendations"></a>Definice a doporučení zásad zabezpečení
Security Center automaticky vytváří výchozí zásady zabezpečení pro každé z vašich předplatných Azure. Zásady můžete upravovat ve službě Security Center nebo můžete pomocí zásad Azure vytvářet nové definice, definovat další zásady, přiřazovat zásady napříč skupinami pro správu (ty mohou představovat celou organizaci, obchodní jednotku atd.) a monitorovat dodržování předpisů těchto zásad v příslušných oborech.

Než začnete konfigurovat zásady zabezpečení, prostudujte si všechna [doporučení zabezpečení](security-center-recommendations.md) a zkontrolujte, jestli jsou dané zásady vhodné pro vaše předplatné a skupiny prostředků. Je také důležité pochopit, jaká akce by se měla provést pro řešení doporučení zabezpečení a kdo z organizace bude zodpovědný za monitorování nových doporučení a provádění potřebných kroků.

## <a name="data-collection-and-storage"></a>Shromažďování dat a úložiště
Azure Security Center používá agenta Log Analytics – to je stejný agent, kterého používá služba Azure Monitor – ke shromažďování dat zabezpečení z virtuálních počítačů. [Data shromážděná](security-center-enable-data-collection.md) z tohoto agenta se budou ukládat do vašich log Analyticsch pracovních prostorů.

### <a name="agent"></a>Agent

Pokud je v zásadách zabezpečení zapnuté Automatické zřizování, agent Log Analytics (pro [Windows](../azure-monitor/agents/agent-windows.md) nebo [Linux](../azure-monitor/vm/quick-collect-linux-computer.md)) je nainstalovaný na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených. Pokud je na VIRTUÁLNÍm počítači nebo počítači již nainstalován agent Log Analytics, Azure Security Center bude využívat aktuálně nainstalovaného agenta. Proces agenta je navržený tak, aby neinvazivní a měl velmi minimální dopad na výkon virtuálního počítače.

Agent Log Analytics pro Windows vyžaduje použití portu TCP 443. Další podrobnosti najdete v [článku Řešení problémů](security-center-troubleshooting-guide.md).

Pokud někdy budete chtít shromažďování dat vypnout, můžete to udělat v zásadě zabezpečení. Vzhledem k tomu, že agent Log Analytics můžou používat jiné služby pro správu a monitorování Azure, Agent se automaticky neodinstaluje, když vypnete shromažďování dat v Security Center. V případě potřeby můžete agenta odinstalovat ručně.

> [!NOTE]
> Pokud chcete najít seznam podporovaných virtuálních počítačů, přečtěte si [Azure Security Center Nejčastější dotazy](faq-vms.md).

### <a name="workspace"></a>Pracovní prostor

Pracovní prostor je prostředek Azure, který slouží jako kontejner pro data. Vy nebo další členové vaší organizace můžete používat víc pracovních prostorů ke správě různých sad dat, která se shromažďují ze všech částí vaší infrastruktury IT.

Data shromážděná z agenta Log Analytics (jménem Azure Security Center) se uloží v existujícím Log Analytics pracovních prostorech přidružených k vašemu předplatnému Azure nebo v nových pracovních prostorech, přičemž se vezme v úvahu geografická oblast virtuálního počítače.

Na webu Azure Portal můžete procházením zobrazit seznam pracovních prostorů Log Analytics, včetně těch vytvořených službou Azure Security Center. Pro nové pracovní prostory se vytvoří související skupina prostředků. Budou se řídit těmito zásadami vytváření názvů:

* Pracovní prostor: *DefaultWorkspace-[ID_předplatného]-[zeměpisné umístění]*
* Skupina prostředků: *DefaultResourceGroup-[geografické]*

U pracovních prostorů vytvořených službou Azure Security Center se data uchovávají po dobu 30 dnů. U existujících pracovních prostorů uchovávání závisí na cenové úrovni pracovního prostoru. Pokud chcete, můžete použít také existující pracovní prostor.

> [!NOTE]
> Společnost Microsoft poskytuje silné závazky na ochranu soukromí a zabezpečení těchto dat. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb. Další informace o zpracování dat a ochraně osobních údajů najdete v článku [Zabezpečení dat ve službě Azure Security Center](security-center-data-security.md).
>

## <a name="onboard-non-azure-resources"></a>Připojování prostředků mimo Azure

Security Center může monitorovat stav zabezpečení počítačů mimo Azure, tyto prostředky je však nejprve potřeba připojit. Další informace o připojování prostředků mimo Azure najdete v tématu připojení [počítačů mimo Azure](quickstart-onboard-machines.md) .

## <a name="ongoing-security-monitoring"></a>Průběžné sledování zabezpečení
Po počáteční konfiguraci a uplatnění doporučení služby Security Center je dalším krokem zvážení provozních procesů Security Center.

Přehled služby Security Center poskytuje jednotný přehled o zabezpečení všech vašich prostředků Azure a připojených prostředků mimo Azure. Následující příklad ukazuje prostředí s mnoha problémy, které je potřeba vyřešit:

![dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Security Center nebude ovlivňovat vaše běžné provozní postupy, bude jenom pasivně sledovat vaše nasazení a poskytovat doporučení na základě zásad zabezpečení, které povolíte.

Když se poprvé přihlásíte k použití Security Center pro aktuální prostředí Azure, ujistěte se, že jste si přesvědčili všechna doporučení, která se dají dělat na stránce **doporučení** .

Jako součást každodenních operací zabezpečení si naplánujte navštívení možnosti Analýza hrozeb. Tam můžete identifikovat bezpečnostní hrozby pro prostředí, například můžete určit, zda je konkrétní počítač součástí botnetu.

### <a name="monitoring-for-new-or-changed-resources"></a>Sledování nových nebo změněných prostředků

Většina prostředí Azure je dynamická, přičemž se prostředky pravidelně vytváří, prodlužuje nebo vymění, překonfigurují a mění. Security Center pomáhá zajistit přehled o stavu zabezpečení těchto nových prostředků.

Když do prostředí Azure přidáte nové prostředky (virtuální počítače, databáze SQL), Security Center tyto prostředky automaticky zjistí a začne sledovat jejich zabezpečení. To zahrnuje také webové role a role pracovního procesu PaaS. Pokud je v [zásadě zabezpečení](tutorial-security-policy.md)povolené shromažďování dat, pro vaše virtuální počítače se automaticky povolí další možnosti monitorování.

Měli byste také pravidelně monitorovat stávající prostředky pro změny konfigurace, které by mohly vytvářet bezpečnostní rizika, odkládat je od doporučených směrných plánů a výstrah zabezpečení. 

### <a name="hardening-access-and-applications"></a>Posílení přístupu a aplikací

V rámci operací zabezpečení byste měli zavést také preventivní opatření pro omezení přístupu k virtuálním počítačům a řízení aplikací spuštěných na virtuálních počítačích. Uzamčením příchozího provozu do virtuálních počítačů Azure snížíte vystavení útokům a zároveň zajistíte snadný přístup pro připojení k virtuálním počítačům v případě potřeby. K posílení přístupu k vašim virtuálním počítačům použijte funkci přístup k VIRTUÁLNÍm počítačům s přístupem [JIT (just-in-time](security-center-just-in-time.md) ).

Můžete použít [Adaptivní řízení aplikací](security-center-adaptive-application.md) k omezení, které aplikace se můžou spouštět na vašich virtuálních počítačích, které se nacházejí v Azure. Kromě dalších výhod to pomůže posílit vaše virtuální počítače proti malwaru. Pomocí strojového učení Security Center analyzuje procesy spuštěné ve virtuálním počítači, které vám pomůžou vytvořit pravidla pro výpisy seznamu povolených.


## <a name="incident-response"></a>Reakce na incidenty
Security Center vyhledává nové hrozby a upozorňuje vás na ně. Organizace by měly výstrahy zabezpečení aktivně sledovat a provádět potřebné kroky, aby útok podrobněji prošetřily nebo napravily. Další informace o tom, jak Security Center ochrana před hrozbami funguje, najdete v článku [jak Azure Security Center detekuje hrozby a reaguje na](security-center-alerts-overview.md#detect-threats)ně.

I když tento článek nemá záměr, který vám pomůže vytvořit vlastní plán reakce na incidenty, budeme v cloudovém životním cyklu používat Microsoft Azure odpověď zabezpečení jako základ pro fáze reakce na incidenty. Jednotlivé fáze jsou uvedeny v následujícím diagramu:

![Fáze reakce na incidenty v životním cyklu cloudu](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Jako referenci při sestavování vlastního plánu můžete využít příručku [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) organizace National Institute of Standards and Technology (NIST).
>

Výstrahy služby Security Center můžete použít během následujících fází:

* **Detekce**: Identifikace podezřelé aktivity v jednom nebo několika prostředcích.
* **Posouzení**: Počáteční zhodnocení a získávání dalších informací o podezřelé aktivitě.
* **Diagnóza**: Technické vyřešení problému podle nápravných kroků.

Každá výstraha zabezpečení poskytuje informace, které umožňují lepší pochopení povahy útoku a navrhují možné kroky ke zmírnění dopadu. Některé výstrahy obsahují i odkazy na další informace nebo další zdroje informací v Azure. Poskytnuté informace můžete využít k dalšímu výzkumu a nápravným krokům. Můžete také prohledávat data související se zabezpečením, která jsou uložena ve vašem pracovním prostoru.

Následující příklad ukazuje probíhající podezřelé aktivity protokolu RDP:

![Podezřelá aktivita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Tato stránka obsahuje podrobné informace o čase útoku, zdrojovém názvu hostitele a cílovém virtuálním počítači a nabízí také doporučené kroky. V některých případech mohou být zdrojové informace o útoku prázdné. Další informace o tomto typu chování najdete v článku [Chybějící informace o zdroji ve výstrahách služby Azure Security Center](/archive/blogs/azuresecurity/missing-source-information-in-azure-security-center-alerts).

Po identifikaci ohroženého systému můžete spustit [automatizaci pracovního postupu](workflow-automation.md) , která byla dříve vytvořena. Jedná se o kolekci procedur, které mohou být provedeny z Security Center po spuštění výstrahy.

V části [jak využít Azure Security Center & Microsoft Operations Management Suite pro video s odpovědí na incidenty](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) můžete zobrazit některá předvádění, která ukazují, jak se Security Center dá v každé z těchto fází použít.

> [!NOTE]
> Přečtěte si téma [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) , kde najdete další informace o použití možností Security Center, které vám pomůžou během procesu reakce na incidenty.
>
>

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak naplánovat přechod na službu Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Sledování partnerských řešení pomocí Azure Security Center](./security-center-partner-integration.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější](faq-general.md) dotazy k Azure Security Center – Přečtěte si nejčastější dotazy týkající se používání služby.
* [Blog o zabezpečení Azure](/archive/blogs/azuresecurity/) – Přečtěte si blogové příspěvky o zabezpečení Azure a dodržování předpisů.