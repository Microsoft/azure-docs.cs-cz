---
title: Možnosti sítí Azure Functions
description: Přehled všech možností sítě, které jsou k dispozici v Azure Functions.
ms.topic: conceptual
ms.date: 4/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 271730e57a2d7ef8324420744b4bcd088b9809cc
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530080"
---
# <a name="azure-functions-networking-options"></a>Možnosti sítí Azure Functions

Tento článek popisuje síťové funkce, které jsou dostupné napříč možnostmi hostování pro Azure Functions. Všechny následující možnosti sítě poskytují uživatelům přístup k prostředkům bez nutnosti adresování prostřednictvím Internetu nebo omezení přístupu k Internetu do aplikace Function App.

Modely hostování mají k dispozici různé úrovně izolace sítě. Výběr správného řešení vám pomůže splnit požadavky na izolaci sítě.

Aplikace Function App můžete hostovat několika způsoby:

* Můžete si vybrat z možností plánu, které běží na víceklientské infrastruktuře s různými úrovněmi připojení a možností škálování virtuální sítě:
    * [Plán spotřeby](functions-scale.md#consumption-plan) se dynamicky škáluje v reakci na načtení a nabízí minimální možnosti izolace sítě.
    * [Plán Premium](functions-scale.md#premium-plan) se také dynamicky škáluje a nabízí komplexnější izolaci sítě.
    * Plán Azure [App Service](functions-scale.md#app-service-plan) pracuje s pevnou škálou a nabízí izolaci sítě podobnou plánu Premium.
* Funkce můžete spouštět v [App Service Environment](../app-service/environment/intro.md). Tato metoda nasadí vaši funkci do vaší virtuální sítě a nabízí kompletní řízení a izolaci sítě.

## <a name="matrix-of-networking-features"></a>Matice síťových funkcí

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-ip-restrictions"></a>Omezení příchozích IP adres

Omezení IP adres můžete použít k definování seznamu IP adres seřazených podle priority, které mají povolený nebo odepřený přístup k vaší aplikaci. Seznam může zahrnovat IPv4 a IPv6 adresy. Pokud existuje aspoň jedna položka, na konci seznamu už existuje implicitní "Odepřít vše". Omezení protokolu IP fungují se všemi možnostmi hostování funkcí.

> [!NOTE]
> V případě použití omezení sítě můžete použít Editor portálu jenom z vaší virtuální sítě, nebo pokud jste IP adresu počítače, který používáte pro přístup k Azure Portal v seznamu bezpečných příjemců, zapnuli. K funkcím na kartě **funkce platformy** ale můžete přistupovat z libovolného počítače.

Další informace najdete v tématu [omezení statického přístupu Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Privátní přístup k webu

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

## <a name="virtual-network-integration"></a>Integrace virtuální sítě

Integrace virtuální sítě umožňuje aplikacím Function App přistupovat k prostředkům v rámci virtuální sítě.
Azure Functions podporuje dva druhy integrace virtuální sítě:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Integrace virtuální sítě v Azure Functions používá sdílenou infrastrukturu s App Service webovými aplikacemi. Další informace o těchto dvou typech integrace virtuální sítě najdete v následujících tématech:

* [Integrace místní virtuální sítě](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Brána – požadovaná integrace virtuální sítě](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Informace o tom, jak nastavit integraci virtuální sítě, najdete v tématu [integrace aplikace Function App s virtuální sítí Azure](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Integrace místní virtuální sítě

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Připojit k zabezpečeným prostředkům koncového bodu služby

Pokud chcete zajistit vyšší úroveň zabezpečení, můžete omezit počet služeb Azure na virtuální síť pomocí koncových bodů služby. Abyste mohli získat přístup k prostředku, musíte aplikaci Function App integrovat s touto virtuální sítí. Tato konfigurace je podporovaná ve všech plánech, které podporují integraci virtuálních sítí.

Další informace najdete v tématu [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Omezení účtu úložiště na virtuální síť

Když vytváříte aplikaci Function App, musíte vytvořit nebo propojit s účtem Azure Storage pro obecné účely, který podporuje objekty blob, Queue a Table Storage. V tuto chvíli nemůžete u tohoto účtu použít žádná omezení virtuální sítě. Pokud nakonfigurujete koncový bod služby virtuální sítě v účtu úložiště, který používáte pro aplikaci Function App, tato konfigurace aplikaci pozastaví.

Další informace najdete v tématu [požadavky na účet úložiště](./functions-create-function-app-portal.md#storage-account-requirements).

## <a name="use-key-vault-references"></a>Použití odkazů na službu Key Vault

Můžete použít Azure Key Vault odkazy na používání tajných kódů z Azure Key Vault v aplikaci Azure Functions bez nutnosti změny kódu. Azure Key Vault je služba, která poskytuje centralizovanou správu tajných kódů s úplnou kontrolou zásad přístupu a historie auditu.

V současné době [Key Vault odkazy](../app-service/app-service-key-vault-references.md) nebudou fungovat, pokud je Trezor klíčů zabezpečený pomocí koncových bodů služby. Pokud se chcete připojit k trezoru klíčů pomocí integrace virtuální sítě, musíte volat Key Vault v kódu aplikace.

## <a name="virtual-network-triggers-non-http"></a>Aktivační události virtuální sítě (jiné než HTTP)

V současné době můžete v rámci virtuální sítě použít funkce triggeru jiného typu než HTTP jedním ze dvou způsobů:

+ Spusťte aplikaci Function App v plánu Premium a aktivujte podporu triggeru virtuální sítě.
+ Spusťte aplikaci Function App v plánu App Service nebo App Service Environment.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plán Premium s triggery virtuální sítě

Když spustíte plán Premium, můžete připojit funkce triggeru jiného typu než HTTP ke službám, které běží ve virtuální síti. K tomu musíte povolit podporu triggeru virtuální sítě pro vaši aplikaci Function App. Nastavení **monitorování škály běhového prostředí** najdete v [Azure Portal](https://portal.azure.com) v **Configuration**části  >  **nastavení modulu runtime konfigurační funkce**.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

Aktivační události virtuální sítě taky můžete povolit pomocí následujícího příkazu Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Aktivační události virtuální sítě se podporují ve verzi 2. x a novějších modulech runtime Functions. Jsou podporovány následující typy triggerů bez protokolu HTTP.

| Linka | Minimální verze |
|-----------|---------| 
|[Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 nebo vyšší |
|[Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 nebo vyšší|
|[Microsoft. Azure. WebJobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 nebo vyšší|
|[Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 nebo vyšší|
|[Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 nebo vyšší|

> [!IMPORTANT]
> Pokud povolíte podporu triggeru virtuální sítě, budou se svou aplikací dynamicky škálovat jenom typy triggerů, které se zobrazují v předchozí tabulce. Stále můžete použít triggery, které nejsou v tabulce, ale nejsou škálované nad rámec jejich předem zavedených instancí. Úplný seznam aktivačních událostí najdete v tématu [triggery a vazby](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service plánování a App Service Environment s triggery virtuální sítě

Když aplikace Function App běží buď v plánu App Service, nebo v App Service Environment, můžete použít funkce triggeru jiného typu než HTTP. Aby se funkce aktivovaly správně, musíte být připojení k virtuální síti s přístupem k prostředku definovanému v připojení triggeru.

Předpokládejme například, že chcete nakonfigurovat Azure Cosmos DB pro příjem provozu pouze z virtuální sítě. V takovém případě musíte aplikaci Function App nasadit v plánu App Service, který poskytuje integraci virtuální sítě s touto virtuální sítí. Integrace umožňuje, aby se aktivovala funkce tohoto Azure Cosmos DB prostředku.

## <a name="hybrid-connections"></a>Hybridní připojení

[Hybrid Connections](../azure-relay/relay-hybrid-connections-protocol.md) je funkce Azure Relay, kterou můžete použít pro přístup k prostředkům aplikací v jiných sítích. Poskytuje přístup z vaší aplikace do koncového bodu aplikace. Nemůžete ho použít pro přístup k aplikaci. Hybrid Connections je k dispozici pro funkce, které běží ve Windows ve všech, ale v plánu spotřeby.

Jak se používá v Azure Functions, každé hybridní připojení se koreluje s jedinou kombinací hostitele TCP a portu. To znamená, že koncový bod hybridního připojení může být v jakémkoli operačním systému a libovolné aplikaci, pokud přistupujete k portu naslouchání TCP. Funkce Hybrid Connections neví ani nezáleží na tom, co je aplikační protokol nebo k čemu přistupujete. Poskytuje jenom přístup k síti.

Další informace najdete v dokumentaci k [App Service pro Hybrid Connections](../app-service/app-service-hybrid-connections.md). Tyto stejné konfigurační kroky Azure Functions podporují.

>[!IMPORTANT]
> Hybrid Connections se podporuje jenom v plánech Windows. Linux se nepodporuje.

## <a name="outbound-ip-restrictions"></a>Omezení odchozích IP adres

Omezení odchozích IP adres jsou k dispozici v plánu Premium, App Service plánu nebo App Service Environment. Můžete nakonfigurovat odchozí omezení pro virtuální síť, ve které je nasazený App Service Environment.

Když integrujete aplikaci funkcí v plánu Premium nebo App Service plánu s virtuální sítí, může aplikace ve výchozím nastavení dál provádět odchozí volání do Internetu. Přidáním nastavení aplikace `WEBSITE_VNET_ROUTE_ALL=1` vynutíte odeslání veškerého odchozího provozu do vaší virtuální sítě, kde je možné použít pravidla skupiny zabezpečení sítě k omezení provozu.

## <a name="automation"></a>Automation
Následující rozhraní API vám umožní programově spravovat integrace místní virtuální sítě:

+ **Azure CLI**: použijte [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) příkazy k přidání, výpisu nebo odebrání integrace místní virtuální sítě.  
+ **Šablony ARM**: integraci regionální virtuální sítě lze povolit pomocí šablony Azure Resource Manager. Úplný příklad najdete v tématu [Šablona pro rychlý Start pro funkce](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Řešení potíží

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Další kroky

Další informace o sítích a Azure Functions:

* [Postup Začínáme s integrací virtuální sítě najdete v kurzu.](./functions-create-vnet.md)
* [Přečtěte si nejčastější dotazy k funkcím sítě](./functions-networking-faq.md)
* [Další informace o integraci virtuální sítě s funkcí App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Další informace o virtuálních sítích v Azure](../virtual-network/virtual-networks-overview.md)
* [Povolení více síťových funkcí a řízení pomocí App Servicech prostředí](../app-service/environment/intro.md)
* [Připojení k jednotlivým místním prostředkům bez změny brány firewall pomocí Hybrid Connections](../app-service/app-service-hybrid-connections.md)
