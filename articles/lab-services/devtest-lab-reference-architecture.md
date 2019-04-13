---
title: Referenční architektura pro Azure DevTest Labs pro podniky
description: Tento článek poskytuje doprovodné materiály k architektuře odkazu pro Azure DevTest Labs v podniku.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 61e76369a4d73bd171c9e5c2462b3f261681ba00
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551379"
---
# <a name="azure-devtest-labs---reference-architecture-for-an-enterprise"></a>Azure DevTest Labs – referenční architektury pro podniky
Tento článek obsahuje referenční architekturu pro nasazení řešení založené na službě Azure DevTest Labs v podniku. Její součástí jsou v místním připojením přes Express Route, Brána vzdálené plochy pro vzdálené přihlášení k virtuálním počítačům, připojení k jako úložiště artefaktů pro privátní artefakty a dalších služeb PaaS používaných v testovacím prostředí.

![Referenční architektura](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektura
Klíčové prvky v referenční architektuře jsou:

- **Azure Active Directory (AAD)**: Pomocí Azure DevTest Labs [služby Azure Active Directory pro správu identit](../active-directory/fundamentals/active-directory-whatis.md). Existují dvě klíčové věci k uvážení při poskytování přístupu k prostředí založené na službě DevTest Labs pro uživatele:
    - **Správa prostředků**:  Poskytuje přístup k webu Azure portal ke správě prostředků (vytvořte virtuální počítače, vytvořte prostředí, spuštění/zastavení nebo restartování/delete/applyartifacts a tak dále). To se provádí v Azure pomocí řízení přístupu na základě Roble (RBAC) a použitím přiřazení role pro uživatele, nastavení prostředků a oprávnění na úrovni přístupu.
    - **Virtual machines (na úrovni sítě)**:  Ve výchozí konfiguraci virtuální počítače používat účet místního správce.  Pokud není k dispozici k doméně ([služby AAD Domain services](../active-directory-domain-services/active-directory-ds-overview.md), doméně místní nebo cloudové doméně), počítačů, může být připojen k doméně. Jakmile připojený, by uživatelé používat své identity založené na doméně pro připojení k virtuálním počítačům.
- **On-Premises připojení**: Diagram architektury výše [Express Route](../expressroute/expressroute-introduction.md) i použít, ale můžete použít také [Site-to-Site VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). I když to není nutné pro DevTest Labs, je obvykle vidět v podnicích. Vyžaduje se pouze pokud je důvod pro připojení k podnikovým prostředkům. Běžné důvody jsou: 
    - Místní data, která nemůže být přesunout na cloud
    - Předvolby testovacího prostředí virtuálních počítačů připojit k místní doméně
    - Vynucení veškerý síťový provoz do a z prostředí cloud prostřednictvím místní bráně firewall z důvodů zabezpečení nebo dodržování předpisů
- **Skupiny zabezpečení sítě**: Běžný způsob, jak omezit přenosy ke cloudovému prostředí (nebo v cloudovém prostředí) založené na zdrojové a cílové IP adresy, je použít [skupinu zabezpečení sítě](../virtual-network/security-overview.md). Například umožňuje pouze síťový provoz pocházející z podnikové sítě do testovacího prostředí sítě.
- **Brána vzdálené plochy**:  Podniky obvykle blokuje odchozí připojení ke vzdálené ploše v podnikové brány firewall. Pokud chcete povolit připojení ke cloudové prostředí ve službě DevTest Labs, máte několik možností, například [Brána vzdálené plochy](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture) (seznam povolených statickou IP adresu nástroje pro vyrovnávání zatížení brány) nebo [směrovat všechny příchozí Provoz protokolu RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) přes připojení Express Route nebo Site-to-Site VPN. Je běžné zvážit při plánování nasazení služby DevTest Labs v podnikové síti.
- **Sítě Azure (virtuální sítě, podsítě)**:  [Sítě Azure](../networking/networking-overview.md) topologie je jiný klíčovým elementem v DevTest Labs architektury. Umožňuje prostředky z labs komunikovat (nebo ne), přístup k místnímu (nebo nemusíte) a přístup k Internetu (nebo nemusíte). Diagram architektury zahrnuje nejběžnější způsob, jak zákazníci používají DevTest Labs (připojené přes všechny testovací prostředí [VNet Peering](../virtual-network/virtual-network-peering-overview.md) pomocí [centra s paprsky modelu](/architecture/reference-architectures/hybrid-networking/hub-spoke) připojení Express Route nebo Site-to-Site VPN v místním prostředí), ale od DevTest Labs pomocí sítě Azure přímo nejsou k dispozici nějaká omezení, o tom, jak nastavit síťové infrastruktury.
- **DevTest Labs**:  DevTest Labs je klíčovou součástí architektury. Další informace o službě, naleznete v tématu [DevTest Labs](devtest-lab-overview.md).
- **Virtuální počítače a dalších prostředků (SaaS, PaaS, IaaS)**:  Mezi klíčové úlohy podporována DevTest Labs jsou virtuální počítače společně s další prostředky Azure.  DevTest Labs usnadňuje a zrychluje podnikům poskytnout přístup k prostředkům Azure (včetně virtuálních počítačů a dalších prostředků Azure).  Další informace o přístup k Azure pro [vývojáři](devtest-lab-developer-lab.md) a [testeři](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Aspekty zabezpečení
I když DevTest Labs neobsahuje žádné předdefinované kvóty nebo limity, dalších prostředků Azure se používají v typické operace testovacího prostředí mají [kvóty na úrovni předplatného](../azure-subscription-service-limits.md). V důsledku toho v typické podnikové nasazení, musí mít více předplatných Azure, aby pokryl velké nasazení služby DevTest Labs. Kvóty nejčastěji dostane podniky jsou:

- **Skupiny prostředků**:  Ve výchozí konfiguraci DevTest Labs vytvoří skupinu prostředků pro každý nový virtuální počítač nebo prostředí, které uživatel vytvoří pomocí služby. Může obsahovat předplatná [maximální 980 skupin prostředků](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager), takže toto omezení je horní limit počtu virtuálních počítačů a prostředí v rámci předplatného. Existují dva další konfigurace, které byste měli zvážit:
    - **[Všechny virtuální počítače přejít do stejné skupiny prostředků](resource-group-control.md)**:  I když to vám pomůže s limitem skupiny prostředků, má vliv na typ prostředku na limitu skupiny prostředků.
    - **Použití sdílených veřejné IP adresy**:  Všechny virtuální počítače stejnou velikost a stejné oblasti, přejděte do stejné skupiny prostředků. Pokud virtuální počítače můžou být veřejné IP adresy je "základu střední" mezi kvóty skupiny prostředků a typ prostředku za kvóty skupiny prostředků. 
- **Skupina prostředků pro každý prostředek na typ prostředku**: Výchozí limit pro [prostředků pro každý prostředek skupiny na typ prostředku je 800](../azure-subscription-service-limits.md#resource-group-limits).  Při použití všechny virtuální počítače, přejděte na stejné konfigurace skupin prostředků, uživatelé se dostanou tento limit předplatného mnohem rychleji, zejména v případě, že virtuální počítače mají mnoho dalších disků.
- **Účty úložiště**: Testovací prostředí v DevTest Labs se dodává s účtem služby storage a Azure kvótu pro [počet účtů úložiště na oblast a předplatné je 250](../azure-subscription-service-limits.md#storage-limits). To znamená, že horní limit pro počet DevTest Labs ve stejné oblasti je také 250.
- **Přiřazení rolí**: Přiřazení role je poskytnout uživatel nebo instanční objekt přístup k prostředku (vlastník, prostředek, úroveň oprávnění). V Azure, existuje [limit 2000 přiřazení rolí na jedno předplatné](../azure-subscription-service-limits.md#role-based-access-control-limits). Služba DevTest Labs (ve výchozím nastavení) vytvoří skupinu prostředků pro každý virtuální počítač a udělí se vlastník **vlastníka** oprávnění pro virtuální počítač DevTest Labs a **čtečky** oprávnění Skupina prostředků.  Tímto způsobem používá každý nově vytvořený virtuální počítač dvě přiřazení rolí k přiřazení role vytvořené při udělení uživatelských oprávnění k testovacím prostředí.
- **Rozhraní API čtení/zápisy**: K automatizaci Azure DevTest Labs a existují různé způsoby (rozhraní REST API, Powershellu, rozhraní příkazového řádku, sady Azure SDK a tak dále) a prostřednictvím automatizace je možné zadat jinou limitu požadavků rozhraní API. Každé předplatné umožňuje až [12000 přečtěte si požadavky a 1200 požadavků na zápis na za hodinu](../azure-resource-manager/resource-manager-request-limits.md).  Je limit, které je potřeba při automatizaci DevTest Labs.

## <a name="manageability-considerations"></a>Aspekty správy
DevTest Labs má rozhraní skvělé administrativní uživatel při práci s jednoho testovacího prostředí. V podniku je pravděpodobně více předplatných Azure a mnoha labs. To znamená, že konzistentně provádění změn pro všechny testovací prostředí vyžaduje/automation.  Tady jsou některé příklady a nejlepší způsoby, jak spravovat nasazení DevTest Labs:

- **Změny v nastavení správy lab**: Běžný scénář, kdy se aktualizovat nastavení konkrétní testovacího prostředí ve všech testovacích prostředí v nasazení. Například novou velikost instance virtuálního počítače je k dispozici a k tomu, aby se musí aktualizovat všechny testovací prostředí.  Doporučujeme automatizovat tyto změny pomocí skriptů prostředí Azure PowerShell, rozhraní příkazového řádku Azure nebo rozhraní REST API.  
- **Token pat úložiště artefaktů**:  Osobní přístupové tokeny pro úložiště Git obvykle vyprší (90 dnů, po dobu 1 roku 2 let). K zajištění kontinuity podnikových procesů, je potřeba rozšířit osobní přístupový token nebo vytvořte novou a provést novou osobní přístupový token všech testovacích prostředí pomocí automatizace.
- **Omezení změn k nastavení testovacího prostředí**:  Je často případ tam, kde je k dispozici konkrétní nastavení (například povolení Marketplace bitové kopie má být použit), který musí být s omezeným přístupem. To můžete udělat buď prostřednictvím Azure policy (zabránění změny do tohoto typu prostředku) nebo vytvořením vlastní role a poskytnutí této role místo 'owner' testovací prostředí. To lze provést většinu nastavení v testovacím prostředí (interní podpory, oznámení testovacího prostředí, velikosti virtuálních počítačů povoleny atd.)
- **Vyžadování, virtuální počítače vhodné používat takové názvy**: Je běžné žádosti pro snadnější identifikaci virtuálních počítačů, které jsou součástí cloudové vývojové a testovací prostředí. Můžete tak učinit [službou Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Je důležité si uvědomit, že DevTest Labs používá základní prostředky v Azure (síť, disky, výpočetní funkce, tak dále), které se spravují stejným způsobem jako v Azure.  Zásady Azure se vztahuje k virtuálním počítačům vytvořeným v testovacím prostředí. Azure Security center může hlásit dodržování předpisů virtuálního počítače. Služba Azure Backup můžete poskytnout pravidelného zálohování pro virtuální počítače v testovacím prostředí a tak dále. 

## <a name="security-considerations"></a>Aspekty zabezpečení
Azure DevTest Labs používá stávající prostředky v Azure (výpočetní, síťové a tak dále) a tím automaticky využívá výhod všechny skvělé funkce zabezpečení integrované do platformy. Například pokud chcete zabezpečit všechny příchozí připojení ke vzdálené ploše pouze pocházejí z podnikové sítě, je stejně jednoduché jako přidání skupiny zabezpečení sítě k virtuální síti Brána vzdálené plochy. Jenom zvážit další aspekty zabezpečení pro Azure DevTest Labs je úroveň oprávnění k dispozici pro členy týmu, kteří budou používat Tato praktická cvičení na každodenní bázi.  Jsou běžné oprávnění udělená ["Vlastník" a "Uživatel služby DevTest Labs"](devtest-lab-add-devtest-user.md). Další informace o těchto rolích najdete v tématu [přidat vlastníky a uživatele ve službě Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Další kroky
Zobrazit další článek v této sérii: [Škálovat infrastrukturu s Azure DevTest Labs](devtest-lab-guidance-scale.md)
