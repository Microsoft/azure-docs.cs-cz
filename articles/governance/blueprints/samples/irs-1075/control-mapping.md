---
title: Ovládací prvky ukázkových plánů finančního úřadu 1075
description: Mapování ovládacího prvku ukázkového plánu 1075. Každý ovládací prvek je namapován na jednu nebo více zásad Azure, které pomáhají s posouzením.
ms.date: 08/19/2020
ms.topic: sample
ms.openlocfilehash: 7bfe1b55868cadd3bb757cb15e95a5c3dc463855
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541301"
---
# <a name="control-mapping-of-the-irs-1075-blueprint-sample"></a>Mapování ovládacího prvku ukázka finančního úřadu 1075

Následující článek podrobně popisuje, jak se ukázka plánu Azure modrotisky finančního úřadu 1075 mapuje na ovládací prvky finančního úřadu pro 1075. Další informace o ovládacích prvcích naleznete v tématu [finanční úřad 1075](https://www.irs.gov/pub/irs-pdf/p1075.pdf).

Následující mapování jsou pro ovládací prvky **finančního úřadu 1075** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte ve ** \[ verzi Preview \] : audit úřadu 1075 a nasaďte specifická rozšíření virtuálního počítače pro podporu požadavků auditu na** integrovanou iniciativu zásad.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/irs-1075/control-mapping.md).

## <a name="9321-ac-2-account-management"></a>Správa účtů 9.3.2.1 AC-2

Tento podrobný plán vám pomůže zkontrolovat účty, které nemusí být v rozporu s požadavky správy účtů vaší organizace. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které auditují externí účty s oprávněním ke čtení, zápisu a vlastníkům u předplatného a zastaralých účtů. Kontrolou účtů, které tyto zásady auditují, můžete podniknout příslušné kroky, abyste zajistili splnění požadavků na správu účtů.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.

## <a name="9321-ac-2-7-account-management--role-based-schemes"></a>9.3.2.1 AC-2 (7) Správa účtů | Role-Based schémata

Azure implementuje [řízení přístupu na základě role Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) , které vám pomůžou se správou toho, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán také přiřadí [Azure Policy](../../../policy/overview.md) definice k auditu používání ověřování Azure Active Directory pro servery SQL a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu. Tento podrobný plán navíc přiřadí definici Azure Policy pro audit používání vlastních pravidel služby Azure RBAC. V případě implementace vlastních pravidel služby Azure RBAC vám může pomáhat ověřit potřebnou a správnou implementaci, protože vlastní pravidla služby RBAC v Azure jsou náchylná k chybám.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.
- Auditovat využití vlastních pravidel RBAC
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.

## <a name="9321-ac-2-12-account-management--account-monitoring--atypical-usage"></a>9.3.2.1 AC-2 (12) Správa účtů | Monitorování účtů/nezvyklé využití

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Všechny požadavky JIT na přístup k virtuálním počítačům se zaznamenávají do protokolu aktivit, což vám umožní monitorovat nezvyklé využití. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="9314-ac-4-information-flow-enforcement"></a>Vynucování toku informací 9.3.1.4 AC-4

Sdílení prostředků mezi zdroji (CORS) umožňuje, aby byly prostředky App Services požadovány z vnější domény. Microsoft doporučuje, abyste povolili interakci jenom požadovaných domén s rozhraním API, funkcí a webovými aplikacemi. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám umožní monitorovat omezení přístupu k prostředkům CORS v Azure Security Center.
Princip implementace CORS vám může pomáhat ověřit, jestli jsou implementované ovládací prvky toku informací.

- CORS by neměl umožňovat každému prostředku přístup k vaší webové aplikaci

## <a name="9315-ac-5-separation-of-duties"></a>9.3.1.5 AC-5 oddělení povinností

Jenom jeden vlastník předplatného Azure nepovoluje redundanci správy. I když má příliš mnoho vlastníků předplatného Azure, může dojít k navýšení potenciálu pro porušení zabezpečení prostřednictvím účtu napadeného vlastníka. Tento podrobný plán vám pomůže zachovat příslušný počet vlastníků předplatného Azure přiřazením [Azure Policy](../../../policy/overview.md) definice, které auditují počet vlastníků předplatných Azure. Tento podrobný plán také přiřadí Azure Policy definice, které vám pomůžou s řízením členství ve skupině Administrators na virtuálních počítačích s Windows. Správa vlastníka předplatného a oprávnění správce virtuálních počítačů vám může pomáhat s implementací vhodného oddělení funkcí.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- Auditovat virtuální počítače s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Auditovat virtuální počítače s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.

