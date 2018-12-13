---
title: Podrobnosti o konfiguraci sítě pro Azure ExpressRoute
description: Podrobnosti o konfiguraci sítě pro službu App Service Environment pro PowerApps do virtuální sítě připojené k okruh Azure ExpressRoute.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 8dc6f595f312326f4082af9f875fefddc3a5fb8d
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53320705"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Podrobnosti o konfiguraci sítě pro službu App Service Environment pro PowerApps s využitím Azure ExpressRoute

Zákazníci mohou připojit [Azure ExpressRoute] [ ExpressRoute] okruhu do své infrastruktury virtuální sítě můžete rozšířit jejich místní síť do Azure. Vytvoření služby App Service Environment v podsíti [virtuální sítě] [ virtualnetwork] infrastruktury. Aplikace, které běží ve službě App Service Environment navazovat zabezpečená připojení k back endové prostředky, které jsou k dispozici pouze prostřednictvím připojení ExpressRoute.  

App Service Environment je možné vytvořit v těchto scénářích:
- Virtuální sítě Azure Resource Manageru.
- Virtuální sítě modelu nasazení Classic.
- Virtuální sítě, které používají rozsahy adres veřejné nebo definice RFC1918 adresní prostory (to znamená, privátních adres). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Vyžaduje síťové připojení

App Service Environment má požadavky na síťové připojení, které nemusí být zpočátku splněny ve virtuální síti, která je připojená k ExpressRoute.

App Service Environment vyžaduje následující nastavení síťového připojení fungoval správně:

* Odchozího síťového připojení k Azure Storage koncových bodů po celém světě na portu 80 a 443. Tyto koncové body jsou umístěné ve stejné oblasti jako služba App Service Environment a také ostatními oblastmi Azure. Koncové body služby Azure Storage vyřešit podle následujících domén DNS: table.core.windows.net, blob.core.windows.net, queue.core.windows.net a file.core.windows.net.  

* Odchozího síťového připojení ke službě soubory Azure na portu 445.

