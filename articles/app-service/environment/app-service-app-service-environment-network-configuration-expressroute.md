---
title: Konfigurace Azure ExpressRoute v1
description: Konfigurace sítě pro App Service Environment pro PowerApps s Azure ExpressRoute. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 3fa2677f7c71b118ec3f7da717e29d5879ee982b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961818"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Podrobnosti o konfiguraci sítě pro App Service Environment pro PowerApps s Azure ExpressRoute

Zákazníci mohou připojit okruh [Azure ExpressRoute][ExpressRoute] ke své infrastruktuře virtuální sítě, aby svou místní síť rozšířili do Azure. App Service Environment se vytvoří v podsíti infrastruktury [virtuální sítě][virtualnetwork] . Aplikace, které běží na App Service Environment navážou zabezpečená připojení k prostředkům back-endu, která jsou přístupná jenom přes připojení ExpressRoute.  

App Service Environment lze vytvořit v těchto scénářích:
- Azure Resource Manager virtuální sítě.
- Virtuální sítě modelu nasazení Classic.
- Virtuální sítě, které používají rozsahy veřejných adres nebo RFC1918 adresní prostory (tj. privátní adresy). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Požadované síťové připojení

App Service Environment má požadavky na připojení k síti, které zpočátku nemusí být splněné ve virtuální síti, která je připojená k ExpressRoute.

App Service Environment vyžaduje, aby následující nastavení připojení k síti fungovalo správně:

* Odchozí připojení k síti Azure Storage koncovým bodům po celém světě na portu 80 a portu 443. Tyto koncové body se nacházejí ve stejné oblasti jako App Service Environment a také v dalších oblastech Azure. Azure Storage koncových bodů se vyhodnotí v následujících doménách DNS: table.core.windows.net, blob.core.windows.net, queue.core.windows.net a file.core.windows.net.  

* Odchozí síťové připojení ke službě soubory Azure na portu 445.

* Odchozí připojení k síti Azure SQL Database koncovým bodům, které se nacházejí ve stejné oblasti jako App Service Environment. SQL Database koncových bodů se vyhodnotí v doméně database.windows.net, která vyžaduje otevřený přístup k portům 1433, 11000-11999 a 14000-14999. Podrobnosti o využití portů SQL Database V12 najdete v tématu [porty nad 1433 pro ADO.NET 4,5](../../azure-sql/database/adonet-v12-develop-direct-route-ports.md).

* Odchozí síťové připojení k koncovým bodům roviny správy Azure (model nasazení Azure Classic a koncové body Azure Resource Manager). Připojení k těmto koncovým bodům zahrnuje domény management.core.windows.net a management.azure.com. 

* Odchozí síťové připojení k doménám ocsp.msocsp.com, mscrl.microsoft.com a crl.microsoft.com. Připojení k těmto doménám je potřeba k podpoře funkcí TLS.

* Konfigurace DNS pro virtuální síť musí být schopná přeložit všechny koncové body a domény, které jsou uvedené v tomto článku. Pokud se koncové body nedají vyřešit, App Service Environment vytváření selhalo. Všechny existující App Service Environment jsou označeny jako chybné.

* Pro komunikaci se servery DNS se vyžaduje odchozí přístup na portu 53.

* Pokud na druhém konci brány VPN existuje vlastní server DNS, musí být server DNS dosažitelný z podsítě, která obsahuje App Service Environment. 

* Cesta pro odchozí síť nemůže cestovat prostřednictvím interních podnikových proxy serverů a nemůže být vynucená tunelová propojení místně. Tyto akce mění efektivní adresu NAT odchozího síťového provozu z App Service Environment. Změny adresy NAT App Service Environment odchozího síťového provozu způsobují selhání připojení k mnoha koncovým bodům. Vytvoření App Service Environment se nezdařilo. Všechny existující App Service Environment jsou označeny jako chybné.

* Musí být povolený příchozí síťový přístup k požadovaným portům pro App Service Environment. Podrobnosti najdete v tématu [postup řízení příchozího provozu do App Service Environment][requiredports].

