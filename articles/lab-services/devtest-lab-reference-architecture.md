---
title: Referenční architektura Enterprise pro Azure DevTest Labs
description: Tento článek obsahuje odkaz na doporučení ohledně architektury pro Azure DevTest Labs v podniku.
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
ms.openlocfilehash: 73a3d426e9040525b0c631db273e59c49a6a9eb0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705886"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Referenční architektura Azure DevTest Labs pro podniky
Tento článek obsahuje referenční architekturu nasadit řešení založené na službě Azure DevTest Labs v podniku. Zahrnuje následující:
- Místního připojení prostřednictvím Azure ExpressRoute
- Brána vzdálené plochy pro vzdálené přihlášení k virtuálním počítačům
- Připojení k jako úložiště artefaktů pro privátní artefakty
- Další služby PaaS, které se používají v testovacích prostředí

![Diagram referenční architektury](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektura
Toto jsou klíčové prvky referenční architektura:

- **Azure Active Directory (Azure AD)**: Používá DevTest Labs [služby Azure AD pro správu identit](../active-directory/fundamentals/active-directory-whatis.md). Když uživatelům poskytnete přístup k prostředí založené na službě DevTest Labs vezměte v úvahu tyto dva klíčové aspekty:
    - **Správa prostředků**: Poskytuje přístup k webu Azure portal ke správě prostředků (vytvářet virtuální počítače, vytvářet prostředí; spuštění, zastavení, restartování, odstranit a použít artefakty; a tak dále). Správa prostředků se provádí v Azure pomocí řízení přístupu na základě role (RBAC). Přiřazení rolí uživatelům a nastavte prostředků a oprávnění na úrovni přístupu.
    - **Virtual machines (na úrovni sítě)**: Ve výchozí konfiguraci virtuální počítače používat účet místního správce. Pokud není k dispozici domény ([Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md), doméně místní nebo cloudové doméně), počítačů, může být připojen k doméně. Uživatelé pak mohou používat své identity založené na doméně pro připojení k virtuálním počítačům.
- **Místní připojení**: V našem diagram architektury [ExpressRoute](../expressroute/expressroute-introduction.md) se používá. Ale můžete použít také [site-to-site VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Ačkoli není vyžadována pro DevTest Labs ExpressRoute, se běžně používá v podnicích. ExpressRoute je povinný, jenom v případě, že potřebujete přístup k firemním prostředkům. Běžné scénáře jsou:
    - Máte místní data, která nelze přesunout do cloudu.
    - Dáváte přednost připojení testovacího prostředí virtuálních počítačů k místní doméně.
    - Chcete vynutit veškerý síťový provoz do a z prostředí cloud prostřednictvím místní bráně firewall pro zabezpečení nebo dodržování předpisů.
- **Skupiny zabezpečení sítě**: Běžný způsob, jak omezit přenosy ke cloudovému prostředí (nebo v cloudovém prostředí) založené na zdrojové a cílové IP adresy, je použít [skupinu zabezpečení sítě](../virtual-network/security-overview.md). Například chcete povolit pouze provoz pocházející z podnikové sítě do testovacího prostředí sítě.
- **Brána vzdálené plochy**: Podniky obvykle blokuje odchozí připojení ke vzdálené ploše v podnikové brány firewall. Existuje několik možností, jak povolit připojení ke cloudové prostředí v DevTest Labs, včetně:
  - Použití [Brána vzdálené plochy](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)a seznam povolených adres statickou IP adresu brány, nástroj pro vyrovnávání zatížení.
  - [Směrovat veškerý příchozí provoz protokolu RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) přes připojení VPN ExpressRoute/site-to-site. Tato funkce je běžné zvážit při podniky plánování nasazení DevTest Labs.
- **Síťové služby (virtuální sítě, podsítě)**: [Sítě Azure](../networking/networking-overview.md) topologie je jiný klíčovým prvkem v architektuře DevTest Labs. Určuje, zda prostředky z testovacího prostředí můžete komunikovat a mají přístup k místním a Internetu. Naše diagram architektury zahrnuje nejběžnějších způsobů, kterou uživatelé používají DevTest Labs: Všechny testovací prostředí se připojuje přes [partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) pomocí [centra s paprsky modelu](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) připojení VPN ExpressRoute/site-to-site k místní. Ale DevTest Labs používá Azure Virtual Network přímo, takže neexistují žádná omezení na nastavení síťové infrastruktury.
- **DevTest Labs**:  DevTest Labs je klíčovou součástí architektury. Další informace o této službě najdete v tématu [DevTest Labs](devtest-lab-overview.md).
- **Virtuální počítače a dalších prostředků (SaaS, PaaS, IaaS)**:  Virtuální počítače jsou klíče úlohu, která podporuje DevTest Labs společně s další prostředky Azure. DevTest Labs umožňuje rychle a snadno pro podniky a zajistit tak přístup k prostředkům Azure (včetně virtuálních počítačů a dalších prostředků Azure). Další informace o přístup k Azure pro [vývojáři](devtest-lab-developer-lab.md) a [testeři](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Aspekty zabezpečení
I když DevTest Labs nemá vestavěné kvóty nebo limity, dalších prostředků Azure, které se používají v typické operaci testovacího prostředí mají [kvóty na úrovni předplatného](../azure-subscription-service-limits.md). V typické podnikové nasazení, musíte proto více předplatných Azure, aby pokryl velké nasazení služby DevTest Labs. Jsou kvóty, které podniky nejčastěji překročí:

- **Skupiny prostředků:** Ve výchozí konfiguraci DevTest Labs vytvoří skupinu prostředků pro každý nový virtuální počítač nebo uživatel vytvoří prostředí pomocí služby. Předplatné může obsahovat [až 980 skupin prostředků](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). Ano, takové je omezení virtuálních počítačů a prostředí v rámci předplatného. Existují dva další konfigurace, které byste měli zvážit:
    - **[Všechny virtuální počítače přejít do stejné skupiny prostředků](resource-group-control.md)**: Přestože tento instalační program pomáhá splnit i ty limitu skupiny prostředků, má vliv omezení prostředků typ za resource-group.
    - **Použití sdílených veřejné IP adresy**: Všechny virtuální počítače stejné velikosti a oblasti, přejděte do stejné skupiny prostředků. Tato konfigurace je "základu střední" mezi kvóty skupiny prostředků a kvóty prostředků typ za resource-group, pokud mají veřejné IP adresy můžou virtuální počítače.
- **Skupina prostředků pro každý prostředek na typ prostředku**: Výchozí limit pro [prostředků na skupinu prostředků na typ prostředku je 800](../azure-subscription-service-limits.md#resource-group-limits).  Při použití *všechny virtuální počítače přejít do stejné skupiny prostředků* konfigurace, stiskněte uživatelé tohoto předplatného omezit mnohem dřív, zejména v případě, že virtuální počítače mají mnoho dalších disků.
- **Účty úložiště**: Testovací prostředí v DevTest Labs se dodává s účtem úložiště. Kvóta Azure pro [počet účtů úložiště na oblast a předplatné je 250](../azure-subscription-service-limits.md#storage-limits). Maximální počet DevTest Labs ve stejné oblasti je také 250.
- **Přiřazení rolí**: Přiřazení role se, jak poskytnout uživatel nebo instanční objekt přístup k prostředku (vlastník, prostředek, úroveň oprávnění). V Azure je [maximálně 2 000 přiřazení rolí na jedno předplatné](../azure-subscription-service-limits.md#role-based-access-control-limits). Ve výchozím nastavení služba DevTest Labs vytvoří skupinu prostředků pro každý virtuální počítač. Vlastníkem jsou udělena *vlastníka* oprávnění pro virtuální počítač DevTest Labs a *čtečky* oprávnění ke skupině prostředků. Tímto způsobem používá každý nový virtuální počítač, který vytvoříte dvě přiřazení rolí kromě přiřazení, které se používají, když uživatelům poskytnete oprávnění k testovacím prostředí.
- **Rozhraní API čtení/zápisy**: Existují různé způsoby, jak automatizovat Azure a DevTest Labs, včetně rozhraní REST API, Powershellu, rozhraní příkazového řádku Azure a Azure SDK. Díky automatizaci budete pravděpodobně dojde k jiné limitu požadavků rozhraní API: Každé předplatné umožňuje až [čtení výhrou 12 000 požadavků za hodinu na zápis požadavků a 1 200](../azure-resource-manager/resource-manager-request-limits.md). Mějte toto omezení při automatizaci DevTest Labs.

## <a name="manageability-considerations"></a>Aspekty správy
DevTest Labs je skvělé pro správu uživatelského rozhraní pro práci s jednoho testovacího prostředí. Ale v podniku, pravděpodobně máte více předplatných Azure a mnoha labs. Provádění změn konzistentně na všech testovacích prostředí vyžaduje/automation. Tady jsou některé příklady a osvědčené postupy správy pro nasazení DevTest Labs:

- **Změny v nastavení správy lab**: Běžný scénář, kdy se aktualizovat nastavení konkrétní testovacího prostředí ve všech testovacích prostředí v nasazení. Například novou velikost instance virtuálního počítače je k dispozici, a k tomu, aby se musí aktualizovat všechny testovací prostředí. Doporučujeme automatizovat tyto změny pomocí skriptů Powershellu, rozhraní příkazového řádku nebo rozhraní REST API.  
- **Token pat úložiště artefaktů**:  Osobní přístupové tokeny pro úložiště Git obvykle vyprší za 90 dnů, jeden, nebo dva roky. K zajištění kontinuity podnikových procesů, je potřeba rozšířit osobní přístupový token nebo vytvořte novou. Pomocí automatizace používat nový osobní přístupový token pro všechny testovací prostředí.
- **Omezit změny nastavení testovacího prostředí**: Často musí být konkrétní nastavení s omezeným přístupem (například můžete umožnit použití imagí marketplace). Azure Policy můžete zabránit změnám typu prostředku. Nebo můžete vytvořit vlastní roli a udělit uživatelům této role místo *vlastníka* role pro testovací prostředí. Můžete to provést pro většinu nastavení v testovacím prostředí (interní podpory oznámení testovacího prostředí, povolené velikosti virtuálních počítačů a tak dále).
- **Vyžadovat virtuální počítače, které dodržovat zásadu vytváření názvů**: Správci obvykle chcete snadno identifikovat virtuální počítače, které jsou součástí cloudové vývojové a testovací prostředí. Můžete to provést pomocí [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Je důležité si uvědomit, že DevTest Labs používá základní prostředky Azure, které se spravují stejným způsobem jako: síť, disky, výpočetní prostředky a tak dále. Azure Policy se vztahuje na virtuální počítače, které jsou vytvořeny v testovacím prostředí. Azure Security Center může hlásit dodržování předpisů virtuálního počítače. A služba Azure Backup může poskytnout pravidelného zálohování pro virtuální počítače v testovacím prostředí.

## <a name="security-considerations"></a>Aspekty zabezpečení
Azure DevTest Labs používá stávající prostředky v Azure (výpočetní, síťové a tak dále). Takže ho automaticky využívá výhod funkce zabezpečení, které jsou součástí této platformy. Například požadovat příchozí připojení ke vzdálené ploše pocházejí pouze z podnikové sítě, jednoduše přidejte skupinu zabezpečení sítě k virtuální síti Brána vzdálené plochy. Jenom zvážit další aspekty zabezpečení je úroveň oprávnění, která je udělit členům týmu, kteří používají Tato praktická cvičení na každodenní bázi. Nejběžnější oprávnění jsou [ *vlastníka* a *uživatele*](devtest-lab-add-devtest-user.md). Další informace o těchto rolích najdete v tématu [přidat vlastníky a uživatele ve službě Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Další postup
Zobrazit další článek v této sérii: [Škálovat infrastrukturu s Azure DevTest Labs](devtest-lab-guidance-scale.md).
