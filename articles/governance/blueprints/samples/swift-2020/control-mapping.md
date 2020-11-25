---
title: SWIFT CSP – ukázkové ovládací prvky CSCF v2020 details
description: Mapování ovládacího prvku ukázka SWIFT CSP-CSCF v2020 details Každý ovládací prvek je namapován na jednu nebo více Azure Policy definic, které pomáhají s posouzením.
ms.date: 08/18/2020
ms.topic: sample
ms.openlocfilehash: 5aa4ee556c4ec7348566f45592b5e9fbf00eaf20
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009394"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>Mapování ovládacího prvku Sample CSP-CSCF v2020 details

Následující článek podrobně popisuje, jak ukázka Azure modrotisky SWIFT CSP – CSCF v2020 Details mapuje na ovládací prvky SWIFT CSP-CSCF v2020. Další informace o ovládacích prvcích najdete v tématu [SWIFT CSP-CSCF v2020](https://www.swift.com/myswift/customer-security-programme-csp).

Následující mapování jsou k ovládacím prvkům **SWIFT CSP-CSCF v2020** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte ve **\[ verzi Preview \] : Auditovat SWIFT CSP-CSCF v2020 Controls a nasaďte specifická rozšíření virtuálních počítačů, která budou podporovat požadavky na audit** integrovaných iniciativ zásad.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. Nicméně často není jedna nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md).

## <a name="12-and-51-account-management"></a>Správa účtů 1,2 a 5,1

Tento podrobný plán vám pomůže zkontrolovat účty, které nemusí být v rozporu s požadavky správy účtů vaší organizace. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které auditují externí účty s oprávněním ke čtení, zápisu a vlastníkům u předplatného a zastaralých účtů. Kontrolou účtů, které tyto zásady auditují, můžete podniknout příslušné kroky, abyste zajistili splnění požadavků na správu účtů.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>Správa účtů 2,6, 5,1, 6,4 a 6.5 | Role-Based schémata

[Řízení přístupu na základě role Azure](../../../../role-based-access-control/overview.md) (Azure RBAC) vám umožní spravovat, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán také přiřadí [Azure Policy](../../../policy/overview.md) definice k auditu používání ověřování Azure Active Directory pro servery SQL a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu. Tento podrobný plán navíc přiřadí definici Azure Policy pro audit používání vlastních pravidel služby Azure RBAC. Princip implementace vlastních pravidel služby Azure RBAC vám může pomáhat ověřit potřebnou a správnou implementaci, protože vlastní pravidla služby Azure RBAC jsou náchylná k chybám.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.
- Auditovat virtuální počítače, které nepoužívají spravované disky
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9 správu účtu | Monitorování účtů/nezvyklé využití

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Všechny požadavky JIT na přístup k virtuálním počítačům se zaznamenávají do protokolu aktivit, což vám umožní monitorovat nezvyklé využití. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu

## <a name="13-51-and-64-separation-of-duties"></a>1,3, 5,1 a 6,4 oddělení povinností

Jenom jeden vlastník předplatného Azure nepovoluje redundanci správy. I když má příliš mnoho vlastníků předplatného Azure, může dojít k navýšení potenciálu pro porušení zabezpečení prostřednictvím účtu napadeného vlastníka. Tento podrobný plán vám pomůže zachovat příslušný počet vlastníků předplatného Azure přiřazením [Azure Policy](../../../policy/overview.md) definice, které auditují počet vlastníků předplatných Azure. Tento podrobný plán také přiřadí Azure Policy definice, které vám pomůžou s řízením členství ve skupině Administrators na virtuálních počítačích s Windows. Správa vlastníka předplatného a oprávnění správce virtuálních počítačů vám může pomáhat s implementací vhodného oddělení funkcí.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>Minimální oprávnění 1,3, 5,1 a 6,4 | Kontrola uživatelských oprávnění

