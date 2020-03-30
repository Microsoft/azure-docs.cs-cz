---
title: Konfigurace Azure ExpressRoute v1
description: Konfigurace sítě pro prostředí služby App Service pro PowerApps s Azure ExpressRoute Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 8a83c2f6ac7599ff37237834a85b7771cf4ee502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243871"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Podrobnosti o konfiguraci sítě pro prostředí služby App Service pro PowerApps s Azure ExpressRoute

Zákazníci můžou připojit okruh [Azure ExpressRoute][ExpressRoute] ke své infrastruktuře virtuální sítě a rozšířit tak místní síť do Azure. Prostředí služby App Service se vytvoří v podsíti infrastruktury [virtuální sítě.][virtualnetwork] Aplikace, které běží v prostředí Služby App Service, vytvářejí zabezpečená připojení k back-endovým prostředkům, které jsou přístupné pouze prostřednictvím připojení ExpressRoute.  

Prostředí služby App Service lze vytvořit v těchto scénářích:
- Virtuální sítě Azure Resource Manageru.
- Virtuální sítě modelu klasického nasazení.
- Virtuální sítě, které používají rozsahy veřejných adres nebo adresní prostory RFC1918 (to znamená soukromé adresy). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Požadované připojení k síti

App Service Environment má požadavky na připojení k síti, které zpočátku nemusí být splněny ve virtuální síti, která je připojena k ExpressRoute.

Prostředí služby App Service vyžaduje, aby správně fungovalo následující nastavení síťového připojení:

* Odchozí síťové připojení ke koncovým bodům Služby Azure Storage po celém světě na portech 80 i portech 443. Tyto koncové body se nacházejí ve stejné oblasti jako prostředí služby App Service a také další oblasti Azure. Koncové body Azure Storage řeší v následujících doménách DNS: table.core.windows.net, blob.core.windows.net, queue.core.windows.net a file.core.windows.net.  

* Odchozí síťové připojení ke službě Azure Files na portu 445.

