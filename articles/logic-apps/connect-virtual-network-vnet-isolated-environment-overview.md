---
title: Přístup k virtuálním sítím Azure
description: Přehled o tom, jak prostředí ISEs (Integration Service Environment) pomůžou přístup k Logic Apps přístup k virtuálním sítím Azure (virtuální sítě)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 12/16/2019
ms.openlocfilehash: d8d57c15fffaa6a9d18ad3c83716f99247512c15
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860741"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Přístup k prostředkům Azure Virtual Network z Azure Logic Apps pomocí prostředí integračních služeb (ISEs)

V některých případech vaše aplikace logiky a účty pro integraci potřebují přístup k zabezpečeným prostředkům, jako jsou virtuální počítače (VM) a další systémy nebo služby, které jsou ve [službě Azure Virtual Network](../virtual-network/virtual-networks-overview.md). K nastavení tohoto přístupu můžete [vytvořit prostředí ISE ( *Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , ve kterém můžete spouštět aplikace logiky a vytvořit účty pro integraci.

Při vytváření ISE Azure *vloží* do vaší virtuální sítě Azure, které ISE, nasadí soukromou a izolovanou instanci služby Logic Apps do vaší virtuální sítě Azure. Tato soukromá instance využívá vyhrazené prostředky, jako je úložiště, a spouští se samostatně z veřejné služby Logic Apps "Global", víceklientské služby. Oddělení vaší izolované privátní instance a veřejné globální instance také pomáhá snižovat dopad, který mohou mít jiní klienti Azure na výkon vašich aplikací, což se označuje také jako ["nepříznivých sousedních"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). ISE také poskytuje vlastní statické IP adresy. Tyto IP adresy jsou oddělené od statických IP adres, které jsou sdílené pomocí Logic Apps ve veřejné víceklientské službě.

Po vytvoření ISE můžete při vytváření vaší aplikace logiky nebo účtu pro integraci vybrat svou ISE jako svou aplikaci logiky nebo umístění účtu pro integraci:

![Vybrat prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Vaše aplikace logiky teď může přímo přistupovat k systémům, které jsou uvnitř nebo připojené k vaší virtuální síti pomocí kterékoli z těchto položek, které se spouštějí ve stejném ISE jako vaše aplikace logiky:

* Konektor **ISE**s popiskem pro tento systém
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

Logic Apps v ISE poskytují stejné uživatelské prostředí a podobné možnosti jako veřejná služba globálního Logic Apps. Můžete použít všechny stejné integrované triggery, akce a spravované konektory, které jsou k dispozici v globální Logic Apps službě. Některé spravované konektory nabízejí další verze ISE. Rozdíly existují v tom, kde se spouštějí, a popisky, které se zobrazují v návrháři aplikace logiky při práci v rámci ISE.

![Konektory s popisky a bez nich v ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-built-in-actions-triggers-managed-connectors.png)

* Vestavěné triggery a akce zobrazují **základní** popisek a vždy se spouštějí ve stejném ISE jako vaše aplikace logiky. Spravované konektory, které zobrazují popisek **ISE** , se také spouštějí ve stejném ISE jako vaše aplikace logiky.

  Například tady jsou některé konektory, které nabízejí verze ISE:

  * Blob Storage Azure, File Storage a Table Storage
  * Fronty Azure, Azure Service Bus, Azure Event Hubs a IBM MQ
  * FTP a SFTP – SSH
  * SQL Server, Azure SQL Data Warehouse Azure Cosmos DB
  * AS2, X12 a EDIFACT

* Spravované konektory, které neobsahují žádné další štítky, se vždy spouštějí ve veřejné globální Logic Apps službě, ale tyto konektory můžete používat i v aplikaci logiky založené na ISE.

ISE také poskytuje zvýšené limity pro dobu trvání běhu, uchovávání úložiště, propustnost, požadavky HTTP a vypršení časového limitu odpovědí, velikosti zpráv a požadavky vlastních konektorů. Další informace najdete v tématu [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU

Při vytváření ISE můžete vybrat SKU pro vývojáře nebo SKU úrovně Premium. Zde jsou rozdíly mezi těmito SKU:

* **Developer**

  Poskytuje ISE s nižšími náklady, které můžete použít pro experimentování, vývoj a testování, ale ne pro produkční nebo výkonnostní testování. SKU pro vývojáře obsahuje integrované triggery a akce, standardní konektory, podnikové konektory a jeden účet pro integraci [bezplatné úrovně](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) za pevnou měsíční cenu. Tato SKU ale nezahrnuje žádnou smlouvu o úrovni služeb (SLA), možnosti pro škálování kapacity nebo redundanci při recyklaci, což znamená, že se můžete setkat s prodlevami nebo výpadky.

* **Premium**

  Poskytuje ISE, který můžete použít pro produkční prostředí, včetně podpory SLA, integrovaných triggerů a akcí, standardních konektorů, podnikových konektorů, jednoho účtu pro integraci na [standardní úrovni](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , možností škálování kapacity a redundance během recyklace za pevnou měsíční cenu.

> [!IMPORTANT]
> Možnost SKU je k dispozici pouze při vytváření ISE a nedá se změnit později.

Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/). Informace o cenách a fakturační práci pro ISEs najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Přístup ke koncovému bodu ISE

Při vytváření ISE můžete použít buď interní nebo externí koncové body přístupu. Váš výběr určuje, jestli žádosti nebo triggery Webhooku v Logic Apps ve vašem ISE můžou přijímat volání z vnějšku vaší virtuální sítě.

Tyto koncové body ovlivňují také způsob, jakým máte přístup ke vstupům a výstupům v historii spuštění aplikace logiky.

* **Interní**: veřejné koncové body, které umožňují volání Logic Apps ve vašich ISE, kde můžete zobrazovat vstupy a výstupy Logic Apps jenom v historii spouštění v *rámci vaší virtuální sítě* .

* **External**: veřejné koncové body, které umožňují volání Logic Apps ve vašich ISE, kde můžete zobrazit vstupy a výstupy Logic Apps a získat přístup k vstupům a výstupům logiky v historii spouštění *mimo virtuální síť*. Pokud používáte skupiny zabezpečení sítě (skupin zabezpečení sítě), zajistěte, aby byly nastavené s příchozími pravidly, aby bylo možné povolit přístup k vstupům a výstupům historie spuštění. Další informace najdete v tématu [Povolení přístupu pro ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> Možnost přístupový bod přístupu je dostupná jenom při vytváření ISE a nedá se změnit později.

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Přístup k místním zdrojům dat

Pro místní systémy, které jsou připojené k virtuální síti Azure, zaISE do této sítě, aby vaše aplikace logiky mohly přímý přístup k těmto systémům pomocí kterékoli z těchto položek:

* Akce HTTP

* Konektor ISE s popiskem pro tento systém

  > [!NOTE]
  > Pokud chcete používat ověřování systému Windows s konektorem SQL Server v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), použijte jinou verzi konektoru než ISE s místní [bránou dat](../logic-apps/logic-apps-gateway-install.md). Verze ISE s popiskem nepodporuje ověřování systému Windows.

* Vlastní konektor

  * Pokud máte vlastní konektory, které vyžadují místní bránu dat, a Vy jste tyto konektory vytvořili mimo ISE, můžete tyto konektory použít i v ISE.
  
  * Vlastní konektory vytvořené ve ISE nefungují s místní bránou dat. Tyto konektory mají ale přímý přístup k místním zdrojům dat, které jsou připojené k virtuální síti hostující ISE. Proto Logic Apps v ISE pravděpodobně nepotřebují bránu dat při komunikaci s těmito prostředky.

U místních systémů, které nejsou připojené k virtuální síti nebo které nemají konektory ISE-labled, musíte nejdřív nastavit místní [bránu dat](../logic-apps/logic-apps-gateway-install.md) , než se aplikace logiky můžou k těmto systémům připojit.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Účty pro integraci s ISE

Účty pro integraci s Logic Apps můžete používat v prostředí ISE (Integration Service Environment). Tyto účty pro integraci ale musí používat *stejné ISE* jako propojené aplikace logiky. Logic Apps ve ISE můžou odkazovat jenom na účty pro integraci, které jsou ve stejném ISE. Při vytváření účtu pro integraci můžete jako umístění účtu pro integraci vybrat ISE. Informace o tom, jak ceny a fakturace pro účty pro integraci s ISE najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Další kroky

* [Připojení k virtuálním sítím Azure z izolovaných aplikací logiky](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Přidání artefaktů do prostředí integračních služeb](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md)
* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informace o [integraci virtuální sítě pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
