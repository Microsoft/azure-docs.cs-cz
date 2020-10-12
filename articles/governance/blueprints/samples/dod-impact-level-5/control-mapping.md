---
title: Ukázkové ovládací prvky podrobného plánu pro úroveň úderu DoD. 5
description: Mapování ovládacího prvku v ukázce 5 podrobného plánu úrovně dopadu DoD. Každý ovládací prvek je namapován na jednu nebo více zásad Azure, které pomáhají s posouzením.
ms.date: 09/17/2020
ms.topic: sample
ms.openlocfilehash: 6cd92cba69367c611a0e3a3c435e41e973a80d73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540536"
---
# <a name="control-mapping-of-the-dod-impact-level-5-blueprint-sample"></a>Mapování ovládacího prvku pro vzorek dopadu na 5. plán na úrovni účinku

Následující článek podrobně popisuje, jak má oddělení Azure modrotisky v rámci podrobného plánu o dopadu ochrany 5 (DoD IL5) na základě kontrolního plánu úrovně účinku DoD. 5. Další informace o ovládacích prvcích najdete v tématu [Průvodce požadavky na zabezpečení služby DoD Cloud Computing (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/Cloud_Computing_SRG_v1r3.pdf). Agentura DISA (obrany Information Systems) je agenturou ministerstva obrany USA, která je odpovědná za vývoj a udržování Průvodce požadavky zabezpečení cloud computingu DoD (SRG). SRG definuje základní požadavky na zabezpečení pro poskytovatele cloudových služeb (CSP), kteří hostují informace, systémy a aplikace, a pro používání cloudových služeb DoD.  

Následující mapování jsou ovládací prvky **úrovně dopadu dod. 5** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte ** \[ ukázkovou iniciativu \] úrovně dopadu dod. 5** .

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit.
> Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/dod-impact-level-5/control-mapping.md).

## <a name="ac-2-account-management"></a>Správa účtů AC-2

Tento podrobný plán vám pomůže zkontrolovat účty, které nemusí být v rozporu s požadavky správy účtů vaší organizace. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které auditují externí účty s oprávněním ke čtení, zápisu a vlastníkovi u předplatného a zastaralých účtů. Kontrolou účtů, které tyto zásady auditují, můžete podniknout příslušné kroky, abyste zajistili splnění požadavků na správu účtů.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Správa účtů | Role-Based schémata

Azure implementuje [řízení přístupu na základě role Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) , které vám pomůžou se správou toho, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán také přiřadí [Azure Policy](../../../policy/overview.md) definice k auditu používání ověřování Azure Active Directory pro servery SQL a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu. Tento podrobný plán navíc přiřadí definici Azure Policy pro audit používání vlastních pravidel služby Azure RBAC. Princip implementace vlastních pravidel služby Azure RBAC vám může pomáhat ověřit potřebnou a správnou implementaci, protože vlastní pravidla služby Azure RBAC jsou náchylná k chybám.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.
- Auditovat využití vlastních pravidel RBAC
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Správa účtů | Monitorování účtů/nezvyklé využití

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Všechny požadavky JIT na přístup k virtuálním počítačům se zaznamenávají do protokolu aktivit, což vám umožní monitorovat nezvyklé využití. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu

## <a name="ac-4-information-flow-enforcement"></a>Vynucování toku informací AC-4

Sdílení prostředků mezi zdroji (CORS) umožňuje, aby byly prostředky App Services požadovány z vnější domény. Microsoft doporučuje, abyste povolili interakci jenom požadovaných domén s rozhraním API, funkcí a webovými aplikacemi. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám umožní monitorovat omezení přístupu k prostředkům CORS v Azure Security Center. Princip implementace CORS vám může pomáhat ověřit, jestli jsou implementované ovládací prvky toku informací.

- CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím

## <a name="ac-5-separation-of-duties"></a>AC-5 oddělení povinností

Jenom jeden vlastník předplatného Azure nepovoluje redundanci správy. I když má příliš mnoho vlastníků předplatného Azure, může dojít k navýšení potenciálu pro porušení zabezpečení prostřednictvím účtu napadeného vlastníka. Tento podrobný plán vám pomůže zachovat příslušný počet vlastníků předplatného Azure přiřazením [Azure Policy](../../../policy/overview.md) definice, které auditují počet vlastníků předplatných Azure. Tento podrobný plán také přiřadí Azure Policy definice, které vám pomůžou s řízením členství ve skupině Administrators na virtuálních počítačích s Windows. Správa vlastníka předplatného a oprávnění správce virtuálních počítačů vám může pomáhat s implementací vhodného oddělení funkcí.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) nejnižší oprávnění | Kontrola uživatelských oprávnění

