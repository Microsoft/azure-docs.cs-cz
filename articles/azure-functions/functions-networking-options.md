---
title: Azure Functions možnosti sítě
description: Přehled všech možností sítě k dispozici ve službě Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: a4ae2d8bad50a4103da6afaa0bee5cbb75c877aa
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545501"
---
# <a name="azure-functions-networking-options"></a>Služba Azure Functions možnostech sítě

Tento dokument popisuje sadu síťových funkcí, které jsou k dispozici v Azure Functions možnosti hostování. Všechny následující možnosti sítě poskytují některé možnost přístupu k prostředkům bez použití internetové adresy směrovatelné nebo omezit přístup k Internetu na aplikaci funkcí. Všechny modelech hostování mají různé úrovně izolace sítě, které jsou k dispozici, a zvolíte tu správnou vám umožní splnit vaše požadavky na izolaci sítě.

Funkce aplikací je možné hostovat v několika různými způsoby.

* Existuje řada možností plánu, na kterých běží v infrastruktuře víceklientské s různými úrovněmi připojení k virtuální síti a možnosti škálování.
    1. Plán Consumption, což se dá dynamicky škálovat v reakci na zatížení a nabízí možnosti izolace sítě minimální.
    1. Do Premium plánu, které se také dynamicky Škáluje a nabízí komplexnější izolace sítě.
    1. Plán služby App Service, který funguje na pevnou škálovací a nabízí podobné izolace sítě k plánu Premium.
* Funkce můžete také spustit na App Service Environment (ASE), která nasadí vaši funkci do vaší virtuální sítě a nabízí plnou síťového ovládacího prvku a izolace.

## <a name="networking-feature-matrix"></a>Přehled funkcí služby sítě