Pokud chcete splnit požadavky DNS, ujistěte se, že je nakonfigurovaná a udržovaná platná infrastruktura DNS pro virtuální síť. Pokud se konfigurace DNS po vytvoření App Service Environment změní, mohou vývojáři vynutit App Service Environment, aby si vybrali novou konfiguraci DNS. Restartování prostředí za provozu můžete aktivovat pomocí ikony **restartování** v části Správa App Service Environment v [Azure Portal][NewPortal]. Restartování způsobí, že prostředí vybralo novou konfiguraci DNS.

Pokud chcete splnit požadavky na příchozí síťový přístup, nakonfigurujte [skupinu zabezpečení sítě (NSG)][NetworkSecurityGroups] na App Service Environment podsíti. NSG umožňuje požadovaný přístup [k řízení příchozího provozu do App Service Environment][requiredports].

## <a name="outbound-network-connectivity"></a>Odchozí připojení k síti

Ve výchozím nastavení inzeruje nově vytvořený okruh ExpressRoute výchozí trasu, která umožňuje odchozí připojení k Internetu. App Service Environment tuto konfiguraci můžete použít pro připojení k jiným koncovým bodům Azure.

Běžnou zákaznickou konfigurací je definování vlastní výchozí trasy (0.0.0.0/0), která vynucuje odchozí internetový provoz do místního toku. Tento tok přenosů invariably přerušují App Service Environment. Odchozí přenosy se buď zablokovaly místně, nebo pomocí překladu adres (NAT) do nerozpoznatelné sady adres, které už nefungují s různými koncovými body Azure.

Řešením je definování jedné (nebo více) uživatelem definovaných tras (udr) v podsíti, která obsahuje App Service Environment. UDR definuje trasy specifické pro podsíť, které se uplatní místo výchozí trasy.

Pokud je to možné, použijte následující konfiguraci:

* Konfigurace ExpressRoute inzeruje adresu 0.0.0.0/0. Ve výchozím nastavení tato konfigurace vynutí tunelování veškerého odchozího provozu v místním prostředí.
* UDR, který se použije pro podsíť obsahující App Service Environment, definuje 0.0.0.0/0 s typem dalšího segmentu směrování Internet. Příklad této konfigurace je popsán dále v tomto článku.

Kombinovaný účinek této konfigurace je, že UDR na úrovni podsítě má přednost před ExpressRoute vynuceným tunelovým propojením. Je zaručený odchozí přístup k Internetu z App Service Environment.

> [!IMPORTANT]
> Trasy definované v UDR musí být dostatečně specifické, aby měly přednost před všemi trasami, které jsou inzerovány konfigurací ExpressRoute. V příkladu popsaném v další části se používá širší rozsah adres 0.0.0.0/0. Tento rozsah může být omylem přepsán reklamními inzeráty, které používají více konkrétních rozsahů adres.
> 
> App Service Environment se nepodporují s konfiguracemi ExpressRoute, které mezi sebou směrují trasy z cesty veřejného partnerského vztahu k cestě soukromého partnerského vztahu. Konfigurace ExpressRoute s nakonfigurovaným veřejným partnerským vztahem obdrží od Microsoftu inzerci tras pro velkou sadu Microsoft Azure rozsahů IP adres. Pokud jsou tyto rozsahy adres přeloženy v privátní partnerské cestě, všechny odchozí síťové pakety z App Service Environment podsítě vynutí tunelování do místní síťové infrastruktury zákazníka. Tento tok sítě není v současnosti s App Service Environment podporován. Jedním z řešení je zastavit trasy mezi reklamními partnery z cesty veřejného partnerského vztahu k cestě privátního partnerského vztahu.
> 
> 

Základní informace o trasách definovaných uživatelem najdete v tématu [směrování provozu virtuální sítě][UDROverview].  

Informace o tom, jak vytvořit a nakonfigurovat uživatelsky definované trasy, najdete v tématu [směrování síťového provozu pomocí směrovací tabulky pomocí PowerShellu][UDRHowTo].

