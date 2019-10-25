---
title: Možnosti Azure Functions sítě
description: Přehled všech možností sítě, které jsou k dispozici v Azure Functions
author: alexkarcher-msft
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: bf5ce8da2ce62a5da821588c8f635bbab04dd3c1
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881575"
---
# <a name="azure-functions-networking-options"></a>Možnosti Azure Functions sítě

Tento článek popisuje síťové funkce, které jsou dostupné napříč možnostmi hostování pro Azure Functions. Všechny následující možnosti sítě poskytují určitou možnost přístupu k prostředkům bez použití adresních IP adres nebo omezení přístupu k Internetu do aplikace Function App.

Modely hostování mají k dispozici různé úrovně izolace sítě. Výběr správné verze vám pomůže splnit požadavky na izolaci sítě.

Aplikace Function App můžete hostovat několika způsoby:

* Existuje sada možností plánu, které běží na víceklientské infrastruktuře s různými úrovněmi připojení a možností škálování virtuální sítě:
    * [Plán spotřeby](functions-scale.md#consumption-plan), který se dynamicky škáluje v reakci na načtení a nabízí minimální možnosti izolace sítě.
    * [Plán Premium](functions-scale.md#premium-plan), který se také dynamicky škáluje a zároveň nabízí komplexnější izolaci sítě.
    * Plán Azure [App Service](functions-scale.md#app-service-plan), který pracuje s pevnou škálou a nabízí podobnou izolaci sítě pro plán Premium.
* Funkce můžete spouštět v [App Service Environment](../app-service/environment/intro.md). Tato metoda nasadí vaši funkci do vaší virtuální sítě a nabízí kompletní řízení a izolaci sítě.

## <a name="matrix-of-networking-features"></a>Matice síťových funkcí

|                |[Plán spotřeby](functions-scale.md#consumption-plan)|[Plán Premium (Preview)](functions-scale.md#premium-plan)|[Plán služby App Service](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Omezení příchozí IP adresy & přístupu k privátnímu webu](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integrace virtuální sítě](#virtual-network-integration)|❌No|✅Yes (místní)|✅Yes (místní a brána)|✅Yes|
|[Aktivační události virtuální sítě (jiné než HTTP)](#virtual-network-triggers-non-http)|❌No| ❌No|✅Yes|✅Yes|
|[Hybridní připojení](#hybrid-connections)|❌No|✅Yes|✅Yes|✅Yes|
|[Omezení odchozích IP adres](#outbound-ip-restrictions)|❌No| ❌No|❌No|✅Yes|

## <a name="inbound-ip-restrictions"></a>Omezení příchozích IP adres

Omezení IP adres můžete použít k definování seznamu IP adres seřazených podle priority, které mají povolený nebo odepřený přístup k vaší aplikaci. Seznam může zahrnovat IPv4 a IPv6 adresy. Pokud existuje jedna nebo více položek, na konci seznamu existuje implicitní "Odepřít vše". Omezení protokolu IP fungují se všemi možnostmi hostování funkcí.

> [!NOTE]
> Se síťovými omezeními můžete použít jenom Editor portálu z vaší virtuální sítě, nebo pokud jste v seznamu přistavili IP adresu počítače, který používáte pro přístup k Azure Portal. K funkcím na kartě **funkce platformy** ale můžete přistupovat z libovolného počítače.

Další informace najdete v tématu [omezení statického přístupu Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Privátní přístup k webu

Přístup k soukromému webu znamená, že vaše aplikace bude přístupná jenom z privátní sítě, jako je například z Azure Virtual Network.

* Přístup k privátní lokalitě je k dispozici v [plánu](functions-scale.md#app-service-plan) [Premium](./functions-premium-plan.md), [Spotřeba](functions-scale.md#consumption-plan) a App Service, když jsou nakonfigurované **koncové body služby** .
    * Koncové body služby je možné nakonfigurovat na základě jednotlivých aplikací v části funkce platformy > sítě > nakonfigurujte omezení přístupu > Přidat pravidlo. Virtuální sítě se teď dají vybrat jako typ pravidla.
    * Další informace najdete v tématu [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) .
        * Mějte na paměti, že u koncových bodů služby má vaše funkce stále plný odchozí přístup k Internetu, a to i s nakonfigurovanou integrací virtuální sítě.
* Přístup k privátní lokalitě je také k dispozici u App Service Environment nakonfigurovaných pomocí interního nástroje pro vyrovnávání zatížení (interního nástroje). Další informace najdete v tématu [Vytvoření a použití interního nástroje pro vyrovnávání zatížení s App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Integrace virtuální sítě

Integrace virtuální sítě umožňuje aplikacím Function App přistupovat k prostředkům v rámci virtuální sítě. Tato funkce je dostupná jak v plánu Premium, tak v plánu App Service. Pokud je vaše aplikace ve App Service Environment, je již ve virtuální síti a nevyžaduje použití integrace virtuální sítě k dosažení prostředků ve stejné virtuální síti.

Integraci virtuální sítě můžete použít k povolení přístupu z aplikací k databázím a webovým službám běžícím ve vaší virtuální síti. S integrací virtuální sítě nemusíte pro aplikace na svém VIRTUÁLNÍm počítači vystavovat veřejný koncový bod. Místo toho můžete použít adresy směrování privátních IP adres, které nejsou v Internetu.

Existují dvě formy integrace virtuální sítě:

+ **Integrace místní virtuální sítě (Preview)** : umožňuje integraci s virtuálními sítěmi ve stejné oblasti. Tento typ integrace vyžaduje podsíť ve virtuální síti ve stejné oblasti. Tato funkce je stále ve verzi Preview, ale podporuje se pro aplikace Function App běžící v systému Windows s upozorněními uvedenými níže.
+ **Integrace virtuální sítě požadovaná bránou**: umožňuje integraci s virtuálními sítěmi ve vzdálených oblastech nebo s klasickými virtuálními sítěmi. Tento typ integrace vyžaduje nasazení Virtual Network brány do vaší virtuální sítě. Jedná se o funkci založenou na síti VPN typu Point-to-site, která je podporovaná jenom pro aplikace Function App běžící v systému Windows.

Aplikace může v jednom okamžiku používat jenom jeden typ funkce integrace virtuální sítě. I když jsou oba užitečné pro mnoho scénářů, v následující tabulce je uvedeno, kde by se měly používat tyto možnosti:

| Problém  | Řešení |
|----------|----------|
| Chcete se spojit s adresou RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) ve stejné oblasti. | Místní integrace virtuální sítě |
| Chcete se připojit k prostředkům v klasické virtuální síti nebo virtuální síti v jiné oblasti. | požadovaná brána Integration VNet |
| Chcete se dostat ke koncovým bodům RFC 1918 napříč ExpressRoute | Místní integrace virtuální sítě |
| Chcete oslovit prostředky napříč koncovými body služby | Místní integrace virtuální sítě |

Ani jedna z funkcí vám neumožní přístup k adresám, které nejsou RFC 1918, mezi ExpressRoute. K tomu je potřeba pro teď použít pomocného mechanismu.

Použití místní integrace virtuální sítě nepřipojuje vaši virtuální síť k místnímu nasazení nebo konfiguraci koncových bodů služby. To je samostatná konfigurace sítě. Regionální integrace virtuální sítě jednoduše umožňuje, aby vaše aplikace provedla volání prostřednictvím těchto typů připojení.

Bez ohledu na použitou verzi udělí integrace virtuální sítě Function App přístup k prostředkům ve vaší virtuální síti, ale neuděluje privátnímu webu přístup k vaší aplikaci Function App z virtuální sítě. Přístup k soukromému webu znamená, že aplikace je přístupná jenom z privátní sítě, jako je například z Azure Virtual Network. Integrace virtuální sítě je jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě. 

Funkce integrace virtuální sítě:

* Vyžaduje plán App Service Standard, Premium nebo PremiumV2.
* podporuje protokoly TCP a UDP
* funguje s aplikacemi App Service a aplikacemi Function App

Integrace virtuální sítě nepodporuje zahrnutí následujících věcí:

* Připojení jednotky
* Integrace služby AD
* NetBIOS

Integrace virtuální sítě v rámci funkcí používá sdílenou infrastrukturu s App Service webovými aplikacemi. Další informace o těchto dvou typech integrace virtuální sítě najdete v těchto tématech:

* [Regionální Integrace virtuální sítě](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Požadovaná brána Integration VNet](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Další informace o použití integrace virtuální sítě najdete v tématu [integrace aplikace Function App s virtuální sítí Azure](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Připojování k zabezpečeným prostředkům koncového bodu služby

> [!NOTE]
> Jakmile nakonfigurujete omezení přístupu k prostředkům pro příjem dat, může trvat až 12 hodin, než se nové koncové body služby stanou dostupnými pro vaši aplikaci Function App. Během této doby bude prostředek pro vaši aplikaci zcela nedostupný.

Aby bylo možné zajistit vyšší úroveň zabezpečení, můžete omezit počet služeb Azure na virtuální síť pomocí koncových bodů služby. Abyste mohli získat přístup k prostředku, musíte aplikaci Function App integrovat s touto virtuální sítí. Tato konfigurace je podporovaná ve všech plánech, které podporují integraci virtuálních sítí.

[Přečtěte si další informace o koncových bodech služby virtuální sítě.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Omezení účtu úložiště na virtuální síť

Při vytváření aplikace Function App musíte vytvořit nebo propojit s Azure Storage účet pro obecné účely, který podporuje úložiště objektů blob, front a tabulek. V tuto chvíli není možné pro tento účet použít žádná omezení virtuální sítě. Pokud nakonfigurujete koncový bod služby virtuální sítě na účtu úložiště, který používáte pro aplikaci Function App, aplikace přeruší.

[Přečtěte si další informace o požadavcích na účet úložiště.](./functions-create-function-app-portal.md#storage-account-requirements
)

## <a name="virtual-network-triggers-non-http"></a>Aktivační události virtuální sítě (jiné než HTTP)

V současné době, aby bylo možné používat triggery funkcí jiné než HTTP z virtuální sítě, je nutné spustit aplikaci Function App v plánu App Service nebo v App Service Environment.

Pokud chcete například nakonfigurovat Azure Cosmos DB, aby přijímala jenom provoz z virtuální sítě, museli byste v plánu služby App Service nasadit aplikaci Function App s integrací virtuální sítě s touto virtuální sítí a nakonfigurovat Azure Cosmos DB triggery. z tohoto prostředku. Ve verzi Preview neumožní konfigurace integrace virtuální sítě, aby se plán Premium vypnul z tohoto Azure Cosmos DB prostředku.

[V tomto seznamu vyhledejte všechny triggery jiného typu než http](./functions-triggers-bindings.md#supported-bindings) , abyste mohli dvakrát kontrolovat, co je podporováno.

## <a name="hybrid-connections"></a>Hybridní připojení

[Hybrid Connections](../service-bus-relay/relay-hybrid-connections-protocol.md) je funkce Azure Relay, kterou můžete použít pro přístup k prostředkům aplikací v jiných sítích. Poskytuje přístup z vaší aplikace do koncového bodu aplikace. Nemůžete ho použít pro přístup k aplikaci. Hybrid Connections je k dispozici pro funkce spuštěné ve všech, ale v plánu spotřeby.

Jak se používá v Azure Functions, každé hybridní připojení se koreluje s jedinou kombinací hostitele TCP a portu. To znamená, že koncový bod hybridního připojení může být v jakémkoli operačním systému a libovolné aplikaci, pokud přistupujete k portu naslouchání TCP. Funkce Hybrid Connections neznáte ani nezáleží na tom, jaký je protokol aplikace nebo k čemu přistupujete. Jednoduše poskytuje přístup k síti.

Další informace najdete v [dokumentaci App Service pro Hybrid Connections](../app-service/app-service-hybrid-connections.md), která podporuje funkce prostřednictvím stejných kroků konfigurace.

## <a name="outbound-ip-restrictions"></a>Omezení odchozích IP adres

Omezení odchozích IP adres jsou k dispozici pouze pro funkce nasazené do App Service Environment. Můžete nakonfigurovat odchozí omezení pro virtuální síť, ve které je nasazený App Service Environment.

Při integraci aplikace Function App do plánu Premium nebo plánu App Service s virtuální sítí stále může aplikace provádět odchozí volání na Internet.

## <a name="next-steps"></a>Další kroky

Další informace o sítích a Azure Functions: 

* [Postup Začínáme s integrací virtuální sítě najdete v kurzu.](./functions-create-vnet.md)
* [Přečtěte si nejčastější dotazy k funkcím sítě](./functions-networking-faq.md)
* [Další informace o integraci virtuální sítě s funkcí App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Další informace o virtuálních sítích v Azure](../virtual-network/virtual-networks-overview.md)
* [Povolení více síťových funkcí a řízení pomocí App Servicech prostředí](../app-service/environment/intro.md)
* [Připojení k jednotlivým místním prostředkům bez změny brány firewall pomocí Hybrid Connections](../app-service/app-service-hybrid-connections.md)
