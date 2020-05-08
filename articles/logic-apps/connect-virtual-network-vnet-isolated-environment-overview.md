---
title: Přístup k virtuálním sítím Azure
description: Přehled o tom, jak prostředí ISEs (Integration Service Environment) pomůžou přístup k Logic Apps přístup k virtuálním sítím Azure (virtuální sítě)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: d74303df74a1e877645b333fa0726a68055c819b
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734908"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Přístup k prostředkům Azure Virtual Network z Azure Logic Apps pomocí prostředí integračních služeb (ISEs)

V některých případech vaše aplikace logiky potřebuje přístup k zabezpečeným prostředkům, jako jsou virtuální počítače (VM) a další systémy nebo služby, které se nacházejí ve [službě Azure Virtual Network](../virtual-network/virtual-networks-overview.md). K nastavení tohoto přístupu můžete [vytvořit *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). ISE je izolovaná instance Logic Apps služby, která využívá vyhrazené prostředky a spouští se odděleně od "globální" víceklientské služby Logic Apps.

Spouštění Logic Apps ve vaší vlastní samostatné izolované instanci pomáhá snižovat dopad, který můžou mít ostatní klienti Azure na výkon vašich aplikací, označované taky jako ["vzdálení" sousedních směrovačů](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). ISE také poskytuje tyto výhody:

* Vaše vlastní statické IP adresy, které jsou oddělené od statických IP adres, které jsou sdíleny pomocí aplikace logiky ve službě pro více tenantů. Pro komunikaci s cílovými systémy můžete také nastavit jednu veřejnou, statickou a předvídatelná odchozí IP adresu. Tímto způsobem není nutné nastavovat další otevřená brána firewall v těchto cílových systémech pro každý ISE.

* Zvýšené limity doby trvání běhu, uchovávání úložiště, propustnosti, požadavků HTTP a časových limitů odpovědí, velikostí zpráv a požadavků vlastních konektorů. Další informace najdete v tématu [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md).

> [!NOTE]
> Některé virtuální sítě Azure používají pro poskytování přístupu ke službám Azure PaaS, jako jsou například Azure Storage, Azure Cosmos DB nebo Azure SQL Database, partnerské služby nebo služby zákazníkům hostované v Azure, privátní koncové body ([privátní propojení Azure](../private-link/private-link-overview.md)). Pokud vaše aplikace logiky potřebují přístup k virtuálním sítím, které používají privátní koncové body, musíte tyto aplikace logiky vytvořit, nasadit a spustit v rámci ISE.

Když vytvoříte ISE, Azure *vloží* nebo nasadí tento ISE do vaší virtuální sítě Azure. Tuto ISE pak můžete použít jako umístění pro aplikace logiky a účty pro integraci, které potřebují přístup.