Azure implementuje [řízení přístupu na základě role Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) , které vám pomůžou se správou toho, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice pro audit účtů, které by měly být v určitém pořadí pro kontrolu. Kontrola těchto indikátorů účtu vám umožní zajistit, aby byly implementované nejméně ovládací prvky pro oprávnění.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) vzdálený přístup | Automatizované monitorování a řízení

Tento podrobný plán vám pomůže s monitorováním a řízením vzdáleného přístupu tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) pro monitorování, že vzdálené ladění pro Azure App Service aplikace je vypnuté a definice zásad, které auditují virtuální počítače se systémem Linux, které umožňují vzdálená připojení z účtů bez hesel. Tento podrobný plán také přiřadí definici Azure Policy, která vám pomůže monitorovat neomezený přístup k účtům úložiště. Monitorování těchto indikátorů vám umožní zajistit, aby metody vzdáleného přístupu dodržovaly vaše zásady zabezpečení.

- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, které umožňují vzdálená připojení z účtů bez hesel
- Nasazení požadavků pro audit virtuálních počítačů Linux, které umožňují vzdálená připojení z účtů bez hesel
- Účty úložiště by měly omezovat síťový přístup
- Vzdálené ladění by mělo být pro API Apps vypnuté.
- Pro aplikace Function app by mělo být vypnuto vzdálené ladění.
- Vzdálené ladění by mělo být pro webové aplikace vypnuté.

## <a name="ac-23-data-mining"></a>Dolování dat AC-23

Tento podrobný plán zajišťuje, že jsou na SQL serverech nakonfigurovaná auditování a pokročilá zabezpečení dat.

- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Auditování na SQL serveru by mělo být povolené.

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) obsah záznamů auditu | Centralizovaná správa obsahu plánovaného záznamu auditu

Data protokolu shromážděná pomocí Azure Monitor jsou uložená v pracovním prostoru Log Analytics, který umožňuje centralizovanou konfiguraci a správu. Tento podrobný plán vám pomůže zajistit, aby se události protokoloval pomocí přiřazování [Azure Policy](../../../policy/overview.md) definic, které auditují a vynutily nasazení agenta Log Analytics na virtuálních počítačích Azure.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy
- Agent Log Analytics by měl být nainstalovaný na Virtual Machine Scale Sets
- Agent Log Analytics by měl být nainstalovaný na virtuálních počítačích

## <a name="au-5-response-to-audit-processing-failures"></a>Reakce AU-5 na selhání zpracování auditu

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které monitorují konfigurace auditu a protokolování událostí. Monitorování těchto konfigurací může poskytnout indikátor selhání systému nebo chybnou konfiguraci a pomáhat s provedením nápravných akcí.

- Auditování nastavení diagnostiky
- Auditování na SQL serveru by mělo být povolené.
- V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) revize auditu, analýza a vytváření sestav | Centrální kontrola a analýza

Data protokolu shromážděná pomocí Azure Monitor jsou uložena v pracovním prostoru Log Analytics umožňující centralizované generování sestav a analýzu. Tento podrobný plán vám pomůže zajistit, aby se události protokoloval pomocí přiřazování [Azure Policy](../../../policy/overview.md) definic, které auditují a vynutily nasazení agenta Log Analytics na virtuálních počítačích Azure.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy

## <a name="au-6-5-audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>AU-6 (5) kontrola auditu, analýza a vytváření sestav | Možnosti integrace, kontroly a monitorování

Tento podrobný plán poskytuje definice zásad, které auditují záznamy s analýzou vyhodnocování ohrožení zabezpečení na virtuálních počítačích, virtuálních počítačů, SQL Databasech serverech a serverech spravovaných instancí SQL. Tyto definice zásad také auditují konfiguraci diagnostických protokolů a poskytují přehled o operacích, které se provádějí v rámci prostředků Azure. Tyto přehledy poskytují informace o stavu zabezpečení nasazených prostředků v reálném čase a můžou vám pomůžou určit prioritu nápravných akcí. Pro podrobnou kontrolu a monitorování ohrožení zabezpečení doporučujeme, abyste využili taky Azure Sentinel a Azure Security Center.

