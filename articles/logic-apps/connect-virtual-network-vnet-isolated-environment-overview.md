---
title: Přístup k virtuálním sítím Azure
description: Přehled o tom, jak prostředí služby integrace (ISEs) pomáhají aplikacím logiky přistupovat k virtuálním sítím Azure (VNET)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127246"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Přístup k prostředkům virtuální sítě Azure z Azure Logic Apps pomocí prostředí integračních služeb (ISEs)

Aplikace logiky někdy potřebují přístup k zabezpečeným prostředkům, jako jsou virtuální počítače (VM) a další systémy nebo služby, které jsou uvnitř [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md). Chcete-li nastavit tento přístup, můžete [vytvořit *prostředí služby integrace* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) ISE je izolovaná instance služby Logic Apps, která používá vyhrazené prostředky a běží odděleně od "globální" služby Logic Apps pro více klientů.

Spuštění aplikací logiky ve vlastní samostatné izolované instanci pomáhá snížit dopad, který mohou mít ostatní klienti Azure na výkon vašich aplikací, označovaný také jako [efekt "hlučné sousedy".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) ISE také poskytuje tyto výhody:

* Vlastní statické IP adresy, které jsou oddělené od statických IP adres, které jsou sdíleny aplikacemi logiky ve službě s více klienty. Můžete také nastavit jednu veřejnou, statickou a předvídatelnou odchozí ADRESU IP pro komunikaci s cílovými systémy. Tímto způsobem není třeba nastavit další otvory brány firewall v těchto cílových systémech pro každou ISE.

* Zvýšená omezení doby běhu, uchovávání úložiště, propustnost, časové limity požadavků http a odpovědí, velikosti zpráv a vlastní požadavky konektoru. Další informace najdete v [tématu Limity a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md).

> [!NOTE]
> Některé virtuální sítě Azure používají privátní koncové body[(Azure Private Link)](../private-link/private-link-overview.md)pro poskytování přístupu ke službám Azure PaaS, jako je Azure Storage, Azure Cosmos DB nebo Azure SQL Database, partnerské služby nebo zákaznické služby, které jsou hostované v Azure. Pokud vaše aplikace logiky potřebují přístup k virtuálním sítím, které používají privátní koncové body, musíte tyto aplikace logiky vytvořit, nasadit a spustit uvnitř služby ISE.

Když vytvoříte ISE, Azure *vloží* nebo nasadí tuto službu ISE do virtuální sítě Azure. Tuto službu ISE pak můžete použít jako umístění pro aplikace logiky a účty integrace, které potřebují přístup.

