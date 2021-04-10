---
title: Seznámení se službou Azure Managed instance pro Apache Cassandra
description: Přečtěte si o spravované instanci Azure pro Apache Cassandra. Tato služba spravuje nasazení a škálování nativních Open Source instancí Apache Cassandra v Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748407"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>Co je Azure Managed instance pro Apache Cassandra? (Preview)

> [!IMPORTANT]
> Spravovaná instance Azure pro Apache Cassandra je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Služba Azure Managed instance pro Apache Cassandra poskytuje automatizované operace nasazení a škálování pro spravovaná Open Source datacentra Apache Cassandra. Tato služba vám pomůže zrychlit hybridní scénáře a snížit průběžnou údržbu. Díky [Azure Cosmos DB rozhraní API Cassandra](../cosmos-db/cassandra-introduction.md) na jeho obecném vydání bude mít rozsáhlou integraci a možnosti přesunu dat.

:::image type="content" source="./media/introduction/icon.gif" alt-text="Spravovaná instance Azure pro Apache Cassandra je spravovaná služba pro Apache Cassandra." border="false":::

## <a name="key-benefits"></a>Klíčové výhody

### <a name="hybrid-deployments"></a>Hybridní nasazení

Pomocí této služby můžete snadno umístit spravované instance datových center Apache Cassandra, které se automaticky nasazují jako virtuální počítače s měřítkem, do nové nebo existující služby Azure Virtual Network. Tato datová centra se dají přidat k existujícímu okruhu Apache Cassandra běžícímu místně prostřednictvím [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) v Azure nebo v jiném cloudovém prostředí.

- **Zjednodušené nasazení:** Po navázání hybridního připojení je nasazení snadné prostřednictvím protokolu Gossip.
- **Hostované metriky:** Metriky se hostují v [Prometheus](https://prometheus.io/docs/introduction/overview/) a sledují aktivity napříč clusterem.

### <a name="simplified-scaling"></a>Zjednodušené škálování

Ve spravované instanci je horizontální navýšení kapacity a škálování uzlů v datacentru plně spravované. Zadáte počet uzlů, které potřebujete, a změna škálování Orchestrator se postará o zřízení jejich provozu v rámci Cassandra ringu.

### <a name="managed-and-cost-effective"></a>Spravované a nákladově efektivní

Služba poskytuje operace správy pro následující běžné úlohy Apache Cassandra:

- Zřízení clusteru
- Zřízení datacentra
- Škálování datového centra
- Odstranění datového centra
- Zahájit akci opravy v prostoru klíčů
- Změna konfigurace datového centra
- Zálohování instalace

Cenový model je flexibilní, na vyžádání, založený na instancích a nemá žádné licenční poplatky. Tento cenový model vám umožní upravit konkrétní potřeby úloh. Zvolíte, kolik jader, jakou SKU virtuálního počítače, jakou velikost paměti a velikost místa na disku potřebujete.

## <a name="next-steps"></a>Další kroky

Začněte s jedním z našich rychlých startů:

* [Vytvoření clusteru spravované instance z Azure Portal](create-cluster-portal.md)
* [Nasazení clusteru spravovaného Apache Spark s využitím Azure Databricks](deploy-cluster-databricks.md)
* [Správa spravované instance Azure pro prostředky Apache Cassandra pomocí Azure CLI](manage-resources-cli.md)