- Auditování nastavení diagnostiky
- Posouzení ohrožení zabezpečení by mělo být povoleno na spravované instanci SQL
- Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu

## <a name="au-12-audit-generation"></a>Generování auditu AU-12

Tento podrobný plán poskytuje definice zásad, které auditují a vynutily nasazení Log Analytics agenta na virtuálních počítačích Azure a konfiguraci nastavení auditu pro jiné typy prostředků Azure.
Tyto definice zásad také auditují konfiguraci diagnostických protokolů a poskytují přehled o operacích, které se provádějí v rámci prostředků Azure. Kromě toho jsou na SQL serverech nakonfigurovaná auditování a rozšířené zabezpečení dat.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy
- Auditování nastavení diagnostiky
- Auditování na SQL serveru by mělo být povolené.
- V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat

## <a name="au-12-01-audit-generation--system-wide--time-correlated-audit-trail"></a>Generování auditu AU-12 (01) | Záznam pro audit System-Wide/Time-Correlated

Tento podrobný plán vám pomůže zajistit, aby byly systémové události zaznamenávány přiřazením [Azure Policy](../../../policy/overview.md) definicí, které auditují nastavení protokolů v prostředcích Azure.
Tato předdefinovaná zásada vyžaduje, abyste určili pole typů prostředků, abyste zkontrolovali, jestli jsou nastavení diagnostiky povolená, nebo ne.

- Auditování nastavení diagnostiky

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM – 7 (2) nejméně funkcí | Zabránit spuštění programu

Adaptivní řízení aplikací v Azure Security Center je inteligentní, automatizované a automatizované řešení seznamu povolených aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací může běžet v režimu vynucení, který zakazuje spuštění neschválené aplikace. Tento podrobný plán přiřadí definici Azure Policy, která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM – 7 (5) nejméně funkcí | Autorizovaný software/seznam povolených

Adaptivní řízení aplikací v Azure Security Center je inteligentní, automatizované a automatizované řešení seznamu povolených aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací pomáhá vytvořit schválené seznamy aplikací pro virtuální počítače. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam aplikací wallow, ale ještě není nakonfigurovaný.

- V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.

## <a name="cm-11-user-installed-software"></a>CM-11 User-Installed software

Adaptivní řízení aplikací v Azure Security Center je inteligentní, automatizované a automatizované řešení seznamu povolených aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací vám může pomáhat vymáhat a monitorovat dodržování zásad omezení softwaru. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.

## <a name="cp-7-alternate-processing-site"></a>Lokalita s alternativním zpracováním CP-7

Azure Site Recovery replikuje úlohy běžící na virtuálních počítačích z primárního umístění do sekundárního umístění. Pokud dojde k výpadku v primární lokalitě, zatížení se v rámci sekundárního umístění převezme. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která Audituje virtuální počítače bez nakonfigurovaného zotavení po havárii. Monitorování tohoto indikátoru vám může pomáhat zajistit, aby byly potřebné pohotovostní kontroly na místě.

- Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii

## <a name="cp-9-05--information-system-backup--transfer-to-alternate-storage-site"></a>CP-9 (05) záloha systému informací | Přenos na alternativní lokalitu úložiště

Tento podrobný plán přiřadí Azure Policy definice, které v elektronické podobě auditují informace o zálohování systému v organizaci na alternativním webu úložiště. Pro fyzickou expedici metadat úložiště zvažte použití Azure Data Box.

- Pro účty úložiště by mělo být povoleno geograficky redundantní úložiště.
- Pro Azure Database for PostgreSQL by měla být povolená geograficky redundantní záloha.
- Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.
- Pro databáze SQL Azure by mělo být povolené dlouhodobé geograficky redundantní zálohování.

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) identifikace a ověřování (uživatelé organizace) | Síťový přístup k privilegovaným účtům

Tento podrobný plán vám pomůže omezit a řídit privilegovaný přístup tím, že přiřadíte definice [Azure Policy](../../../policy/overview.md) k auditování účtů s oprávněním vlastníka nebo zápisu, u kterých není povolené Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) identifikace a ověřování (uživatelé organizace) | Přístup k síti bez privilegovaných účtů