![Vybrat prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Aplikace logiky mají přístup k prostředkům, které jsou uvnitř nebo připojeny k virtuální síti pomocí těchto položek, které se spouštějí ve stejném ISE jako vaše aplikace logiky:

* Integrovaná aktivační událost nebo akce s označením **Core**, jako je například Trigger nebo akce http
* Konektor **ISE**s popiskem pro daný systém nebo službu
* Vlastní konektor

I nadále můžete použít konektory, které nemají štítek **Core** nebo **ISE** , s Logic Apps v ISE. Tyto konektory se spouštějí ve službě víceklientské Logic Apps služby. Další informace najdete v těchto částech:

* [Izolované oproti více tenantů](#difference)
* [Připojení z prostředí integrační služby](../connectors/apis-list.md#integration-service-environment)
* [Konektory ISE](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Logic Apps, integrované triggery, integrované akce a konektory spouštěné ve vašem ISE používají Cenový tarif, který se liší od cenového plánu založeného na spotřebě. Další informace najdete v tématu [Logic Apps cenového modelu](../logic-apps/logic-apps-pricing.md#fixed-pricing). Podrobnosti o cenách najdete v tématu [Logic Apps ceny](../logic-apps/logic-apps-pricing.md).

Tento přehled popisuje další informace o tom, jak ISE poskytuje Logic Apps přímý přístup k vaší virtuální síti Azure a porovnává rozdíly mezi ISE a službou víceklientské Logic Apps.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Izolované oproti více tenantů

Když vytváříte a spouštíte Logic Apps v ISE, získáte stejné uživatelské prostředí a podobné možnosti jako služba pro více tenantů Logic Apps. Můžete použít všechny stejné integrované triggery, akce a spravované konektory, které jsou k dispozici ve víceklientské Logic Apps službě. Některé spravované konektory nabízejí další verze ISE. Rozdíl mezi konektory ISE a neISEými konektory existují v místě, kde se spouštějí, a popisky, které mají v návrháři aplikace logiky při práci v rámci ISE.

![Konektory s popisky a bez nich v ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Vestavěné triggery a akce zobrazují **základní** popisek. Vždycky se spouštějí ve stejném ISE jako vaše aplikace logiky. Spravované konektory, které zobrazují popisek **ISE** , se také spouštějí ve stejném ISE jako vaše aplikace logiky.

  Například tady jsou některé konektory, které nabízejí verze ISE:

  * Blob Storage Azure, File Storage a Table Storage
  * Fronty Azure, Azure Service Bus, Azure Event Hubs a IBM MQ
  * FTP a SFTP – SSH
  * SQL Server, Azure SQL Data Warehouse Azure Cosmos DB
  * AS2, X12 a EDIFACT

* Spravované konektory, které neobsahují žádné další popisky, se vždycky spouštějí ve víceklientské Logic Apps službě, ale tyto konektory můžete používat i v aplikaci logiky hostované v ISE.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Přístup k místním systémům

Pro přístup k místním systémům nebo zdrojům dat, které jsou připojené k virtuální síti Azure, můžou Logic Apps ve službě ISE používat tyto položky:

* Akce HTTP

* Konektor ISE s popiskem pro tento systém

  > [!NOTE]
  > Pokud chcete používat ověřování systému Windows s konektorem SQL Server v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), použijte jinou verzi konektoru než ISE s místní [bránou dat](../logic-apps/logic-apps-gateway-install.md). Verze ISE s popiskem nepodporuje ověřování systému Windows.

* Vlastní konektor

  * Pokud máte vlastní konektory, které vyžadují místní bránu dat, a Vy jste tyto konektory vytvořili mimo ISE, můžete tyto konektory použít i v ISE.

  * Vlastní konektory vytvořené ve ISE nefungují s místní bránou dat. Tyto konektory mají ale přímý přístup k místním zdrojům dat, které jsou připojené k virtuální síti hostující ISE. Proto Logic Apps v ISE pravděpodobně nepotřebují bránu dat při komunikaci s těmito prostředky.

U místních systémů, které nejsou připojené k virtuální síti nebo které nemají konektory ISE, musíte nejdřív [nastavit místní bránu dat](../logic-apps/logic-apps-gateway-install.md) , než se aplikace logiky můžou k těmto systémům připojit.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU

Při vytváření ISE můžete vybrat SKU pro vývojáře nebo SKU úrovně Premium. Zde jsou rozdíly mezi těmito SKU:

* **Vývojář**

  Poskytuje ISE s nižšími náklady, které můžete použít pro experimentování, vývoj a testování, ale ne pro produkční nebo výkonnostní testování. SKU pro vývojáře obsahuje integrované triggery a akce, standardní konektory, podnikové konektory a jeden účet pro integraci [bezplatné úrovně](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) za pevnou měsíční cenu. Tato SKU ale nezahrnuje žádnou smlouvu o úrovni služeb (SLA), možnosti pro škálování kapacity nebo redundanci při recyklaci, což znamená, že se můžete setkat s prodlevami nebo výpadky.

* **Premium**

  Poskytuje ISE, který můžete použít pro produkční prostředí, včetně podpory SLA, integrovaných triggerů a akcí, standardních konektorů, podnikových konektorů, jednoho účtu pro integraci na [standardní úrovni](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , možností škálování kapacity a redundance během recyklace za pevnou měsíční cenu.

> [!IMPORTANT]
> Možnost SKU je k dispozici pouze při vytváření ISE a nedá se změnit později.

Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/). Informace o cenách a fakturační práci pro ISEs najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Přístup ke koncovému bodu ISE

Při vytváření ISE můžete použít buď interní nebo externí koncové body přístupu. Váš výběr určuje, jestli žádosti nebo triggery Webhooku v Logic Apps ve vašem ISE můžou přijímat volání z vnějšku vaší virtuální sítě. Tyto koncové body ovlivňují také způsob, jakým máte přístup ke vstupům a výstupům z historie spuštění aplikace logiky.

> [!IMPORTANT]
> Koncový bod přístupu můžete vybrat jenom během vytváření ISE a tuto možnost nemůžete později změnit.

* **Interní**: soukromé koncové body umožňují volání Logic Apps ve vašich ISE, kde můžete zobrazit a přistupovat ke vstupům a výstupům z historie spuštění Logic Apps *jenom z vaší virtuální sítě*. Ujistěte se, že máte síťové připojení mezi soukromými koncovými body a počítačem, ze kterého chcete získat přístup k historii spuštění. Například klientský počítač může existovat uvnitř virtuální sítě ISE nebo uvnitř virtuální sítě, která je připojená k virtuální síti ISE, například prostřednictvím partnerského vztahu nebo virtuální privátní sítě.

* **External**: veřejné koncové body umožňují volání Logic Apps ve vašich ISE, kde můžete zobrazit a přistupovat ke vstupům a výstupům z historie spuštění Logic Apps *z vnějšku vaší virtuální sítě*. Pokud používáte skupiny zabezpečení sítě (skupin zabezpečení sítě), zajistěte, aby byly nastavené s příchozími pravidly, aby bylo možné povolit přístup k vstupům a výstupům historie spuštění. Další informace najdete v tématu [Povolení přístupu pro ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Pokud chcete zjistit, jestli váš ISE používá interní nebo externí přístupový bod, v nabídce ISE v části **Nastavení**vyberte **vlastnosti**a najděte vlastnost **koncového bodu přístupu** :

![Najít koncový bod pro přístup k ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Účty pro integraci s ISE

Účty pro integraci s Logic Apps můžete používat v prostředí ISE (Integration Service Environment). Tyto účty pro integraci ale musí používat *stejné ISE* jako propojené aplikace logiky. Logic Apps ve ISE můžou odkazovat jenom na účty pro integraci, které jsou ve stejném ISE. Při vytváření účtu pro integraci můžete jako umístění účtu pro integraci vybrat ISE. Informace o tom, jak ceny a fakturace pro účty pro integraci s ISE najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Další kroky

* [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informace o [integraci virtuální sítě pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
