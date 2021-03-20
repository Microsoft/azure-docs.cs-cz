---
title: Přístup k virtuálním sítím Azure
description: Přehled o tom, jak prostředí ISEs (Integration Service Environment) pomůžou přístup k Logic Apps přístup k virtuálním sítím Azure (virtuální sítě)
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 4d83609eea57c2350881360ef757b1a291627c23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374724"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Přístup k prostředkům Azure Virtual Network z Azure Logic Apps pomocí prostředí integračních služeb (ISEs)

V některých případech vaše aplikace logiky potřebuje přístup k zabezpečeným prostředkům, jako jsou virtuální počítače (VM) a další systémy nebo služby, které jsou uvnitř nebo připojené k [virtuální síti Azure](../virtual-network/virtual-networks-overview.md). K nastavení tohoto přístupu můžete [vytvořit *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). ISE je instance služby Logic Apps, která využívá vyhrazené prostředky a spouští se odděleně od "globální" víceklientské služby Logic Apps. Data ve ISE zůstanou ve stejné oblasti, ve které [vytvoříte a nasadíte tuto ISE](https://azure.microsoft.com/global-infrastructure/data-residency/).

Některé virtuální sítě Azure například používají privátní koncové body, které můžete nastavit prostřednictvím [privátního propojení Azure](../private-link/private-link-overview.md)a poskytnout tak přístup ke službám Azure PaaS, jako jsou Azure Storage, Azure Cosmos DB nebo Azure SQL Database, partnerské služby nebo služby zákazníkům hostované v Azure. Pokud vaše aplikace logiky potřebují přístup k virtuálním sítím, které používají privátní koncové body, musíte tyto aplikace logiky vytvořit, nasadit a spustit v rámci ISE.

Když vytvoříte ISE, Azure *vloží* nebo nasadí tento ISE do vaší virtuální sítě Azure. Tuto ISE pak můžete použít jako umístění pro aplikace logiky a účty pro integraci, které potřebují přístup.

![Vybrat prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

V tomto přehledu najdete další informace o tom, [Proč byste chtěli použít ISE](#benefits), [rozdíly mezi vyhrazenou a víceklientské Logic Apps službou](#difference)a způsob, jak získat přímý přístup k prostředkům, které jsou uvnitř nebo připojené ke službě Azure Virtual Network.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Proč používat ISE?

Spouštění Logic Apps ve vaší vlastní samostatné vyhrazené instanci pomáhá snižovat dopad, který mohou mít ostatní klienti Azure na výkon vašich aplikací, označované také jako ["nepříznivých sousedních směrovačů"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). ISE také poskytuje tyto výhody:

* Přímý přístup k prostředkům, které jsou uvnitř nebo připojené k vaší virtuální síti

  Logic Apps, které vytvoříte a spustíte ve ISE, můžou používat [speciálně navržené konektory, které běží ve vašich ISE](../connectors/apis-list.md#ise-connectors). Pokud konektor ISE existuje pro místní systém nebo zdroj dat, můžete se připojit přímo, aniž byste museli používat místní [bránu dat](../logic-apps/logic-apps-gateway-connection.md). Další informace najdete v části [vyhrazená oproti více tenantů](#difference) a [přístup k místním systémům](#on-premises) dále v tomto tématu.

* Pokračování přístupu k prostředkům, které jsou mimo nebo nejsou připojené k virtuální síti

  Logic Apps, které vytvoříte a spustíte ve službě ISE, můžou dál používat konektory, které běží ve víceklientské Logic Apps službě, když není k dispozici konektor pro konkrétní ISE. Další informace najdete v tématu [vyhrazeno versus více tenantů](#difference).

* Vaše vlastní statické IP adresy, které jsou oddělené od statických IP adres, které jsou sdíleny pomocí aplikace logiky ve službě pro více tenantů. Pro komunikaci s cílovými systémy můžete také nastavit jednu veřejnou, statickou a předvídatelná odchozí IP adresu. Tímto způsobem není nutné nastavovat další otevřená brána firewall v těchto cílových systémech pro každý ISE.

* Zvýšené limity doby trvání běhu, uchovávání úložiště, propustnosti, požadavků HTTP a časových limitů odpovědí, velikostí zpráv a požadavků vlastních konektorů. Další informace najdete v tématu [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Vyhrazeno versus více tenantů

Když vytváříte a spouštíte Logic Apps v ISE, získáte stejné uživatelské prostředí a podobné možnosti jako služba pro více tenantů Logic Apps. Můžete použít všechny stejné integrované triggery, akce a spravované konektory, které jsou k dispozici ve víceklientské Logic Apps službě. Některé spravované konektory nabízejí další verze ISE. Rozdíl mezi konektory ISE a neISEými konektory existují v místě, kde se spouštějí, a popisky, které mají v návrháři aplikace logiky při práci v rámci ISE.

![Konektory s popisky a bez nich v ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Integrované triggery a akce, jako je HTTP, zobrazují **základní** popisek a spouštějí se ve stejném ISE jako vaše aplikace logiky.

* Spravované konektory, které zobrazují popisek **ISE** , jsou speciálně navržené pro ISEs a *vždy se SPOUŠTĚJÍ ve stejném ISE jako vaše aplikace logiky*. Například tady jsou některé [konektory, které nabízejí verze ISE](../connectors/apis-list.md#ise-connectors):<p>

  * Blob Storage Azure, File Storage a Table Storage
  * Azure Service Bus, fronty Azure, Azure Event Hubs
  * Protokoly Azure Automation, Azure Key Vault, Azure Event Grid a Azure Monitor
  * FTP, SFTP – SSH, systém souborů a SMTP
  * SAP, IBM MQ, IBM DB2 a IBM 3270
  * SQL Server, analýza Azure synapse, Azure Cosmos DB
  * AS2, X12 a EDIFACT

  Pokud je konektor ISE dostupný pro místní systém nebo zdroj dat, můžete se s vzácnými výjimkami připojit přímo bez použití [místní brány dat](../logic-apps/logic-apps-gateway-connection.md). Další informace najdete v části [přístup k místním systémům](#on-premises) dále v tomto tématu.

* Spravované konektory, které nezobrazují popisek **ISE** , fungují i pro Logic Apps uvnitř ISE. Tyto konektory se *vždycky spouštějí ve službě Logic Apps více tenantů*, nikoli v ISE.

* Vlastní konektory, které vytvoříte *mimo ISE*, bez ohledu na to, jestli vyžadují místní [bránu dat](../logic-apps/logic-apps-gateway-connection.md), budou i nadále fungovat pro Logic Apps v rámci ISE. Vlastní konektory, které vytvoříte *ve ISE* , ale nebudou fungovat s místní bránou dat. Další informace najdete v tématu [přístup k místním systémům](#on-premises).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Přístup k místním systémům

Logic Apps, které běží v rámci ISE, můžou přímo přistupovat k místním systémům a zdrojům dat, které jsou uvnitř nebo připojené k virtuální síti Azure, pomocí těchto položek:<p>

* Aktivační událost nebo akce HTTP, která zobrazuje štítek **Core**

* Konektor **ISE** , pokud je k dispozici pro místní systém nebo zdroj dat

  Pokud je k dispozici konektor ISE, můžete získat přímý přístup k systému nebo zdroji dat bez místní [brány dat](../logic-apps/logic-apps-gateway-connection.md). Pokud ale potřebujete získat přístup k SQL Server z ISE a používat ověřování systému Windows, musíte použít verzi non-ISE konektoru a místní bránu dat. Verze ISE konektoru nepodporuje ověřování systému Windows. Další informace najdete v tématech [konektory ISE](../connectors/apis-list.md#ise-connectors) a [připojení z prostředí integrační služby](../connectors/apis-list.md#integration-service-environment).

* Vlastní konektor

  * Vlastní konektory, které vytvoříte *mimo ISE*, bez ohledu na to, jestli vyžadují místní [bránu dat](../logic-apps/logic-apps-gateway-connection.md), budou i nadále fungovat pro Logic Apps v rámci ISE.

  * Vlastní konektory, které vytvoříte *ve ISE* , nefungují s místní bránou dat. Tyto konektory mají ale přímý přístup k místním systémům a zdrojům dat, které jsou uvnitř nebo připojené k virtuální síti, která je hostitelem vaší ISE. Proto Logic Apps, které jsou uvnitř ISE, obvykle nepotřebují bránu dat při přístupu k těmto prostředkům.

Chcete-li získat přístup k místním systémům a zdrojům dat, které nemají konektory ISE, jsou mimo vaši virtuální síť nebo nejsou připojené k virtuální síti, je stále nutné použít místní bránu dat. Logic Apps v rámci ISE můžou dál používat konektory, které nemají štítek **Core** nebo **ISE** . Tyto konektory se spouštějí ve víceklientské Logic Apps službě, nikoli v ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU

Při vytváření ISE můžete vybrat SKU pro vývojáře nebo SKU úrovně Premium. Tato možnost SKU je k dispozici pouze při vytváření ISE a nedá se změnit později. Zde jsou rozdíly mezi těmito SKU:

* **Vývojář**

  Poskytuje ISE s nižšími náklady, které můžete použít pro zkoumání, experimenty, vývoj a testování, ale ne pro produkční nebo výkonnostní testování. SKU pro vývojáře obsahuje integrované triggery a akce, standardní konektory, podnikové konektory a jeden účet pro integraci [bezplatné úrovně](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) za [pevnou měsíční cenu](https://azure.microsoft.com/pricing/details/logic-apps). 

  > [!IMPORTANT]
  > Tato SKU nemá žádnou smlouvu o úrovni služeb (SLA), schopnost horizontálního navýšení kapacity ani redundanci při recyklaci, což znamená, že se může vyskytnout zpoždění nebo výpadek. Aktualizace back-endu můžou službu obcházet bez výpadků.

  Informace o kapacitě a omezeních najdete [v tématu omezení ISE v Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Informace o tom, jak účtování funguje pro ISEs, najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

* **Premium**

  Poskytuje ISE, který můžete použít k testování produkčního prostředí a výkonu. SKU Premium zahrnuje podporu smlouvy SLA, integrovaných triggerů a akcí, standardních konektorů, podnikových konektorů, jediného účtu pro integraci na úrovni [Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , možností horizontálního navýšení kapacity a redundance během recyklace za [pevnou měsíční cenu](https://azure.microsoft.com/pricing/details/logic-apps).

  Informace o kapacitě a omezeních najdete [v tématu omezení ISE v Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Informace o tom, jak účtování funguje pro ISEs, najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Přístup ke koncovému bodu ISE

Při vytváření ISE můžete použít buď interní nebo externí koncové body přístupu. Váš výběr určuje, jestli žádosti nebo triggery Webhooku v Logic Apps ve vašem ISE můžou přijímat volání z vnějšku vaší virtuální sítě. Tyto koncové body ovlivňují také způsob, jakým máte přístup ke vstupům a výstupům z historie spuštění aplikace logiky.

> [!IMPORTANT]
> Koncový bod přístupu můžete vybrat jenom během vytváření ISE a tuto možnost nemůžete později změnit.

* **Interní**: soukromé koncové body umožňují volání Logic Apps ve vašich ISE, kde můžete zobrazit a přistupovat ke vstupům a výstupům z historie spuštění Logic Apps *jenom z vaší virtuální sítě*.

  > [!IMPORTANT]
  > Pokud potřebujete tyto triggery založené na webhookech použít, při vytváření ISE použijte externí koncové body, *ne* interní koncové body:
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (verze ISE)
  > 
  > Také se ujistěte, že máte síťové připojení mezi soukromými koncovými body a počítačem, ze kterého chcete získat přístup k historii spuštění. V opačném případě se při pokusu o zobrazení historie spuštění vaší aplikace logiky zobrazí chybová zpráva oznamující, že došlo k neočekávané chybě. Nepovedlo se načíst.
  >
  > ![Azure Storage chyba akce, která způsobila neschopnost odesílat přenosy přes bránu firewall](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Například klientský počítač může existovat uvnitř virtuální sítě ISE nebo uvnitř virtuální sítě, která je připojená k virtuální síti ISE prostřednictvím partnerského vztahu nebo virtuální privátní sítě. 

* **External**: veřejné koncové body umožňují volání Logic Apps ve vašich ISE, kde můžete zobrazit a přistupovat ke vstupům a výstupům z historie spuštění Logic Apps *z vnějšku vaší virtuální sítě*. Pokud používáte skupiny zabezpečení sítě (skupin zabezpečení sítě), zajistěte, aby byly nastavené s příchozími pravidly, aby bylo možné povolit přístup k vstupům a výstupům historie spuštění. Další informace najdete v tématu [Povolení přístupu pro ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Pokud chcete zjistit, jestli váš ISE používá interní nebo externí přístupový bod, v nabídce ISE v části **Nastavení** vyberte **vlastnosti** a najděte vlastnost **koncového bodu přístupu** :

![Najít koncový bod pro přístup k ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Cenový model

Logic Apps, integrované triggery, integrované akce a konektory, které běží ve vašem ISE, používají pevný Cenový tarif, který se liší od cenového plánu založeného na spotřebě. Další informace najdete v tématu [Logic Apps cenového modelu](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Účty pro integraci s ISE

Účty pro integraci s Logic Apps můžete používat v prostředí ISE (Integration Service Environment). Tyto účty pro integraci ale musí používat *stejné ISE* jako propojené aplikace logiky. Logic Apps ve ISE můžou odkazovat jenom na účty pro integraci, které jsou ve stejném ISE. Při vytváření účtu pro integraci můžete jako umístění účtu pro integraci vybrat ISE. Informace o tom, jak ceny a fakturace pro účty pro integraci s ISE najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/). Omezení informací najdete v tématu [omezení účtu pro integraci](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="next-steps"></a>Další kroky

* [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informace o [integraci virtuální sítě pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
