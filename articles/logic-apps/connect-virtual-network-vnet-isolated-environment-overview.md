---
title: Přístup k virtuálním sítím Azure z Azure Logic Apps pomocí prostředí integračních služeb (ISEs)
description: Tento přehled popisuje, jak prostředí ISEs (Integration Service Environment) pomůžou přístup k Logic Apps přístup k virtuálním sítím Azure (virtuální sítě).
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/19/2019
ms.openlocfilehash: 3e14604955a64c7a146a947c5c320b42ea3ebcba
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325415"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Přístup k prostředkům Azure Virtual Network z Azure Logic Apps pomocí prostředí integračních služeb (ISEs)

V některých případech vaše aplikace logiky a účty pro integraci potřebují přístup k zabezpečeným prostředkům, jako jsou virtuální počítače (VM) a další systémy nebo služby, které jsou ve [službě Azure Virtual Network](../virtual-network/virtual-networks-overview.md). K nastavení tohoto přístupu můžete [vytvořit prostředí ISE ( *Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , ve kterém můžete spouštět aplikace logiky a vytvořit účty pro integraci.

Při vytváření ISE Azure *vloží* do vaší virtuální sítě Azure, které ISE, nasadí soukromou a izolovanou instanci služby Logic Apps do vaší virtuální sítě Azure. Tato soukromá instance využívá vyhrazené prostředky, jako je úložiště, a spouští se odděleně od veřejné Logic Apps služby Global. Oddělení vaší izolované privátní instance a veřejné globální instance také pomáhá snižovat dopad, který mohou mít jiní klienti Azure na výkon vašich aplikací, což se označuje také jako ["nepříznivých sousedních"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Po vytvoření ISE můžete při vytváření vaší aplikace logiky nebo účtu pro integraci vybrat svou ISE jako svou aplikaci logiky nebo umístění účtu pro integraci:

![Vybrat prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Vaše aplikace logiky teď může přímo přistupovat k systémům, které jsou uvnitř nebo připojené k vaší virtuální síti pomocí kterékoli z těchto položek:

* Konektor **ISE**s popiskem pro tento systém, například SQL Server
* Integrovaná aktivační událost nebo akce s označením **Core**, jako je například Trigger nebo akce http
* Vlastní konektor

Tento přehled popisuje další podrobnosti o tom, jak ISE poskytuje vašim aplikacím logiky a integračním účtům přímý přístup k vaší virtuální síti Azure a porovnává rozdíly mezi ISE a službou Global Logic Apps.

> [!IMPORTANT]
> Logic Apps, integrované triggery, integrované akce a konektory spouštěné ve vašem ISE používají Cenový tarif, který se liší od cenového plánu založeného na spotřebě. Informace o cenách a fakturační práci pro ISEs najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](../logic-apps/logic-apps-pricing.md).
>
> Vaše ISE také zvýšila omezení doby trvání běhu, uchovávání úložiště, propustnosti, požadavků HTTP a časových limitů odpovědí, velikostí zpráv a požadavků vlastních konektorů. 
> Další informace najdete v tématu [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Izolované versus globální

Když v Azure vytvoříte prostředí integrovaných služeb (ISE), můžete vybrat virtuální síť Azure, do které chcete *Vložit* své ISE. Azure potom do vaší virtuální sítě vloží nebo nasadí soukromou instanci služby Logic Apps. Tato akce vytvoří izolované prostředí, kde můžete vytvářet a spouštět aplikace logiky na vyhrazených prostředcích. Když vytvoříte aplikaci logiky, vyberete svou ISE jako umístění vaší aplikace, což umožní vaší aplikaci logiky přímý přístup k vaší virtuální síti a prostředkům v této síti.

Logic Apps v ISE poskytují stejné uživatelské prostředí a podobné možnosti jako služba Global Logic Apps. Nestačí, když použijete stejné integrované aktivační události, integrované akce a konektory z globální Logic Apps služby, ale můžete také použít konektory specifické pro ISE. Tady jsou například některé standardní konektory, které nabízejí verze, které běží na ISE:

* Blob Storage Azure, File Storage a Table Storage
* Fronty Azure, Azure Service Bus, Azure Event Hubs a IBM MQ
* FTP a SFTP – SSH
* SQL Server, SQL Data Warehouse Azure Cosmos DB
* AS2, X12 a EDIFACT

Rozdíl mezi konektory ISE a non-ISE je v umístěních, kde se spouštějí triggery a akce:

* V ISE jsou integrované triggery a akce, jako je HTTP, vždy spouštěny ve stejném ISE jako aplikace logiky a zobrazují **základní** popisek.

  ![Výběr vestavěných aktivačních událostí a akcí "Core"](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Konektory, které běží v ISE, mají veřejně hostované verze, které jsou k dispozici v globálním Logic Apps službě. Pro konektory, které nabízí dvě verze, konektory s popiskem **ISE** se vždycky spouštějí ve stejném ISE jako vaše aplikace logiky. Konektory bez popisku **ISE** se spouštějí v globálním Logic Apps službě.

  ![Vybrat konektory ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

ISE také poskytuje zvýšené limity pro dobu trvání běhu, uchovávání úložiště, propustnost, požadavky HTTP a vypršení časového limitu odpovědí, velikosti zpráv a požadavky vlastních konektorů. Další informace najdete v tématu [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU

Při vytváření ISE můžete vybrat SKU pro vývojáře nebo SKU úrovně Premium. Zde jsou rozdíly mezi těmito SKU:

* **Developer**

  Poskytuje ISE s nižšími náklady, které můžete použít pro experimentování, vývoj a testování, ale ne pro produkční nebo výkonnostní testování. SKU pro vývojáře obsahuje integrované triggery a akce, standardní konektory, podnikové konektory a jeden účet pro integraci [bezplatné úrovně](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) za pevnou měsíční cenu. Tato SKU ale nezahrnuje žádnou smlouvu o úrovni služeb (SLA), možnosti pro škálování kapacity nebo redundanci při recyklaci, což znamená, že se můžete setkat s prodlevami nebo výpadky.

* **Premium**

  Poskytuje ISE, který můžete použít pro produkční prostředí, včetně podpory SLA, integrovaných triggerů a akcí, standardních konektorů, podnikových konektorů, jediného účtu pro integraci na úrovni [Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , možností škálování kapacity a redundance během recyklace za pevnou měsíční cenu.

Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/). Informace o cenách a fakturační práci pro ISEs najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Přístup k místním zdrojům dat

Pro místní systémy, které jsou připojené k virtuální síti Azure, zaISE do této sítě, aby vaše aplikace logiky mohly přímý přístup k těmto systémům pomocí kterékoli z těchto položek:

* Konektor ISE-Version pro tento systém, například SQL Server
* Akce HTTP
* Vlastní konektor

  * Pokud máte vlastní konektory, které vyžadují místní bránu dat, a Vy jste tyto konektory vytvořili mimo ISE, můžete tyto konektory použít i v ISE.
  
  * Vlastní konektory vytvořené ve ISE nefungují s místní bránou dat. Tyto konektory mají ale přímý přístup k místním zdrojům dat, které jsou připojené k virtuální síti hostující ISE. Proto Logic Apps v ISE pravděpodobně nepotřebují bránu dat při komunikaci s těmito prostředky.

U místních systémů, které nejsou připojené k virtuální síti nebo které nemají konektory ISE-Version, musíte nejdřív nastavit místní [bránu dat](../logic-apps/logic-apps-gateway-install.md) , než se aplikace logiky můžou k těmto systémům připojit.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Účty pro integraci s ISE

Účty pro integraci s Logic Apps můžete používat v prostředí ISE (Integration Service Environment). Tyto účty pro integraci ale musí používat *stejné ISE* jako propojené aplikace logiky. Logic Apps ve ISE můžou odkazovat jenom na účty pro integraci, které jsou ve stejném ISE. Při vytváření účtu pro integraci můžete jako umístění účtu pro integraci vybrat ISE. Informace o tom, jak ceny a fakturace pro účty pro integraci s ISE najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Další postup

* Přečtěte si, jak se [připojit k virtuálním sítím Azure z izolovaných aplikací logiky](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) .
* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informace o [integraci virtuální sítě pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
