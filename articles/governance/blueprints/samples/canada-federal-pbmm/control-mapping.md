---
title: Ukázkové ovládací prvky PBMM podrobného plánu pro Kanadu
description: Mapování ovládacích prvků pro ukázky federální PBMM podrobného plánu pro Kanadu Každý ovládací prvek je namapován na jednu nebo více Azure Policy definic, které pomáhají s posouzením.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 440b07db514d8af0cf7f4177a6bac497eead2b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627210"
---
# <a name="control-mapping-of-the-canada-federal-pbmm-blueprint-sample"></a>Mapování ovládacích prvků pro ukázku federálního PBMM podrobného plánu pro Kanadu

Následující článek podrobně popisuje, jak ukázkové mapy Azure modrotisky Kanady – Federal Protected B, střední integrita, střední dostupnost (PBMM) je podrobná na federální PBMM ovládací prvky Kanady. Další informace o ovládacích prvcích najdete v části [Kanada – federální PBMM](https://www.canada.ca/en/government/system/digital-government/digital-government-innovations/cloud-services/government-canada-security-control-profile-cloud-based-it-services.html).

Následující mapování jsou pro **federální PBMM** ovládací prvky Kanady. Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte ve **\[ verzi Preview \] : audit PBMMa Kanada Federal řídí** integrovanou iniciativu zásad.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. Nicméně často není jedna nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/canada-federal-pbmm/control-mapping.md).

## <a name="location-constraints"></a>Omezení umístění

Tento podrobný plán vám pomůže omezit umístění nasazení všech prostředků a skupin prostředků na "Kanada – střed" a "Kanada – východ" přiřazením následujících Azure Policych definic:

- Povolená umístění (byla pevně zakódována v oblasti Kanada – střed a Kanada – východ)
- Povolená umístění pro skupiny prostředků (je pevně zakódovaná do oblasti Kanada – střed a Kanada – východ)

## <a name="ac-2-account-management"></a>Správa účtů AC-2

Tento podrobný plán vám pomůže zkontrolovat účty, které nemusí být v rozporu s požadavky správy účtů vaší organizace. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které auditují externí účty s oprávněním ke čtení, zápisu a vlastníkům u předplatného a zastaralých účtů. Kontrolou účtů, které tyto zásady auditují, můžete podniknout příslušné kroky, abyste zajistili splnění požadavků na správu účtů.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.


## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Správa účtů | Role-Based schémata

Azure implementuje [řízení přístupu na základě role Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) , které vám pomůžou se správou toho, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán také přiřadí [Azure Policy](../../../policy/overview.md) definice k auditu používání ověřování Azure Active Directory pro servery SQL a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu. Tento podrobný plán navíc přiřadí definici Azure Policy pro audit používání vlastních pravidel služby Azure RBAC. V případě implementace vlastních pravidel služby Azure RBAC vám může pomáhat ověřit potřebnou a správnou implementaci, protože vlastní pravidla služby RBAC v Azure jsou náchylná k chybám.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.

## <a name="ac-4-information-flow-enforcement"></a>Vynucování toku informací AC-4

Sdílení prostředků mezi zdroji (CORS) umožňuje, aby byly prostředky App Services požadovány z vnější domény. Microsoft doporučuje, abyste povolili interakci jenom požadovaných domén s rozhraním API, funkcí a webovými aplikacemi. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám umožní monitorovat omezení přístupu k prostředkům CORS v Azure Security Center. Princip implementace CORS vám může pomáhat ověřit, jestli jsou implementované ovládací prvky toku informací.

- CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím

## <a name="ac-5-separation-of-duties"></a>AC-5 oddělení povinností

Jenom jeden vlastník předplatného Azure nepovoluje redundanci správy. I když má příliš mnoho vlastníků předplatného Azure, může dojít k navýšení potenciálu pro porušení zabezpečení prostřednictvím účtu napadeného vlastníka. Tento podrobný plán vám pomůže zachovat příslušný počet vlastníků předplatného Azure přiřazením [Azure Policy](../../../policy/overview.md) definice, které auditují počet vlastníků předplatných Azure. Tento podrobný plán také přiřadí Azure Policy definice, které vám pomůžou s řízením členství ve skupině Administrators na virtuálních počítačích s Windows. Správa vlastníka předplatného a oprávnění správce virtuálních počítačů vám může pomáhat s implementací vhodného oddělení funkcí.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Auditovat počítače s Windows, které mají zadané členy ve skupině Administrators
- Audit počítačů s Windows chybějících zadaných členů ve skupině Administrators

## <a name="ac-6-least-privilege"></a>Minimální oprávnění AC-6

