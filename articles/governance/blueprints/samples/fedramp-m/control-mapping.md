---
title: Ukázkové ovládací prvky FedRAMP středního podrobného plánu
description: Mapování ovládacích prvků FedRAMP pro střední plán. Každý ovládací prvek je namapován na jednu nebo více zásad Azure, které pomáhají s posouzením.
ms.date: 07/31/2020
ms.topic: sample
ms.openlocfilehash: adc91813e40c18d2ecd67d912db214d77776b8a5
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530166"
---
# <a name="control-mapping-of-the-fedramp-moderate-blueprint-sample"></a>Mapování ovládacích prvků FedRAMP pro střední plán

Následující článek podrobně popisuje, jak se vzorový vzorek FedRAMP pro střední plán Azure mapuje na střední ovládací prvky FedRAMP. Další informace o ovládacích prvcích najdete v tématu [základní informace o ovládacích prvcích zabezpečení FedRAMP](https://www.fedramp.gov/).

Následující mapování jsou pro střední ovládací prvky **FedRAMP** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte ve ** \[ verzi Preview \] : audit FedRAMP mírná řízení a nasaďte specifická rozšíření virtuálních počítačů, aby podporovaly** integrovanou iniciativu zásad pro požadavky na audit.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/fedramp-m/control-mapping.md).

## <a name="ac-2-account-management"></a>Správa účtů AC-2

Tento podrobný plán vám pomůže zkontrolovat účty, které nemusí být v rozporu s požadavky správy účtů vaší organizace. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které auditují externí účty s oprávněním ke čtení, zápisu a vlastníkovi u předplatného a zastaralých účtů. Kontrolou účtů, které tyto zásady auditují, můžete podniknout příslušné kroky, abyste zajistili splnění požadavků na správu účtů.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Správa účtů | Schémata založená na rolích

[Řízení přístupu na základě role Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) pomáhá spravovat, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán také přiřadí [Azure Policy](../../../policy/overview.md) definice k auditu používání ověřování Azure Active Directory pro servery SQL a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu. Tento podrobný plán navíc přiřadí definici Azure Policy pro audit používání vlastních pravidel služby Azure RBAC. Princip implementace vlastních pravidel služby Azure RBAC vám může pomáhat ověřit potřebnou a správnou implementaci, protože vlastní pravidla služby Azure RBAC jsou náchylná k chybám.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.
- Auditovat využití vlastních pravidel RBAC
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Správa účtů | Monitorování účtů/nezvyklé využití

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Všechny požadavky JIT na přístup k virtuálním počítačům se zaznamenávají do protokolu aktivit, což vám umožní monitorovat nezvyklé využití. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="ac-4-information-flow-enforcement"></a>Vynucování toku informací AC-4

Sdílení prostředků mezi zdroji (CORS) umožňuje, aby byly prostředky App Services požadovány z vnější domény. Microsoft doporučuje, abyste povolili interakci jenom požadovaných domén s rozhraním API, funkcí a webovými aplikacemi. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám umožní monitorovat omezení přístupu k prostředkům CORS v Azure Security Center.
Princip implementace CORS vám může pomáhat ověřit, jestli jsou implementované ovládací prvky toku informací.

- CORS by neměl umožňovat každému prostředku přístup k vaší webové aplikaci

## <a name="ac-5-separation-of-duties"></a>AC-5 oddělení povinností

Jenom jeden vlastník předplatného Azure nepovoluje redundanci správy. I když má příliš mnoho vlastníků předplatného Azure, může dojít k navýšení potenciálu pro porušení zabezpečení prostřednictvím účtu napadeného vlastníka. Tento podrobný plán vám pomůže zachovat příslušný počet vlastníků předplatného Azure přiřazením [Azure Policy](../../../policy/overview.md) definice, které auditují počet vlastníků předplatných Azure. Tento podrobný plán také přiřadí Azure Policy definice, které vám pomůžou s řízením členství ve skupině Administrators na virtuálních počítačích s Windows. Správa vlastníka předplatného a oprávnění správce virtuálních počítačů vám může pomáhat s implementací vhodného oddělení funkcí.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- Auditovat virtuální počítače s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Auditovat virtuální počítače s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) vzdálený přístup | Automatizované monitorování a řízení

