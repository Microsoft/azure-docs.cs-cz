---
title: Přístup k virtuálním sítím Azure z Azure Logic Apps se prostředí integrační služby (ISEs)
description: Tento přehled popisuje, jak pomocí prostředí integrační služby (ISEs) přístup k virtuálním sítím Azure (Vnet) aplikace logiky
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 1ef8c8eec3865f2a6e363e7da1dbda9504b81c05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65546434"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Přístup k prostředkům Azure Virtual Network v Azure Logic Apps s využitím prostředí integrační služby (ISEs)

V některých případech logic apps a účty pro integraci potřebují přístup k zabezpečeným prostředkům, jako jsou virtuální počítače (VM) a jiné systémy nebo služby, které jsou uvnitř [virtuální síť Azure](../virtual-network/virtual-networks-overview.md). Chcete-li nastavit tento přístup můžete [vytvořit *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) ve kterém můžete spustit aplikace logiky a vytvořit integraci vašich účtů.

Při vytváření ISE Azure nasadí privátní a izolované instance služby Logic Apps do vaší virtuální sítí Azure. Tato privátní instance používá vyhrazené prostředky, jako jsou úložiště a běží odděleně od veřejné "globální" služba Logic Apps. Oddělení vaší izolované instance privátní a veřejné globální instanci také pomáhá snižovat dopad, který jiných tenantů Azure může mít na výkon vaší aplikace, která je také označována jako ["" hlučným sousedům"" efekt](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Po vytvoření vašeho ISE, když přejdete k vytvoření logiky aplikace nebo integračního účtu, můžete vybrat vaše ISE jako umístění logic app nebo integračního účtu:

![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Aplikace logiky teď přímo přístupné systémy, které jsou uvnitř nebo připojené k vaší virtuální sítě pomocí některého z těchto položek:

* **ISE**-označené jako konektor pro daný systém, jako je SQL Server
* A **Core**-označené jako integrované triggeru nebo akce, jako je například HTTP triggeru nebo akce.
* Vlastní konektor

Tento přehled popisuje další podrobnosti o jak ISE poskytuje aplikace logiky a integrace účty přímý přístup ke službě Azure virtual network a porovnává rozdíly mezi ISE a globální služba Logic Apps.

> [!NOTE]
> Logic apps, integrované aktivačních událostí, integrované akce a konektory, které běží ve vaší ISE cenový plán liší od založenou na skutečné spotřebě cenového plánu. Další informace najdete v tématu [ceny Logic Apps](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Izolovaná a globální

Když vytvoříte integrované služby prostředí (ISE) v Azure, můžete vybrat virtuální síť Azure, ve které chcete *vložit* vaše ISE. Azure pak vloží nebo nasadí privátní instanci služby Logic Apps do vaší virtuální sítě. Tato akce vytvoří izolovaného prostředí, ve kterém můžete vytvořit a spustit aplikace logiky na vyhrazených prostředcích. Když vytvoříte aplikaci logiky, vyberte vaše ISE jako umístění vaší aplikace, která poskytuje přímý přístup vašich logic app k vaší virtuální sítě a prostředky v této síti.

Aplikace logiky do ISE poskytují stejné uživatelské prostředí a podobné funkce jako globální služba Logic Apps. Nejen můžete použijete stejné integrované aktivačních událostí, integrované akce a konektory ze globální služby Logic Apps, ale můžete také použít konkrétní ISE konektory. Tady je příklad, některé standardní konektory, které nabízí verze, na kterých běží v prostředí ISE:

* Azure Blob Storage, File Storage a Table Storage
* Fronty Azure, Azure Service Bus, Azure Event Hubs a IBM MQ
* FTP a SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X 12 a EDIFACT

Rozdíl mezi konektory ISE a jiných ISE je v umístění, kde spouštění triggerů a akcí:

* V ISE, integrované aktivační události a akce, jako je například HTTP, vždy spustit v prostředí ISE stejné jako vaše aplikace logiky a zobrazení **Core** popisek.

  ![Vyberte "Základní" integrované triggery a akce](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Konektory, které běží v prostředí ISE mít veřejně hostované verze k dispozici v globální službě Logic Apps. Pro konektory, které nabízejí dvě verze, s konektory **ISE** popisek vždy spustit v prostředí ISE stejné jako aplikace logiky. Konektory bez **ISE** popisek spustit v globální službě Logic Apps.

  ![Výběr konektorů ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

### <a name="access-to-on-premises-data-sources"></a>Přístup k místním zdrojům dat

Pro místní systémy, které jsou připojené ke službě Azure virtual network vložit ISE do této sítě tak, aby aplikace logiky můžete tyto systémy přístup přímo pomocí některé z těchto položek:

* Konektor ISE-version pro daný systém, například SQL Server
  
* Akce HTTP
  
* Vlastní konektor

  * Pokud máte vlastní konektory, které vyžadují místní brány dat a vytvoříte tyto konektory mimo ISE, aplikace logiky do ISE můžete také použít tyto konektory.
  
  * Vlastních konektorů vytvořených v prostředí ISE nefungují s místní bránou dat Ale tyto konektory můžete přímý přístup k místním zdrojům dat, které jsou připojené k virtuální síti, který je hostitelem ISE. Tak aplikace logiky do ISE pravděpodobně nepotřebujete bránu dat při komunikaci s těmito prostředky.

Pro místní systémy, které nejsou připojené k virtuální síti nebo nemají ISE verze konektorů, je nutné nejprve [nastavit na místní bránu dat](../logic-apps/logic-apps-gateway-install.md) před svoji logiku aplikace můžete připojit k těmto systémům.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Účty pro integraci s ISE

Můžete použít účty pro integraci s logic apps uvnitř prostředí integrační služby (ISE). Ale musí používat tyto účty pro integraci *stejné ISE* jako propojené logic apps. Aplikace logiky do ISE může odkazovat pouze tyto účty pro integraci, které jsou ve stejném ISE. Při vytváření účtu pro integraci, můžete vybrat vaše ISE jako umístění pro váš účet integrace.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [připojit k virtuálním sítím Azure z izolované logic apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Další informace o [integrace služby virtual network pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