## <a name="9316-ac-6-7-least-privilege--review-of-user-privileges"></a>9.3.1.6 AC-6 (7) nejnižší oprávnění | Kontrola uživatelských oprávnění

Azure implementuje [řízení přístupu na základě role Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) , které vám pomůžou se správou toho, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice pro audit účtů, které by měly být v určitém pořadí pro kontrolu. Kontrola těchto indikátorů účtu vám umožní zajistit, aby byly implementované nejméně ovládací prvky pro oprávnění.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- Auditovat virtuální počítače s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Auditovat virtuální počítače s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.

## <a name="93112-ac-17-1-remote-access--automated-monitoring--control"></a>9.3.1.12 AC – 17 (1) vzdálený přístup | Automatizované monitorování a řízení

Tento podrobný plán vám pomůže s monitorováním a řízením vzdáleného přístupu přiřazením [Azure Policy](../../../policy/overview.md) definic pro monitorování, že vzdálené ladění pro Azure App Service aplikace je vypnuté. Podrobný plán také přiřazuje definice zásad, které auditují virtuální počítače se systémem Linux, které umožňují vzdálená připojení z účtů bez hesel. Plán navíc přiřadí definici Azure Policy, která vám pomůže monitorovat neomezený přístup k účtům úložiště. Monitorování těchto indikátorů vám umožní zajistit, aby metody vzdáleného přístupu dodržovaly vaše zásady zabezpečení.

- \[Preview \] : Auditovat virtuální počítače Linux, které umožňují vzdálená připojení z účtů bez hesel
- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů se systémem Linux, které umožňují vzdálená připojení z účtů bez hesla
- Auditování neomezeného síťového přístupu k účtům úložiště
- Pro aplikaci API by mělo být vypnuto vzdálené ladění.
- Vzdálené ladění by mělo být pro Function App vypnuté.
- Vzdálené ladění by mělo být pro webovou aplikaci vypnuté.

## <a name="9313-au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>9.3.1.3 AU-3 (2) obsah auditu záznamů | Centralizovaná správa obsahu plánovaného záznamu auditu

Data protokolu shromážděná pomocí Azure Monitor jsou uložená v pracovním prostoru Log Analytics, který umožňuje centralizovanou konfiguraci a správu. Tento podrobný plán vám pomůže zajistit, aby se události protokoloval pomocí přiřazování [Azure Policy](../../../policy/overview.md) definic, které auditují a vynutily nasazení agenta Log Analytics na virtuálních počítačích Azure.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- \[Preview \] : Audit log Analyticsho nasazení agenta v VMSS-VM Image (OS) bez seznamu
- \[Preview \] : Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestav
- \[Verze Preview \] : nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- \[Verze Preview \] : nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows

## <a name="9335-au-5-response-to-audit-processing-failures"></a>9.3.3.5 odpověď AU-5 na selhání zpracování auditu

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které monitorují konfigurace auditu a protokolování událostí. Monitorování těchto konfigurací může poskytnout indikátor selhání systému nebo chybnou konfiguraci a pomáhat s provedením nápravných akcí.

- Auditování nastavení diagnostiky
- Auditovat nastavení auditování na úrovni SQL serveru
- Na spravovaných instancích by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat

## <a name="9336-au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>9.3.3.6 AU-6 (4) revize auditu, analýza a vytváření sestav | Centrální kontrola a analýza

Data protokolu shromážděná pomocí Azure Monitor jsou uložena v pracovním prostoru Log Analytics umožňující centralizované generování sestav a analýzu. Tento podrobný plán vám pomůže zajistit, aby se události protokoloval pomocí přiřazování [Azure Policy](../../../policy/overview.md) definic, které auditují a vynutily nasazení agenta Log Analytics na virtuálních počítačích Azure.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- \[Preview \] : Audit log Analyticsho nasazení agenta v VMSS-VM Image (OS) bez seznamu
- \[Preview \] : Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestav
- \[Verze Preview \] : nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- \[Verze Preview \] : nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows

## <a name="93311-au-12-audit-generation"></a>9.3.3.11 AU – 12. generace auditu

