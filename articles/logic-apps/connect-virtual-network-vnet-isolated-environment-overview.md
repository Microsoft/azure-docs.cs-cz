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
ms.date: 11/29/2018
ms.openlocfilehash: eb296a436f6c09a4f592ba3a26ee1c3a0f8e18bb
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678828"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Přístup k prostředkům Azure Virtual Network v Azure Logic Apps s využitím prostředí integrační služby (ISEs)

> [!NOTE]
> Tato funkce je v *ve verzi private preview*. Chcete-li požádat o přístup, [vytváření žádosti o připojení tady](https://aka.ms/iseprivatepreview).

V některých případech logic apps a účty pro integraci potřebují přístup k zabezpečeným prostředkům, jako jsou virtuální počítače (VM) a jiné systémy nebo služby, v [virtuální síť Azure](../virtual-network/virtual-networks-overview.md). Chcete-li nastavit tento přístup můžete [vytvořit *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) pro spouštění vašich logic apps a účty pro integraci. 

![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Vytvoření ISE nasadí privátní a izolované instance Logic Apps do vaší virtuální sítí Azure. Tato privátní instance používá vyhrazené prostředky, jako jsou úložiště a běží odděleně od veřejné "globální" služba Logic Apps. Tato separace pomáhá také omezit dopad, který jiných tenantů Azure může mít na výkon vaší aplikace, nebo ["" hlučným sousedům"" efekt](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Tento přehled popisuje, jak ISE poskytuje aplikace logiky a účty pro integraci přímý přístup ke službě Azure virtual network a porovnává rozdíly mezi ISE a globální služba Logic Apps.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Izolovaná a globální

Když vytvoříte integrované služby prostředí (ISE) v Azure, vyberte virtuální síť Azure kde vaše *vložit* prostředí. Azure nasadí privátní instanci služby Logic Apps do vaší virtuální sítě. Tato akce vytvoří izolovaného prostředí, ve kterém můžete vytvořit a spustit aplikace logiky na vyhrazených prostředcích. Když vytvoříte aplikaci logiky, vyberte toto prostředí jako umístění vaší aplikace, která poskytuje přímý přístup vašich logic app k prostředkům ve vaší virtuální síti. 

Aplikace logiky do ISE poskytují stejné uživatelské prostředí a podobné funkce jako globální služba Logic Apps. Nejen můžete použijete stejné integrované akce a konektory v globální službě Logic Apps, ale můžete také použít konkrétní ISE konektory. Tady je příklad, některé standardní konektory, které nabízí verze, na kterých běží v prostředí ISE:
 
* Azure Blob Storage, File Storage a Table Storage
* Fronty Azure, Azure Service Bus, Azure Event Hubs a IBM MQ
* FTP a SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X 12 a EDIFACT

Rozdíl mezi konektory ISE a jiných ISE je v umístění, kde spouštění triggerů a akcí:

* V ISE, integrované triggery a akce, jako je HTTP vždy spustit v prostředí ISE stejné jako aplikace logiky. 

* Pro konektory, které nabízejí dvě verze jednu verzi běží ISE, zatímco běží jiné verze v globální službě Logic Apps.  

  Konektory, které mají **ISE** popisek vždy spustit v prostředí ISE stejné jako aplikace logiky. Konektory bez **ISE** popisek spustit v globální službě Logic Apps. 

  ![Výběr konektorů ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Konektory, které běží v prostředí ISE jsou dostupné v globální služba Logic Apps. 

> [!IMPORTANT]
> Logic apps, integrované akce a konektory, na kterých běží vaše ISE používá jiný cenový plán není založenou na skutečné spotřebě cenového plánu. Další informace najdete v tématu [ceny Logic Apps](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Oprávnění pro přístup k virtuální síti

Při vytváření prostředí integrační služby (ISE), vyberte virtuální síť Azure tom, kde jste *vložit* vašeho prostředí. Vkládání nasadí privátní instanci služby Logic Apps do vaší virtuální sítě. Tato akce výsledků v izolovaném prostředí, ve kterém můžete vytvořit a spustit aplikace logiky na vyhrazených prostředcích. Při vytváření aplikací logiky vyberte váš ISE jako umístění pro vaše aplikace. Tyto aplikace logiky můžete přímo přistupovat k vaší virtuální sítě a připojení k prostředkům v dané síti. 

Pro místní systémy ve virtuální síti, který je propojen ISE aplikace logiky přímo přístupné tyto systémy pomocí některé z těchto položek: 

* ISE konektor pro daný systém, například SQL Server
* Akce HTTP 
* Vlastní konektor

Pro místní systémy, které nejsou ve virtuální síti nebo nemají ISE konektory, můžete připojit k těmto systémům až [nastavit a používat místní brány dat](../logic-apps/logic-apps-gateway-install.md).

Abyste mohli vybrat virtuální síť Azure pro vkládání prostředí, musíte vytvořit oprávnění řízení přístupu na základě Role (RBAC) ve vaší virtuální síti pro službu Azure Logic Apps. Tato úloha vyžaduje, abyste přiřadili **Přispěvatel sítě** a **Přispěvatel modelu Classic** role ve službě Azure Logic Apps.
Nastavení těchto oprávnění naleznete v tématu [připojit k virtuálním sítím Azure z aplikací logiky](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#vnet-access)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Účty pro integraci s ISE

Můžete použít účty pro integraci s logic apps uvnitř prostředí integrační služby (ISE). Ale musí používat tyto účty pro integraci *stejné ISE* jako propojené logic apps. Aplikace logiky do ISE může odkazovat pouze tyto účty pro integraci, které jsou ve stejném ISE. Při vytváření účtu pro integraci, můžete vybrat vaše ISE jako umístění pro váš účet integrace.

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">fórum Azure Logic Apps</a>.
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte <a href="https://aka.ms/logicapps-wish" target="_blank">web zpětné vazby od uživatelů Logic Apps</a>.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [připojit k virtuálním sítím Azure z izolované logic apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Další informace o [integrace služby virtual network pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