Tento podrobný plán vám pomůže s monitorováním a řízením vzdáleného přístupu tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) pro monitorování, že vzdálené ladění pro Azure App Service aplikace je vypnuté a definice zásad, které auditují virtuální počítače se systémem Linux, které umožňují vzdálená připojení z účtů bez hesel. Tento podrobný plán také přiřadí definici Azure Policy, která vám pomůže monitorovat neomezený přístup k účtům úložiště. Monitorování těchto indikátorů vám umožní zajistit, aby metody vzdáleného přístupu dodržovaly vaše zásady zabezpečení.

- \[Preview \] : Auditovat virtuální počítače Linux, které umožňují vzdálená připojení z účtů bez hesel
- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů se systémem Linux, které umožňují vzdálená připojení z účtů bez hesla
- Auditování neomezeného síťového přístupu k účtům úložiště
- Pro aplikaci API by mělo být vypnuto vzdálené ladění.
- Vzdálené ladění by mělo být pro Function App vypnuté.
- Vzdálené ladění by mělo být pro webovou aplikaci vypnuté.

## <a name="au-5-response-to-audit-processing-failures"></a>Reakce AU-5 na selhání zpracování auditu

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které monitorují konfigurace auditu a protokolování událostí. Monitorování těchto konfigurací může poskytnout indikátor selhání systému nebo chybnou konfiguraci a pomáhat s provedením nápravných akcí.

- Auditování nastavení diagnostiky
- Auditování na SQL serveru by mělo být povolené.
- Na spravovaných instancích by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat

## <a name="au-12-audit-generation"></a>Generování auditu AU-12

Tento podrobný plán vám pomůže zajistit, aby byly systémové události zaznamenávány přiřazením [Azure Policy](../../../policy/overview.md) definicí, které auditují nastavení protokolů v prostředcích Azure. Tyto definice zásad auditují a vynutily nasazení Log Analytics agenta na virtuálních počítačích Azure a konfiguraci nastavení auditu pro jiné typy prostředků Azure. Tyto definice zásad také auditují konfiguraci diagnostických protokolů a poskytují přehled o operacích, které se provádějí v rámci prostředků Azure. Kromě toho jsou na SQL serverech nakonfigurovaná auditování a rozšířené zabezpečení dat.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- \[Preview \] : Audit log Analyticsho nasazení agenta v VMSS-VM Image (OS) bez seznamu
- \[Preview \] : Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestav
- \[Verze Preview \] : nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- \[Verze Preview \] : nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows
- Auditování nastavení diagnostiky
- Auditování na SQL serveru by mělo být povolené.
- Na spravovaných instancích by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- Nasazení auditování na SQL serverech
- Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM – 7 (2) nejméně funkcí | Zabránit spuštění programu

Adaptivní řízení aplikací v Azure Security Center je inteligentní a automatizované řešení pro filtrování aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací může běžet v režimu vynucení, který zakazuje spuštění neschválené aplikace. Tento podrobný plán přiřadí definici Azure Policy, která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM – 7 (5) nejméně funkcí | Autorizovaný software/seznam povolených

Adaptivní řízení aplikací v Azure Security Center je inteligentní a automatizované řešení pro filtrování aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací pomáhá vytvořit schválené seznamy aplikací pro virtuální počítače. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="cm-11-user-installed-software"></a>Software nainstalovaný uživatelem CM-11

Adaptivní řízení aplikací v Azure Security Center je inteligentní a automatizované řešení pro filtrování aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací vám může pomáhat vymáhat a monitorovat dodržování zásad omezení softwaru. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="cp-7-alternate-processing-site"></a>Lokalita s alternativním zpracováním CP-7

Azure Site Recovery replikuje úlohy běžící na virtuálních počítačích z primárního umístění do sekundárního umístění. Pokud dojde k výpadku v primární lokalitě, zatížení se v rámci sekundárního umístění převezme. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která Audituje virtuální počítače bez nakonfigurovaného zotavení po havárii. Monitorování tohoto indikátoru vám může pomáhat zajistit, aby byly potřebné pohotovostní kontroly na místě.

- Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) identifikace a ověřování (uživatelé organizace) | Síťový přístup k privilegovaným účtům

Tento podrobný plán vám pomůže omezit a řídit privilegovaný přístup tím, že přiřadíte definice [Azure Policy](../../../policy/overview.md) k auditování účtů s oprávněním vlastníka nebo zápisu, u kterých není povolené Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněním k zápisu do vašeho předplatného by se měla povolit vícefaktorové ověřování.

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) identifikace a ověřování (uživatelé organizace) | Přístup k síti bez privilegovaných účtů

Tento podrobný plán vám pomůže omezit a řídit přístup přiřazením definice [Azure Policy](../../../policy/overview.md) k auditování účtů s oprávněním ke čtení, u kterých není povolené ověřování pomocí služby Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.