Tento podrobný plán vám pomůže omezit a řídit přístup přiřazením definice [Azure Policy](../../../policy/overview.md) k auditování účtů s oprávněním ke čtení, u kterých není povolené ověřování pomocí služby Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.

## <a name="ia-5-authenticator-management"></a>Správa ověřovacích dat IA-5

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které auditují virtuální počítače se systémem Linux, které umožňují vzdálená připojení z účtů bez hesla nebo mají nastavená nesprávná oprávnění pro soubor passwd. Tento podrobný plán také přiřazuje definice zásad, které auditují konfiguraci typu šifrování hesla pro virtuální počítače s Windows. Monitorování těchto ukazatelů vám pomůže zajistit, aby ověřovatelé systému dodržovali zásady pro identifikaci a ověřování vaší organizace.

- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, u kterých není oprávnění k souboru passwd nastaveno na 0644
- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, které mají účty bez hesla
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které neukládají hesla pomocí reverzibilního šifrování
- Nasaďte požadavky pro audit virtuálních počítačů Linux, které nemají oprávnění k souboru passwd nastavené na 0644.
- Nasazení požadavků pro audit virtuálních počítačů se systémem Linux, které mají účty bez hesla
- Nasazení požadavků pro audit virtuálních počítačů s Windows, které neukládají hesla pomocí reverzibilního šifrování

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Správa ověřovatele | Ověřování Password-Based

Tento podrobný plán vám pomůže vynutilit silná hesla tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které auditují virtuální počítače s Windows, které nevyžadují minimální sílu a jiné požadavky na heslo. Povědomí o problémech s virtuálními počítači, které jsou v rozporu s zásadami složitosti hesla, vám pomůžou provést nápravné akce, které zajistí, aby hesla všech uživatelských účtů virtuálních počítačů byla v pořádku se zásadami hesel vaší organizace.

- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které umožňují opakované použití předchozích 24 hesel
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které nemají maximální stáří hesla 70 dní
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které nemají minimální stáří hesla 1 den
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, u kterých není povolené nastavení složitosti hesla
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které neomezují minimální délku hesla na 14 znaků
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které neukládají hesla pomocí reverzibilního šifrování
- Nasazení požadavků pro audit virtuálních počítačů s Windows, které umožňují opakované použití předchozích 24 hesel
- Nasaďte požadavky pro audit virtuálních počítačů s Windows, které nemají maximální stáří hesla 70 dnů.
- Nasazení požadavků pro audit virtuálních počítačů s Windows, které nemají minimální stáří hesla 1 den
- Nasazení požadavků pro audit virtuálních počítačů s Windows, u kterých není povolené nastavení složitosti hesla
- Nasaďte požadavky pro audit virtuálních počítačů s Windows, které neomezují minimální délku hesla na 14 znaků.
- Nasazení požadavků pro audit virtuálních počítačů s Windows, které neukládají hesla pomocí reverzibilního šifrování

## <a name="ir-6-2-incident-reporting--vulnerabilities-related-to-incidents"></a>IR-6 (2) generování sestav incidentů | Ohrožení zabezpečení související s incidenty

Tento podrobný plán poskytuje definice zásad, které auditují záznamy s analýzou posouzení ohrožení zabezpečení na virtuálních počítačích, Virtual Machine Scale Sets a SQL serverech. Tyto přehledy poskytují informace o stavu zabezpečení nasazených prostředků v reálném čase a můžou vám pomůžou určit prioritu nápravných akcí.

- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno

## <a name="ra-5-vulnerability-scanning"></a>Kontrola ohrožení zabezpečení RA-5

Tento podrobný plán vám pomůže spravovat chyby zabezpečení systému pomocí přiřazení [Azure Policy](../../../policy/overview.md) definicí, které sledují chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center. Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase. Tento podrobný plán také přiřazuje definice zásad, které auditují a vynutily pokročilou zabezpečení dat na SQL serverech. Pokročilé zabezpečení dat zahrnuje posouzení ohrožení zabezpečení a rozšířené možnosti ochrany před internetovými útoky, které vám pomůžou pochopit ohrožení zabezpečení v nasazených prostředcích.

