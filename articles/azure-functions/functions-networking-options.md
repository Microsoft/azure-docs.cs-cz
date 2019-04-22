---
title: Služba Azure Functions možnostech sítě
description: Přehled všech možností sítě k dispozici ve službě Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: b7af0149a690e3cc3a357a5cb769751e3674d374
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698197"
---
# <a name="azure-functions-networking-options"></a>Služba Azure Functions možnostech sítě

Tento článek popisuje síťové funkce, které jsou k dispozici napříč možnosti hostování pro službu Azure Functions. Všechny následující možnosti sítě poskytují některé možnost přístupu k prostředkům bez použití internetové adresy směrovatelné nebo omezit přístup k Internetu na aplikaci funkcí. 

Hostování modely mají různé úrovně izolace sítě, které jsou k dispozici. Výběr správné vám pomůže splnit vaše požadavky na izolaci sítě.

Můžete hostovat aplikace function App v několika způsoby:

* Existuje sada možností plánu, které běží na infrastrukturu víceklientské s různými úrovněmi připojení k virtuální síti a možnosti škálování:
    * [Plánu Consumption](functions-scale.md#consumption-plan), která se dynamicky škáluje v reakci na zatížení a nabízí možnosti izolace sítě minimální.
    * [Plán Premium](functions-scale.md#premium-plan-public-preview), což také dynamicky, škálování a nabízí komplexnější izolace sítě.
    * Azure [plán služby App Service](functions-scale.md#app-service-plan), který funguje na pevnou škálovací a nabízí podobné izolace sítě k plánu Premium.
* Funkce můžete spustit v [služby App Service Environment](../app-service/environment/intro.md). Tato metoda nasadí funkce do vaší virtuální sítě a nabízí plnou síťového ovládacího prvku a izolaci.

## <a name="matrix-of-networking-features"></a>Matice síťových funkcí

|                |[Plán consumption](functions-scale.md#consumption-plan)|⚠ [Premium plan](functions-scale.md#premium-plan-public-preview)|[Plán služby App Service](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Příchozí omezení IP adres](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integrace virtuální sítě](#virtual-network-integration)|❌No|❌No|✅Yes|✅Yes|
|[Přehled integrace služby virtual network (koncové body služby a Azure ExpressRoute)](#preview-version-of-virtual-network-integration)|❌No|⚠Ano|⚠Ano|✅Yes|
|[Hybridní připojení](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[přístup privátního lokality](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ Tato funkce ve verzi preview není pro použití v produkčním prostředí.

## <a name="inbound-ip-restrictions"></a>Příchozí omezení IP adres

Omezení IP adres můžete použít k definování priority seřazené seznam IP adres, které jsou povolený/zamítnutý přístup k vaší aplikaci. Tento seznam může obsahovat adresy IPv4 a IPv6. Když je jeden nebo více položek, implicitní "Zamítnout vše" na konci seznamu existuje. Omezení IP adres fungovat se všemi možnostmi funkce hostování.

> [!NOTE]
> Použití editoru webu Azure portal, musí být přístup přímo k běžící aplikaci function app na portálu. Zařízení, které používáte pro přístup k portálu také musí mít jeho seznam povolených IP adres. Síťové omezení na místě, můžete stále přístup k žádné funkce v **funkce platformy** kartu.

Další informace najdete v tématu [omezení statických přístupu službě Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="virtual-network-integration"></a>Integrace virtuální sítě

Integrace služby Virtual network umožňuje vaší aplikaci funkcí přístup k prostředkům ve virtuální síti. Tato funkce je dostupná v plánu Premium a plán služby App Service. Pokud je vaše aplikace ve službě App Service Environment, se už ve virtuální síti a nevyžaduje použití integrace služby virtual network k přístupu k prostředkům ve stejné virtuální síti.

Integrace služby Virtual network poskytuje aplikaci funkcí přístup k prostředkům ve vaší virtuální síti, ale nebude udělit [přístup k webům privátní](#private-site-access) aplikaci function App z virtuální sítě.

Integrace služby virtual network můžete povolit přístup z aplikací a databází, webových služeb běžících ve vaší virtuální síti. Integrace virtuální sítě není nutné vystavit veřejný koncový bod pro aplikace na svém virtuálním počítači. Místo toho můžete adresy směrovatelné privátní, bez Internetu.

Obecně dostupnou verzi integrace služby virtual network spoléhá na bránu sítě VPN pro připojení aplikace function App k virtuální síti. Je k dispozici v funkcí hostované v plánu služby App Service. Další informace o konfiguraci této funkce, najdete v článku [integrujte svou aplikaci s Azure virtual network](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-version-of-virtual-network-integration"></a>Ve verzi Preview integrace služby virtual network

Nová verze funkci integrace virtuální sítě je ve verzi preview. Nejsou závislé na point-to-site VPN. Podporuje přístup k prostředkům přes ExpressRoute nebo koncové body služby. Je k dispozici v plánu Premium a v plánech služby App Service škálovat, aby PremiumV2.

Tady jsou některé vlastnosti této verze:

* Není nutné bránu používat.
* Můžete přistupovat k prostředkům napříč připojeními ExpressRoute bez jakékoli další konfigurace kromě integrace s virtuální sítí připojené k ExpressRoute.
* Můžete využívat zabezpečený koncový bod služby prostředků z spouštění služby functions. Uděláte to tak, povolte koncové body služby v podsíti pro integrace služby virtual network.
* Nelze nakonfigurovat aktivačních událostí používat zabezpečený koncový bod služby prostředky. 
* Aplikace function app a virtuální sítě musí být ve stejné oblasti.
* Nová funkce vyžaduje nevyužité podsítě ve virtuální síti, kterou jste nasadili prostřednictvím Azure Resource Manageru.
* Produkční úlohy nejsou podporovány, kdy je funkce ve verzi preview.
* Směrovací tabulky a globální partnerský vztah ještě nejsou k dispozici s funkcí.
* Jedna adresa se používá pro každý potenciální výskyt aplikaci function app. Vzhledem k tomu, že po přiřazení nelze změnit velikost podsítě, použijte podsíť, která lze snadno podporuje maximální měřítko. Například pro podporu, který je možné škálovat na 80 instance plánu Premium, doporučujeme `/25` podsíť, která poskytuje 126 hostitelské adresy.

Další informace o používání integrace služby virtual network ve verzi preview, najdete v článku [integrace aplikace function app s Azure virtual network](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Hybridní připojení

[Hybridní připojení](../service-bus-relay/relay-hybrid-connections-protocol.md) je funkce služby Azure Relay, který používáte pro přístup k prostředkům aplikace v jiných sítích. Poskytuje přístup z vaší aplikace na koncový bod aplikace. Nelze ho použít pro přístup k aplikaci. Hybridní připojení je k dispozici funkce používané [plán služby App Service](functions-scale.md#app-service-plan) a [služby App Service Environment](../app-service/environment/intro.md).

Používá se ve službě Azure Functions, koreluje každé hybridní připojení na jedné kombinaci hostitele a port TCP. To znamená, že koncový bod hybridní připojení může být libovolný operační systém a všechny aplikace, za předpokladu, že přistupujete k portu naslouchání TCP. Funkce Hybrid Connections neznáte nebo care co aplikační protokol, nebo co chcete získat přístup. Jednoduše poskytuje přístup k síti.

Další informace najdete v tématu [dokumentaci služby App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md), který podporuje funkce v plánu služby App Service.

## <a name="private-site-access"></a>Privátní přístup k webu

Přístup k webům privátní odkazuje na zpřístupnění aplikace jen z privátní sítě, jako z v rámci virtuální sítě Azure. Přístup k privátním serveru je k dispozici pouze s nakonfigurovanou interní nástroj pro vyrovnávání zatížení (ILB) služby App Service Environment. Další informace najdete v tématu [vytvoření a použití interního nástroje load balancer pomocí služby App Service Environment](../app-service/environment/create-ilb-ase.md).

Existuje mnoho způsobů, jak přistupovat k prostředkům virtuální sítě v další možnosti hostování. Ale službu App Service Environment je jediný způsob, jak povolit aktivačních událostí pro funkci probíhá přes virtuální síť.

## <a name="next-steps"></a>Další postup
Další informace o síťových a Azure Functions: 

* [Postupujte podle kurzu o zahájení práce s integrace služby virtual network](./functions-create-vnet.md)
* [Přečtěte si funkce sítě – nejčastější dotazy](./functions-networking-faq.md)
* [Další informace o integraci virtuální sítě s App Service/funkce](../app-service/web-sites-integrate-with-vnet.md)
* [Další informace o virtuálních sítí v Azure](../virtual-network/virtual-networks-overview.md)
* [Povolit další síťové funkce a ovládací prvek s App Service Environment](../app-service/environment/intro.md)
* [Připojení k jednotlivým místním prostředkům bez nutnosti změn brány firewall pomocí hybridních připojení](../app-service/app-service-hybrid-connections.md)
