---
title: Přehled služby Azure Arc
description: Přečtěte si, co je Azure ARC a jak pomáhá zákazníkům povolit správu a řízení hybridních prostředků pomocí dalších služeb a funkcí Azure.
ms.date: 03/02/2021
ms.topic: overview
ms.openlocfilehash: 33c9d6ca87c3d8d2d8920ff429902f5876bbdc59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650188"
---
# <a name="azure-arc-overview"></a>Přehled služby Azure Arc

V dnešní době si společnosti bojovati řídit a řídit stále složitá prostředí. Tato prostředí se šíří napříč datovými centry, několika cloudy a hraničními zařízeními. Každé prostředí a Cloud disponuje svou vlastní sadou oddělených nástrojů pro správu, které se musí naučit a pracovat.

V paralelních případech je obtížné implementovat nové provozní modely DevOps a ITOps, protože stávající nástroje neposkytují podporu pro nové nativní vzory cloudu.

Azure ARC usnadňuje řízení a správu díky zajištění konzistentního prostředí pro více cloudů a místní platformy pro správu. Azure ARC umožňuje spravovat celé prostředí s jediným podoknem skla a tím se vaše stávající prostředky do Azure Resource Manager. Teď můžete spravovat virtuální počítače, clustery Kubernetes a databáze, jako kdyby běžely v Azure. Bez ohledu na to, kde jsou živé, můžete používat známé služby a možnosti správy Azure. Azure ARC umožňuje dál používat tradiční ITOps a přitom zavádět postupy DevOps pro podporu nových nativních vzorů cloudu ve vašem prostředí.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Diagram roviny řízení správy ARC v Azure" border="false":::

V dnešní době vám Azure ARC umožňuje spravovat následující typy prostředků hostované mimo Azure:

* Servery – fyzické i virtuální počítače se systémem Windows nebo Linux.
* Clustery Kubernetes – podpora více distribucí Kubernetes.
* Datové služby Azure – Azure SQL Database a PostgreSQL služby pro škálování na více procesorů.

## <a name="what-does-azure-arc-deliver"></a>Co Azure ARC doručí?

Mezi klíčové funkce Azure ARC patří:

* Implementujte konzistentní inventarizaci, správu, zásady správného řízení a zabezpečení pro vaše servery ve vašem prostředí.

* Nakonfigurujte [rozšíření virtuálních počítačů Azure](./servers/manage-vm-extensions.md) tak, aby se k monitorování, zabezpečení a aktualizaci serverů používaly služby pro správu Azure.

* Spravujte a spravujte clustery Kubernetes ve velkém měřítku.

* Pomocí GitOps Nasaďte konfiguraci v jednom nebo víc clusterech z úložišť Git.

*  Dodržování předpisů a konfigurace pro clustery Kubernetes s nulovými gesty pomocí Azure Policy.

* Azure Data Services spouštějte v jakémkoli prostředí Kubernetes, jako by běželo v Azure (konkrétně spravovaná instance Azure SQL a Azure Database for PostgreSQL škálování, s využitím výhod, jako jsou upgrady, aktualizace, zabezpečení a monitorování). Používejte Elastické škálování a používejte aktualizace bez jakéhokoli výpadku aplikací, a to i bez nepřetržitého připojení k Azure.

* Jednotné zobrazení prostředků s povolenou možností ARC Azure, ať už používáte Azure Portal, Azure CLI, Azure PowerShell nebo Azure REST API.

## <a name="how-much-does-azure-arc-cost"></a>Kolik stojí Azure ARC?

Níže jsou uvedeny podrobnosti o cenách pro funkce, které jsou dnes k dispozici v Azure ARC.

### <a name="arc-enabled-servers"></a>Servery s podporou služby Arc

Následující funkce plochy ovládacího prvku ARC Azure se nabízí bez dalších poplatků:

* Organizace prostředků prostřednictvím skupin a značek pro správu Azure

* Vyhledávání a indexování pomocí Azure Resource graphu

* Přístup a zabezpečení prostřednictvím Azure RBAC a předplatných.

* Prostředí a automatizace prostřednictvím šablon a rozšíření.

* Správa aktualizací

Všechny služby Azure, které se používají na serverech s podporou ARC, například Azure Security Center nebo Azure Monitor, se budou účtovat podle ceny za tuto službu. Další informace najdete na stránce s [cenami Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes s podporou Azure Arc

Jakákoli služba Azure, která se používá v Kubernetes s povoleným Arcm, například Azure Security Center nebo Azure Monitor, se bude účtovat podle ceny za tuto službu. Další informace o cenách pro konfigurace nad Kubernetesem povoleným ARC Azure najdete na [stránce s cenami Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-data-services"></a>Datové služby s podporou služby Azure Arc

V aktuální fázi Preview jsou datové služby s podporou ARC Azure nabízeny bez dalších poplatků.

## <a name="next-steps"></a>Další kroky

* Další informace o serverech s podporou ARC najdete v následujícím [přehledu](./servers/overview.md) .

* Další informace o Kubernetes s povoleným ARC najdete v následujícím [přehledu](./kubernetes/overview.md) .

* Další informace o datových službách s podporou ARC najdete v následujícím [přehledu](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/) .

* Služby s povoleným Arcm v [rychlé zprovozněníi pro zkoušku konceptu](https://azurearcjumpstart.io/azure_arc_jumpstart/)