Azure implementuje [řízení přístupu na základě role Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) , které vám pomůžou se správou toho, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice pro audit účtů, které by měly být v určitém pořadí pro kontrolu. Kontrola těchto indikátorů účtu vám umožní zajistit, aby byly implementované nejméně ovládací prvky pro oprávnění.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Auditovat počítače s Windows, které mají zadané členy ve skupině Administrators
- Audit počítačů s Windows chybějících zadaných členů ve skupině Administrators

## <a name="ac-7-security-attributes"></a>Atributy zabezpečení AC-7

Funkce zjišťování a klasifikace dat pro rozšířené zabezpečení dat pro Azure SQL Database poskytuje možnosti pro zjišťování, klasifikaci, označování a ochranu citlivých dat ve vašich databázích. Může sloužit k poskytování přehledu o stavu klasifikace databáze a ke sledování přístupu k citlivým datům v databázi i mimo ni. Pokročilé zabezpečení dat vám může pomáhat zajistit informace, které jsou přidruženy k příslušným atributům zabezpečení vaší organizace. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice pro monitorování a prosazování použití pokročilých zabezpečení dat na SQL serveru.

- V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) vzdálený přístup | Automatizované monitorování a řízení

Tento podrobný plán vám pomůže s monitorováním a řízením vzdáleného přístupu přiřazením [Azure Policy](../../../policy/overview.md) definic pro monitorování, že vzdálené ladění pro Azure App Service aplikace je vypnuté. Podrobný plán také přiřazuje definice zásad, které auditují virtuální počítače se systémem Linux, které umožňují vzdálená připojení z účtů bez hesel. Plán navíc přiřadí definici Azure Policy, která vám pomůže monitorovat neomezený přístup k účtům úložiště. Monitorování těchto indikátorů vám umožní zajistit, aby metody vzdáleného přístupu dodržovaly vaše zásady zabezpečení.

- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, které umožňují vzdálená připojení z účtů bez hesel
- Účty úložiště by měly omezovat síťový přístup
- Vzdálené ladění by mělo být pro API Apps vypnuté.
- Pro aplikace Function app by mělo být vypnuto vzdálené ladění.
- Vzdálené ladění by mělo být pro webové aplikace vypnuté.

## <a name="au-3-2-content-of-audit-records"></a>AU-3 (2) obsah záznamů auditu

Data protokolu shromážděná pomocí Azure Monitor jsou uložená v pracovním prostoru Log Analytics, který umožňuje centralizovanou konfiguraci a správu. Tento podrobný plán vám pomůže zajistit, aby se události protokoloval pomocí přiřazování [Azure Policy](../../../policy/overview.md) definic, které auditují a vynutily nasazení agenta Log Analytics na virtuálních počítačích Azure.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows

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
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows

## <a name="au-12-audit-generation"></a>Generování auditu AU-12

Tento podrobný plán vám pomůže zajistit, aby byly systémové události zaznamenávány přiřazením [Azure Policy](../../../policy/overview.md) definicí, které auditují nastavení protokolů v prostředcích Azure.
Tyto definice zásad auditují a vynutily nasazení Log Analytics agenta na virtuálních počítačích Azure a konfiguraci nastavení auditu pro jiné typy prostředků Azure. Tyto definice zásad také auditují konfiguraci diagnostických protokolů a poskytují přehled o operacích, které se provádějí v rámci prostředků Azure. Kromě toho jsou na SQL serverech nakonfigurovaná auditování a rozšířené zabezpečení dat.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy

- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows
- Auditování nastavení diagnostiky
- Auditování na SQL serveru by mělo být povolené.
- V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- Nasazení auditování na SQL serverech
- Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM – 7 (5) nejméně funkcí | Autorizovaný software/seznam povolených

Adaptivní řízení aplikací v Azure Security Center je inteligentní, automatizované a automatizované řešení seznamu povolených aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací pomáhá vytvořit schválené seznamy aplikací pro virtuální počítače. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.

## <a name="cm-11-user-installed-software"></a>CM-11 User-Installed software

Adaptivní řízení aplikací v Azure Security Center je inteligentní, automatizované a automatizované řešení seznamu povolených aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací vám může pomáhat vymáhat a monitorovat dodržování zásad omezení softwaru. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.

## <a name="cp-7-alternate-processing-site"></a>Lokalita s alternativním zpracováním CP-7

Azure Site Recovery replikuje úlohy běžící na virtuálních počítačích z primárního umístění do sekundárního umístění. Pokud dojde k výpadku v primární lokalitě, zatížení se v rámci sekundárního umístění převezme. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která Audituje virtuální počítače bez nakonfigurovaného zotavení po havárii. Monitorování tohoto indikátoru vám může pomáhat zajistit, aby byly potřebné pohotovostní kontroly na místě.

- Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) identifikace a ověřování (uživatelé organizace) | Síťový přístup k privilegovaným účtům

