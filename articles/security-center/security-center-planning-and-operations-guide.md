---
title: Průvodce plánováním a provozem Security Center | Microsoft Docs
description: Tento dokument vám pomůže plánovat před přijetím Azure Security Center a důležitých informací týkajících se každodenních operací.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: abcd6dc8c50b819dd02347b938602af7f2152d04
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996621"
---
# <a name="azure-security-center-planning-and-operations-guide"></a>Průvodce plánováním a provozem Azure Security Center
Tato příručka je určena odborníkům v oblasti informačních technologií (IT), architektům v oblasti IT, analytikům zabezpečení informací a správcům cloudu plánuje používat Azure Security Center.


## <a name="planning-guide"></a>Průvodce plánováním
Tato příručka popisuje úlohy, které můžete provést při optimalizaci používání Security Center v závislosti na požadavcích zabezpečení vaší organizace a modelu správy cloudu. Abyste mohli plně využít výhod Security Center, je důležité pochopit, jak různí jednotlivci nebo týmy ve vaší organizaci používají službu ke splnění požadavků na zabezpečený vývoj a provoz, monitorování, řízení a reakce na incidenty. Důležité oblasti, které je třeba vzít v úvahu při plánování použití Security Center:

* Role zabezpečení a řízení přístupu
* Zásady zabezpečení a doporučení
* Shromažďování a ukládání dat
* Probíhající prostředky mimo Azure
* Průběžné sledování zabezpečení
* Reakce na incidenty

V další části se dozvíte, jak naplánovat každou z těchto oblastí a uplatnit tato doporučení podle svých požadavků.


> [!NOTE]
> Přečtěte si [Azure Security Center Nejčastější dotazy](security-center-faq.md) a seznam běžných otázek, které mohou být užitečné také při fázi návrhu a plánování.
>

## <a name="security-roles-and-access-controls"></a>Role zabezpečení a řízení přístupu
V závislosti na velikosti a struktuře vaší organizace můžou k provádění různých úloh souvisejících se zabezpečením používat Security Center více jednotlivců a týmů. V následujícím diagramu máte příklad fiktivního osoby a jejich příslušné role a bezpečnostní zodpovědnosti:

![Role](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Security Center umožňuje těmto jednotlivcům plnit tyto různé povinnosti. Příklad:

**Jan (Vlastník úlohy)**

* Správa cloudové úlohy a souvisejících prostředků
* Zodpovídá za implementaci a údržbu ochrany v souladu se zásadami zabezpečení společnosti.

**Ellen (ředitelka zabezpečení informací/CIO)**

* Zodpovídá za všechny aspekty zabezpečení společnosti.
* Chce pochopit zabezpečení stav společnosti napříč cloudovou úlohou.
* Musí být informováni o hlavních útokech a rizicích

**David (zabezpečení IT)**

* Nastaví zásady zabezpečení společnosti, aby se zajistilo, že budou zajištěny správné ochrany.
* Monitorování dodržování zásad
* Generuje sestavy pro vedoucí nebo auditory.

**Jana (operace zabezpečení)**

* Sleduje výstrahy zabezpečení 24/7 a reaguje na ně.
* Přestupňování na vlastníka úlohy cloudu nebo analytikovi zabezpečení IT

**Sam (analytik zabezpečení)**

* Prozkoumat útoky
* Práce s vlastníkem úlohy cloudu pro použití nápravy

Security Center používá [Access Control na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md), která poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md) , které se dají přiřadit uživatelům, skupinám a službám v Azure. Když uživatel otevře Security Center, uvidí jenom informace související s prostředky, ke kterým mají přístup. To znamená, že uživatel má přiřazenou roli vlastník, přispěvatel nebo čtenář k předplatnému nebo skupině prostředků, do které prostředek patří. Kromě těchto rolí jsou k dispozici dvě konkrétní role Security Center:

- **Čtenář zabezpečení**: uživatel patřící do této role může zobrazit jenom konfigurace Security Center, což zahrnuje doporučení, výstrahy, zásady a stav, ale nemůže provádět změny.
- **Správce zabezpečení**: totéž jako čtecí modul zabezpečení, ale může také aktualizovat zásady zabezpečení, zrušit doporučení a výstrahy.

Výše popsané role Security Center nemají přístup k jiným oblastem služeb Azure, jako jsou Storage, web & Mobile nebo Internet věcí.

Pomocí osoby vysvětlené v předchozím diagramu by se vyžadovala Tato akce RBAC:

**Jan (Vlastník úlohy)**

* Vlastník/Přispěvatel skupiny prostředků

**David (zabezpečení IT)**

* Vlastník/Přispěvatel předplatného nebo správce zabezpečení

**Jana (operace zabezpečení)**

* Čtenář předplatného nebo čtenář zabezpečení pro zobrazení výstrah
* Vlastník/Přispěvatel předplatného nebo správce zabezpečení nutný k zavření výstrah

**Sam (analytik zabezpečení)**

* Čtenář předplatného pro zobrazení výstrah
* Vlastník/Přispěvatel předplatného nutný k zavření výstrahy
* Může se vyžadovat přístup k pracovnímu prostoru.

Některé další důležité informace, které je potřeba vzít v úvahu:

* Zásady zabezpečení můžou upravovat jenom vlastníci/přispěvatelé předplatného a správci zabezpečení.
* Doporučení zabezpečení pro určitý prostředek můžou uplatnit jenom vlastníci a přispěvatelé předplatného a skupiny prostředků.

Při plánování řízení přístupu pomocí RBAC pro Security Center nezapomeňte pochopit, kdo ve vaší organizaci bude používat Security Center. Také typy úloh, které budou prováděny, a pak odpovídajícím způsobem nakonfigurujte RBAC.

> [!NOTE]
> Doporučujeme, abyste přiřadili nejnižší opravňující roli, kterou uživatelé potřebují k dokončení svých úkolů. Například uživatelé, kteří potřebují jenom zobrazit informace o stavu zabezpečení prostředků, ale ne provádět akce, jako je třeba použití doporučení nebo úprav zásad, by měly mít přiřazenou roli Čtenář.
>
>

## <a name="security-policies-and-recommendations"></a>Zásady zabezpečení a doporučení
Zásady zabezpečení definují požadovanou konfiguraci vašich úloh a pomáhají zajistit dodržování požadavků na zabezpečení společnosti nebo legislativních předpisů. V Security Center můžete definovat zásady pro vaše předplatná Azure, která je možné přizpůsobit typu úlohy nebo citlivosti dat.