[Řízení přístupu na základě role Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) pomáhá spravovat, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice pro audit účtů, které by měly být v určitém pořadí pro kontrolu. Kontrola těchto indikátorů účtu vám umožní zajistit, aby byly implementované nejméně ovládací prvky pro oprávnění.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které nejsou připojené k zadané doméně
- Nasaďte požadavky pro audit virtuálních počítačů s Windows, které nejsou připojené k zadané doméně.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.

## <a name="22-and-27-security-attributes"></a>Atributy zabezpečení 2,2 a 2,7

Funkce zjišťování a klasifikace dat pro rozšířené zabezpečení dat pro Azure SQL Database poskytuje možnosti pro zjišťování, klasifikaci, označování a ochranu citlivých dat ve vašich databázích. Může sloužit k poskytování přehledu o stavu klasifikace databáze a ke sledování přístupu k citlivým datům v databázi i mimo ni. Pokročilé zabezpečení dat vám může pomáhat zajistit informace, které jsou přidruženy k příslušným atributům zabezpečení vaší organizace. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice pro monitorování a prosazování použití pokročilých zabezpečení dat na SQL serveru.

- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2,2, 2,7, 4,1 a 6,1 vzdálený přístup | Automatizované monitorování a řízení

Tento podrobný plán vám pomůže s monitorováním a řízením vzdáleného přístupu tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) pro monitorování, že vzdálené ladění pro Azure App Service aplikace je vypnuté a definice zásad, které auditují virtuální počítače se systémem Linux, které umožňují vzdálená připojení z účtů bez hesel. Tento podrobný plán také přiřadí definici Azure Policy, která vám pomůže monitorovat neomezený přístup k účtům úložiště. Monitorování těchto indikátorů vám umožní zajistit, aby metody vzdáleného přístupu dodržovaly vaše zásady zabezpečení.

- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, které umožňují vzdálená připojení z účtů bez hesel
- Nasazení požadavků pro audit virtuálních počítačů Linux, které umožňují vzdálená připojení z účtů bez hesel
- Účty úložiště by měly omezovat síťový přístup
- Pro aplikaci API by mělo být vypnuto vzdálené ladění.
- Vzdálené ladění by mělo být pro Function App vypnuté.
- Vzdálené ladění by mělo být pro webovou aplikaci vypnuté.

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1,3 a 6,4 obsahu záznamů auditu | Centralizovaná správa obsahu plánovaného záznamu auditu

Data protokolu shromážděná pomocí Azure Monitor jsou uložená v pracovním prostoru Log Analytics, který umožňuje centralizovanou konfiguraci a správu. Tento podrobný plán vám pomůže zajistit, aby se události protokoloval pomocí přiřazování [Azure Policy](../../../policy/overview.md) definic, které auditují a vynutily nasazení agenta Log Analytics na virtuálních počítačích Azure.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- Nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- Nasazení agenta Log Analytics pro virtuální počítače s Windows

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2,2, 2,7 a 6,4 Reakce na selhání zpracování auditu

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které monitorují konfigurace auditu a protokolování událostí. Monitorování těchto konfigurací může poskytnout indikátor selhání systému nebo chybnou konfiguraci a pomáhat s provedením nápravných akcí.

- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Auditování nastavení diagnostiky
- Auditování na SQL serveru by mělo být povolené.

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1,3 a 6,4 revize auditu, analýza a vytváření sestav | Centrální kontrola a analýza

Data protokolu shromážděná pomocí Azure Monitor jsou uložena v pracovním prostoru Log Analytics umožňující centralizované generování sestav a analýzu. Tento podrobný plán vám pomůže zajistit, aby se události protokoloval pomocí přiřazování [Azure Policy](../../../policy/overview.md) definic, které auditují a vynutily nasazení agenta Log Analytics na virtuálních počítačích Azure.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- Nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- Nasazení agenta Log Analytics pro virtuální počítače s Windows

## <a name="13-22-27-64-and-65a-audit-generation"></a>1,3, 2,2, 2,7, 6,4 a 6.5 generování auditu

