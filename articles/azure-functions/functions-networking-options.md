---
title: Možnosti Azure Functions sítě
description: Přehled všech možností sítě, které jsou k dispozici v Azure Functions.
author: alexkarcher-msft
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 34759077bd7223d17fea70d32bda63fd1b2595eb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668127"
---
# <a name="azure-functions-networking-options"></a>Možnosti Azure Functions sítě

Tento článek popisuje síťové funkce, které jsou dostupné napříč možnostmi hostování pro Azure Functions. Všechny následující možnosti sítě umožňují přístup k prostředkům bez použití adres, které jsou směrovatelný v Internetu, nebo k omezení přístupu k Internetu do aplikace Function App.

Modely hostování mají k dispozici různé úrovně izolace sítě. Výběr správné verze vám pomůže splnit požadavky na izolaci sítě.

Aplikace Function App můžete hostovat několika způsoby:

* Existuje sada možností plánu, které běží na infrastruktuře s více klienty, a to s různými úrovněmi možností připojení a škálování virtuální sítě:
    * [Plán spotřeby](functions-scale.md#consumption-plan), který se dynamicky škáluje v reakci na načtení a nabízí minimální možnosti izolace sítě.
    * [Plán Premium](functions-scale.md#premium-plan), který se také dynamicky škáluje a zároveň nabízí komplexnější izolaci sítě.
    * Plán Azure [App Service](functions-scale.md#app-service-plan), který pracuje s pevnou škálou a nabízí podobnou izolaci sítě jako plán Premium.
* Funkce můžete spouštět v [App Service Environment](../app-service/environment/intro.md). Tato metoda nasadí vaši funkci do vaší virtuální sítě a nabízí kompletní řízení a izolaci sítě.

## <a name="matrix-of-networking-features"></a>Matice síťových funkcí

|                |[Plán spotřeby](functions-scale.md#consumption-plan)|[Plán Premium](functions-scale.md#premium-plan)|[Plán služby App Service](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Omezení příchozí IP adresy & přístupu k privátnímu webu](#inbound-ip-restrictions)|✅Ano|✅Ano|✅Ano|✅Ano|
|[Integrace virtuální sítě](#virtual-network-integration)|❌ne|✅Ano (místní)|✅Ano (místní a brána)|✅Ano|
|[Aktivační události virtuální sítě (jiné než HTTP)](#virtual-network-triggers-non-http)|❌ne| ❌ne|✅Ano|✅Ano|
|[Hybrid Connections](#hybrid-connections)|❌ne|✅Ano|✅Ano|✅Ano|
|[Omezení odchozích IP adres](#outbound-ip-restrictions)|❌ne| ❌ne|❌ne|✅Ano|

## <a name="inbound-ip-restrictions"></a>Omezení příchozích IP adres

Omezení IP adres můžete použít k definování seznamu IP adres seřazených podle priority, které mají povolený nebo odepřený přístup k vaší aplikaci. Seznam může zahrnovat IPv4 a IPv6 adresy. Pokud existuje aspoň jedna položka, na konci seznamu už existuje implicitní "Odepřít vše". Omezení protokolu IP fungují se všemi možnostmi hostování funkcí.

> [!NOTE]
> V případě použití omezení sítě můžete použít Editor portálu jenom z vaší virtuální sítě, nebo pokud jste IP adresu počítače, který používáte pro přístup k Azure Portal v seznamu bezpečných příjemců, zapnuli. K funkcím na kartě **funkce platformy** ale můžete přistupovat z libovolného počítače.

Další informace najdete v tématu [omezení statického přístupu Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Privátní přístup k webu

Přístup k privátní lokalitě znamená, že vaše aplikace bude přístupná jenom z privátní sítě, jako je třeba virtuální síť Azure.

* Přístup k privátní lokalitě je k dispozici v plánech [Premium](./functions-premium-plan.md), [Spotřeba](functions-scale.md#consumption-plan)a [App Service](functions-scale.md#app-service-plan) , když jsou nakonfigurované koncové body služby.
    * Koncové body služby je možné nakonfigurovat na základě jednotlivých aplikací v části **funkce platformy** > **sítě** > **nakonfigurujte omezení přístupu** > **Přidat pravidlo**. Virtuální sítě se teď dají vybrat jako typ pravidla.
    * Další informace najdete v tématu [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Mějte na paměti, že u koncových bodů služby má vaše funkce stále plný odchozí přístup k Internetu, a to i s nakonfigurovanou integrací virtuální sítě.
* Přístup k privátní lokalitě je také k dispozici v rámci App Service Environment, který je nakonfigurován s interním nástrojem pro vyrovnávání zatížení (interního nástroje). Další informace najdete v tématu [Vytvoření a použití interního nástroje pro vyrovnávání zatížení s App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Integrace virtuální sítě

Integrace virtuální sítě umožňuje aplikacím Function App přistupovat k prostředkům v rámci virtuální sítě. Tato funkce je dostupná jak v plánu Premium, tak v plánu App Service. Pokud je vaše aplikace ve App Service Environment, je již ve virtuální síti a nevyžaduje integraci virtuální sítě pro dosažení prostředků ve stejné virtuální síti.

Integraci virtuální sítě můžete použít k povolení přístupu z aplikací k databázím a webovým službám běžícím ve vaší virtuální síti. S integrací virtuální sítě nemusíte pro aplikace na svém VIRTUÁLNÍm počítači vystavovat veřejný koncový bod. Místo toho můžete použít soukromé IP adresy, které nejsou v Internetu.

Existují dvě formy integrace virtuální sítě:

+ **Integrace místní virtuální sítě (Preview)** : umožňuje integraci s virtuálními sítěmi ve stejné oblasti. Tento typ integrace vyžaduje podsíť ve virtuální síti ve stejné oblasti. Tato funkce je stále ve verzi Preview, ale je podporovaná pro aplikace Function App běžící v systému Windows s upozorněními popsanými po následující tabulce problému nebo řešení.
+ **Integrace virtuální sítě požadovaná bránou**: umožňuje integraci s virtuálními sítěmi ve vzdálených oblastech nebo s klasickými virtuálními sítěmi. Tento typ integrace vyžaduje nasazení brány virtuální sítě do vaší virtuální sítě. Jedná se o funkci založenou na síti VPN typu Point-to-site, která je podporovaná jenom pro aplikace Function App běžící v systému Windows.

Aplikace může v jednom okamžiku používat jenom jeden typ funkce integrace virtuální sítě. I když jsou obě užitečné pro mnoho scénářů, v následující tabulce je uvedeno, kde by se měly použít:

| Problém  | Řešení |
|----------|----------|
| Chcete se spojit s adresou RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) ve stejné oblasti. | Integrace místní virtuální sítě |
| Chcete se připojit k prostředkům v klasické virtuální síti nebo ve virtuální síti v jiné oblasti. | Brána vyžaduje integraci virtuální sítě |
| Chcete se dostat ke koncovým bodům RFC 1918 napříč Azure ExpressRoute | Integrace místní virtuální sítě |
| Chcete oslovit prostředky napříč koncovými body služby | Integrace místní virtuální sítě |

Ani jedna z funkcí neumožňuje přístup k adresám, které nejsou RFC 1918, mezi ExpressRoute. K tomu je v současné době nutné použít App Service Environment.

Použití místní integrace virtuální sítě nepřipojuje vaši virtuální síť k místním koncovým bodům nebo konfiguraci koncových bodů služby. To je samostatná konfigurace sítě. Integrace místní virtuální sítě umožňuje, aby aplikace provedla volání prostřednictvím těchto typů připojení.

Bez ohledu na použitou verzi poskytuje integrace s aplikací Function App přístup k prostředkům ve vaší virtuální síti, ale neuděluje privátnímu webu přístup k vaší aplikaci Function App z virtuální sítě. Přístup k soukromému webu znamená, že vaše aplikace je přístupná jenom z privátní sítě, jako je Azure Virtual Network. Integrace virtuální sítě je jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě.

Funkce integrace virtuální sítě:

* Vyžaduje plán App Service Standard, Premium nebo PremiumV2.
* podporuje protokoly TCP a UDP
* Práce s aplikacemi App Service a aplikacemi funkcí

Existuje několik věcí, které integrace virtuální sítě nepodporuje, včetně:

* Připojení jednotky
* Integrace Active Directory
* Názv

Integrace virtuální sítě v Azure Functions používá sdílenou infrastrukturu s App Service webovými aplikacemi. Další informace o těchto dvou typech integrace virtuální sítě najdete v následujících tématech:

* [Integrace místní virtuální sítě](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Brána vyžaduje integraci virtuální sítě](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Další informace o použití integrace virtuální sítě najdete v tématu [integrace aplikace Function App s virtuální sítí Azure](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Připojování k zabezpečeným prostředkům koncového bodu služby

> [!NOTE]
> V současné době může trvat až 12 hodin, než se nové koncové body služby stanou dostupnými pro vaši aplikaci Function App po konfiguraci omezení přístupu pro prostředek pro příjem dat. Během této doby bude prostředek pro vaši aplikaci zcela nedostupný.

Pokud chcete zajistit vyšší úroveň zabezpečení, můžete omezit počet služeb Azure na virtuální síť pomocí koncových bodů služby. Abyste mohli získat přístup k prostředku, musíte aplikaci Function App integrovat s touto virtuální sítí. Tato konfigurace je podporovaná ve všech plánech, které podporují integraci virtuálních sítí.

[Přečtěte si další informace o koncových bodech služby virtuální sítě.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Omezení účtu úložiště na virtuální síť

Když vytváříte aplikaci Function App, musíte vytvořit nebo propojit s účtem Azure Storage pro obecné účely, který podporuje objekty blob, Queue a Table Storage. V tuto chvíli nemůžete u tohoto účtu použít žádná omezení virtuální sítě. Pokud v účtu úložiště, který používáte pro aplikaci Function App, nakonfigurujete koncový bod služby virtuální sítě, bude aplikace přerušit.

[Přečtěte si další informace o požadavcích na účet úložiště.](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="virtual-network-triggers-non-http"></a>Aktivační události virtuální sítě (jiné než HTTP)

Pokud v současné době chcete použít triggery funkcí jiné než HTTP z virtuální sítě, musíte aplikaci Function App spustit v plánu App Service nebo v App Service Environment.

Předpokládejme například, že chcete nakonfigurovat Azure Cosmos DB pro příjem provozu pouze z virtuální sítě. Je potřeba nasadit aplikaci Function App v plánu služby App Service, který poskytuje integraci virtuální sítě s touto virtuální sítí, aby bylo možné nakonfigurovat Azure Cosmos DB triggery z tohoto prostředku. V průběhu období Preview konfigurace integrace virtuální sítě neumožní, aby plán Premium vyvolal tento Azure Cosmos DB prostředek.

V [tomto seznamu najdete všechny triggery jiného typu než http](./functions-triggers-bindings.md#supported-bindings) , které pokontrolují, co se podporuje.

## <a name="hybrid-connections"></a>Hybridní připojení

[Hybrid Connections](../service-bus-relay/relay-hybrid-connections-protocol.md) je funkce Azure Relay, kterou můžete použít pro přístup k prostředkům aplikací v jiných sítích. Poskytuje přístup z vaší aplikace do koncového bodu aplikace. Nemůžete ho použít pro přístup k aplikaci. Hybrid Connections je k dispozici pro funkce spuštěné ve všech, ale v plánu spotřeby.

Jak se používá v Azure Functions, každé hybridní připojení se koreluje s jedinou kombinací hostitele TCP a portu. To znamená, že koncový bod hybridního připojení může být v jakémkoli operačním systému a libovolné aplikaci, pokud přistupujete k portu naslouchání TCP. Funkce Hybrid Connections neví ani nezáleží na tom, co je aplikační protokol nebo k čemu přistupujete. Poskytuje jenom přístup k síti.

Další informace najdete v dokumentaci k [App Service pro Hybrid Connections](../app-service/app-service-hybrid-connections.md). Tyto stejné konfigurační kroky Azure Functions podporují.

## <a name="outbound-ip-restrictions"></a>Omezení odchozích IP adres

Omezení odchozích IP adres jsou k dispozici pouze pro funkce nasazené do App Service Environment. Můžete nakonfigurovat odchozí omezení pro virtuální síť, ve které je nasazený App Service Environment.

Když integrujete aplikaci funkcí v plánu Premium nebo App Service plánu s virtuální sítí, může aplikace dál provádět odchozí volání na Internet.

## <a name="next-steps"></a>Další kroky

Další informace o sítích a Azure Functions:

* [Postup Začínáme s integrací virtuální sítě najdete v kurzu.](./functions-create-vnet.md)
* [Přečtěte si nejčastější dotazy k funkcím sítě](./functions-networking-faq.md)
* [Další informace o integraci virtuální sítě s funkcí App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Další informace o virtuálních sítích v Azure](../virtual-network/virtual-networks-overview.md)
* [Povolení více síťových funkcí a řízení pomocí App Servicech prostředí](../app-service/environment/intro.md)
* [Připojení k jednotlivým místním prostředkům bez změny brány firewall pomocí Hybrid Connections](../app-service/app-service-hybrid-connections.md)