* Odchozího síťového připojení ke koncovým bodům Azure SQL Database, které se nacházejí ve stejné oblasti jako služba App Service Environment. V rámci database.windows.net domény, který vyžaduje otevřený přístup k portům 1433, 11000 11999 a 14000 14999 vyřešit koncových bodů SQL Database. Podrobnosti o použití SQL Database V12 portů najdete v tématu [porty nad 1433 pro technologii ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Odchozího síťového připojení k Azure koncových bodů roviny správy (model nasazení Azure classic a Azure Resource Manageru koncových bodů). Připojení s těmito koncovými body zahrnuje management.core.windows.net a management.azure.com domény. 

* Odchozího síťového připojení k doménám ocsp.msocsp.com mscrl.microsoft.com a crl.microsoft.com. Připojení k těmto doménám je potřeba k podpoře funkce SSL.

* Konfigurace DNS pro virtuální síť musí být schopni vyřešit všechny koncové body a domén uvedených v tomto článku. Pokud koncových bodů nerozpozná, vytváření služby App Service Environment se nezdaří. Všechny existující App Service Environment je označen jako není v pořádku.

* Odchozí přístup na port 53, je nutné pro komunikaci se servery DNS.

* Pokud vlastní server DNS existuje na druhém konci bránu VPN, DNS server musí být dostupný z podsítě, která obsahuje službu App Service Environment. 

* Odchozí síťová cesta nemůže přecházet mezi interní podnikové proxy servery a nemůže být vynucení tunelového propojení místní. Tyto akce změnit adresu efektivní NAT odchozí síťový provoz ze služby App Service Environment. Změny adres NAT odchozího provozu sítě služby App Service Environment způsobit selhání připojení k na řadu koncových bodů. Vytvoření prostředí App Service se nepovedlo. Všechny existující App Service Environment je označen jako není v pořádku.

* Příchozí síťový přístup k požadované porty pro App Service Environment musí být povoleno. Podrobnosti najdete v tématu [řízení příchozího provozu do služby App Service Environment][requiredports].

Ke splnění požadavků na DNS, ujistěte se, že je platný infrastruktury DNS konfiguraci a údržbu pro virtuální síť. Pokud se po vytvoření služby App Service Environment se změnila konfigurace DNS, můžete vynutit vývojáři App Service Environment, aby se získaly novou konfiguraci DNS. Postupné restartování prostředí můžete aktivovat pomocí **restartovat** ikonu spravován pomocí služby App Service Environment na [webu Azure Portal] [NewPortal]. Restartování způsobí, že prostředí tak, aby získaly novou konfiguraci DNS.

Ke splnění požadavků na přístup příchozích dat, konfigurace [skupina zabezpečení sítě (NSG)][NetworkSecurityGroups] v podsíti služby App Service Environment. Skupiny zabezpečení sítě povoluje požadovaný přístup [řízení příchozího provozu do služby App Service Environment][requiredports].

## <a name="outbound-network-connectivity"></a>Odchozího síťového připojení

Ve výchozím nastavení nově vytvořený okruh ExpressRoute inzeruje výchozí trasu, která umožňuje odchozí připojení k Internetu. App Service Environment tuto konfiguraci můžete použít pro připojení k jiné koncové body Azure.

Běžnou konfigurací zákazníků je definovat vlastní výchozí trasa (0.0.0.0/0), která vynutí odchozí internetový provoz do toku místní. Tento tok provozu vždy přeruší služba App Service Environment. Odchozí provoz je blokované v místním nebo NAT by nerozpoznatelný sadu adresy, které přestane fungovat v různých koncových bodů Azure.

Toto řešení je definování (nejméně) trasy definované uživatelem (udr) na podsíť, která obsahuje službu App Service Environment. Definuje trasu UDR trasy konkrétní podsítě, které se uplatní místo výchozí trasu.

Pokud je to možné použijte následující konfiguraci:

* Konfigurace ExpressRoute inzeruje 0.0.0.0/0. Ve výchozím nastavení konfiguraci platnost tunelových propojení všech odchozích přenosů do místní.
* Uživatelem definovaná TRASA použitá na podsíť, která obsahuje službu App Service Environment definuje 0.0.0.0/0 s dalším segmentem směrování typu internet. Příklad této konfigurace je popsána dále v tomto článku.

Celkové požadavky tato konfigurace je, že uživatelem definovaná TRASA úrovni podsítě ExpressRoute vynuceného tunelování má přednost před. Je zaručeno, odchozí internetový přístup z aplikace App Service Environment.

> [!IMPORTANT]
> Trasy definované v trase UDR musí být dost konkrétní, aby přednost před všechny trasy, které jsou inzerovanými konfigurací ExpressRoute. Popsané v další části příkladu široký rozsah adres 0.0.0.0/0. Tento rozsah může být přepsána omylem inzerování tras, které používají konkrétnější rozsahy adres.
> 
> App Service Environment není podporované v konfiguracích ExpressRoute, které víc inzerování tras z cesty veřejného partnerského vztahu do cestou soukromého partnerského vztahu. Konfigurace ExpressRoute s nakonfigurovanými veřejnými partnerskými uzly přijímají inzerci tras od Microsoftu pro velkou sadu rozsahů adres IP adres Microsoft Azure. Pokud tyto rozsahy adres křížová inzerce na cestou soukromého partnerského vztahu, všechny odchozí síťové pakety z podsítě služby App Service Environment jsou vynuceným tunelovým propojením do zákazníka místní síťové infrastruktury. Tento tok sítí nepodporuje aktuálně služby App Service Environment. Jedním z řešení je ukončit křížovou inzerci tras z cesty veřejného partnerského vztahu pro cestou soukromého partnerského vztahu.
> 
> 

Informace o trasách definovaných uživatelem najdete v tématu [směrování provozu virtuální sítě][UDROverview].  

Zjistěte, jak vytvořit a nakonfigurovat trasy definované uživatelem, najdete v článku [směrování síťového provozu pomocí směrovací tabulky pomocí Powershellu] [UDRHowTo].

## <a name="udr-configuration"></a>Konfigurace směrování definovaného uživatelem

Tato část popisuje příklad konfigurace uživatelem definovaná TRASA pro App Service Environment.

### <a name="prerequisites"></a>Požadavky

* Instalace Azure Powershellu z [Azure stáhne stránka] [AzureDownloads]. Zvolte download s datem. června 2015 nebo novější. V části **nástroje příkazového řádku** > **prostředí Windows PowerShell**vyberte **nainstalovat** nainstalovat nejnovější rutiny prostředí PowerShell.

* Vytvořte jedinečnou podsíť pro výhradní použití ve službě App Service Environment. Jedinečnou podsíť zajišťuje, že u otevřít odchozí provoz podsítě služby App Service Environment pouze trasy definované uživatelem.

> [!IMPORTANT]
> Po dokončení kroků konfigurace pouze nasazení služby App Service Environment. Kroky Ujistěte se, že před pokusem o nasazení služby App Service Environment je k dispozici odchozího síťového připojení.

### <a name="step-1-create-a-route-table"></a>Krok 1: Vytvoření směrovací tabulky

Vytvoření směrovací tabulky s názvem **DirectInternetRouteTable** v oblasti Azure USA – Západ, jak je znázorněno v tomto fragmentu kódu:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Krok 2: Vytvářet trasy v tabulce

Přidáte trasy do směrovací tabulky k povolení odchozího přístupu k Internetu.  

Konfigurace odchozího přístupu k Internetu. Definujte trasu pro 0.0.0.0/0, jak je znázorněno v tomto fragmentu kódu:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 je rozsah široké adres. Rozsah je přepsán rozsahy adres inzerovanými ExpressRoute jsou konkrétnější. Trasu UDR s trasy 0.0.0.0/0 byste měli použít ve spojení s konfigurací ExpressRoute, který zajistí inzerci pouze 0.0.0.0/0. 

Jako alternativu stáhněte si aktuální, komplexní seznam rozsahy CIDR používaných službou Azure. Soubor XML pro všechny rozsahy adres IP adres Azure je k dispozici [Microsoft Download Center] [DownloadCenterAddressRanges].  

> [!NOTE]
>
> Rozsahy IP adres Azure adres v průběhu času měnit. Trasy definované uživatelem musí pravidelné ruční aktualizace pro synchronizaci.
>
> Jeden uživatelem definovaná TRASA má výchozí horní limit 100 tras. Je potřeba "shrnout" rozsahy adres IP adres Azure, aby vyhovovaly limitu 100-route. Trasy definované uživatelem definovaná TRASA musí být konkrétnější než trasy, které jsou inzerovanými připojení ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Krok 3: Přidružení tabulky k podsíti

Přidružení směrovací tabulky podsítě, ve kterém plánujete nasadit službu App Service Environment. Tento příkaz přidruží **DirectInternetRouteTable** tabulky **ASESubnet** podsíť, která bude obsahovat službu App Service Environment.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Krok 4: Testování a potvrďte trasu

Po směrovací tabulka je vázána k podsíti, testování a potvrďte trasu.

Nasazení virtuálního počítače do podsítě a zkontrolujte tyto podmínky:

* Odchozí provoz do Azure a mimo Azure koncovým bodům popsaným v tomto článku se **není** směřují okruh ExpressRoute. Pokud odchozího provozu z podsítě je vynucení tunelového propojení on-premises vždy vytvoření služby App Service Environment se nezdaří.
* Vyhledávání DNS pro koncovým bodům popsaným v tomto článku všechny správně přeloženy. 

Po dokončení kroků konfigurace a potvrďte trasu, odstraňte virtuální počítač. Podsíť musí být "prázdný" při vytváření služby App Service Environment.

Nyní jste připraveni nasadit službu App Service Environment!

## <a name="next-steps"></a>Další postup

Začínáme s App Service Environment pro PowerApps, najdete v článku [Úvod do služby App Service Environment] [IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[networkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
<!-- Old link -- [UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ --> [UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell [HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md [AzureDownloads]: https://azure.microsoft.com/downloads/ [DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ [IntroToAppServiceEnvironment]: app-service-app-service-environment-intro.md [NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
