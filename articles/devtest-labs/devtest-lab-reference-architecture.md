---
title: Referenční architektura podniku pro Azure DevTest Labs
description: Tento článek poskytuje pokyny pro referenční architekturu pro Azure DevTest Labs v podniku.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e0791fb6c4de3da8108ffbd296c681f993c6b6cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91367746"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Referenční architektura Azure DevTest Labs pro podniky
Tento článek popisuje referenční architekturu, která vám může pomáhat s nasazením řešení na základě Azure DevTest Labs v podniku. Zahrnuje následující:
- Místní připojení prostřednictvím Azure ExpressRoute
- Brána vzdálené plochy pro vzdálené přihlášení k virtuálním počítačům
- Připojení k úložišti artefaktů pro privátní artefakty
- Další služby PaaS, které se používají v laboratořích

![Diagram referenční architektury](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektura
Toto jsou klíčové prvky referenční architektury:

- **Azure Active Directory (Azure AD)**: DevTest Labs používá [službu Azure AD pro správu identit](../active-directory/fundamentals/active-directory-whatis.md). Tyto dvě klíčové aspekty zvažte, když uživatelům udělíte přístup k prostředí založenému na DevTest Labs:
    - **Správa prostředků**: poskytuje přístup k Azure Portal ke správě prostředků (vytváření virtuálních počítačů, vytváření prostředí, spouštění, zastavování, restartování, odstraňování a uplatnění artefaktů atd.). Správa prostředků se provádí v Azure pomocí řízení přístupu na základě role (RBAC). Role přiřadíte uživatelům a nastavíte oprávnění na úrovni prostředků a přístupu.
    - **Virtuální počítače (na úrovni sítě)**: ve výchozí konfiguraci používají virtuální počítače účet místního správce. Pokud je k dispozici doména ([Azure AD Domain Services](../active-directory-domain-services/overview.md), místní doména nebo cloudová doména), můžete počítače připojit k doméně. Uživatelé pak můžou k připojení k virtuálním počítačům používat své identity založené na doméně.
- **Místní připojení**: v našem diagramu architektury se používá [ExpressRoute](../expressroute/expressroute-introduction.md) . Můžete ale také použít síť [VPN typu Site-to-site](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). I když se ExpressRoute pro DevTest Labs nevyžaduje, obvykle se používá v podnicích. ExpressRoute se vyžaduje jenom v případě, že potřebujete přístup k podnikovým prostředkům. Mezi běžné scénáře patří:
    - Máte místní data, která se nedají přesunout do cloudu.
    - Upřednostňujete připojení virtuálních počítačů testovacího prostředí k místní doméně.
    - Chcete vynutit veškerý síťový provoz v cloudovém prostředí a z něj prostřednictvím místní brány firewall pro zabezpečení/dodržování předpisů.
- **Skupiny zabezpečení sítě**: běžný způsob, jak omezit provoz do cloudového prostředí (nebo v cloudovém prostředí) založeného na zdrojové a cílové IP adrese, je použití [skupiny zabezpečení sítě](../virtual-network/security-overview.md). Například chcete v sítích testovacího prostředí, které pocházejí z podnikové sítě, umožňovat pouze provoz.
- **Brána vzdálené plochy**: podniky obvykle blokují odchozí připojení ke vzdálené ploše v podnikové bráně firewall. Existuje několik možností, jak povolit připojení ke cloudovém prostředí v DevTest Labs, včetně těchto:
  - Použijte [bránu Vzdálená plocha](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)a povolte statickou IP adresu nástroje pro vyrovnávání zatížení brány.
  - [Nasměrujte veškerý příchozí provoz protokolu RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) prostřednictvím připojení ExpressRoute/site-to-Site VPN. Tato funkce je běžně zvážena v případě, že podniky naplánují nasazení DevTest Labs.
- **Síťové služby (virtuální sítě, podsítě)**: síťová topologie [Azure](../networking/networking-overview.md) je další klíčovým prvkem v architektuře DevTest Labs. Řídí, jestli prostředky z testovacího prostředí můžou komunikovat a mít přístup k místnímu a Internetu. Náš diagram architektury zahrnuje nejběžnější způsoby, jak zákazníci používají DevTest Labs: všechny laboratoře se připojují prostřednictvím [partnerského vztahu virtuálních sítí](../virtual-network/virtual-network-peering-overview.md) pomocí [modelu](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) hvězdicové sítě pro připojení VPN typu ExpressRoute/site-to-site k místnímu umístění. DevTest Labs ale používá Azure Virtual Network přímo, takže neexistují žádná omezení způsobu nastavení síťové infrastruktury.
- **DevTest Labs**: DevTest Labs je klíčovou součástí celkové architektury. Další informace o této službě najdete v tématu [o DevTest Labs](devtest-lab-overview.md).
- **Virtuální počítače a další prostředky (SaaS, PaaS, IaaS)**: virtuální počítače jsou klíčovou úlohou, kterou DevTest Labs podporuje spolu s dalšími prostředky Azure. DevTest Labs usnadňuje a urychluje podnikové poskytování přístupu k prostředkům Azure (včetně virtuálních počítačů a dalších prostředků Azure). Přečtěte si další informace o přístupu k Azure pro [vývojáře](devtest-lab-developer-lab.md) a [testery](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Aspekty zabezpečení
I když DevTest Labs nemá předdefinované kvóty nebo limity, další prostředky Azure, které se používají při typickém provozu testovacího prostředí, mají [kvóty na úrovni předplatného](../azure-resource-manager/management/azure-subscription-service-limits.md). V typickém podnikovém nasazení tedy budete potřebovat několik předplatných Azure, které pokrývají velké nasazení DevTest Labs. K kvótám, které se v podnicích nejčastěji dosahují:

- **Skupiny prostředků**: ve výchozí konfiguraci vytvoří DevTest Labs skupinu prostředků pro každý nový virtuální počítač, nebo uživatel vytvoří prostředí pomocí služby. Předplatná můžou obsahovat [až 980 skupin prostředků](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). To znamená, že se jedná o limit virtuálních počítačů a prostředí v rámci předplatného. Existují dvě další konfigurace, které byste měli zvážit:
    - **[Všechny virtuální počítače jdou do stejné skupiny prostředků](resource-group-control.md)**: i když vám tato instalace pomůže splnit omezení skupiny prostředků, ovlivňuje omezení typu prostředku podle prostředků.
    - **Použití sdílených veřejných IP adres**: všechny virtuální počítače se stejnou velikostí a oblastí jdou do stejné skupiny prostředků. Tato konfigurace je střední deska mezi kvótami skupin prostředků a kvótami typu prostředku a skupiny prostředků, pokud mají virtuální počítače povoleno mít veřejné IP adresy.
- **Prostředky na skupinu prostředků na typ prostředku**: výchozí limit pro [prostředky na skupinu prostředků na typ prostředku je 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Když použijete *všechny virtuální počítače ve stejné konfiguraci skupiny prostředků* , uživatelé budou mít tento limit předplatného mnohem dřív, zejména v případě, že virtuální počítače mají mnoho dalších disků.
- **Účty úložiště**: testovací prostředí v DevTest Labs se dodává s účtem úložiště. Kvóta Azure pro [počet účtů úložiště podle oblasti v rámci předplatného je 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Maximální počet DevTest Labs ve stejné oblasti je také 250.
- **Přiřazení rolí**: přiřazení role je způsob, jakým udělíte uživateli nebo hlavní přístup k prostředku (vlastník, prostředek, úroveň oprávnění). V Azure existuje [omezení 2 000 přiřazení rolí na předplatné](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-role-based-access-control-limits). Ve výchozím nastavení vytvoří služba DevTest Labs skupinu prostředků pro každý virtuální počítač. Vlastníkovi je uděleno oprávnění *vlastníka* pro virtuální počítač DevTest Labs a oprávnění *Čtenář* pro skupinu prostředků. Každý nově vytvořený virtuální počítač navíc používá přiřazení dvou rolí k přiřazením, která se používají, když uživatelům udělíte oprávnění k testovacímu prostředí.
- **Čtení a zápisy rozhraní API**: Existují různé způsoby, jak automatizovat Azure a DevTest Labs, včetně rozhraní REST API, PowerShellu, Azure CLI a sady Azure SDK. Prostřednictvím automatizace můžete získat další omezení požadavků rozhraní API: každé předplatné umožňuje až [12 000 požadavků na čtení a 1 200 požadavků na zápis za hodinu](../azure-resource-manager/management/request-limits-and-throttling.md). Při automatizaci DevTest Labs si pamatujte na toto omezení.

## <a name="manageability-considerations"></a>Aspekty správy
DevTest Labs má skvělé administrativní uživatelské rozhraní pro práci s jedním testovacím prostředím. Ale v podniku máte pravděpodobně několik předplatných Azure a mnoho laboratoří. Provádění změn konzistentně pro všechny vaše cvičení vyžaduje skriptování/automatizaci. Tady jsou některé příklady a osvědčené postupy správy pro nasazení DevTest Labs:

- **Změny nastavení testovacího prostředí**: běžným scénářem je aktualizovat konkrétní nastavení testovacího prostředí napříč všemi laboratořemi v nasazení. Například je k dispozici nová velikost instance virtuálního počítače a všechny laboratoře je nutné aktualizovat, aby byly povoleny. Tyto změny je vhodné automatizovat pomocí skriptů PowerShellu, rozhraní příkazového řádku nebo rozhraní REST API.  
- **Token osobního přístupového úložiště artefaktů**: obvykle vyprší platnost tokenů osobního přístupu pro úložiště Git za 90 dní, jeden rok nebo dva roky. Aby se zajistila kontinuita, je důležité, abyste rozšířili osobní přístupový token nebo si vytvořili nový. Pak pomocí automatizace aplikujte nový osobní přístupový token na všechny laboratoře.
- **Omezení změn nastavení testovacího prostředí**: často se musí omezit konkrétní nastavení (například povolení použití imagí na webu Marketplace). Pomocí Azure Policy můžete zabránit změnám typu prostředku. Případně můžete vytvořit vlastní roli a udělit uživatelům, aby ji místo role *vlastníka* testovacího prostředí vytvořili. To můžete provést pro většinu nastavení v testovacím prostředí (interní podpora, oznámení testovacího prostředí, povolené velikosti virtuálních počítačů atd.).
- **Vyžadovat, aby virtuální počítače dodržovaly zásady vytváření názvů**: manažeři často chtějí snadno identifikovat virtuální počítače, které jsou součástí cloudového vývojového a testovacího prostředí. To můžete provést pomocí [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Je důležité si uvědomit, že DevTest Labs používá základní prostředky Azure, které jsou spravované stejným způsobem: sítě, disky, výpočetní prostředky a tak dále. Například Azure Policy platí pro virtuální počítače, které jsou vytvořeny v testovacím prostředí. Azure Security Center může hlásit dodržování předpisů virtuálních počítačů. A služba Azure Backup může poskytovat pravidelné zálohování pro virtuální počítače v testovacím prostředí.

## <a name="security-considerations"></a>Důležité informace o zabezpečení
Azure DevTest Labs používá existující prostředky v Azure (výpočty, sítě a tak dále). Proto automaticky přináší výhody funkcí zabezpečení, které jsou součástí platformy. Pokud třeba chcete, aby příchozí připojení ke vzdálené ploše vycházela jenom z podnikové sítě, stačí přidat skupinu zabezpečení sítě do virtuální sítě v bráně Vzdálená plocha. Jediným dalším aspektem zabezpečení je úroveň oprávnění, která udělujete členům týmu, kteří používají cvičení na každodenní bázi. Nejběžnějšími oprávněními jsou [ *vlastník* a *uživatel*](devtest-lab-add-devtest-user.md). Další informace o těchto rolích najdete [v tématu Přidání vlastníků a uživatelů v Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Další kroky
Podívejte se na další článek v této sérii: [horizontální navýšení kapacity Azure DevTest Labs infrastruktury](devtest-lab-guidance-scale.md).
