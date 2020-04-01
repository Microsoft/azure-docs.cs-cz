---
title: Průvodce plánováním a provozem služby Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže s plánováním před přijetím řešení Azure Security Center a s vyřešením aspektů každodenního provozu.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 46994413ba765e18a826eebfe85a38bb65efc749
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435621"
---
# <a name="azure-security-center-planning-and-operations-guide"></a>Průvodce plánováním a provozem služby Azure Security Center
Tato příručka je určen pro odborníky v oblasti informačních technologií (IT), architekty IT, analytiky zabezpečení informací a správce cloudu, kteří plánují používat Azure Security Center.


## <a name="planning-guide"></a>Průvodce plánováním
Tato příručka popisuje úkoly, které můžete sledovat při optimalizaci používání Centra zabezpečení na základě požadavků na zabezpečení vaší organizace a modelu správy cloudu. Pokud chcete využívat všech výhod služby Security Center, je důležité pochopit, jak různí jednotlivci nebo týmy ve vaší organizaci používají tuto službu k tomu, aby vyhověli požadavkům na zabezpečený vývoj a provoz, sledování, řízení a reakce na incidenty. Při plánování použití služby Security Center je potřeba zvážit tyto klíčové oblasti:

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

Security Center umožňuje těmto osobám tyto různé povinnosti plnit. Například:

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

Security Center používá [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md). To poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md), které se dají v Azure přiřadit uživatelům, skupinám a službám. Když uživatel otevře službu Security Center, uvidí jenom informace související s prostředky, ke kterým má přístup. To znamená, že uživateli je přiřazena role Vlastníka, Přispěvatele nebo Čtenáře předplatného nebo skupiny prostředků, do které prostředek patří. Kromě těchto rolí existují ve službě Security Center dvě specifické role:

- **Čtenář zabezpečení:** uživatel patřící do této role může zobrazovat konfigurace služby Security Center jen pro čtení, což zahrnuje doporučení, výstrahy, zásady a stav, ale nemůže provádět změny.
- **Správce zabezpečení:** stejné jako čtenář zabezpečení, ale uživatel může také aktualizovat zásady zabezpečení a rušit doporučení a výstrahy.

Výše popsané role služby Security Center nemají přístup k jiným oblastem služeb Azure, jako je služba Storage, web a mobilní zařízení nebo Internet věcí.

Použití osob uvedených v předchozím diagramu vyžaduje následující nastavení RBAC:

**Jeff (vlastník úloh)**

* Vlastník/přispěvatel skupiny prostředků

**Ellen (ředitelka zabezpečení informací / ředitelka IT)**

* Vlastník předplatného/přispěvatel nebo správce zabezpečení

**David (zabezpečení IT)**

* Vlastník předplatného/přispěvatel nebo správce zabezpečení

**Judy (pracovnice oddělení zabezpečení)**

* Čtenář předplatného nebo čtenář zabezpečení s oprávněním zobrazovat výstrahy
* Vlastník/přispěvatel nebo správce zabezpečení je nutné zavřít výstrahy

**Sam (analytik zabezpečení)**

* Čtenář předplatného s oprávněním zobrazovat výstrahy
* Vlastník/přispěvatel předplatného, který musí upozornění zavřít, je vyžadován
* Může být vyžadován přístup k pracovnímu prostoru

Další informace, které je potřeba vzít v úvahu:

* Zásady zabezpečení můžou upravovat jenom vlastníci/přispěvatelé předplatného a správci zabezpečení.
* Doporučení zabezpečení pro určitý prostředek můžou uplatňovat jenom vlastníci a přispěvatelé předplatného a skupiny prostředků.

Při plánování řízení přístupu pomocí RBAC pro Security Center je potřeba si dobře rozmyslet, kdo z vaší organizace bude Security Center používat. Ke správné konfiguraci RBAC je také důležité, jaké typy úloh budou uživatelé provádět.

> [!NOTE]
> Doporučujeme přiřadit uživatelům tu nejvíc omezenou roli, kterou ke své práci potřebují. Třeba uživatelům, kteří potřebují jenom zobrazovat informace o stavu zabezpečení prostředků, ale nemusí provádět žádné kroky, třeba uplatňovat doporučení nebo upravovat zásady, by se měla přiřadit role Čtenář.
>
>

## <a name="security-policies-and-recommendations"></a>Zásady a doporučení zabezpečení
Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení. Ve službě Security Center můžete pro předplatná Azure definovat zásady upravené pro příslušný typ úlohy nebo citlivost dat.