- V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="sc-5-denial-of-service-protection"></a>SC-5 odmítnutí služby Service Protection

Úroveň Standard Azure distributed denial of DDoS () poskytuje další funkce a možnosti zmírnění služeb úrovně Basic. Mezi tyto další funkce patří Azure Monitor integrace a možnost kontrolovat zprávy o zmírnění útoků po útoku. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která Audituje, jestli je povolená úroveň Standard DDoS. Porozumět rozdílům schopností mezi úrovněmi služeb vám pomůžou vybrat nejlepší řešení pro řešení odepření ochrany služeb pro vaše prostředí Azure.

- Měla by být povolená Azure DDoS Protection Standard.

## <a name="sc-7-boundary-protection"></a>Ochrana hranice SC-7

Tento podrobný plán vám pomůže spravovat a řídit hranici systému tím, že přiřadí definici [Azure Policy](../../../policy/overview.md) , která monitoruje doporučení pro posílení zabezpečení skupiny zabezpečení sítě v Azure Security Center. Azure Security Center analyzuje modely provozu virtuálních počítačů s internetem a poskytuje doporučení pro pravidlo skupiny zabezpečení sítě, aby se snížila potenciální plocha pro útok.
Kromě toho tento podrobný plán také přiřazuje definice zásad, které sledují nechráněné koncové body, aplikace a účty úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem, můžou dovolit neúmyslný přístup k informacím obsaženým v informačním systému.

- Přístup přes internetový koncový bod by měl být omezený.
- Účty úložiště by měly omezovat síťový přístup

## <a name="sc-7-3-boundary-protection--access-points"></a>Ochrana hranice SC-7 (3) | Přístupové body

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT vám pomůže omezit počet externích připojení k vašim prostředkům v Azure. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>Ochrana hranice SC-7 (4) | Externí telekomunikační služby

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT vám pomůže spravovat výjimky pro zásady toku provozu tím, že usnadňují procesy přístupu a žádosti o přístup. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Důvěrnost a integrita přenosu SC-8 (1) | Kryptografická nebo alternativní fyzická ochrana

Tento podrobný plán vám pomůže chránit důvěrnou a integritu odesílaných informací tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které vám pomůžou monitorovat kryptografické mechanismy implementované pro komunikační protokoly. Zajištění správné šifry komunikace vám může přispět k splnění požadavků vaší organizace nebo ochraně informací před neoprávněným zveřejněním a úpravou.

- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Zobrazit výsledky auditu z webových serverů Windows, které nepoužívají protokoly zabezpečené komunikace
- Nasazení požadavků pro audit webových serverů Windows, které nepoužívají zabezpečené komunikační protokoly
- Function App by měl být přístupný jenom přes HTTPS
- Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Webová aplikace by měla být přístupná jen přes protokol HTTPS

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Ochrana informací v klidovém znění | Kryptografická ochrana

Tento podrobný plán vám pomůže vyhovět zásadám používání ovládacích prvků cryptograph k ochraně informací v klidovém prostředí tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které vynutily konkrétní ovládací prvky cryptograph a auditují použití slabého nastavení kryptografie. Porozumět tomu, kde vaše prostředky Azure můžou mít neoptimální Kryptografické konfigurace, můžou podniknout nápravné akce, které zajistí konfiguraci prostředků v souladu s vašimi zásadami zabezpečení informací. Konkrétně definice zásad přiřazené tímto plánem vyžaduje šifrování pro účty Data Lake Storage. vyžadovat transparentní šifrování dat na databázích SQL; a auditovat chybějící šifrování pro databáze SQL, disky virtuálních počítačů a proměnné účtu Automation.

- V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Na virtuálních počítačích by se mělo použít šifrování disku
- Je třeba povolit transparentní šifrování dat databází SQL.

## <a name="si-2-flaw-remediation"></a>Náprava – 2 chyba

Tento podrobný plán vám pomůže spravovat chyby v systému tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které monitorují chybějící aktualizace systému, chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center. Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase. V tomto podrobném plánu se taky přiřadí definice zásady, která zajišťuje opravy operačního systému pro sady škálování virtuálních počítačů.

- Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.
- Na počítače by se měly nainstalovat aktualizace systému
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="si-02-06-flaw-remediation--removal-of-previous-versions-of-software--firmware"></a>-02 (06) Chyba při nápravě | Odebrání předchozích verzí softwaru nebo firmwaru