## <a name="udr-configuration"></a>Konfigurace UDR

Tato část ukazuje příklad konfigurace UDR pro App Service Environment.

### <a name="prerequisites"></a>Požadavky

* Nainstalujte Azure PowerShell na [stránce soubory ke stažení pro Azure][AzureDownloads]. Vyberte soubor ke stažení s datem od června 2015 nebo novějším. V části **nástroje příkazového řádku**  >  **Windows PowerShell** vyberte **instalovat** a nainstalujte nejnovější rutiny PowerShellu.

* Vytvořte jedinečnou podsíť pro výhradní použití pomocí App Service Environment. Jedinečná podsíť zajišťuje, že udr, který se použije pro podsíť, otevírá odchozí provoz jenom pro App Service Environment.

> [!IMPORTANT]
> Nasaďte App Service Environment jenom po dokončení kroků konfigurace. Tento postup zajistí, aby bylo k dispozici odchozí připojení k síti, než se pokusíte nasadit App Service Environment.

### <a name="step-1-create-a-route-table"></a>Krok 1: vytvoření směrovací tabulky

V Západní USA oblasti Azure vytvořte směrovací tabulku s názvem **DirectInternetRouteTable** , jak je znázorněno v tomto fragmentu kódu:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Krok 2: vytvoření tras v tabulce

Pokud chcete povolit odchozí přístup k Internetu, přidejte do směrovací tabulky trasy.  

Nakonfigurujte odchozí přístup k Internetu. Definujte trasu pro 0.0.0.0/0, jak je znázorněno v tomto fragmentu kódu:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

hodnota 0.0.0.0/0 je širokou škálou adres. Rozsah je přepsán rozsahy adres inzerovaných podle ExpressRoute, které jsou konkrétnější. UDR s trasou 0.0.0.0/0 by se měly používat ve spojení s konfigurací ExpressRoute, která inzeruje jenom 0.0.0.0/0. 

Jako alternativu si můžete stáhnout aktuální a kompletní seznam rozsahů CIDR používaných v Azure. Soubor XML pro všechny rozsahy IP adres Azure je k dispozici na [webu služby Stažení softwaru][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Rozsahy IP adres Azure se v průběhu času mění. Trasy definované uživatelem vyžadují pravidelné ruční aktualizace, aby zůstaly synchronizované.
>
> Jeden UDR má výchozí horní limit 100 tras. Musíte "shrnout" rozsahy IP adres Azure, které se vejdou do limitu 100-tras. Trasy definované UDR musí být specifičtější než trasy, které jsou inzerovány vaším připojením ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Krok 3: přidružení tabulky k podsíti

Přidružte směrovací tabulku k podsíti, do které chcete nasadit App Service Environment. Tento příkaz přidruží tabulku **DirectInternetRouteTable** k podsíti **ASESubnet** , která bude obsahovat App Service Environment.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Krok 4: testování a potvrzení trasy

Poté, co je tabulka směrování svázána s podsítí, otestujte a potvrďte trasu.

Nasaďte virtuální počítač do podsítě a potvrďte tyto podmínky:

* Odchozí přenosy do koncových bodů Azure a mimo Azure popsaných v tomto článku **nesměrují** okruh ExpressRoute. Pokud se odchozí přenos z podsítě vynutí pro místní připojení, App Service Environment vytváření vždy selhává.
* Hledání ve službě DNS pro koncové body popsané v tomto článku se vyřeší správně. 

Po dokončení kroků konfigurace a potvrzení trasy odstraňte virtuální počítač. Pokud je vytvořena App Service Environment, musí být podsíť "prázdná".

Teď jste připraveni nasadit App Service Environment!

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s App Service Environment pro PowerApps, přečtěte si téma [Úvod do App Service Environment][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[UDROverview]: ../../virtual-network/virtual-networks-udr-overview.md
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: ../../virtual-network/tutorial-create-route-table-powershell.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->