Tento podrobný plán vám pomůže zajistit, aby byly systémové události zaznamenávány přiřazením [Azure Policy](../../../policy/overview.md) definicí, které auditují nastavení protokolů v prostředcích Azure.
Tyto definice zásad auditují a vynutily nasazení Log Analytics agenta na virtuálních počítačích Azure a konfiguraci nastavení auditu pro jiné typy prostředků Azure. Tyto definice zásad také auditují konfiguraci diagnostických protokolů a poskytují přehled o operacích, které se provádějí v rámci prostředků Azure. Kromě toho jsou na SQL serverech nakonfigurovaná auditování a rozšířené zabezpečení dat.

- Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) není v seznamu
- Nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)
- Nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- Nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)
- Nasazení agenta Log Analytics pro virtuální počítače s Windows
- Auditování nastavení diagnostiky
- Auditovat nastavení auditování na úrovni SQL serveru
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- Auditování na SQL serveru by mělo být povolené.
- Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě

## <a name="11-least-functionality--prevent-program-execution"></a>1,1 nejméně funkcí | Zabránit spuštění programu

Adaptivní řízení aplikací v Azure Security Center je inteligentní a automatizované řešení pro filtrování aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací může běžet v režimu vynucení, který zakazuje spuštění neschválené aplikace. Tento podrobný plán přiřadí definici Azure Policy, která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1,1 nejméně funkcí | Autorizovaný software/seznam povolených

Adaptivní řízení aplikací v Azure Security Center je inteligentní a automatizované řešení pro filtrování aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací pomáhá vytvořit schválené seznamy aplikací pro virtuální počítače. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.

## <a name="11-user-installed-software"></a>1,1 User-Installed software

Adaptivní řízení aplikací v Azure Security Center je inteligentní a automatizované řešení pro filtrování aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací vám může pomáhat vymáhat a monitorovat dodržování zásad omezení softwaru. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.
- Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4,2 identifikace a ověřování (uživatelé organizace) | Síťový přístup k privilegovaným účtům

Tento podrobný plán vám pomůže omezit a řídit privilegovaný přístup tím, že přiřadíte definice [Azure Policy](../../../policy/overview.md) k auditování účtů s oprávněním vlastníka nebo zápisu, u kterých není povolené Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4,2 identifikace a ověřování (uživatelé organizace) | Přístup k síti bez privilegovaných účtů

Tento podrobný plán vám pomůže omezit a řídit přístup přiřazením definice [Azure Policy](../../../policy/overview.md) k auditování účtů s oprávněním ke čtení, u kterých není povolené ověřování pomocí služby Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.

## <a name="23-and-41-authenticator-management"></a>Správa ověřovacích dat 2,3 a 4,1

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které auditují virtuální počítače se systémem Linux, které umožňují vzdálená připojení z účtů bez hesla nebo mají nastavená nesprávná oprávnění pro soubor passwd. Tento podrobný plán také přiřazuje definice zásad, které auditují konfiguraci typu šifrování hesla pro virtuální počítače s Windows. Monitorování těchto ukazatelů vám pomůže zajistit, aby ověřovatelé systému dodržovali zásady pro identifikaci a ověřování vaší organizace.

- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, u kterých není oprávnění k souboru passwd nastaveno na 0644
- Nasaďte požadavky pro audit virtuálních počítačů Linux, které nemají oprávnění k souboru passwd nastavené na 0644.
- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, které mají účty bez hesla
- Nasazení požadavků pro audit virtuálních počítačů se systémem Linux, které mají účty bez hesla
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které neukládají hesla pomocí reverzibilního šifrování
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, které neukládají hesla pomocí reverzibilního šifrování.

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>Správa ověřovacích dat 2,3 a 4,1 | Ověřování Password-Based

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

## <a name="22-and-27-vulnerability-scanning"></a>Kontrola ohrožení zabezpečení 2,2 a 2,7

Tento podrobný plán vám pomůže spravovat chyby zabezpečení systému pomocí přiřazení [Azure Policy](../../../policy/overview.md) definicí, které sledují chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center.
Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase. Tento podrobný plán také přiřazuje definice zásad, které auditují a vynutily pokročilou zabezpečení dat na SQL serverech. Pokročilé zabezpečení dat zahrnuje posouzení ohrožení zabezpečení a rozšířené možnosti ochrany před internetovými útoky, které vám pomůžou pochopit ohrožení zabezpečení v nasazených prostředcích.

- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Auditování na SQL serveru by mělo být povolené.
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno 
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno

## <a name="13-denial-of-service-protection"></a>1,3 odmítnutí služby Service Protection

Úroveň Standard Azure distributed denial of DDoS () poskytuje další funkce a možnosti zmírnění služeb úrovně Basic. Mezi tyto další funkce patří Azure Monitor integrace a možnost kontrolovat zprávy o zmírnění útoků po útoku. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která Audituje, jestli je povolená úroveň Standard DDoS. Porozumět rozdílům schopností mezi úrovněmi služeb vám pomůžou vybrat nejlepší řešení pro řešení odepření ochrany služeb pro vaše prostředí Azure.

- Měla by být povolená Azure DDoS Protection Standard.

## <a name="11-and-61-boundary-protection"></a>1,1 a 6,1 hranice ochrany

Tento podrobný plán vám pomůže spravovat a řídit hranici systému tím, že přiřadí definici [Azure Policy](../../../policy/overview.md) , která monitoruje doporučení pro posílení zabezpečení skupiny zabezpečení sítě v Azure Security Center. Azure Security Center analyzuje modely provozu virtuálních počítačů s internetem a poskytuje doporučení pro pravidlo skupiny zabezpečení sítě, aby se snížila potenciální plocha pro útok. Kromě toho tento podrobný plán také přiřazuje definice zásad, které sledují nechráněné koncové body, aplikace a účty úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem, můžou dovolit neúmyslný přístup k informacím obsaženým v informačním systému.

- Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.
- Přístup přes internetový koncový bod by měl být omezený.
- Auditování neomezeného síťového přístupu k účtům úložiště

## <a name="29a-boundary-protection--access-points"></a>2.9 Protection hranice | Přístupové body

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT vám pomůže omezit počet externích připojení k vašim prostředkům v Azure. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9 Protection hranice | Externí telekomunikační služby

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT vám pomůže spravovat výjimky pro zásady toku provozu tím, že usnadňují procesy přístupu a žádosti o přístup. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2,1, 2,4, 2.4 a, 2.5 a a 2,6 důvěrnosti a integrity přenosu | Kryptografická nebo alternativní fyzická ochrana

Tento podrobný plán vám pomůže chránit důvěrnou a integritu odesílaných informací tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které vám pomůžou monitorovat kryptografické mechanismy implementované pro komunikační protokoly. Zajištění správné šifry komunikace vám může přispět k splnění požadavků vaší organizace nebo ochraně informací před neoprávněným zveřejněním a úpravou.

- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Zobrazit výsledky auditu z webových serverů Windows, které nepoužívají protokoly zabezpečené komunikace
- Nasazení požadavků pro audit webových serverů Windows, které nepoužívají zabezpečené komunikační protokoly
- Function App by měl být přístupný jenom přes HTTPS
- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Webová aplikace by měla být přístupná jen přes protokol HTTPS

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2,2, 2,3, 2,5, 4,1 a 2,7 Ochrana informací v klidovém znění | Kryptografická ochrana

Tento podrobný plán vám pomůže vyhovět zásadám používání ovládacích prvků cryptograph k ochraně informací v klidovém prostředí tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které vynutily konkrétní ovládací prvky cryptograph a auditují použití slabého nastavení kryptografie. Porozumět tomu, kde vaše prostředky Azure můžou mít neoptimální Kryptografické konfigurace, můžou podniknout nápravné akce, které zajistí konfiguraci prostředků v souladu s vašimi zásadami zabezpečení informací. Konkrétně definice zásad přiřazené tímto plánem vyžaduje šifrování pro účty Data Lake Storage. vyžadovat transparentní šifrování dat na databázích SQL; a auditovat chybějící šifrování pro databáze SQL, disky virtuálních počítačů a proměnné účtu Automation.

- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- Nasazení transparentního šifrování dat databáze SQL
- Je třeba povolit transparentní šifrování dat databází SQL.

## <a name="13-22-and-27-flaw-remediation"></a>1,3, 2,2 a 2,7 náprava chyby

Tento podrobný plán vám pomůže spravovat chyby v systému tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které monitorují chybějící aktualizace systému, chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center. Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase. V tomto podrobném plánu se taky přiřadí definice zásady, která zajišťuje opravy operačního systému pro sady škálování virtuálních počítačů.

- Vyžadovat automatické opravy imagí operačního systému na Virtual Machine Scale Sets
- Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.
- Na virtuálních počítačích by se měly nainstalovat aktualizace systému
- Auditování nasazení agenta závislostí ve virtuálních počítačích Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- Proměnné účtu Automation by se měly šifrovat.
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Oprava ohrožení zabezpečení v konfiguraci zabezpečení na virtuálních počítačích by měla být opravena
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno

## <a name="61-malicious-code-protection"></a>6,1 ochrana škodlivých kódů

Tento podrobný plán vám pomůže spravovat ochranu koncových bodů včetně ochrany škodlivých kódů tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které monitorují chybějící Endpoint Protection na virtuálních počítačích v Azure Security Center a vynutila řešení Microsoft Antimalware na virtuálních počítačích s Windows.

- Nasadit výchozí rozšíření Microsoft IaaSAntimalware pro Windows Server
- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.
- Monitorovat chybějící Endpoint Protection v Azure Security Center
- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.

## <a name="61-malicious-code-protection--central-management"></a>6,1 Ochrana před škodlivým kódem | Centrální správa

Tento podrobný plán vám pomůže spravovat službu Endpoint Protection, včetně ochrany škodlivých kódů, přiřazením [Azure Policy](../../../policy/overview.md) definic, které monitorují chybějící Endpoint Protection na virtuálních počítačích v Azure Security Center. Azure Security Center poskytuje centralizované možnosti správy a vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase.

- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.
- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1,1, 1,3, 2,2, 2,7, 2,8 a 6,4 monitorování informačních systémů

Tento podrobný plán vám pomůže monitorovat systém pomocí auditování a vynucování protokolování a zabezpečení dat napříč prostředky Azure. Konkrétně zásady přiřazují audit a vynutily nasazení agenta Log Analytics a rozšířené nastavení zabezpečení pro databáze SQL, účty úložiště a síťové prostředky. Tyto funkce vám pomůžou detekovat neobvyklé chování a indikátory útoků, abyste mohli podniknout příslušné akce.

- Zobrazit výsledky auditu z virtuálních počítačů s Windows, na kterých je agent Log Analytics nepřipojený podle očekávání
- Nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)
- Nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- Nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)
- Nasazení agenta Log Analytics pro virtuální počítače s Windows
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Pokročilá nastavení zabezpečení dat pro SQL Server by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.
- Měly by být povolené diagnostické protokoly v Azure Stream Analytics.
- Nasazení pokročilých zabezpečení dat na SQL serverech
- Nasazení auditování na SQL serverech
- Při vytváření virtuálních sítí nasadit sledovací proces sítě
- Nasazení detekce hrozeb na SQL serverech

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Monitorování systému 2,2 a 2,8 informace | Analýza provozu/tajnosti exfiltrace

Rozšířená ochrana před internetovými útoky pro Azure Storage detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Výstrahy ochrany zahrnují vzory přístupu k neobvyklé, neobvyklé extrahování a nahrávání a podezřelou aktivitu úložiště. Tyto indikátory vám pomůžou detekovat exfiltrace informací.

- Nasazení detekce hrozeb na SQL serverech

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit.

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacího prvku SWIFT CSP-CSCF v2020, přečtěte si následující články, kde se dozvíte o podrobném plánu a jak nasadit tuto ukázku:

> [!div class="nextstepaction"]
> [SWIFT CSP – CSCF v2020 podrobný plán – přehled](./index.md) 
>  [SWIFT CSP – CSCF v2020 podrobný plán – postup nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