Zásady Security Center obsahují následující součásti:
- [Shromažďování dat:](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) Zřizování agentů a nastavení shromažďování dat.
- [Zásady zabezpečení:](https://docs.microsoft.com/azure/security-center/security-center-policies) [Zásady Azure,](../governance/policy/overview.md) které určují, které ovládací prvky monitoruje a doporučuje Security Center, nebo pomocí zásad Azure vytvořte nové definice, definujte další zásady a přiřazujte zásady napříč skupinami pro správu.
- [E-mailová oznámení:](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) Kontakty zabezpečení a nastavení oznámení.
- [Cenová úroveň:](https://docs.microsoft.com/azure/security-center/security-center-pricing) Výběr cenové úrovně Free nebo Standard, která určuje, jaké funkce Security Center jsou k dispozici pro prostředky v oboru (u toho je možné určit předplatná, skupiny prostředků a pracovní prostory).

> [!NOTE]
> Zadáním smlouvy o zabezpečení zajistíte, že Azure v případě incidentu zabezpečení bude moci kontaktovat správného člověka ve vaší organizaci. Další informace o povolení tohoto doporučení najdete v článku [Zadání podrobností o kontaktu zabezpečení do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).

### <a name="security-policies-definitions-and-recommendations"></a>Definice a doporučení zásad zabezpečení
Security Center automaticky vytváří výchozí zásady zabezpečení pro každé z vašich předplatných Azure. Zásady můžete upravovat ve službě Security Center nebo můžete pomocí zásad Azure vytvářet nové definice, definovat další zásady, přiřazovat zásady napříč skupinami pro správu (ty mohou představovat celou organizaci, obchodní jednotku atd.) a monitorovat dodržování předpisů těchto zásad v příslušných oborech.

Než začnete konfigurovat zásady zabezpečení, prostudujte si všechna [doporučení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-recommendations) a zkontrolujte, jestli jsou dané zásady vhodné pro vaše předplatné a skupiny prostředků. Je také důležité pochopit, jaká akce by se měla provést pro řešení doporučení zabezpečení a kdo z organizace bude zodpovědný za monitorování nových doporučení a provádění potřebných kroků.

## <a name="data-collection-and-storage"></a>Shromažďování dat a úložiště
Azure Security Center používá agenta Analýzy protokolů – to je stejný agent používaný službou Azure Monitor – ke shromažďování dat zabezpečení z vašich virtuálních počítačů. [Data shromážděná](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) od tohoto agenta budou uložena v pracovním prostoru (pracovních prostorech) služby Log Analytics.

### <a name="agent"></a>Agent

Když je v zásadách zabezpečení povoleno automatické zřizování, agent Log Analytics (pro [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) nebo [Linux)](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)se nainstaluje na všech podporovaných virtuálních počítačích Azure a na všech nových, které se vytvoří. Pokud virtuální počítač nebo počítač už má nainstalovaného agenta Analýzy protokolů, Azure Security Center bude využívat aktuálního nainstalovaného agenta. Proces agenta je navržen tak, aby nenarušoval běžný chod a měl zcela minimální dopad na výkon virtuálního počítače.

Agent Log Analytics pro Systém Windows vyžaduje použití portu TCP 443. Další podrobnosti najdete v [článku Řešení problémů](security-center-troubleshooting-guide.md).

Pokud někdy budete chtít shromažďování dat vypnout, můžete to udělat v zásadě zabezpečení. Protože však agent a analýzy protokolů může být používán jinými službami správy a monitorování Azure, agent se při vypnutí shromažďování dat v Centru zabezpečení automaticky neodinstaluje. V případě potřeby můžete agenta odinstalovat ručně.

> [!NOTE]
> Pokud chcete najít seznam podporovaných virtuálních počítačích, přečtěte si [nejčastější dotazy Centra zabezpečení Azure ( FAQ).](faq-vms.md)

### <a name="workspace"></a>Pracovní prostor

Pracovní prostor je prostředek Azure, který slouží jako kontejner pro data. Vy nebo další členové vaší organizace můžete používat víc pracovních prostorů ke správě různých sad dat, která se shromažďují ze všech částí vaší infrastruktury IT.

Data shromážděná od agenta Analýzy protokolů (jménem Centra zabezpečení Azure) se budou ukládat buď v existujícím pracovním prostoru (pracovních prostorech) služby Log Analytics přidruženém k vašemu předplatnému Azure, nebo v novém pracovním prostoru (pracovních prostorech) s přihlédnutím k geografické virtuálnímu počítači.

Na webu Azure Portal můžete procházením zobrazit seznam pracovních prostorů Log Analytics, včetně těch vytvořených službou Azure Security Center. Pro nové pracovní prostory se vytvoří související skupina prostředků. Budou se řídit těmito zásadami vytváření názvů:

* Pracovní prostor: *DefaultWorkspace-[ID_předplatného]-[zeměpisné umístění]*
* Skupina prostředků: *DefaultResourceGroup-[geo]*

U pracovních prostorů vytvořených službou Azure Security Center se data uchovávají po dobu 30 dnů. U existujících pracovních prostorů uchovávání závisí na cenové úrovni pracovního prostoru. Pokud chcete, můžete použít také existující pracovní prostor.

> [!NOTE]
> Microsoft se pevně zavazuje, že soukromí a bezpečnost těchto dat bude chránit. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb. Další informace o zpracování dat a ochraně osobních údajů najdete v článku [Zabezpečení dat ve službě Azure Security Center](security-center-data-security.md).
>

## <a name="onboarding-non-azure-resources"></a>Připojení prostředků mimo Azure

Security Center může monitorovat stav zabezpečení počítačů mimo Azure, tyto prostředky je však nejprve potřeba připojit. Další informace o připojení prostředků mimo Azure najdete v tématu popisujícím [Připojení ke službě Azure Security Center úrovně Standard pro zvýšení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers).

## <a name="ongoing-security-monitoring"></a>Průběžné sledování zabezpečení
Po počáteční konfiguraci a uplatnění doporučení služby Security Center je dalším krokem zvážení provozních procesů Security Center.

Přehled služby Security Center poskytuje jednotný přehled o zabezpečení všech vašich prostředků Azure a připojených prostředků mimo Azure. Následující příklad ukazuje prostředí s mnoha problémy, které je potřeba vyřešit:

![řídicí panel](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Security Center nebude ovlivňovat vaše běžné provozní postupy, bude jenom pasivně sledovat vaše nasazení a poskytovat doporučení na základě zásad zabezpečení, které povolíte.

Když se poprvé rozhodnete pro používání služby Security Center pro stávající prostředí Azure, nezapomeňte si projít všechna doporučení. To můžete udělat na dlaždici **Doporučení** nebo u jednotlivých prostředků (**Compute**, **Sítě**, **Úložiště a data**, **Aplikace**).

Až vyřešíte všechna doporučení, v části **Prevention** (Prevence) by měly být všechny vyřešené prostředky označené zeleně. Průběžné sledování je od této chvíle jednodušší, protože budete provádět kroky jenom na základě změn stavu zabezpečení prostředků a dlaždic doporučení.

Část **Detection** (Detekce) vyžaduje víc reakcí, obsahuje totiž výstrahy týkající se problémů, které jsou aktuální nebo nastaly v minulosti, kdy je zjistily ovládací prvky služby Security Center a systémy jiných výrobců. Dlaždice Výstrahy zabezpečení zobrazí pruhové grafy, které představují počet výstrah, které byly nalezeny v každý den, a jejich rozdělení mezi různé kategorie závažnosti (nízká, střední, vysoká). Další informace o výstrahách zabezpečení najdete v tématu [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md).

Jako součást každodenních operací zabezpečení si naplánujte navštívení možnosti [Analýza hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-intel). Tam můžete identifikovat bezpečnostní hrozby pro prostředí, například můžete určit, zda je konkrétní počítač součástí botnetu.

### <a name="monitoring-for-new-or-changed-resources"></a>Sledování nových nebo změněných prostředků
Většina prostředí Azure je dynamická, přičemž prostředky se pravidelně vytvářejí, otáčejí nahoru nebo dolů, překonfigurují a mění. Security Center pomáhá zajistit přehled o stavu zabezpečení těchto nových prostředků.

Když do prostředí Azure přidáte nové prostředky (virtuální počítače, databáze SQL), Security Center tyto prostředky automaticky zjistí a začne sledovat jejich zabezpečení. To zahrnuje také webové role a role pracovního procesu PaaS. Pokud je shromažďování dat povoleno v [zásadách zabezpečení](tutorial-security-policy.md), budou pro vaše virtuální počítače automaticky povoleny další možnosti monitorování.

![Klíčové oblasti](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. U virtuálních počítačů klikněte v části **Hygiena zabezpečení prostředků** na **výpočetní & aplikací.** Případné problémy s povolováním shromažďování dat nebo související doporučení se zobrazí na kartě **Přehled** a v části **Doporučení pro monitorování**.
2. V části **Recommendations** (Doporučení) zjistíte, jestli se u nového prostředku zjistila nějaká rizika zabezpečení.
3. Je velmi běžné, že když se do vašeho prostředí přidají nové virtuální počítače, nejdřív mají nainstalovaný jen operační systém. Vlastníkovi prostředku může nějakou dobu trvat, než nasadí další aplikace, které budou tyto virtuální počítače používat.  V ideálním případě byste měli znát konečný záměr této úlohy. Bude se jednat o aplikační server? Podle toho, co k čemu bude tato nová úloha sloužit, můžete povolit vhodnou **zásadu zabezpečení**, což je třetí krok v tomto pracovním postupu.
4. Při přidávání nových prostředků do prostředí Azure se nové výstrahy mohou zobrazit na dlaždici **Výstrahy zabezpečení.** Vyhledejte nová upozornění na této dlaždici a postupujte podle doporučení.

Měli byste také pravidelně sledovat existující prostředky pro změny konfigurace, které by mohly způsobit bezpečnostní rizika, posun od doporučených směrných plánů a výstrahy zabezpečení. Začněte na řídicím panelu Security Center. Odtud máte tři hlavní oblasti, které musíte důsledně kontrolovat.

![Operace](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. Část **Prevence** nabízí rychlý přístup ke klíčovým prostředkům. Pomocí této možnosti můžete monitorovat službu Compute, Sítě, Úložiště a data a Aplikace.
2. Panel **Recommendations** (Doporučení) umožňuje kontrolovat doporučení služby Security Center. Během průběžného monitorování můžete zjistit, že nemáte doporučení na denní bázi, což je normální, protože jste se obrátili na všechna doporučení na počáteční nastavení Centra zabezpečení. Z toho důvodu se vám v této části nemusí každý den zobrazovat nové informace, takže ji musíte otevírat, jenom když je to nutné.
3. Část **Detekce** se může měnit velmi často i velmi zřídka. Vždy zkontrolujte výstrahy zabezpečení a proveďte akce na základě doporučení služby Security Center.

### <a name="hardening-access-and-applications"></a>Posílení přístupu a aplikací

V rámci operací zabezpečení byste měli zavést také preventivní opatření pro omezení přístupu k virtuálním počítačům a řízení aplikací spuštěných na virtuálních počítačích. Uzamčením příchozího provozu do virtuálních počítačů Azure snížíte vystavení útokům a zároveň zajistíte snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Pomocí funkce přístupu k [virtuálním počítači just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) k posílení zabezpečení přístupu k virtuálním počítačům.

[Pomocí adaptivních ovládacích prvků aplikací](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) můžete omezit, které aplikace se můžou spouštět na virtuálních počítačích umístěných v Azure. Mezi další výhody to pomáhá posílit vaše virtuální počítače proti malwaru. Pomocí strojového učení Security Center analyzuje procesy spuštěné ve virtuálním počítači, aby vám pomohla vytvořit pravidla whitelistingu.


## <a name="incident-response"></a>Reakce na incidenty
Security Center vyhledává nové hrozby a upozorňuje vás na ně. Organizace by měly výstrahy zabezpečení aktivně sledovat a provádět potřebné kroky, aby útok podrobněji prošetřily nebo napravily. Další informace o tom, jak funguje ochrana před hrozbami Centra zabezpečení, našetvete, [jak Azure Security Center rozpozná hrozby a reaguje na ně](security-center-alerts-overview.md#detect-threats).

Cílem tohoto článku není pomoct vám s vytvořením vlastního plánu reakcí na incidenty, postup uvedený v dokumentu Microsoft Azure Security Response in the Cloud (Reakce zabezpečení Microsoft Azure v cloudu) ale použijeme jako základ pro fáze reakcí na incidenty. Jednotlivé fáze jsou uvedeny v následujícím diagramu:

![Podezřelá aktivita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

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

Tato stránka obsahuje podrobné informace o čase útoku, zdrojovém názvu hostitele a cílovém virtuálním počítači a nabízí také doporučené kroky. V některých případech mohou být informace o zdroji útoku prázdné. Další informace o tomto typu chování najdete v článku [Chybějící informace o zdroji ve výstrahách služby Azure Security Center](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/).

Na této stránce také můžete zahájit [šetření](https://docs.microsoft.com/azure/security-center/security-center-investigation) pro lepší pochopení časové osy a způsobu provedení útoku, identifikaci potenciálně ohrožených systémů a použitých přihlašovacích údajů a zobrazení grafické reprezentace řetězení útoku.

Jakmile identifikujete ohrožený systém, můžete spustit [automatizaci pracovního postupu,](workflow-automation.md) která byla dříve vytvořena. Jedná se o kolekci procedur, které lze provést z Centra zabezpečení, jakmile se aktivuje výstraha.

V tématu [Jak využít Azure Security Center & Microsoft Operations Management Suite pro](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) video reakce na incidenty, můžete vidět některé ukázky, které vám pomohou pochopit, jak security center lze použít v každé z těchto fází.

> [!NOTE]
> Další informace o tom, jak používat funkce Security Center, které vám pomohou během procesu reakce na incidenty, najdete v části Správa a reakce na [výstrahy zabezpečení v Centru zabezpečení Azure.](security-center-managing-and-responding-alerts.md)
>
>

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak naplánovat přechod na službu Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Správa výstrah zabezpečení a reakce na ně v Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Monitorování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak sledovat stav prostředků Azure.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Centru zabezpečení Azure –](faq-general.md) Nejčastější dotazy týkající se používání služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Najděte blogové příspěvky o zabezpečení Azure a dodržování předpisů.