|                |[Plán consumption](functions-scale.md#consumption-plan)|⚠ [Premium Plan](functions-scale.md##premium-plan-public-preview)|[Plán služby App Service](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**Příchozí omezení IP adres**](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[**Integrace virtuální sítě**](#vnet-integration)|❌No|❌No|✅Yes|✅Yes|
|[**Integrace virtuální sítě ve verzi Preview (Express Route a koncové body služby)**](#preview-vnet-integration)|❌No|⚠Ano|⚠Ano|✅Yes|
|[**Hybridní připojení**](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[**Privátní přístup na web**](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ Funkce ve verzi Preview, nikoli pro produkční použití

## <a name="inbound-ip-restrictions"></a>Příchozí omezení IP adres

Omezení IP adres umožňují definovat prioritu seřazený povolit nebo zamítnout seznam IP adres, které můžou přistupovat k vaší aplikace. Seznam povolených tříd může obsahovat adresy IPv4 a IPv6. Pokud je jeden nebo více položek, je pak implicitní Zamítnout vše, která existuje na konci seznamu. Funkce omezení podle IP spolupracuje s možností hostování všechny funkce.

> [!NOTE]
> Aby bylo možné používat editoru webu Azure portal, musí být schopen přímý přístup k běžící aplikaci function app na portálu a zařízení, které používáte pro přístup k portálu musí mít jeho seznam povolených IP adres. Síťové omezení na místě, budete k němu přístup žádné funkce v **funkce platformy** kartu.

[Další informace najdete tady](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>Integrace virtuální sítě

Integrace virtuální sítě umožňuje vaší aplikaci funkcí přístup k prostředkům v síti VNET. Integrace virtuální sítě je k dispozici v plánu Premium a plán služby App Service. Pokud je vaše aplikace ve službě App Service Environment, se už ve virtuální síti a nevyžaduje, použijte funkci integrace virtuální sítě k přístupu k prostředkům ve stejné virtuální síti.

Integrace virtuální sítě poskytuje aplikaci funkcí přístup k prostředkům ve vaší virtuální síti, ale nebude udělit [přístup k webům privátní](#private-site-access) aplikaci function App z virtuální sítě.

Integrace virtuální sítě se často používá k povolení přístupu z aplikace do databáze a webových služeb spuštěných ve virtuální síti. Integrace virtuální sítě není nutné vystavit veřejný koncový bod pro aplikace na virtuální počítač ale může používání privátních adres směrovatelný bez internet místo.

Obecně dostupná verze integrace virtuální sítě závisí na bránu sítě VPN pro připojení aplikace Function App k virtuální síti. Je k dispozici v funkcí hostované v plánu služby app service. Další informace o konfiguraci této funkce, najdete v článku [dokumentu služby App Service pro stejnou funkci](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-vnet-integration"></a>Integrace virtuální sítě ve verzi Preview

Existuje nová verze funkci integrace virtuální sítě, která je ve verzi preview. Nezávisí na point-to-site VPN a také podporuje přístup k prostředkům přes ExpressRoute nebo koncové body služby. Tato funkce je dostupná v plánu Premium a plány služby App Service škálovat, aby PremiumV2.

Nová verze integrace virtuální sítě, která je aktuálně ve verzi preview, poskytuje následující výhody:

* Žádná brána je potřeba využít novou funkci integrace virtuální sítě
* Prostředky můžete přistupovat přes připojení ExpressRoute, bez jakékoli další konfigurace nad rámec integraci se službou ExpressRoute připojené virtuální sítě.
* Můžete využívat koncový bod služby Zabezpečené prostředky z spouštění služby Functions. Uděláte to tak, povolte koncové body služby v podsíti pro integrace virtuální sítě.
* Nelze nakonfigurovat aktivačních událostí použití koncového bodu služby Zabezpečené prostředky díky nové funkci integrace virtuální sítě. 
* Aplikace function app a virtuální síť musí být ve stejné oblasti.
* Nová funkce vyžaduje nevyužité podsítě ve virtuální síti správce prostředků.
* Produkční úlohy nejsou podporovány na novou verzi integrace virtuální sítě, i když je ve verzi preview.
* Směrovací tabulky a globální partnerský vztah ještě nejsou k dispozici díky nově zavedené integraci virtuální sítě.
* Jedna adresa se používá pro každý potenciální instance aplikace funkce. Vzhledem k tomu, že po přiřazení nelze změnit velikost podsítě, použijte podsíť, která lze snadno podporuje maximální měřítko. Například pro podporu, který je možné škálovat na 80 instance plánu Premium, doporučujeme `/25` podsíť, která poskytuje 126 hostitelské adresy.

Další informace o používání integrace virtuální sítě ve verzi preview, najdete v článku [integrace aplikace function app s Azure Virtual Network](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Hybridní připojení

[Hybridní připojení](../service-bus-relay/relay-hybrid-connections-protocol.md) je funkce služby Azure Relay, který lze použít pro přístup k prostředkům aplikace v jiných sítích. Poskytuje přístup z vaší aplikace na koncový bod aplikace. Nelze použít pro přístup k aplikaci. Hybridní připojení je k dispozici funkce používané [plán služby App Service](functions-scale.md#app-service-plan) a [služby App Service Environment](../app-service/environment/intro.md).

Používá se ve funkcích, koreluje každé hybridní připojení na jedné kombinaci hostitele a port TCP. To znamená, že koncový bod hybridní připojení může být pro všechny operační systémy a jakékoli aplikace, je k dispozici mají přístup k portu naslouchání TCP. Funkce Hybrid Connections neznáte nebo care co aplikační protokol, nebo co přistupujete. Jednoduše poskytuje přístup k síti.

Další informace najdete v tématu [dokumentaci služby App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md), který podporuje funkce a webové aplikace.

## <a name="private-site-access"></a>Privátní přístup k webu

Přístup k webům privátní odkazuje na zpřístupnění aplikace jen z privátní sítě, jako z v rámci virtuální sítě Azure. Přístup k privátním serveru je k dispozici pouze s ASE nakonfigurovaný s interní zatížení nástroje pro vyrovnávání (ILB). Podrobnosti o použití prostředí ILB ASE najdete v tématu [vytváření a používání prostředí ILB ASE](../app-service/environment/create-ilb-ase.md).

Existuje mnoho způsobů, jak přistupovat k prostředkům virtuální sítě v další možnosti hostování, ale služba ASE je jediný způsob, jak povolit aktivačních událostí pro funkci probíhá přes virtuální síť.

## <a name="next-steps"></a>Další postup
Další informace o možnostech sítě a funkce: 

* [Postupujte podle virtuální sítě integrace kurz Začínáme](./functions-create-vnet.md)
* [Přečtěte si funkce tady sítě – nejčastější dotazy](./functions-networking-faq.md)
* [Další informace o integraci virtuální sítě pomocí služby App Service / funkce zde](../app-service/web-sites-integrate-with-vnet.md)
* [Další informace o virtuálních sítí v Azure](../virtual-network/virtual-networks-overview.md)
* [Povolit další síťové funkce a ovládací prvek s App Service Environment](../app-service/environment/intro.md)
* [Připojení k jednotlivým místních prostředků bez nutnosti změn brány firewall pomocí hybridních připojení](../app-service/app-service-hybrid-connections.md)