Zásady Security Center obsahují následující komponenty:
- [Shromažďování dat](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): zřizování agentů a nastavení shromažďování dat.
- [Zásady zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-policies): [Azure Policy](../governance/policy/overview.md) , které určují, které ovládací prvky se monitorují a doporučují Security Center, nebo pomocí Azure Policy vytvořit nové definice, definovat další zásady a přiřazovat zásady napříč skupinami pro správu.
- [E-mailová oznámení](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): kontakty zabezpečení a nastavení oznámení.
- [Cenová úroveň](https://docs.microsoft.com/azure/security-center/security-center-pricing): výběr cen zdarma nebo Standard, který určuje, které Security Center funkce jsou k dispozici pro prostředky v oboru (lze zadat pro předplatná, skupiny prostředků a pracovní prostory).

> [!NOTE]
> Zadáním kontaktu se zabezpečením zajistíte, že Azure bude moct ve vaší organizaci kontaktovat správné osoby, pokud dojde k incidentu zabezpečení. Další informace o tom, jak toto doporučení povolit, najdete [v tématu Zadání podrobností o kontaktu zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) .

### <a name="security-policies-definitions-and-recommendations"></a>Definice a doporučení zásad zabezpečení
Security Center automaticky vytvoří výchozí zásadu zabezpečení pro každé předplatné Azure. Zásadu můžete upravit v Security Center nebo pomocí Azure Policy vytvářet nové definice, definovat další zásady a přiřazovat zásady napříč Skupiny pro správu (které můžou představovat celou organizaci, obchodní jednotku atd.) a monitorovat dodržování předpisů. Tyto zásady napříč těmito obory.

Než začnete konfigurovat zásady zabezpečení, Projděte si každé z [doporučení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-recommendations)a určete, jestli jsou tyto zásady vhodné pro vaše různá předplatná a skupiny prostředků. Je také důležité pochopit, jaká akce by se měla provést pro řešení doporučení zabezpečení a kdo ve vaší organizaci bude zodpovědný za monitorování nových doporučení a provedení potřebných kroků.

## <a name="data-collection-and-storage"></a>Shromažďování a ukládání dat
Azure Security Center používá Microsoft Monitoring Agent – jedná se o stejného agenta, kterého používá služba Azure Monitor – ke shromažďování dat zabezpečení z virtuálních počítačů. [Data shromážděná](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) z tohoto agenta se budou ukládat do vašich log Analyticsch pracovních prostorů.

### <a name="agent"></a>Agenta

Pokud je v zásadách zabezpečení povolené Automatické zřizování, Microsoft Monitoring Agent (pro [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) nebo [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)) se nainstaluje na všechny podporované virtuální počítače Azure a všechny nově vytvořené. Pokud je ve VIRTUÁLNÍm počítači nebo počítači již Microsoft Monitoring Agent nainstalován, Azure Security Center bude využívat aktuálně nainstalovaného agenta. Proces agenta je navržený tak, aby neinvazivní a měl velmi minimální dopad na výkon virtuálního počítače.

Microsoft Monitoring Agent pro Windows vyžaduje použití portu TCP 443. Další podrobnosti najdete v [článku věnovaném řešení potíží](security-center-troubleshooting-guide.md) .

Pokud v nějakém okamžiku chcete shromažďování dat zakázat, můžete ho vypnout v zásadách zabezpečení. Vzhledem k tomu, že Microsoft Monitoring Agent můžou používat jiné služby pro správu a monitorování Azure, Agent se automaticky neodinstaluje, když vypnete shromažďování dat v Security Center. V případě potřeby můžete agenta odinstalovat ručně.

> [!NOTE]
> Pokud chcete najít seznam podporovaných virtuálních počítačů, přečtěte si [Azure Security Center Nejčastější dotazy](security-center-faq.md).
>

### <a name="workspace"></a>Pracovní prostor

Pracovní prostor je prostředek Azure, který slouží jako kontejner pro data. Vy nebo jiní členové vaší organizace můžete používat víc pracovních prostorů ke správě různých sad dat, která se shromažďují ze všech nebo částí vaší infrastruktury IT.

Data shromážděná z Microsoft Monitoring Agent (jménem Azure Security Center) se uloží v existujícím Log Analytics pracovních prostorech přidružených k vašemu předplatnému Azure nebo v nových pracovních prostorech, přičemž vezme v úvahu geografickou oblast virtuálního počítače.

V Azure Portal můžete procházením zobrazit seznam pracovních prostorů Log Analytics, včetně všech vytvořených Azure Security Center. Pro nové pracovní prostory se vytvoří související skupina prostředků. Tato konvence vytváření názvů se bude řídit těmito zásadami:

* Pracovní prostor: *DefaultWorkspace-[ID předplatného]-[Geo]*
* Skupina prostředků: *DefaultResourceGroup-[geografické]*

U pracovních prostorů vytvořených pomocí Azure Security Center se data uchovávají po dobu 30 dnů. U existujících pracovních prostorů je doba uchování založená na cenové úrovni pracovního prostoru. Pokud chcete, můžete použít i existující pracovní prostor.

> [!NOTE]
> Microsoft se silným závazkem chránit soukromí a zabezpečení těchto dat. Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od kódování po provozování služby. Další informace o manipulaci s daty a ochraně osobních údajů najdete v článku [Azure Security Center zabezpečení dat](security-center-data-security.md).
>

## <a name="onboarding-non-azure-resources"></a>Připojování prostředků mimo Azure

Security Center můžou monitorovat stav zabezpečení počítačů mimo Azure, ale je potřeba nejdřív tyto prostředky připojit. Další informace o tom, jak se zaregistrují prostředky mimo Azure, najdete v tématu připojení [k Azure Security Center Standard pro rozšířené zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers) .

## <a name="ongoing-security-monitoring"></a>Průběžné sledování zabezpečení
Po počáteční konfiguraci a použití doporučení Security Center se další krok zvažuje Security Center provozní procesy.

Přehled Security Center poskytuje jednotný přehled o zabezpečení napříč všemi prostředky Azure a dalšími prostředky mimo Azure, které jste připojili. Následující příklad ukazuje prostředí s mnoha problémy, které je potřeba řešit:

![ovládacího](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Security Center nebude kolidovat s běžnými provozními postupy, bude tyto nasazení společně monitorovat a poskytovat doporučení na základě zásad zabezpečení, které jste povolili.

Když se poprvé přihlásíte k použití Security Center pro aktuální prostředí Azure, ujistěte se, že jste provedli všechna doporučení, která se dají dělat na dlaždici **doporučení** nebo podle prostředků (**výpočetní**prostředky, **sítě**, **úložiště & data.** , **Aplikace**).

Po vyřešení všech doporučení by měl být oddíl **prevence** zelený pro všechny prostředky, které byly řešeny. Průběžné monitorování se v tomto okamžiku bude jednodušší, protože budete provádět jenom akce založené na změnách na dlaždicích stav zabezpečení prostředku a doporučení.

Oddíl **detekce** je více reaktivní, jedná se o výstrahy týkající se problémů, které probíhají v minulosti nebo k ní došlo v minulosti a byly detekovány Security Center ovládacími prvky a systémy jiných výrobců. Dlaždice výstrahy zabezpečení zobrazí pruhové grafy, které reprezentují počet výstrah detekce hrozeb nalezených v každém dni, a jejich distribuci mezi různými kategoriemi závažnosti (nízká, střední, vysoká). Pokud chcete získat další informace o výstrahách zabezpečení, přečtěte si téma [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md).

Naplánujte návštěvu možnosti [Analýza hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) jako součást každodenních operací zabezpečení. Můžete identifikovat bezpečnostní hrozby pro prostředí, například zjistit, jestli je konkrétní počítač součástí botnetuu.

### <a name="monitoring-for-new-or-changed-resources"></a>Monitorování nových nebo změněných prostředků
Většina prostředí Azure je dynamická, přičemž se prostředky pravidelně vytváří, prodlužuje nebo vymění, překonfigurují a mění. Security Center pomáhá zajistit, že budete mít přehled o stavu zabezpečení těchto nových prostředků.

Když do prostředí Azure přidáte nové prostředky (virtuální počítače, databáze SQL), Security Center tyto prostředky automaticky zjistí a začnou monitorovat jejich zabezpečení. To zahrnuje i PaaS webové role a role pracovního procesu. Pokud je v [zásadě zabezpečení](tutorial-security-policy.md)povolené shromažďování dat, pro vaše virtuální počítače se automaticky povolí další možnosti monitorování.

![Klíčové oblasti](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Pro virtuální počítače klikněte v části **hygiena zabezpečení prostředků** na **COMPUTE & aplikace**. Jakékoli problémy s povolením dat nebo souvisejících doporučení se zobrazí na kartě **Přehled** a v části **doporučení pro monitorování** .
2. Podívejte se na **doporučení** a zjistěte, co, pokud nějaké bylo pro nový prostředek identifikováno bezpečnostní rizika.
3. Je velmi běžné, že když se do vašeho prostředí přidají nové virtuální počítače, nejdřív se nainstalují jenom operační systém. Pro nasazení dalších aplikací, které budou tyto virtuální počítače používat, může vlastník prostředků nějakou dobu potřebovat.  V ideálním případě byste měli znát konečný záměr této úlohy. Bude jít o aplikační server? Na základě toho, co bude tato nová úloha odpovídat, můžete povolit příslušné **zásady zabezpečení**, což je třetí krok v tomto pracovním postupu.
4. Při přidání nových prostředků do prostředí Azure se můžou nové výstrahy zobrazit na dlaždici **výstrahy zabezpečení** . Vyhledejte v této dlaždici nové výstrahy a postupujte podle doporučení.

Měli byste také pravidelně monitorovat stávající prostředky pro změny konfigurace, které by mohly vytvářet bezpečnostní rizika, odkládat je od doporučených směrných plánů a výstrah zabezpečení. Začněte na řídicím panelu Security Center. Odtud máte tři hlavní oblasti, které byste měli kontrolovat konzistentně.

![Operations](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. Panel oddílu **prevence** nabízí rychlý přístup ke klíčovým prostředkům. Pomocí této možnosti můžete monitorovat výpočetní prostředí, síťové služby, úložiště & data a aplikace.
2. Panel **doporučení** vám umožní zkontrolovat Security Center doporučení. Během průběžného monitorování můžete zjistit, že nebudete mít doporučení denně, což je normální, protože jste všechna doporučení při prvotním Security Center nastavení nevyřešili. Z tohoto důvodu možná nebudete mít nové informace v této části každý den a k nim bude mít přístup jenom podle potřeby.
3. Oddíl **detekce** se může u velmi častých nebo velmi častých intervalů změnit. Vždy zkontrolujte výstrahy zabezpečení a proveďte akce na základě doporučení Security Center.

### <a name="hardening-access-and-applications"></a>Posílení přístupu a aplikací

V rámci svých operací zabezpečení byste měli také přijmout preventivní opatření k omezení přístupu k virtuálním počítačům a řízení aplikací, které běží na virtuálních počítačích. Uzamčením příchozího provozu do virtuálních počítačů Azure snížíte riziko útoků a zároveň zadáte snadný přístup k připojení k virtuálním počítačům v případě potřeby. K posílení přístupu k virtuálním počítačům použijte funkci přístupu k [virtuálním počítačům za běhu](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) .

Můžete použít [Adaptivní řízení aplikací](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) k omezení, které aplikace se můžou spouštět na vašich virtuálních počítačích, které se nacházejí v Azure. Kromě dalších výhod to pomůže posílit vaše virtuální počítače proti malwaru. Pomocí strojového učení Security Center analyzuje procesy běžící na virtuálním počítači, které vám pomůžou vytvořit pravidla přidávání na seznam povolených.


## <a name="incident-response"></a>Reakce na incidenty
Security Center detekuje a upozorňuje na hrozby při jejich výskytu. V organizacích by se měly monitorovat nové výstrahy zabezpečení a provádět akce podle potřeby k dalšímu zkoumání nebo nápravě útoku. Další informace o tom, jak Security Center detekce hrozeb funguje, najdete v článku [jak Azure Security Center detekuje hrozby a reaguje na](security-center-alerts-overview.md#detect-threats)ně.

I když tento článek nemá záměr, který vám pomůže vytvořit vlastní plán reakce na incidenty, budeme v cloudovém životním cyklu používat Microsoft Azure odpověď zabezpečení jako základ pro fáze reakce na incidenty. Fáze jsou zobrazeny v následujícím diagramu:

![Podezřelá aktivita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Pomocí příručky National Institute of Standards and Technology (NIST) [Computer pro zpracování incidentů](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) můžete vytvářet vlastní informace, které vám pomůžou vytvořit vlastní.
>

Výstrahy Security Center můžete použít v následujících fázích:

* **Detekovat**: Identifikujte podezřelou aktivitu v jednom nebo více prostředcích.
* **Vyhodnocení**: proveďte počáteční posouzení a získejte další informace o podezřelé aktivitě.
* **Diagnostika**: pomocí kroků nápravy proveďte technický postup řešení problému.

Každé upozornění zabezpečení poskytuje informace, které je možné použít k lepšímu pochopení povahy útoku a k navržení možných rizik. Některé výstrahy také poskytují odkazy na Další informace nebo jiné zdroje informací v rámci Azure. Můžete použít informace poskytované k dalšímu výzkumu a zahájit zmírnění a můžete také prohledávat data související se zabezpečením, která jsou uložená ve vašem pracovním prostoru.

Následující příklad ukazuje podezřelou aktivitu protokolu RDP na místě:

![Podezřelá aktivita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Na této stránce se zobrazují podrobnosti o čase útoku, zdrojovém názvu hostitele, cílovém virtuálním počítači a také postup pro doporučení. V některých případech mohou být zdrojové informace o útoku prázdné. Další informace o tomto typu chování najdete [v tématu upozornění na chybějící zdrojové informace v Azure Security Center](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) .

Na této stránce můžete také zahájit [šetření](https://docs.microsoft.com/azure/security-center/security-center-investigation) , abyste lépe pochopili časovou osu útoku, jak k útoku došlo, které systémy mohly být potenciálně ohrožené, které přihlašovací údaje se použily, a zobrazit grafické znázornění celého řetězec útoku.

Po identifikaci ohroženého systému můžete spustit [playbooky](https://docs.microsoft.com/azure/security-center/security-center-playbooks) zabezpečení, které jste vytvořili dříve. PlayBook zabezpečení je kolekce procedur, které se dají spouštět z Security Center, když se konkrétní PlayBook aktivují z vybrané výstrahy.

V části [jak využít Azure Security Center & Microsoft Operations Management Suite pro video s odpovědí na incidenty](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) si můžete prohlédnout některé ukázky, které vám pomůžou pochopit, jak se Security Center dá v každé z těchto fází použít.

> [!NOTE]
> Přečtěte si téma [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) , kde najdete další informace o použití možností Security Center, které vám pomůžou během procesu reakce na incidenty.
>
>

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak naplánovat Security Center přijetí. Další informace o Security Center najdete v následujících tématech:

* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak monitorovat stav vašich partnerských řešení.
* [Nejčastější](security-center-faq.md) dotazy k Azure Security Center – Přečtěte si nejčastější dotazy týkající se používání služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si blogové příspěvky o zabezpečení Azure a dodržování předpisů.