Tento podrobný plán vám pomůže zajistit, aby byly systémové události zaznamenávány přiřazením [Azure Policy](../../../policy/overview.md) definicí, které auditují nastavení protokolů v prostředcích Azure. Tyto definice zásad auditují a vynutily nasazení Log Analytics agenta na virtuálních počítačích Azure a konfiguraci nastavení auditu pro jiné typy prostředků Azure. Tyto definice zásad také auditují konfiguraci diagnostických protokolů a poskytují přehled o operacích, které se provádějí v rámci prostředků Azure. Kromě toho jsou na SQL serverech nakonfigurovaná auditování a rozšířené zabezpečení dat.

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- \[Preview \] : Audit log Analyticsho nasazení agenta v VMSS-VM Image (OS) bez seznamu
- \[Preview \] : Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestav
- \[Verze Preview \] : nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux
- \[Verze Preview \] : nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)
- \[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows
- Auditování nastavení diagnostiky
- Auditovat nastavení auditování na úrovni SQL serveru
- Na spravovaných instancích by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- Nasazení auditování na SQL serverech
- Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě

## <a name="9357-cm-7-2-least-functionality--prevent-program-execution"></a>9.3.5.7 CM – 7 (2) nejméně funkcí | Zabránit spuštění programu

Adaptivní řízení aplikací v Azure Security Center je inteligentní a automatizované řešení pro filtrování aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací může běžet v režimu vynucení, který zakazuje spuštění neschválené aplikace. Tento podrobný plán přiřadí definici Azure Policy, která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="9357-cm-7-5-least-functionality--authorized-software--whitelisting"></a>9.3.5.7 CM-7 (5) nejméně funkcí | Autorizovaný software/seznam povolených

Adaptivní řízení aplikací v Azure Security Center je inteligentní a automatizované řešení pro filtrování aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací pomáhá vytvořit schválené seznamy aplikací pro virtuální počítače. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="93511-cm-11-user-installed-software"></a>9.3.5.11 CM – 11 User-Installed software

Adaptivní řízení aplikací v Azure Security Center je inteligentní a automatizované řešení pro filtrování aplikací, které může blokovat nebo bránit spuštění určitého softwaru na virtuálních počítačích. Řízení aplikací vám může pomáhat vymáhat a monitorovat dodržování zásad omezení softwaru. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, ve kterých se doporučuje seznam povolených aplikací, ale ještě není nakonfigurovaný.

- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="9366-cp-7-alternate-processing-site"></a>Lokalita s alternativním zpracováním 9.3.6.6 CP-7

Azure Site Recovery replikuje úlohy běžící na virtuálních počítačích z primárního umístění do sekundárního umístění. Pokud dojde k výpadku v primární lokalitě, zatížení se v rámci sekundárního umístění převezme. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která Audituje virtuální počítače bez nakonfigurovaného zotavení po havárii. Monitorování tohoto indikátoru vám může pomáhat zajistit, aby byly potřebné pohotovostní kontroly na místě.

- Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii

## <a name="9372-ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>9.3.7.2 IA-2 (1) identifikace a ověřování (uživatelé organizace) | Síťový přístup k privilegovaným účtům

Tento podrobný plán vám pomůže omezit a řídit privilegovaný přístup tím, že přiřadíte definice [Azure Policy](../../../policy/overview.md) k auditování účtů s oprávněním vlastníka nebo zápisu, u kterých není povolené Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA

## <a name="9372-ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>9.3.7.2 IA-2 (2) identifikace a ověřování (uživatelé organizace) | Přístup k síti bez privilegovaných účtů

Tento podrobný plán vám pomůže omezit a řídit přístup přiřazením definice [Azure Policy](../../../policy/overview.md) k auditování účtů s oprávněním ke čtení, u kterých není povolené ověřování pomocí služby Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.

## <a name="9375-ia-5-authenticator-management"></a>Správa ověřovacích dat 9.3.7.5 IA-5

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které auditují virtuální počítače se systémem Linux, které umožňují vzdálená připojení z účtů bez hesla nebo mají nastavená nesprávná oprávnění pro soubor passwd. Tento podrobný plán také přiřazuje definice zásad, které auditují konfiguraci typu šifrování hesla pro virtuální počítače s Windows. Monitorování těchto ukazatelů vám pomůže zajistit, aby ověřovatelé systému dodržovali zásady pro identifikaci a ověřování vaší organizace.

- \[Preview \] : Auditovat virtuální počítače Linux, které nemají oprávnění k souboru passwd nastavené na 0644
- \[Verze Preview \] : Auditovat virtuální počítače se systémem Linux, které mají účty bez hesla
- \[Verze Preview \] : Auditovat virtuální počítače s Windows, které neukládají hesla pomocí reverzibilního šifrování
- \[Verze Preview \] : nasaďte požadavky na auditování virtuálních počítačů se systémem Linux, které nemají oprávnění k souboru passwd nastavené na 0644.
- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů se systémem Linux, které mají účty bez hesla
- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů s Windows, které neukládají hesla pomocí reverzibilního šifrování

## <a name="9375-ia-5-1-authenticator-management--password-based-authentication"></a>9.3.7.5 IA-5 (1) Správa ověřovatele | Ověřování Password-Based

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

## <a name="93143-ra-5-vulnerability-scanning"></a>prověřování ohrožení zabezpečení 9.3.14.3 RA-5

Tento podrobný plán vám pomůže spravovat chyby zabezpečení systému pomocí přiřazení [Azure Policy](../../../policy/overview.md) definicí, které sledují chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center. Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase. Tento podrobný plán také přiřazuje definice zásad, které auditují a vynutily pokročilou zabezpečení dat na SQL serverech. Pokročilé zabezpečení dat zahrnuje posouzení ohrožení zabezpečení a rozšířené možnosti ochrany před internetovými útoky, které vám pomůžou pochopit ohrožení zabezpečení v nasazených prostředcích.

- Na spravovaných instancích by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Oprava ohrožení zabezpečení v konfiguraci zabezpečení na virtuálních počítačích by měla být opravena
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="93164-sc-5-denial-of-service-protection"></a>9.3.16.4 SC-5 – odmítnutí služby Service Protection

Úroveň Standard Azure distributed denial of DDoS () poskytuje další funkce a možnosti zmírnění služeb úrovně Basic. Mezi tyto další funkce patří Azure Monitor integrace a možnost kontrolovat zprávy o zmírnění útoků po útoku. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která Audituje, jestli je povolená úroveň Standard DDoS. Porozumět rozdílům schopností mezi úrovněmi služeb vám pomůžou vybrat nejlepší řešení pro řešení odepření ochrany služeb pro vaše prostředí Azure.

- Měla by být povolená DDoS Protection Standard.

## <a name="93165-sc-7-boundary-protection"></a>9.3.16.5 SC-7 – Ochrana hranic

Tento podrobný plán vám pomůže spravovat a řídit hranici systému tím, že přiřadí definici [Azure Policy](../../../policy/overview.md) , která monitoruje doporučení pro posílení zabezpečení skupiny zabezpečení sítě v Azure Security Center. Azure Security Center analyzuje modely provozu virtuálních počítačů s internetem a poskytuje doporučení pro pravidlo skupiny zabezpečení sítě, aby se snížila potenciální plocha pro útok.
Kromě toho tento podrobný plán také přiřazuje definice zásad, které sledují nechráněné koncové body, aplikace a účty úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem, můžou dovolit neúmyslný přístup k informacím obsaženým v informačním systému.

- Pravidla skupiny zabezpečení sítě pro virtuální počítače s přístupem k Internetu by měla být zesílená.
- Přístup přes internetový koncový bod by měl být omezený.
- Pravidla skupin zabezpečení sítě pro webové aplikace v IaaS by se měla posílit.
- Auditování neomezeného síťového přístupu k účtům úložiště

## <a name="93165-sc-7-3-boundary-protection--access-points"></a>Ochrana hranic 9.3.16.5 SC-7 (3) | Přístupové body

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT vám pomůže omezit počet externích připojení k vašim prostředkům v Azure. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="93165-sc-7-4-boundary-protection--external-telecommunications-services"></a>Ochrana hranic 9.3.16.5 SC-7 (4) | Externí telekomunikační služby

Přístup k virtuálnímu počítači za běhu zamkne příchozí provoz do virtuálních počítačů Azure. tím se snižuje riziko útoků na útoky a současně zajišťuje snadný přístup pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT vám pomůže spravovat výjimky pro zásady toku provozu tím, že usnadňují procesy přístupu a žádosti o přístup. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat virtuální počítače, které můžou podporovat přístup za běhu, ale ještě nejsou nakonfigurované.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="96163-sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Důvěrnost a integrita přenosu 9.6.16.3 SC-8 (1) | Kryptografická nebo alternativní fyzická ochrana

Tento podrobný plán vám pomůže chránit důvěrnou a integritu odesílaných informací tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které vám pomůžou monitorovat kryptografické mechanismy implementované pro komunikační protokoly. Zajištění správné šifry komunikace vám může přispět k splnění požadavků vaší organizace nebo ochraně informací před neoprávněným zveřejněním a úpravou.

- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Auditovat webové servery systému Windows, které nepoužívají protokoly zabezpečených komunikací
- Nasaďte požadavky na auditovat webové servery Windows, které nepoužívají protokoly zabezpečené komunikace.
- Function App by měl být přístupný jenom přes HTTPS
- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Webová aplikace by měla být přístupná jen přes protokol HTTPS

## <a name="93166-sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>9.3.16.6 SC-28 (1) Ochrana informací v klidovém znění | Kryptografická ochrana

Tento podrobný plán vám pomůže vyhovět zásadám používání ovládacích prvků cryptograph k ochraně informací v klidovém prostředí tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které vynutily konkrétní ovládací prvky cryptograph a auditují použití slabého nastavení kryptografie. Porozumět tomu, kde vaše prostředky Azure můžou mít neoptimální Kryptografické konfigurace, můžou podniknout nápravné akce, které zajistí konfiguraci prostředků v souladu s vašimi zásadami zabezpečení informací. Konkrétně definice zásad přiřazené tímto plánem vyžaduje šifrování pro účty Data Lake Storage. vyžadovat transparentní šifrování dat na databázích SQL; a auditovat chybějící šifrování pro databáze SQL, disky virtuálních počítačů a proměnné účtu Automation.

- Na spravovaných instancích by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Nasazení pokročilých zabezpečení dat na SQL serverech
- Nasazení transparentního šifrování dat databáze SQL
- Na virtuálních počítačích by se mělo použít šifrování disku
- Vyžadovat šifrování u Data Lake Store účtů
- Je třeba povolit transparentní šifrování dat databází SQL.

## <a name="93172-si-2-flaw-remediation"></a>9.3.17.2 náprava chyby a až 2

Tento podrobný plán vám pomůže spravovat chyby v systému tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které monitorují chybějící aktualizace systému, chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center. Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase. V tomto podrobném plánu se taky přiřadí definice zásady, která zajišťuje opravy operačního systému pro sady škálování virtuálních počítačů.

- Vyžadovat automatické opravy imagí operačního systému na Virtual Machine Scale Sets
- Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.
- Na virtuálních počítačích by se měly nainstalovat aktualizace systému
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Oprava ohrožení zabezpečení v konfiguraci zabezpečení na virtuálních počítačích by měla být opravena
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="93173-si-3-malicious-code-protection"></a>9.3.17.3 Ochrana proti škodlivým kódem SI. 3

Tento podrobný plán vám pomůže spravovat ochranu koncových bodů včetně ochrany škodlivých kódů tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které monitorují chybějící Endpoint Protection na virtuálních počítačích v Azure Security Center a vynutila řešení Microsoft Antimalware na virtuálních počítačích s Windows.

- Nasadit výchozí rozšíření Microsoft IaaSAntimalware pro Windows Server
- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.
- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="93173-si-3-1-malicious-code-protection--central-management"></a>9.3.17.3 ochranu proti škodlivým kódem SI pokaždé 3 (1) | Centrální správa

Tento podrobný plán vám pomůže spravovat službu Endpoint Protection, včetně ochrany škodlivých kódů, přiřazením [Azure Policy](../../../policy/overview.md) definic, které monitorují chybějící Endpoint Protection na virtuálních počítačích v Azure Security Center. Azure Security Center poskytuje centralizované možnosti správy a vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase.

- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.
- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="93174-si-4-information-system-monitoring"></a>Monitorování systému 9.3.17.4 SI o tom SI 4

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

## <a name="93174-si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>9.3.17.4 a 4 (18) sledování systému informací | Analýza provozu/tajnosti exfiltrace

Rozšířená ochrana před internetovými útoky pro Azure Storage detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Výstrahy ochrany zahrnují vzory přístupu k neobvyklé, neobvyklé extrahování a nahrávání a podezřelou aktivitu úložiště. Tyto indikátory vám pomůžou detekovat exfiltrace informací.

- Nasazení rozšířené ochrany před internetovými útoky na účty úložiště

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit. 

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacího prvku pro plán 1075, Projděte si následující články, kde se dozvíte o podrobném plánu a způsobu nasazení této ukázky:

> [!div class="nextstepaction"]
> [Finanční úřad pro 1075 – přehled](./index.md) 
>  [Finanční úřad finančního úřadu 1075 – postup nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)