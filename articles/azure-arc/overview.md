---
title: Přehled služby Azure Arc
description: Přečtěte si, co je Azure ARC a jak pomáhá zákazníkům povolit správu a řízení hybridních prostředků pomocí dalších služeb a funkcí Azure.
ms.date: 09/23/2020
ms.topic: overview
ms.openlocfilehash: 3a8b054d9b7fd8f48dc667a5bd49a2c6994de1a3
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616520"
---
# <a name="azure-arc-overview"></a>Přehled služby Azure Arc

V současné době se společnosti působit potížey k řízení a řízení prostředí, které se bude lépe a komplexnější. Tato prostředí se šíří napříč datovými centry, několika cloudy a hraničními zařízeními. Každé prostředí a Cloud má vlastní sadu oddělených nástrojů pro správu, které se musí naučit a fungovat.

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

* Použijte konfiguraci založenou na GitOps jako správu kódu pro nasazení aplikací a konfigurací v rámci jednoho nebo více clusterů přímo ze správy zdrojového kódu, jako je GitHub.

* Pro clustery Kubernetes s využitím Azure Policy se neshodují dodržování předpisů a konfigurace.

* Spouštějte datové služby Azure v jakémkoli prostředí Kubernetes, konkrétně na spravované instanci Azure SQL a Azure Database for PostgreSQL škálování, s výhodami, jako jsou upgrady/aktualizace, zabezpečení a monitorování, jako kdyby běžely v Azure. Využijte elastické škálování, použijte aktualizace bez jakýchkoli výpadků aplikací, a to i v případě, že se nejedná o nepřetržité připojení k Azure.

* Jednotné zobrazení prostředků s povolenou možností ARC Azure, ať už používáte Azure Portal, Azure CLI, Azure PowerShell nebo Azure REST API.

## <a name="how-much-does-azure-arc-cost"></a>Kolik stojí Azure ARC?

Níže jsou uvedeny podrobnosti o cenách pro funkce, které jsou dnes k dispozici v Azure ARC.

### <a name="arc-enabled-servers"></a>Servery s podporou služby Arc

Funkce plochy ovládacího prvku ARC Azure se nabízí bez dalších poplatků. Sem patří:

* Organizace prostředků prostřednictvím skupin a značek pro správu Azure

* Vyhledávání a indexování pomocí Azure Resource graphu

* Přístup a zabezpečení prostřednictvím Azure RBAC a předplatných.

* Prostředí a automatizace prostřednictvím šablon a rozšíření.

* Správa aktualizací

Všechny služby Azure, které se používají na serverech s podporou ARC, například Azure Security Center nebo Azure Monitor, se budou účtovat podle ceny za tuto službu. Další informace najdete na [stránce s cenami za Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes s podporou Azure Arc

V aktuální fázi Preview se Kubernetes s povoleným Azure ARC bez dalších nákladů.

### <a name="azure-arc-enabled-data-services"></a>Datové služby s podporou služby Azure Arc

V aktuální fázi Preview se datové služby s podporou ARC Azure nabízejí bez dalších nákladů.

## <a name="next-steps"></a>Další kroky

* Spuštění služeb s povoleným obloukem z [rychlé zprovoznění scénářů](https://azurearcjumpstart.io/azure_arc_jumpstart/)

* Další informace o serverech s podporou ARC najdete v následujícím [přehledu](./servers/overview.md) .

* Další informace o Kubernetes s povoleným ARC najdete v následujícím [přehledu](./kubernetes/overview.md) .

* Další informace o datových službách s podporou ARC najdete v následujícím [přehledu](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/) .