Tento podrobný plán vám pomůže omezit a řídit privilegovaný přístup tím, že přiřadíte definice [Azure Policy](../../../policy/overview.md) k auditování účtů s oprávněním vlastníka nebo zápisu, u kterých není povolené Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.

## <a name="ia-5-authenticator-management"></a>Správa ověřovacích dat IA-5

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které auditují virtuální počítače se systémem Linux, které umožňují vzdálená připojení z účtů bez hesla nebo mají nastavená nesprávná oprávnění pro soubor passwd. Tento podrobný plán také přiřazuje definice zásad, které auditují konfiguraci typu šifrování hesla pro virtuální počítače s Windows. Monitorování těchto ukazatelů vám pomůže zajistit, aby ověřovatelé systému dodržovali zásady pro identifikaci a ověřování vaší organizace.

- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, u kterých není oprávnění k souboru passwd nastaveno na 0644
- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, které mají účty bez hesla

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Správa ověřovatele | Ověřování Password-Based

Tento podrobný plán vám pomůže vynutilit silná hesla tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které auditují virtuální počítače s Windows, které nevyžadují minimální sílu a jiné požadavky na heslo. Povědomí o problémech s virtuálními počítači, které jsou v rozporu s zásadami složitosti hesla, vám pomůžou provést nápravné akce, které zajistí, aby hesla všech uživatelských účtů virtuálních počítačů byla v pořádku se zásadami hesel vaší organizace.

- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které umožňují opakované použití předchozích 24 hesel
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které nemají maximální stáří hesla 70 dní
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které nemají minimální stáří hesla 1 den
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, u kterých není povolené nastavení složitosti hesla
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které neomezují minimální délku hesla na 14 znaků

## <a name="ia-8-100-identification-and-authentication-non-organizational-users--identity-and-credential-assurance-levels"></a>Identifikace a ověřování IA-8 (100) (uživatelé bez organizace) | Úrovně identity a záruky pověření

Tento podrobný plán vám pomůže omezit a řídit privilegovaný přístup tím, že přiřadíte definice [Azure Policy](../../../policy/overview.md) k auditování účtů s oprávněním vlastníka nebo zápisu, u kterých není povolené Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.

## <a name="ra-5-vulnerability-scanning"></a>Kontrola ohrožení zabezpečení RA-5

Tento podrobný plán vám pomůže spravovat chyby zabezpečení systému pomocí přiřazení [Azure Policy](../../../policy/overview.md) definicí, které sledují chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center.
Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase. Tento podrobný plán také přiřazuje definice zásad, které auditují a vynutily pokročilou zabezpečení dat na SQL serverech. Pokročilé zabezpečení dat zahrnuje posouzení ohrožení zabezpečení a rozšířené možnosti ochrany před internetovými útoky, které vám pomůžou pochopit ohrožení zabezpečení v nasazených prostředcích.

- V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="sc-5-denial-of-service-protection"></a>SC-5 odmítnutí služby Service Protection

Úroveň Standard Azure distributed denial of DDoS () poskytuje další funkce a možnosti zmírnění služeb úrovně Basic. Mezi tyto další funkce patří Azure Monitor integrace a možnost kontrolovat zprávy o zmírnění útoků po útoku. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která Audituje, jestli je povolená úroveň Standard DDoS. Porozumět rozdílům schopností mezi úrovněmi služeb vám pomůžou vybrat nejlepší řešení pro řešení odepření ochrany služeb pro vaše prostředí Azure.

- Měla by být povolená DDoS Protection Standard.

## <a name="sc-7-boundary-protection"></a>Ochrana hranice SC-7

Tento podrobný plán vám pomůže spravovat a řídit hranici systému tím, že přiřadí definici [Azure Policy](../../../policy/overview.md) , která monitoruje doporučení pro posílení zabezpečení skupiny zabezpečení sítě v Azure Security Center. Azure Security Center analyzuje modely provozu virtuálních počítačů s internetem a poskytuje doporučení pro pravidlo skupiny zabezpečení sítě, aby se snížila potenciální plocha pro útok. Kromě toho tento podrobný plán také přiřazuje definice zásad, které sledují nechráněné koncové body, aplikace a účty úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem, můžou dovolit neúmyslný přístup k informacím obsaženým v informačním systému.

- Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.
- Přístup přes internetový koncový bod by měl být omezený.
- Účty úložiště by měly omezovat síťový přístup

## <a name="sc-7-3-boundary-protection--access-points"></a>Ochrana hranice SC-7 (3) | Přístupové body

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT vám pomůže omezit počet externích připojení k vašim prostředkům v Azure. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Měla by být povolená Azure DDoS Protection Standard.

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>Ochrana hranice SC-7 (4) | Externí telekomunikační služby

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT vám pomůže spravovat výjimky pro zásady toku provozu tím, že usnadňují procesy přístupu a žádosti o přístup. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Měla by být povolená Azure DDoS Protection Standard.

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Důvěrnost a integrita přenosu SC-8 (1) | Kryptografická nebo alternativní fyzická ochrana

