---
title: Možnosti sítí Azure Functions
description: Přehled všech možností sítě dostupných v Azure Functions.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 6637627d48df8f9b6126debc215aac9bceb76f6b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419554"
---
# <a name="azure-functions-networking-options"></a>Možnosti sítí Azure Functions

Tento článek popisuje síťové funkce, které jsou k dispozici v rámci možností hostování pro funkce Azure. Všechny následující možnosti sítě poskytují určitou možnost přístupu k prostředkům bez použití internetových směrovatelných adres nebo k omezení přístupu k internetu na aplikaci funkcí.

Hostitelské modely mají k dispozici různé úrovně izolace sítě. Výběr správného vám pomůže splnit požadavky na izolaci sítě.

Aplikace funkcí můžete hostovat několika způsoby:

* Můžete si vybrat z možností plánu, které běží na víceklientské infrastruktuře, s různými úrovněmi připojení virtuální sítě a možností škálování:
    * Plán [spotřeby](functions-scale.md#consumption-plan) se dynamicky škáluje v reakci na zatížení a nabízí minimální možnosti izolace sítě.
    * [Plán Premium](functions-scale.md#premium-plan) se také dynamicky škáluje a nabízí komplexnější izolaci sítě.
    * Plán [služby](functions-scale.md#app-service-plan) Azure App Service funguje v pevném měřítku a nabízí izolaci sítě podobnou plánu Premium.
* Funkce můžete spouštět v [prostředí služby App Service](../app-service/environment/intro.md). Tato metoda nasazuje vaši funkci do vaší virtuální sítě a nabízí úplné řízení a izolaci sítě.

## <a name="matrix-of-networking-features"></a>Matice síťových funkcí

|                |[Plán Consumption](functions-scale.md#consumption-plan)|[Plán Premium](functions-scale.md#premium-plan)|[Plán služby App Service](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Omezení příchozí ip adresy a přístup k privátnímu webu](#inbound-ip-restrictions)|✅Ano|✅Ano|✅Ano|✅Ano|
|[Integrace virtuální sítě](#virtual-network-integration)|❌Ne|✅Ano (regionální)|✅Ano (regionální a brána)|✅Ano|
|[Aktivační události virtuální sítě (jiné než HTTP)](#virtual-network-triggers-non-http)|❌Ne| ✅Ano |✅Ano|✅Ano|
|[Hybridní připojení](#hybrid-connections) (pouze systém Windows)|❌Ne|✅Ano|✅Ano|✅Ano|
|[Omezení odchozí ip adresy](#outbound-ip-restrictions)|❌Ne| ✅Ano|✅Ano|✅Ano|

## <a name="inbound-ip-restrictions"></a>Omezení příchozí ip adresy

Omezení IP adres můžete použít k definování seznamu adres IP, které jsou povoleny nebo odepřen přístup k vaší aplikaci podle priority. Seznam může obsahovat adresy IPv4 a IPv6. Pokud existuje jedna nebo více položek, implicitní "odepřít vše" existuje na konci seznamu. Omezení IP práce se všemi možnostmi hostování funkcí.

> [!NOTE]
> S omezenísítě na místě, můžete použít editor portálu jenom z vaší virtuální sítě, nebo když jste vložili IP adresu počítače, který používáte pro přístup k portálu Azure v seznamu bezpečné příjemce. Stále však můžete přistupovat ke všem funkcím na kartě **Funkce platformy** z libovolného počítače.

Další informace najdete v [tématu omezení statického přístupu služby Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Privátní přístup k webu

Přístup k privátnímu webu znamená zpřístupnění aplikace jenom z privátní sítě, jako je například virtuální síť Azure.

* Přístup k privátnímu webu je k dispozici v plánech [Premium](./functions-premium-plan.md), [Spotřeba](functions-scale.md#consumption-plan)a [Služba aplikace](functions-scale.md#app-service-plan) při konfiguraci koncových bodů služby.
    * Koncové body služby lze konfigurovat pro aplikaci v části **Funkce platformy** > **Síťové** > **konfigurace omezení přístupu** > **Přidat pravidlo**. Virtuální sítě lze nyní vybrat jako typ pravidla.
    * Další informace naleznete v tématu [Koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Mějte na paměti, že s koncovými body služby má vaše funkce stále plný odchozí přístup k Internetu, a to i s nakonfigurovanou integrací virtuální sítě.
* Přístup k privátnímu webu je k dispozici také v prostředí služby App Service, které je nakonfigurované pomocí interního systému vyrovnávání zatížení (ILB). Další informace naleznete v [tématu Vytvoření a použití interního vynakladače zatížení s prostředím služby App Service](../app-service/environment/create-ilb-ase.md).

Informace o tom, jak nastavit přístup k privátnímu webu, najdete [v tématu Establish Azure Functions private site access](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Integrace virtuální sítě

Integrace virtuální sítě umožňuje vaší aplikaci funkcí přístup k prostředkům uvnitř virtuální sítě.
Funkce Azure podporují dva druhy integrace virtuální sítě:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Integrace virtuálních sítí ve funkcích Azure využívá sdílenou infrastrukturu s webovými aplikacemi Služby App Service. Další informace o dvou typech integrace virtuální sítě najdete v následujících tématech:

* [Integrace regionální virtuální sítě](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integrace virtuální sítě požadovaná bránou](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Informace o tom, jak nastavit integraci virtuálních sítí, najdete v [tématu Integrace aplikace pro funkce s virtuální sítí Azure](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Integrace regionální virtuální sítě

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Připojení k prostředkům zabezpečenému koncovým bodem služby

Chcete-li zajistit vyšší úroveň zabezpečení, můžete omezit počet služeb Azure na virtuální síť pomocí koncových bodů služby. Potom je nutné integrovat aplikace funkce s tímto virtuálním sítí pro přístup k prostředku. Tato konfigurace je podporována ve všech plánech, které podporují integraci virtuální sítě.

Další informace najdete v tématu [Koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Omezení účtu úložiště na virtuální síť

Když vytvoříte aplikaci funkcí, musíte vytvořit nebo propojit účet úložiště Azure pro obecné účely, který podporuje úložiště objektů Blob, Fronta a Tabulka. V tomto účtu nelze aktuálně použít žádná omezení virtuální sítě. Pokud nakonfigurujete koncový bod služby virtuální sítě v účtu úložiště, který používáte pro aplikaci funkcí, tato konfigurace vaši aplikaci přeruší.

Další informace najdete v [tématu Požadavky na účet úložiště](./functions-create-function-app-portal.md#storage-account-requirements).

## <a name="use-key-vault-references"></a>Použití odkazů trezoru klíčů

Odkazy na Azure Key Vault můžete použít k použití tajných kódů z Azure Key Vault v aplikaci Azure Functions bez nutnosti jakýchkoli změn kódu. Azure Key Vault je služba, která poskytuje centralizovanou správu tajných klíčů s plnou kontrolou nad zásadami přístupu a historií auditu.

V současné době [odkazy trezoru klíčů](../app-service/app-service-key-vault-references.md) nebude fungovat, pokud váš trezor klíčů je zabezpečený koncovými body služby. Chcete-li se připojit k trezoru klíčů pomocí integrace virtuální sítě, musíte volat Trezor klíčů v kódu aplikace.

## <a name="virtual-network-triggers-non-http"></a>Aktivační události virtuální sítě (jiné než HTTP)

V současné době můžete použít funkce aktivační události bez protokolu HTTP z virtuální sítě jedním ze dvou způsobů:

+ Spusťte aplikaci funkcí v tarifu Premium a povolte podporu spuštění virtuální sítě.
+ Spusťte aplikaci funkce v plánu služby App Service nebo prostředí služby App Service.

### <a name="premium-plan-with-virtual-network-triggers"></a>Prémiový plán s aktivačními událostmi virtuální sítě

Při spuštění plánu Premium můžete připojit funkce aktivační události než HTTP ke službám, které běží uvnitř virtuální sítě. Chcete-li to provést, musíte povolit podporu aktivační události virtuální sítě pro vaši aplikaci funkcí. Nastavení **podpory aktivační události virtuální sítě** se nachází na webu Azure [Portal](https://portal.azure.com) v části Nastavení **aplikace Function**.

![Přepnout virtuální síť](media/functions-networking-options/virtual-network-trigger-toggle.png)

Aktivační události virtuální sítě můžete povolit také pomocí následujícího příkazu Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Aktivační události virtuální sítě jsou podporovány ve verzi 2.x a vyšší modulu Runtime Funkce. Podporovány jsou následující typy aktivačních událostí bez protokolu HTTP.

| Linka | Minimální verze |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 nebo vyšší |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 nebo vyšší|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 nebo vyšší|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 nebo vyšší|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 nebo vyšší|

> [!IMPORTANT]
> Když povolíte podporu aktivační události virtuální sítě, dynamicky se s vaší aplikací změní pouze typy aktivačních událostí zobrazené v předchozí tabulce. Stále můžete používat aktivační události, které nejsou v tabulce, ale nejsou škálované nad jejich předem zahřátý počet instancí. Úplný seznam aktivačních událostí naleznete v tématu [Aktivační události a vazby](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Plán služby App Service a prostředí služby App Service environment s aktivačními událostmi virtuální sítě

Když vaše aplikace funkce běží v plánu služby App Service nebo prostředí služby App Service, můžete použít funkce aktivační události bez HTTP. Aby se vaše funkce aktivovaly správně, musíte být připojeni k virtuální síti s přístupem k prostředku definovanému v aktivačním připojení.

Předpokládejme například, že chcete nakonfigurovat Azure Cosmos DB přijímat provoz pouze z virtuální sítě. V takovém případě je nutné nasadit aplikaci funkce v plánu služby App Service, který poskytuje integraci virtuální sítě s touto virtuální sítí. Integrace umožňuje funkci aktivovat tímto prostředkem Azure Cosmos DB.

## <a name="hybrid-connections"></a>Hybridní připojení

[Hybridní připojení](../service-bus-relay/relay-hybrid-connections-protocol.md) je funkce Azure Relay, kterou můžete použít pro přístup k prostředkům aplikací v jiných sítích. Poskytuje přístup z vaší aplikace ke koncovému bodu aplikace. Nelze jej použít pro přístup k aplikaci. Hybridní připojení je k dispozici pro funkce, které běží v systému Windows ve všech, ale plán spotřeby.

Jak se používá ve funkcích Azure, každé hybridní připojení koreluje s jedním hostitelem TCP a kombinací portů. To znamená, že koncový bod hybridního připojení může být v libovolném operačním systému a libovolné aplikaci, pokud přistupujete k naslouchacímu portu TCP. Funkce Hybridní připojení neví ani se nestará o to, co je aplikační protokol nebo k čemu přistupujete. Poskytuje pouze přístup k síti.

Další informace najdete v [dokumentaci ke službě App Service pro hybridní připojení](../app-service/app-service-hybrid-connections.md). Tyto stejné kroky konfigurace podporují funkce Azure.

>[!IMPORTANT]
> Hybridní připojení jsou podporována pouze v plánech systému Windows. Linux není podporován.

## <a name="outbound-ip-restrictions"></a>Omezení odchozí ip adresy

Omezení odchozíCH IP adres jsou k dispozici v tarifu Premium, plánu služby App Service nebo prostředí služby App Service. Můžete nakonfigurovat odchozí omezení pro virtuální síť, ve které se nasazuje prostředí služby App Service.

Když integrujete aplikaci funkcí do plánu Premium nebo plán služby App Service s virtuální sítí, aplikace může ve výchozím nastavení stále volat odchozí volání na internet. Přidáním nastavení `WEBSITE_VNET_ROUTE_ALL=1`aplikace vynutíte odeslání všech odchozích přenosů do virtuální sítě, kde lze omezit provoz.

## <a name="troubleshooting"></a>Řešení potíží

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Další kroky

Další informace o sítích a funkcích Azure:

* [Postupujte podle kurzu o začínáme s integrací virtuální sítě](./functions-create-vnet.md)
* [Přečtěte si nejčastější dotazy k funkcím v síti](./functions-networking-faq.md)
* [Další informace o integraci virtuálních sítí pomocí služby App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Další informace o virtuálních sítích v Azure](../virtual-network/virtual-networks-overview.md)
* [Povolení dalších síťových funkcí a řízení pomocí prostředí služby App Service](../app-service/environment/intro.md)
* [Připojení k jednotlivým místním prostředkům bez změn brány firewall pomocí hybridních připojení](../app-service/app-service-hybrid-connections.md)