* Odchozí síťové připojení ke koncovým bodům Azure SQL Database, které jsou umístěné ve stejné oblasti jako prostředí služby App Service. Koncové body databáze SQL vyřešit v rámci domény database.windows.net, který vyžaduje otevřený přístup k portům 1433, 11000-11999 a 14000-14999. Podrobnosti o využití portů SQL Database V12 naleznete [v tématu Porty nad 1433 pro ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Odchozí síťové připojení ke koncovým bodům schránky azure (klasický model nasazení Azure a koncové body Azure Resource Manageru). Připojení k těmto koncovým bodům zahrnuje management.core.windows.net a management.azure.com domény. 

* Odchozí síťové připojení k ocsp.msocsp.com, mscrl.microsoft.com a crl.microsoft.com doménám. Připojení k těmto doménám je nutné pro podporu funkcí SSL.

* Konfigurace DNS pro virtuální síť musí být schopná vyřešit všechny koncové body a domény uvedené v tomto článku. Pokud koncové body nelze přeložit, vytvoření prostředí služby App Service environment se nezdaří. Všechny existující prostředí služby App Service je označenjako není v pořádku.

* Pro komunikaci se servery DNS je vyžadován odchozí přístup na portu 53.

* Pokud na druhém konci brány VPN existuje vlastní server DNS, musí být server DNS dostupný z podsítě, která obsahuje prostředí služby App Service. 

* Odchozí síťová cesta nemůže cestovat přes interní podnikové proxy servery a nemůže být vynucena tunelovým propojením místně. Tyto akce změnit efektivní ADRESU NAT odchozích síťových přenosů z prostředí App Service. Změny adresy NAT prostředí App Service odchozí sítě způsobit selhání připojení k mnoha koncových bodů. Vytvoření prostředí služby App Service se nezdaří. Všechny existující prostředí služby App Service je označenjako není v pořádku.

* Musí být povolen příchozí síťový přístup k požadovaným portům pro prostředí služby App Service. Podrobnosti najdete v tématu [Jak řídit příchozí provoz do prostředí Služby App Service][requiredports].

Chcete-li splnit požadavky DNS, ujistěte se, že je pro virtuální síť nakonfigurovaná a udržovaná platná infrastruktura DNS. Pokud se konfigurace DNS po vytvoření prostředí služby App Service změní, vývojáři můžou vynutit, aby prostředí služby App Service vyzdvihla novou konfiguraci DNS. Postupné restartování prostředí můžete spustit pomocí ikony **Restartovat** v části Správa prostředí služby App Service na [webu Azure Portal][NewPortal]. Restartování způsobí, že prostředí vyzvednout novou konfiguraci DNS.

Chcete-li splnit požadavky na příchozí přístup k síti, nakonfigurujte [skupinu zabezpečení sítě (NSG)][NetworkSecurityGroups] v podsíti Prostředí služby App Service. NSG umožňuje požadovaný přístup [k řízení příchozí provoz do prostředí služby App Service][requiredports].

## <a name="outbound-network-connectivity"></a>Odchozí připojení k síti

Ve výchozím nastavení nově vytvořený okruh ExpressRoute inzeruje výchozí trasu, která umožňuje odchozí připojení k internetu. Prostředí služby App Service můžete použít tuto konfiguraci pro připojení k jiným koncovým bodům Azure.

Běžnou konfigurací zákazníka je definování vlastní výchozí trasy (0.0.0.0/0), která vynutí tok odchozího internetového provozu místně. Tento tok provozu vždy přeruší prostředí služby App Service. Odchozí provoz je blokován místně nebo NAT'd na nerozpoznatelnou sadu adres, které už nefungují s různými koncovými body Azure.

Řešením je definovat jednu (nebo více) uživatelem definovaných tras (UDR) v podsíti, která obsahuje prostředí služby App Service. UDR definuje trasy specifické pro podsíť, které jsou dodrženy namísto výchozí trasy.

Pokud je to možné, použijte následující konfiguraci:

* Konfigurace ExpressRoute inzeruje 0.0.0.0/0. Ve výchozím nastavení vynutitu konfigurace tunely všechny odchozí provoz v místním prostředí.
* UDR aplikované na podsíť, která obsahuje prostředí služby App Service definuje 0.0.0.0/0 s dalším typem směrování internetu. Příklad této konfigurace je popsán dále v tomto článku.

Kombinovaný efekt této konfigurace spoá, že udr na úrovni podsítě má přednost před tunelovým propojením síly ExpressRoute. Odchozí přístup k internetu z prostředí App Service Environment je zaručen.

> [!IMPORTANT]
> Trasy definované v UDR musí být dostatečně specifické, aby měly přednost před všemi trasami, které jsou inzerovány konfigurací ExpressRoute. Příklad popsaný v další části používá široký rozsah adres 0.0.0.0/0. Tento rozsah může být omylem přepsán a reklamy na trasu, které používají konkrétnější rozsahy adres.
> 
> Prostředí služby App Service není podporováno konfiguracemi ExpressRoute, které křížově inzerují trasy z cesty veřejného partnerského vztahu k cestě soukromého partnerského vztahu. Konfigurace ExpressRoute, které mají nakonfigurované veřejné partnerské vztahy, přijímají inzerování směrování od Microsoftu pro rozsáhlou sadu rozsahů IP adres Microsoft Azure. Pokud jsou tyto rozsahy adres křížově inzerovány na cestě soukromého partnerského vztahu, všechny odchozí síťové pakety z podsítě Prostředí služby App Service jsou vynuceny tunelové propojení do místní síťové infrastruktury zákazníka. Tento síťový tok není aktuálně podporován s prostředím služby App Service. Jedním z řešení je zastavit křížové inzerce trasy z veřejného partnerského vztahu cestu k soukromé partnerské cesty.
> 
> 

Základní informace o uživatelem definovaných trasách naleznete [v tématu Směrování provozu virtuální sítě][UDROverview].  

Informace o vytváření a konfiguraci uživatelem definovaných tras naleznete [v tématu Směrování síťového provozu pomocí směrovací tabulky pomocí prostředí PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>Konfigurace UDR

Tato část ukazuje příklad konfigurace UDR pro prostředí služby App Service.

### <a name="prerequisites"></a>Požadavky

* Nainstalujte Azure PowerShell ze [stránky Azure Ke stažení][AzureDownloads]. Vyberte si stahování s datem června 2015 nebo novějším. V části **Nástroje** > příkazového řádku Windows**PowerShell**vyberte **nainstalovat** a nainstalujte nejnovější rutiny prostředí PowerShell.

* Vytvořte jedinečnou podsíť pro výhradní použití prostředím Služby App Service. Jedinečná podsíť zajišťuje, že udr.

> [!IMPORTANT]
> Prostředí služby App Service nasadit pouze po dokončení kroků konfigurace. Postup zajistí, že odchozí síťové připojení je k dispozici před pokusem o nasazení prostředí služby App Service.

### <a name="step-1-create-a-route-table"></a>Krok 1: Vytvoření směrovací tabulky

Vytvořte směrovací tabulku s názvem **DirectInternetRouteTable** v oblasti Západní US Azure, jak je znázorněno v tomto fragmentu:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Krok 2: Vytvoření tras v tabulce

Přidáním tras do směrovací tabulky povolíte odchozí přístup k Internetu.  

Konfigurace odchozího přístupu k Internetu. Definujte trasu pro 0.0.0.0/0, jak je znázorněno v tomto fragmentu:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 je široký rozsah adres. Rozsah je přepsán rozsahy adres inzerované ExpressRoute, které jsou konkrétnější. UDR s trasou 0.0.0.0/0 by měl být použit ve spojení s konfigurací ExpressRoute, která inzeruje pouze 0.0.0.0/0. 

Jako alternativu si stáhněte aktuální, komplexní seznam rozsahů CIDR, které používá Azure. Soubor XML pro všechny rozsahy IP adres Azure je k dispozici na [webu Microsoft Download Center][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Rozsahy IP adres Azure se v průběhu času mění. Uživatelem definované trasy vyžadují pravidelné ruční aktualizace, aby byly synchronizovány.
>
> Jeden UDR má výchozí horní limit 100 tras. Musíte "shrnout" rozsahy IP adres Azure, aby se vešly do limitu 100 tras. Trasy definované UDR musí být konkrétnější než trasy inzerované připojením ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Krok 3: Přidružení tabulky k podsíti

Přidružte směrovací tabulku k podsíti, do které chcete nasadit prostředí služby App Service. Tento příkaz přidruží tabulku **DirectInternetRouteTable** k podsíti **ASESubnet,** která bude obsahovat prostředí služby App Service.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Krok 4: Testování a potvrzení trasy

Po spoji tabulky postupu svázané s podsítí otestujte a potvrďte trasu.

Nasazení virtuálního počítače do podsítě a potvrďte tyto podmínky:

* Odchozí provoz do koncových bodů Azure a mimo Azure **not** popsaných v tomto článku nestéká okruhem ExpressRoute. Pokud odchozí provoz z podsítě je vynuceno tunelové propojení v místním prostředí, vytvoření prostředí služby App Service vždy nezdaří.
* Dns vyhledávání pro koncové body popsané v tomto článku všechny vyřešit správně. 

Po dokončení kroků konfigurace a potvrzení trasy odstraňte virtuální počítač. Podsíť musí být "prázdný" při vytvoření prostředí služby App Service.

Nyní jste připraveni nasadit prostředí služby App Service!

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s prostředím Služby aplikací pro PowerApps, [přečtěte si úvodní informace o prostředí služby App Service][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