Tento podrobný plán vám pomůže chránit důvěrnou a integritu odesílaných informací tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které vám pomůžou monitorovat kryptografické mechanismy implementované pro komunikační protokoly. Zajištění správné šifry komunikace vám může přispět k splnění požadavků vaší organizace nebo ochraně informací před neoprávněným zveřejněním a úpravou.

- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Zobrazit výsledky auditu z webových serverů Windows, které nepoužívají protokoly zabezpečené komunikace
- Function App by měl být přístupný jenom přes HTTPS
- Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.
- Webová aplikace by měla být přístupná jen přes protokol HTTPS
- Měl by se povolit zabezpečený přenos do účtů úložiště

## <a name="sc-28-1-protection-of-information-at-rest"></a>SC-28 (1) Ochrana informací v klidovém umístění

Tento podrobný plán vám pomůže vyhovět zásadám používání ovládacích prvků cryptograph k ochraně informací v klidovém prostředí tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které vynutily konkrétní ovládací prvky cryptograph a auditují použití slabého nastavení kryptografie. Porozumět tomu, kde vaše prostředky Azure můžou mít neoptimální Kryptografické konfigurace, můžou podniknout nápravné akce, které zajistí konfiguraci prostředků v souladu s vašimi zásadami zabezpečení informací. Konkrétně definice zásad přiřazené tímto plánem vyžaduje šifrování pro účty Data Lake Storage. vyžadovat transparentní šifrování dat na databázích SQL; a auditovat chybějící šifrování pro databáze SQL, disky virtuálních počítačů a proměnné účtu Automation.

- V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- Nasazení transparentního šifrování dat databáze SQL
- Na virtuálních počítačích by se mělo použít šifrování disku
- Vyžadovat šifrování u Data Lake Store účtů
- Je třeba povolit transparentní šifrování dat databází SQL.

## <a name="si-2-flaw-remediation"></a>Náprava – 2 chyba

Tento podrobný plán vám pomůže spravovat chyby v systému tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které monitorují chybějící aktualizace systému, chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center. Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase. V tomto podrobném plánu se taky přiřadí definice zásady, která zajišťuje opravy operačního systému pro sady škálování virtuálních počítačů.

- Vyžadovat automatické opravy imagí operačního systému na Virtual Machine Scale Sets
- Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.
- Na počítače by se měly nainstalovat aktualizace systému
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="si-3-malicious-code-protection"></a>Ochrana proti škodlivým kódem SI od-3

Tento podrobný plán vám pomůže spravovat ochranu koncových bodů včetně ochrany škodlivých kódů tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které monitorují chybějící Endpoint Protection na virtuálních počítačích v Azure Security Center a vynutila řešení Microsoft Antimalware na virtuálních počítačích s Windows.

- Nasadit výchozí rozšíření Microsoft IaaSAntimalware pro Windows Server
- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.
- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="si-3-1-malicious-code-protection--central-management"></a>-3 (1) Ochrana proti škodlivým kódem | Centrální správa

Tento podrobný plán vám pomůže spravovat službu Endpoint Protection, včetně ochrany škodlivých kódů, přiřazením [Azure Policy](../../../policy/overview.md) definic, které monitorují chybějící Endpoint Protection na virtuálních počítačích v Azure Security Center. Azure Security Center poskytuje centralizované možnosti správy a vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase.

- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.
- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="si-4-information-system-monitoring"></a>Sledování informačních systémů SI – 4

Tento podrobný plán vám pomůže monitorovat systém pomocí auditování a vynucování protokolování a zabezpečení dat napříč prostředky Azure. Konkrétně zásady přiřazují audit a vynutily nasazení agenta Log Analytics a rozšířené nastavení zabezpečení pro databáze SQL, účty úložiště a síťové prostředky. Tyto funkce vám pomůžou detekovat neobvyklé chování a indikátory útoků, abyste mohli podniknout příslušné akce.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows
- V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- Nasazení rozšířené ochrany před internetovými útoky na účty úložiště
- Nasazení auditování na SQL serverech
- Při vytváření virtuálních sítí nasadit sledovací proces sítě
- Nasazení detekce hrozeb na SQL serverech

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit. 

## <a name="next-steps"></a>Další kroky

Zkontrolovali jste mapování ovládacího prvku pro ukázku Federal PBMM details. Další informace o přehledu a způsobu nasazení této ukázky najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Kanada – federální PBMM podrobný plán – přehled](./control-mapping.md) 
>  [Kanada – federální PBMM podrobný plán – postup nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