Tento podrobný plán přiřadí definice zásad, které vám pomůžou zajistit, aby aplikace používaly nejnovější verzi HTTP, Java, PHP, Pythonu a TLS. V tomto podrobném plánu se taky přiřadí definice zásady, která zajistí, že se služby Kubernetes upgradují na jeho nezranitelnou verzi.

- Ujistěte se, že hodnota HTTP verze je nejnovější, pokud se používá ke spuštění aplikace API.
- Ujistěte se, že hodnota HTTP Version je nejnovější, pokud se používá ke spuštění aplikace Function App.
- Ujistěte se, že hodnota HTTP verze je nejnovější, pokud se používá ke spuštění webové aplikace.
- Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást aplikace API.
- Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást aplikace Function App.
- Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást webové aplikace.
- Zajistěte, aby byla verze PHP nejnovější, pokud se používá jako součást aplikace API.
- Zajistěte, aby byla verze PHP nejnovější, pokud se používá jako součást webové aplikace.
- Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást aplikace API.
- Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást aplikace Function App.
- Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást webové aplikace.
- V aplikaci API by se měla použít nejnovější verze TLS.
- V Function App by se měla použít nejnovější verze TLS.
- Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.
- Služby Kubernetes by se měly upgradovat na nezranitelnou Kubernetes verzi.

## <a name="si-3-malicious-code-protection"></a>Ochrana proti škodlivým kódem SI od-3

Tento podrobný plán vám pomůže spravovat ochranu koncových bodů včetně ochrany škodlivých kódů tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které monitorují chybějící Endpoint Protection na virtuálních počítačích v Azure Security Center a vynutila řešení Microsoft Antimalware na virtuálních počítačích s Windows.

- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.
- Monitorovat chybějící Endpoint Protection v Azure Security Center
- Rozšíření Microsoft IaaSAntimalware Extension by mělo být nasazeno na serverech Windows

## <a name="si-3-1-malicious-code-protection--central-management"></a>-3 (1) Ochrana proti škodlivým kódem | Centrální správa

Tento podrobný plán vám pomůže spravovat službu Endpoint Protection, včetně ochrany škodlivých kódů, přiřazením [Azure Policy](../../../policy/overview.md) definic, které monitorují chybějící Endpoint Protection na virtuálních počítačích v Azure Security Center. Azure Security Center poskytuje centralizované možnosti správy a vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase.

- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.
- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="si-4-information-system-monitoring"></a>Sledování informačních systémů SI – 4

Tento podrobný plán vám pomůže monitorovat systém pomocí auditování a vynucování protokolování a zabezpečení dat napříč prostředky Azure. Konkrétně zásady přiřazují audit a vynutily nasazení agenta Log Analytics a rozšířené nastavení zabezpečení pro databáze SQL, účty úložiště a síťové prostředky. Tyto funkce vám pomůžou detekovat neobvyklé chování a indikátory útoků, abyste mohli podniknout příslušné akce.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy
- V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Network Watcher by měl být povolený.

## <a name="si-4-12-information-system-monitoring--automated-alerts"></a>SI-4 (12) sledování systému informací | Automatizované výstrahy

Tento podrobný plán poskytuje definice zásad, které vám pomohou zajistit, aby byla oznámení zabezpečení dat správně povolena. Tento podrobný plán navíc zajišťuje, že pro Azure Security Center je povolená cenová úroveň Standard. Počítejte s tím, že cenová úroveň Standard umožňuje detekci hrozeb pro sítě a virtuální počítače, poskytuje analýzy hrozeb, detekci anomálií a analýzu chování v Azure Security Center.

- Mělo by se povolit e-mailové oznámení vlastníkovi předplatného pro upozornění s vysokou závažností.
- Pro vaše předplatné by se měla zadat e-mailová adresa kontaktu pro zabezpečení.
- Pro vaše předplatné by se mělo zadat telefonní číslo kontaktu zabezpečení.

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit. 

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacího prvku na základě dopadu 5, najdete v následujících článcích informace o podrobném plánu a způsobu nasazení této ukázky:

> [!div class="nextstepaction"]
> [Úroveň dopadu dod. 5 Details – přehled](./index.md) 
>  [Úroveň dopadu dod. 5 – postup nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)