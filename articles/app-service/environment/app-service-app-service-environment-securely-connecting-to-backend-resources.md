---
title: Připojení k back-endu v1
description: Přečtěte si, jak se bezpečně připojit k back-endu prostředkům z App Service Environment. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 03f773e286697a12188f238cf2f422a18a20054f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74687299"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Zabezpečené připojení k prostředkům back-endu z App Serviceho prostředí
Vzhledem k [tomu, že][virtualnetwork]je vždy vytvořen App Service Environment v rámci virtuální sítě Azure Resource Manager **nebo** modelu nasazení Classic, odchozí připojení z App Service Environment do jiných back-end prostředků mohou být **výhradně v rámci** virtuální sítě.  V důsledku nedávné změny provedené v červnu 2016 lze služby ASE také nasadit do virtuálních sítí, které používají buď rozsahy veřejných adres, nebo RFC1918 adresní prostory (tj. soukromé adresy).  

Může se například jednat o SQL Server běžící na clusteru virtuálních počítačů s uzamčeným portem 1433.  Koncový bod může být ACLd, aby povoloval přístup jenom z jiných prostředků ve stejné virtuální síti.  

V jiném příkladu můžou být citlivé koncové body spouštěné místně a připojené k Azure prostřednictvím připojení [site-to-site][SiteToSite] nebo [Azure ExpressRoute][ExpressRoute] .  V důsledku toho budou mít přístup k místním koncovým bodům pouze prostředky ve virtuálních sítích připojených k tunelům typu Site-to-site nebo ExpressRoute.

U všech těchto scénářů se aplikace běžící na App Service Environment můžou bezpečně připojit k různým serverům a prostředkům.  Odchozí přenosy z aplikací běžících v App Service Environment do privátních koncových bodů ve stejné virtuální síti (nebo připojené ke stejné virtuální síti) budou přenášet jenom přes virtuální síť.  Odchozí přenosy do privátních koncových bodů nebudou přenášet přes veřejný Internet.

Jedna výstraha se vztahuje na odchozí přenosy z App Service Environment do koncových bodů v rámci virtuální sítě.  Prostředí App Service nemůžou mít přístup k koncovým bodům virtuálních počítačů umístěných ve **stejné** podsíti jako App Service Environment.  Obvykle by se nemělo jednat o problém, pokud App Service prostředí se nasazují do podsítě vyhrazené pro výhradní použití jenom pomocí App Service Environment.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Odchozí připojení a požadavky na DNS
Aby App Service Environment správně fungovalo, vyžaduje odchozí přístup k různým koncovým bodům. Úplný seznam externích koncových bodů používaných pomocným mechanismem najdete v části "požadované připojení k síti" v článku [Konfigurace sítě pro ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service prostředí vyžadují pro virtuální síť platnou infrastrukturu DNS.  Pokud z nějakého důvodu dojde ke změně konfigurace DNS po vytvoření App Service Environment, můžou vývojáři vynutit App Service Environment, aby si vybrali novou konfiguraci DNS.  Aktivuje se restartování prostředí za běhu pomocí ikony "restartovat", která se nachází v horní části okna správy App Service Environment na portálu způsobí, že prostředí vybralo novou konfiguraci DNS.

Před vytvořením App Service Environment taky doporučujeme, aby všechny vlastní servery DNS ve virtuální síti byly napřed času.  Pokud dojde ke změně konfigurace DNS virtuální sítě během vytváření App Service Environment, což způsobí selhání procesu vytváření App Service Environment.  Pokud v podobném přístupnosti existuje vlastní server DNS na druhém konci brány VPN a server DNS je nedosažitelný nebo nedostupný, proces vytváření App Service Environment se také nezdaří.

## <a name="connecting-to-a-sql-server"></a>Připojení k SQL Server
Konfigurace běžného SQL Server má koncový bod naslouchat na portu 1433:

![SQL Server koncový bod][SqlServerEndpoint]

Existují dva přístupy k omezení provozu do tohoto koncového bodu:

* [Seznamy síťových Access Control][NetworkAccessControlLists] (seznamy ACL sítě)
* [Skupiny zabezpečení sítě][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Omezení přístupu pomocí seznamu ACL sítě
Port 1433 se dá zabezpečit pomocí seznamu řízení přístupu k síti.  Následující příklad obsahuje adresy klientů, které pocházejí z virtuální sítě, a blokuje přístup ke všem ostatním klientům.

![Příklad seznamu Access Control sítě][NetworkAccessControlListExample]

Všechny aplikace běžící v App Service Environment ve stejné virtuální síti jako SQL Server se budou moci připojit k instanci SQL Server pomocí **interní** IP adresy virtuální sítě pro virtuální počítač SQL Server.  

Vzorový připojovací řetězec níže odkazuje na SQL Server pomocí jeho privátní IP adresy.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

I když má virtuální počítač taky veřejný koncový bod, pokusy o připojení s použitím veřejné IP adresy se odmítnou z důvodu seznamu ACL sítě. 

## <a name="restricting-access-with-a-network-security-group"></a>Omezení přístupu pomocí skupiny zabezpečení sítě
Alternativním přístupem k zabezpečení přístupu je skupina zabezpečení sítě.  Skupiny zabezpečení sítě je možné použít pro jednotlivé virtuální počítače nebo pro podsíť obsahující virtuální počítače.

Nejdřív je potřeba vytvořit skupinu zabezpečení sítě:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Omezení přístupu pouze k internímu provozu virtuální sítě je velmi jednoduché pro skupinu zabezpečení sítě.  Výchozí pravidla ve skupině zabezpečení sítě povolují přístup jenom z jiných síťových klientů ve stejné virtuální síti.

V důsledku toho dojde k uzamknutí přístupu k SQL Server tak jednoduché jako při použití skupiny zabezpečení sítě s výchozími pravidly na virtuální počítače se systémem SQL Server nebo na podsíť obsahující virtuální počítače.

Následující ukázka aplikuje skupinu zabezpečení sítě na nadřazenou podsíť:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Konečný výsledek je sada pravidel zabezpečení, která blokují externí přístup, a současně umožňuje interní přístup k virtuální síti:

![Výchozí pravidla zabezpečení sítě][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Začínáme
Pokud chcete začít pracovat se App Service prostředími, přečtěte si téma [Úvod do App Service Environment][IntroToAppServiceEnvironment]

Podrobnosti o řízení příchozího provozu na App Service Environment najdete v tématu [řízení příchozího provozu do App Service Environment][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