![Vybrat prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Aplikace logiky můžete přistupovat k prostředkům, které jsou uvnitř nebo připojené k virtuální síti pomocí těchto položek, které běží ve stejné ISE jako vaše aplikace logiky:

* Integrovaná aktivační událost nebo akce s popiskem **CORE,** například aktivační událost nebo akce HTTP
* Konektor s označením **ISE**pro tento systém nebo službu
* Vlastní konektor

Stále můžete také použít konektory, které nemají štítek **CORE** nebo **ISE** s aplikacemi logiky ve vašem ISE. Tyto konektory běží ve službě Logic Apps s více tenanty. Další informace naleznete v těchto částech:

* [Izolované versus víceklientské](#difference)
* [Připojení z prostředí integrační služby](../connectors/apis-list.md#integration-service-environment)
* [Konektory ISE](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Aplikace logiky, integrované aktivační události, předdefinované akce a konektory spuštěné ve vaší službě ISE používají cenový plán, který se liší od cenového plánu založeného na spotřebě. Další informace naleznete v [tématu Logic Apps cenový model](../logic-apps/logic-apps-pricing.md#fixed-pricing). Podrobnosti o cenách najdete v [tématu Logic Apps pricing](../logic-apps/logic-apps-pricing.md).

Tento přehled popisuje další informace o tom, jak ISE poskytuje vašim aplikacím logiky přímý přístup k virtuální síti Azure a porovnává rozdíly mezi ISE a službou Logic Apps s více klienty.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Izolované versus víceklientské

Při vytváření a spouštění aplikací logiky v ise získáte stejné uživatelské prostředí a podobné funkce jako služba Logic Apps s více tenanty. Můžete použít všechny stejné integrované aktivační události, akce a spravované konektory, které jsou k dispozici ve službě Logic Apps s více tenanty. Některé spravované konektory nabízejí další verze ISE. Rozdíl mezi konektory ISE a konektory bez ISE existují v where they run a popisky, které mají v Návrháři aplikací logiky při práci v rámci ise.

![Konektory s popisky i bez štítků v ise](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Vestavěné aktivační události a akce zobrazují popisek **CORE.** Vždy běží ve stejné ISE jako aplikace logiky. Spravované konektory, které zobrazují popisek **ISE** také spustit ve stejné ISE jako aplikace logiky.

  Zde jsou například některé konektory, které nabízejí verze ISE:

  * Úložiště objektů blob Azure, úložiště souborů a úložiště tabulek
  * Fronty Azure, Azure Service Bus, Azure Event Hubs a IBM MQ
  * FTP a SFTP-SSH
  * SQL Server, Datový sklad Azure SQL, Azure Cosmos DB
  * AS2, X12 a EDIFACT

* Spravované konektory, které nezobrazují žádné další popisky vždy spustit ve službě Logic Apps s více klienty, ale můžete stále používat tyto konektory v aplikaci logiky hostované ISE.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Přístup k místním systémům

Pro přístup k místním systémům nebo zdrojům dat, které jsou připojené k virtuální síti Azure, aplikace logiky v ise můžete použít tyto položky:

* Akce HTTP

* Konektor označený systémem ISE pro tento systém

  > [!NOTE]
  > Chcete-li použít ověřování systému Windows s konektorem SQL Server v [prostředí služby integrace (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)použijte verzi konektoru, která není ise, s [místní bránou dat](../logic-apps/logic-apps-gateway-install.md). Verze označená službou ISE nepodporuje ověřování systému Windows.

* Vlastní konektor

  * Pokud máte vlastní konektory, které vyžadují místní bránu dat a vytvořili jste tyto konektory mimo ISE, aplikace logiky v ISE můžete také použít tyto konektory.

  * Vlastní konektory vytvořené v ise nefungují s místní bránou dat. Tyto konektory však mohou přímo přistupovat k místním zdrojům dat, které jsou připojeny k virtuální síti hostující službu ISE. Takže aplikace logiky v ISE s největší pravděpodobností nepotřebují bránu dat při komunikaci s těmito prostředky.

Pro místní systémy, které nejsou připojené k virtuální síti nebo nemají konektory s popiskem ISE, musíte nejprve [nastavit místní bránu dat,](../logic-apps/logic-apps-gateway-install.md) než se vaše aplikace logiky budou moct připojit k těmto systémům.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKUs

Při vytváření služby ISE můžete vybrat skladovou položku vývojáře nebo výhodu premium. Zde jsou rozdíly mezi těmito sku:

* **Developer**

  Poskytuje levnější ise, které můžete použít pro experimentování, vývoj a testování, ale ne pro produkční nebo testování výkonu. Vývojářská skladová položka obsahuje předdefinované aktivační události a akce, standardní konektory, podnikové konektory a jeden účet integrace [úrovně Free](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) za pevnou měsíční cenu. Tato skladová položka však neobsahuje žádnou smlouvu o úrovni služeb (SLA), možnosti pro navýšení kapacity nebo redundanci během recyklace, což znamená, že může dojít ke zpoždění nebo prostojům.

* **Premium**

  Poskytuje službu ISE, kterou můžete použít pro produkční prostředí, a zahrnuje podporu sla, integrované aktivační události a akce, standardní konektory, podnikové konektory, jeden účet integrace [úrovně Standard,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) možnosti pro zvýšení kapacity a redundanci během recyklace za pevnou měsíční cenu.

> [!IMPORTANT]
> Možnost skladové položky je k dispozici pouze při vytváření služby ISE a nelze ji později změnit.

Cenové sazby najdete v tématu [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps/). Informace o tom, jak fungují ceny a fakturace pro ises, najdete v tématu [Cenové homodelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Přístup ke koncovému bodu ISE

Při vytváření ise můžete použít interní nebo externí koncové body přístupu. Váš výběr určuje, zda požadavek nebo webhooku aktivační události na aplikace logiky ve vašem ISE můžete přijímat volání z mimo virtuální síť.

Tyto koncové body také ovlivňují způsob, jakým můžete přistupovat k vstupům a výstupům v historii spuštění aplikací logiky.

* **Interní**: Soukromé koncové body, které povolují volání aplikací logiky ve vašem ISE, kde můžete zobrazit a přistupovat ke vstupům a výstupům aplikací logiky v historii spuštění *pouze z virtuální sítě.*

* **Externí**: Veřejné koncové body, které umožňují volání aplikací logiky ve vaší službě ISE, kde můžete zobrazit a přistupovat ke vstupům a výstupům aplikací logiky v historii běhu *mimo virtuální síť*. Pokud používáte skupiny zabezpečení sítě (NSG), ujistěte se, že jsou nastaveny s příchozí pravidla povolit přístup ke vstupu a výstupům historie spuštění. Další informace naleznete v [tématu Povolení přístupu pro systém ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> Možnost koncového bodu přístupu je k dispozici pouze při vytváření služby ISE a nelze ji později změnit.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrační účty s ISE

Účty integrace s aplikacemi logiky v prostředí služby integrace (ISE). Tyto účty integrace však musí používat *stejné ISE* jako propojené aplikace logiky. Aplikace logiky v ise můžete odkazovat pouze na ty účty integrace, které jsou ve stejné ISE. Při vytváření účtu integrace můžete vybrat ise jako umístění pro váš účet integrace. Informace o tom, jak fungují ceny a fakturace pro účty integrace pomocí služby ISE, najdete v [tématu Model oceňování aplikací Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Další kroky

* [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Další informace o [virtuální síti Azure](../virtual-network/virtual-networks-overview.md)
* Informace o [integraci virtuálních sítí pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
