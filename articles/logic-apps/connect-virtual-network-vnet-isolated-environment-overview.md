---
title: Přístup k virtuálním sítím Azure z Azure Logic Apps se prostředí integrační služby (ISEs)
description: Tento přehled popisuje, jak pomocí prostředí integrační služby (ISEs) přístup k virtuálním sítím Azure aplikace logiky
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: f21af23cf0b7b121441b1433f382db60ef7f13fe
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408655"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Přístup k prostředkům Azure Virtual Network v Azure Logic Apps s využitím prostředí integrační služby (ISEs)

> [!NOTE]
> Tato funkce je v *ve verzi private preview*. Chcete-li požádat o přístup, [vytváření žádosti o připojení tady](https://aka.ms/iseprivatepreview).

V některých případech logic apps a účty pro integraci potřebují přístup k zabezpečeným prostředkům, jako jsou virtuální počítače (VM) a jiné systémy nebo služby uvnitř [virtuální síť Azure](../virtual-network/virtual-networks-overview.md). Chcete-li nastavit tento přístup můžete [vytvořit *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , který používáte jako umístění pro logic apps a účty pro integraci. 

![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Vytvoření ISE nasadí privátní a izolované instance Logic Apps do vaší virtuální sítí Azure. Soukromé instanci používá vyhrazené prostředky, jako jsou úložiště a běží odděleně od veřejné "globální" služba Logic Apps. Tato separace pomáhá také omezit dopad, který jiných tenantů Azure může mít na výkon vaší aplikace, nebo ["" hlučným sousedům"" efekt](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Tento přehled popisuje, jak vytvářet ISE pomáhá logic apps a účty pro integraci přímý přístup k prostředkům v rámci vaší virtuální sítí Azure a porovnává rozdíly mezi ISE a globální služba Logic Apps.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Izolovaná a globální

Když vytvoříte integrované služby prostředí (ISE) v Azure, můžete vybrat virtuální síť Azure jako *peer* pro vaše prostředí. Azure nasadí privátní instanci služby Logic Apps do vaší virtuální sítě, což vede k izolované prostředí, ve kterém můžete vytvořit a spustit aplikace logiky na vyhrazených prostředcích. Když vytvoříte aplikaci logiky, můžete vybrat toto prostředí jako umístění vaší aplikace, které také poskytuje vaše logic app přímý přístup k prostředkům ve vaší virtuální síti.  

Aplikace logiky do ISE poskytují stejné uživatelské prostředí a podobné funkce jako globální služba Logic Apps. Nejen můžete použijete stejné předdefinované a konektory poskytuje globální službu Logic Apps, ale můžete si vybrat z konektorů, které poskytují ISE verze. Tady je příklad, některé standardní konektory, které nabízí verze, na kterých běží v prostředí ISE:
 
* Azure Blob Storage, File Storage a Table Storage
* Fronty Azure
* Azure Service Bus
* FTP
* SSH FTP (SFTP)
* SQL Server
* AS2, X 12 a EDIFACT

Rozdíl mezi konektory ISE a jiných ISE je v umístění, kde spouštění triggerů a akcí:

* Pokud používáte integrované triggery a akce, jako je HTTP ve vaší ISE, tyto triggery a akce vždy spustit v prostředí ISE stejné jako aplikace logiky. 

* Pro konektory, které nabízejí dvě verze: jedna verze běží v ISE, zatímco jiné verze běží v globální službě Logic Apps.  

  Konektory, které mají **ISE** popisek vždy spustit v prostředí ISE stejné jako aplikace logiky. Konektory bez **ISE** popisek spustit v globální službě Logic Apps. 

  ![Výběr konektorů ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Konektory, které nakonfigurujete v vaše prostředí ISE jsou také k dispozici pro použití v globální službě Logic Apps. 

> [!IMPORTANT]
> Logic apps, integrované akce a konektory, na kterých běží vaše ISE používá jiný cenový plán není založenou na skutečné spotřebě cenového plánu. Další informace najdete v tématu [ceny Logic Apps](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Oprávnění pro přístup k virtuální síti

Při vytváření prostředí integrační služby (ISE), můžete vybrat virtuální síť Azure jako *peer* pro vaše prostředí. Můžete však *pouze* vytvoření tohoto vztahu nebo *partnerský vztah*, při vytváření vašeho ISE. Tento vztah umožňuje vaše ISE přístup k prostředkům ve virtuální síti, kterou pak můžou aplikace logiky v, aby se ISE připojit přímo k prostředkům ve vaší virtuální síti. Pro místní systémy ve virtuální síti, který je propojen ISE aplikace logiky přímo přístupné tyto systémy pomocí některé z těchto položek: 

* ISE konektor pro daný systém, například SQL Server

* Akce HTTP 

* Vlastní konektor

Pro místní systémy, které nejsou ve virtuální síti nebo nemají ISE konektory, můžete se připojit až [nastavit a používat místní brány dat](../logic-apps/logic-apps-gateway-install.md).

Pro vaše prostředí, abyste mohli vybrat jako partnerské virtuální síti Azure, musíte nastavit oprávnění řízení přístupu na základě Role (RBAC) ve vaší virtuální síti pro službu Azure Logic Apps. Tato úloha vyžaduje, abyste přiřadili **Přispěvatel sítě** a **Přispěvatel modelu Classic** role ve službě Azure Logic Apps. Další informace o roli oprávnění požadovaná pro partnerský vztah, najdete v článku [oprávnění tématu Vytvoření, změna nebo odstranění partnerského vztahu virtuálních sítí](../virtual-network/virtual-network-manage-peering.md#permissions).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Účty pro integraci s ISE

Můžete použít účty pro integraci s logic apps, které běží v prostředí integrační služby (ISE), ale také musí používat tyto účty pro integraci *stejné ISE* jako propojené logic apps. Aplikace logiky do ISE může odkazovat pouze tyto účty pro integraci, které jsou ve stejném ISE. Při vytváření účtu pro integraci, můžete vybrat vaše ISE jako umístění pro váš účet integrace.

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">fórum Azure Logic Apps</a>.
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte <a href="http://aka.ms/logicapps-wish" target="_blank">web zpětné vazby od uživatelů Logic Apps</a>.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [připojit k virtuálním sítím Azure z izolované logic apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Další informace o [integrace služby virtual network pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