## <a name="ia-5-authenticator-management"></a>Správa ověřovacích dat IA-5

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které auditují virtuální počítače se systémem Linux, které umožňují vzdálená připojení z účtů bez hesla nebo mají nastavená nesprávná oprávnění pro soubor passwd. Tento podrobný plán také přiřazuje definice zásad, které auditují konfiguraci typu šifrování hesla pro virtuální počítače s Windows. Monitorování těchto ukazatelů vám pomůže zajistit, aby ověřovatelé systému dodržovali zásady pro identifikaci a ověřování vaší organizace.

- \[Preview \] : Auditovat virtuální počítače Linux, které nemají oprávnění k souboru passwd nastavené na 0644
- \[Verze Preview \] : Auditovat virtuální počítače se systémem Linux, které mají účty bez hesla
- \[Verze Preview \] : Auditovat virtuální počítače s Windows, které neukládají hesla pomocí reverzibilního šifrování
- \[Verze Preview \] : nasaďte požadavky na auditování virtuálních počítačů se systémem Linux, které nemají oprávnění k souboru passwd nastavené na 0644.
- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů se systémem Linux, které mají účty bez hesla
- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů s Windows, které neukládají hesla pomocí reverzibilního šifrování

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Správa ověřovatele | Ověřování založené na heslech

Tento podrobný plán vám pomůže vynutilit silná hesla tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které auditují virtuální počítače s Windows, které nevyžadují minimální sílu a jiné požadavky na heslo. Povědomí o problémech s virtuálními počítači, které jsou v rozporu s zásadami složitosti hesla, vám pomůžou provést nápravné akce, které zajistí, aby hesla všech uživatelských účtů virtuálních počítačů byla v pořádku se zásadami hesel vaší organizace.

- \[Verze Preview \] : Auditovat virtuální počítače s Windows, které umožňují opakované použití předchozích 24 hesel
- \[Verze Preview \] : Auditovat virtuální počítače s Windows, které nemají maximální stáří hesla 70 dní
- \[Verze Preview \] : Auditovat virtuální počítače s Windows, které nemají minimální stáří hesla 1 den
- \[Verze Preview \] : Auditovat virtuální počítače s Windows, u kterých není povolené nastavení složitosti hesla
- \[Verze Preview \] : Auditovat virtuální počítače s Windows, které neomezují minimální délku hesla na 14 znaků
- \[Verze Preview \] : Auditovat virtuální počítače s Windows, které neukládají hesla pomocí reverzibilního šifrování
- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů s Windows, které umožňují opakované použití předchozích 24 hesel
- \[Verze Preview \] : nasaďte požadavky na auditovat virtuální počítače s Windows, které nemají maximální stáří hesla 70 dní.
- \[Verze Preview \] : nasaďte požadavky na auditovat virtuální počítače s Windows, které nemají minimální stáří hesla 1 den.
- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů s Windows, u kterých není povolené nastavení složitosti hesla
- \[Verze Preview \] : nasaďte požadavky na auditovat virtuální počítače s Windows, které neomezují minimální délku hesla na 14 znaků.
- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů s Windows, které neukládají hesla pomocí reverzibilního šifrování

## <a name="ra-5-vulnerability-scanning"></a>Kontrola ohrožení zabezpečení RA-5

Tento podrobný plán vám pomůže spravovat chyby zabezpečení systému pomocí přiřazení [Azure Policy](../../../policy/overview.md) definicí, které sledují chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center. Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase. Tento podrobný plán také přiřazuje definice zásad, které auditují a vynutily pokročilou zabezpečení dat na SQL serverech. Pokročilé zabezpečení dat zahrnuje posouzení ohrožení zabezpečení a rozšířené možnosti ochrany před internetovými útoky, které vám pomůžou pochopit ohrožení zabezpečení v nasazených prostředcích.

- Na spravovaných instancích by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Oprava ohrožení zabezpečení v konfiguraci zabezpečení na virtuálních počítačích by měla být opravena
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="sc-5-denial-of-service-protection"></a>SC-5 odmítnutí služby Service Protection

Úroveň Standard Azure distributed denial of DDoS () poskytuje další funkce a možnosti zmírnění služeb úrovně Basic. Mezi tyto další funkce patří Azure Monitor integrace a možnost kontrolovat zprávy o zmírnění útoků po útoku. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která Audituje, jestli je povolená úroveň Standard DDoS. Porozumět rozdílům schopností mezi úrovněmi služeb vám pomůžou vybrat nejlepší řešení pro řešení odepření ochrany služeb pro vaše prostředí Azure.

