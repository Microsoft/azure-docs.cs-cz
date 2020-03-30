---
title: Podniková referenční architektura pro Azure DevTest Labs
description: Tento článek obsahuje pokyny k referenční architektuře pro Azure DevTest Labs v rozlehlé síti.
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
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132842"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Referenční architektura Azure DevTest Labs pro podniky
Tento článek obsahuje referenční architekturu, která vám pomůže nasadit řešení založené na Azure DevTest Labs v podniku. Zahrnuje následující:
- Místní připojení přes Azure ExpressRoute
- Brána vzdálené plochy pro vzdálené přihlášení k virtuálním počítačům
- Připojení k úložišti artefaktů pro soukromé artefakty
- Další služby PaaS, které se používají v laboratořích

![Diagram referenční architektury](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektura
Jedná se o klíčové prvky referenční architektury:

- **Azure Active Directory (Azure AD):** DevTest Labs používá [službu Azure AD pro správu identit](../active-directory/fundamentals/active-directory-whatis.md). Zvažte tyto dva klíčové aspekty, když uživatelům uděláte přístup k prostředí založenému na devTest Labs:
    - **Správa prostředků**: Poskytuje přístup k portálu Azure ke správě prostředků (vytváření virtuálních počítačů; vytváření prostředí; spuštění, zastavení, restartování, odstranění a použití artefaktů a tak dále). Správa prostředků se provádí v Azure pomocí řízení přístupu na základě rolí (RBAC). Přiřazujete role uživatelům a nastavujete oprávnění na úrovni prostředků a přístupu.
    - **Virtuální počítače (úroveň sítě)**: Ve výchozí konfiguraci virtuální počítače používají účet místního správce. Pokud je k dispozici doména[(Služby Azure AD Domain Services](../active-directory-domain-services/overview.md), místní doména nebo doména na bázi cloudu), počítače se můžou připojit k doméně. Uživatelé pak mohou používat své identity založené na doméně pro připojení k virtuálním počítačům.
- **Místní připojení**: V diagramu architektury se používá [ExpressRoute.](../expressroute/expressroute-introduction.md) Ale můžete také použít [site-to-site VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Přestože ExpressRoute není vyžadováno pro DevTest Labs, se běžně používá v podnicích. ExpressRoute je vyžadovánpouze v případě, že potřebujete přístup k podnikovým prostředkům. Běžné scénáře jsou:
    - Máte místní data, která nelze přesunout do cloudu.
    - Dáváte přednost připojení virtuálních počítačů testovacího prostředí k místní doméně.
    - Chcete vynutit veškerý síťový provoz v cloudovém prostředí a z jeho okolí prostřednictvím místní brány firewall pro zabezpečení a dodržování předpisů.
- **Skupiny zabezpečení sítě**: Běžným způsobem, jak omezit provoz v cloudovém prostředí (nebo v cloudovém prostředí) na základě zdrojových a cílových adres IP, je použití [skupiny zabezpečení sítě](../virtual-network/security-overview.md). Chcete například povolit pouze přenosy, které pocházejí z podnikové sítě do sítí testovacího prostředí.
- **Brána vzdálené plochy**: Podniky obvykle blokují odchozí připojení ke vzdálené ploše na podnikové bráně firewall. Existuje několik možností, jak povolit připojení ke cloudovému prostředí v devTest Labs, včetně:
  - Použijte [bránu vzdálené plochy](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)a povolte statickou IP adresu nástroje pro vyrovnávání zatížení brány.
  - [Přímé všechny příchozí RDP provoz](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) přes ExpressRoute/site-site VPN připojení. Tato funkce je běžné při podniky plánovat nasazení DevTest Labs.
- **Síťové služby (virtuální sítě, podsítě):** Topologie [sítě Azure](../networking/networking-overview.md) je dalším klíčovým prvkem v architektuře DevTest Labs. Řídí, zda prostředky z testovacího prostředí mohou komunikovat a mít přístup k místnímu a internetu. Náš diagram architektury obsahuje nejběžnější způsoby, jak zákazníci používají DevTest Labs: Všechna testovací prostředí se připojují prostřednictvím [partnerského vztahu virtuální sítě](../virtual-network/virtual-network-peering-overview.md) pomocí [modelu s rozbočovačem](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) s připojením k síti VPN ExpressRoute/site-to-site k místnímu. Ale DevTest Labs používá Azure Virtual Network přímo, takže neexistují žádná omezení na to, jak nastavit síťovou infrastrukturu.
- **DevTest Labs**: DevTest Labs je klíčovou součástí celkové architektury. Další informace o službě najdete v tématu [O testovacích laboratořích](devtest-lab-overview.md).
- **Virtuální počítače a další prostředky (SaaS, PaaS, IaaS)**: Virtuální počítače jsou klíčové úlohy, které DevTest Labs podporuje spolu s dalšími prostředky Azure. DevTest Labs usnadňuje a rychle poskytuje podniku přístup k prostředkům Azure (včetně virtuálních počítačů a dalších prostředků Azure). Přečtěte si další informace o přístupu k Azure pro [vývojáře](devtest-lab-developer-lab.md) a [testery](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Aspekty zabezpečení
Přestože DevTest Labs nemá integrované kvóty nebo omezení, jiné prostředky Azure, které se používají v typické operace testovacího prostředí mají [kvóty na úrovni předplatného](../azure-resource-manager/management/azure-subscription-service-limits.md). Takže v typické maješky potřebujete více předplatných Azure, abyste pokryli velké nasazení DevTest Labs. Kvóty, kterých podniky nejčastěji dosahují, jsou:

- **Skupiny prostředků**: Ve výchozí konfiguraci DevTest Labs vytvoří skupinu prostředků pro každý nový virtuální počítač nebo uživatel vytvoří prostředí pomocí služby. Předplatná může obsahovat [až 980 skupin prostředků](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). Tak, to je limit virtuálních počítačů a prostředí v předplatném. Existují dvě další konfigurace, které byste měli zvážit:
    - **[Všechny virtuální počítače přejít do stejné skupiny prostředků:](resource-group-control.md)** I když toto nastavení vám pomůže splnit limit skupiny prostředků, ovlivňuje limit typu prostředku na skupinu prostředků.
    - **Použití sdílených veřejných IP adres**: Všechny virtuální počítače stejné velikosti a oblasti přejdou do stejné skupiny prostředků. Tato konfigurace je "střední cesta" mezi kvótami skupiny prostředků a kvótami typu prostředků na skupinu prostředků, pokud virtuální počítače mohou mít veřejné IP adresy.
- **Zdroje podle skupiny prostředků podle typu prostředku**: Výchozí limit pro prostředky pro [skupinu prostředků podle typu prostředku je 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Při použití *všech virtuálních počítačů přejít na stejnou* konfiguraci skupiny prostředků, uživatelé hit tento limit předplatného mnohem dříve, zejména v případě, že virtuální počítače mají mnoho dalších disků.
- **Účty úložiště**: Testovací prostředí v DevTest Labs je dodáván s účtem úložiště. Kvóta Azure pro [počet účtů úložiště na oblast na předplatné je 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Maximální počet DevTest Labs ve stejné oblasti je také 250.
- **Přiřazení rolí**: Přiřazení role je způsob, jakým udělujete uživateli nebo objektu zabezpečení přístup k prostředku (vlastník, prostředek, úroveň oprávnění). V Azure je [limit 2 000 přiřazení rolí na jedno předplatné](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits). Ve výchozím nastavení vytvoří služba DevTest Labs skupinu prostředků pro každý virtuální virtuální ms. Vlastník je udělena oprávnění *vlastníka* pro virtuální hod DevTest Labs a *čtenář* oprávnění ke skupině prostředků. Tímto způsobem každý nový virtuální virtuální počítačů, který vytvoříte používá dvě přiřazení rolí kromě přiřazení, které se používají při udělit uživatelům oprávnění k testovacímu prostředí.
- **Čtení a zápisy rozhraní API**: Existují různé způsoby automatizace Azure a DevTest Labs, včetně rozhraní API REST, PowerShell, Azure CLI a Azure SDK. Prostřednictvím automatizace můžete narazit na další limit pro požadavky rozhraní API: Každé předplatné umožňuje až [12 000 požadavků na čtení a 1 200 požadavků na zápis za hodinu](../azure-resource-manager/management/request-limits-and-throttling.md). Uvědomte si tento limit při automatizaci DevTest Labs.

## <a name="manageability-considerations"></a>Aspekty správy
DevTest Labs má skvělé uživatelské rozhraní pro správu pro práci s jedním testovacím prostředím. Ale v podniku pravděpodobně máte více předplatných Azure a mnoho testovacích prostředí. Provádění změn konzistentně ve všech testovacích prostředích vyžaduje skriptování/automatizaci. Tady je několik příkladů a doporučené postupy správy pro nasazení DevTest Labs:

- **Změny nastavení testovacího prostředí**: Běžným scénářem je aktualizace konkrétního nastavení testovacího prostředí ve všech testovacích prostředích v nasazení. Například je k dispozici nová velikost instance virtuálního počítače a všechny testovací prostředí musí být aktualizovány, aby ji umožnily. Tyto změny je nejlepší automatizovat pomocí skriptů prostředí PowerShell, CLI nebo REST API.  
- **Token osobního přístupu úložiště artefaktů**: Obvykle vyprší platnost osobních přístupových tokenů pro úložiště Git za 90 dní, jeden rok nebo dva roky. Chcete-li zajistit kontinuitu, je důležité rozšířit osobní přístupový token nebo vytvořit nový. Pak použijte automatizaci použít nový token osobního přístupu pro všechna testovací prostředí.
- **Omezit změny nastavení testovacího prostředí**: Často musí být určité nastavení omezeno (například povolení použití bitových kopií marketplace). Zásady Azure můžete použít k zabránění změnám typu prostředku. Nebo můžete vytvořit vlastní roli a udělit uživatelům tuto roli namísto role *vlastníka* pro testovací prostředí. Můžete to udělat pro většinu nastavení v testovacím prostředí (interní podpora, oznámení testovacího prostředí, povolené velikosti virtuálních her a tak dále).
- **Vyžadovat, aby virtuální společnosti dodržovaly konvenci pojmenování**: Manažeři obvykle chtějí snadno identifikovat virtuální zařízení, které jsou součástí cloudového vývojového a testovacího prostředí. Můžete to provést pomocí [zásad Azure](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Je důležité si uvědomit, že DevTest Labs používá základní prostředky Azure, které jsou spravované stejným způsobem: sítě, disky, výpočetní prostředky a tak dále. Například Zásady Azure se vztahuje na virtuální počítače, které jsou vytvořené v testovacím prostředí. Azure Security Center může hlásit dodržování předpisů virtuálních počítači. A služba Azure Backup může poskytovat pravidelné zálohování pro virtuální počítače v testovacím prostředí.

## <a name="security-considerations"></a>Důležité informace o zabezpečení
Azure DevTest Labs používá existující prostředky v Azure (výpočetní prostředky, sítě a tak dále). Takže automaticky těží z funkcí zabezpečení, které jsou integrovány do platformy. Chcete-li například vyžadovat, aby příchozí připojení ke vzdálené ploše pocházela pouze z podnikové sítě, jednoduše přidejte skupinu zabezpečení sítě do virtuální sítě v bráně vzdálené plochy. Jediným dalším aspektem zabezpečení je úroveň oprávnění, která udělujete členům týmu, kteří používají testovací prostředí na každodenní bázi. Nejběžnější oprávnění jsou [ *vlastník* a *uživatel*](devtest-lab-add-devtest-user.md). Další informace o těchto rolích najdete [v tématu Přidání vlastníků a uživatelů v laboratořích Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Další kroky
Podívejte se na další článek v této sérii: [Vertikálně navýšit kapacitu infrastruktury Azure DevTest Labs](devtest-lab-guidance-scale.md).