- Měla by být povolená DDoS Protection Standard.

## <a name="sc-7-boundary-protection"></a>Ochrana hranice SC-7

Tento podrobný plán vám pomůže spravovat a řídit hranici systému tím, že přiřadí definici [Azure Policy](../../../policy/overview.md) , která monitoruje doporučení pro posílení zabezpečení skupiny zabezpečení sítě v Azure Security Center. Azure Security Center analyzuje modely provozu virtuálních počítačů s internetem a poskytuje doporučení pro pravidlo skupiny zabezpečení sítě, aby se snížila potenciální plocha pro útok.
Kromě toho tento podrobný plán také přiřazuje definice zásad, které sledují nechráněné koncové body, aplikace a účty úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem, můžou dovolit neúmyslný přístup k informacím obsaženým v informačním systému.

- Pravidla skupiny zabezpečení sítě pro virtuální počítače s přístupem k Internetu by měla být zesílená.
- Přístup přes internetový koncový bod by měl být omezený.
- Webové porty by měly být omezené na skupinách zabezpečení sítě přidružených k vašemu VIRTUÁLNÍmu počítači.
- Auditování neomezeného síťového přístupu k účtům úložiště

## <a name="sc-7-3-boundary-protection--access-points"></a>Ochrana hranice SC-7 (3) | Přístupové body

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT vám pomůže omezit počet externích připojení k vašim prostředkům v Azure. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>Ochrana hranice SC-7 (4) | Externí telekomunikační služby

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT vám pomůže spravovat výjimky pro zásady toku provozu tím, že usnadňují procesy přístupu a žádosti o přístup. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Důvěrnost a integrita přenosu SC-8 (1) | Kryptografická nebo alternativní fyzická ochrana

Tento podrobný plán vám pomůže chránit důvěrnou a integritu odesílaných informací tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které vám pomůžou monitorovat kryptografické mechanismy implementované pro komunikační protokoly. Zajištění správné šifry komunikace vám může přispět k splnění požadavků vaší organizace nebo ochraně informací před neoprávněným zveřejněním a úpravou.

- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Auditovat webové servery systému Windows, které nepoužívají protokoly zabezpečených komunikací
- Nasaďte požadavky na auditovat webové servery Windows, které nepoužívají protokoly zabezpečené komunikace.
- Function App by měl být přístupný jenom přes HTTPS
- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Webová aplikace by měla být přístupná jen přes protokol HTTPS

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Ochrana informací v klidovém znění | Kryptografická ochrana

Tento podrobný plán vám pomůže vyhovět zásadám používání ovládacích prvků cryptograph k ochraně informací v klidovém prostředí tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které vynutily konkrétní ovládací prvky cryptograph a auditují použití slabého nastavení kryptografie. Porozumět tomu, kde vaše prostředky Azure můžou mít neoptimální Kryptografické konfigurace, můžou podniknout nápravné akce, které zajistí konfiguraci prostředků v souladu s vašimi zásadami zabezpečení informací. Konkrétně definice zásad přiřazené tímto plánem vyžaduje šifrování pro účty Data Lake Storage. vyžadovat transparentní šifrování dat na databázích SQL; a auditovat chybějící šifrování pro databáze SQL, disky virtuálních počítačů a proměnné účtu Automation.

- Na spravovaných instancích by mělo být povolené rozšířené zabezpečení dat
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
- Na virtuálních počítačích by se měly nainstalovat aktualizace systému
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Oprava ohrožení zabezpečení v konfiguraci zabezpečení na virtuálních počítačích by měla být opravena
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
- \[Preview \] : Audit log Analyticsho nasazení agenta v VMSS-VM Image (OS) bez seznamu
- \[Preview \] : Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestav
- \[Verze Preview \] : nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- \[Verze Preview \] : nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows
- Na spravovaných instancích by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- Nasazení rozšířené ochrany před internetovými útoky na účty úložiště
- Nasazení auditování na SQL serverech
- Při vytváření virtuálních sítí nasadit sledovací proces sítě
- Nasazení detekce hrozeb na SQL serverech

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit. 

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacích prvků FedRAMP pro střední plán, najdete v následujících článcích informace o podrobném plánu a způsobu nasazení této ukázky:

> [!div class="nextstepaction"]
> [FedRAMP – střední podrobný plán – přehled](./index.md) 
>  [FodRAMP střední podrobný plán – nasazení kroků](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)