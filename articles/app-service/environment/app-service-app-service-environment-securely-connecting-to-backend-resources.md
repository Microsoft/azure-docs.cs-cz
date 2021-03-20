---
title: Připojení k back-endu v1
description: Přečtěte si, jak se bezpečně připojit k back-endu prostředkům z App Service Environment. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9f8e288f771b9d584a0fd3430115f5fa60f68e47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88961801"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Zabezpečené připojení k prostředkům back-endu z App Serviceho prostředí
Vzhledem k [tomu, že][virtualnetwork]je vždy vytvořen App Service Environment v rámci virtuální sítě Azure Resource Manager **nebo** modelu nasazení Classic, odchozí připojení z App Service Environment do jiných back-end prostředků mohou být **výhradně v rámci** virtuální sítě. Od června 2016 lze služby ASE také nasadit do virtuálních sítí, které používají buď rozsahy veřejných adres, nebo RFC1918 adresních prostorů (privátní adresy).  

Může se například jednat o SQL Server běžící na clusteru virtuálních počítačů s uzamčeným portem 1433.  Koncový bod může být ACLd, aby povoloval přístup jenom z jiných prostředků ve stejné virtuální síti.  

V jiném příkladu můžou být citlivé koncové body spouštěné místně a připojené k Azure prostřednictvím připojení [site-to-site][SiteToSite] nebo [Azure ExpressRoute][ExpressRoute] .  V důsledku toho mohou přistupovat k místním koncovým bodům pouze prostředky ve virtuálních sítích připojených k tunelovým propojením typu Site-to-site nebo ExpressRoute.

U všech těchto scénářů se aplikace běžící na App Service Environment můžou bezpečně připojit k různým serverům a prostředkům. Pokud odchozí přenosy z aplikací běží v App Service Environment do privátních koncových bodů ve stejné virtuální síti nebo připojené ke stejné virtuální síti, budou se přenášet jenom přes virtuální síť.  Odchozí provoz do privátních koncových bodů se nesměruje přes veřejný Internet.

Jeden problém se týká odchozího provozu z App Service Environment do koncových bodů v rámci virtuální sítě. App Service prostředí nemůžou mít přístup k koncovým bodům virtuálních počítačů, které jsou umístěné ve **stejné** podsíti jako App Service Environment. Toto omezení obvykle by nemělo být problém, pokud App Service prostředí se nasazují do podsítě, která je vyhrazená pro použití výhradně App Service Environment.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Odchozí připojení a požadavky na DNS
Aby App Service Environment správně fungovalo, vyžaduje odchozí přístup k různým koncovým bodům. Úplný seznam externích koncových bodů používaných pomocným mechanismem najdete v části "požadované připojení k síti" v článku [Konfigurace sítě pro ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service prostředí vyžadují pro virtuální síť platnou infrastrukturu DNS.  Pokud se konfigurace DNS po vytvoření App Service Environment změní, mohou vývojáři vynutit App Service Environment, aby si vybrali novou konfiguraci DNS. V horní části okna pro správu App Service Environment na portálu vyberte ikonu **restart** , aby se aktivovalo restartování prostředí pro zavedení systému, což způsobí, že prostředí vybere novou konfiguraci DNS.

Před vytvořením App Service Environment taky doporučujeme, aby se všechny vlastní servery DNS ve virtuální síti nastavily předem.  Pokud se konfigurace DNS virtuální sítě během vytváření App Service Environment změnila, výsledkem bude selhání procesu vytváření App Service Environment. Pokud je na druhém konci služby VPN Gateway vlastní server DNS, který je nedosažitelný nebo nedostupný, proces vytváření App Service Environment se také nezdaří.

## <a name="connecting-to-a-sql-server"></a>Připojení k SQL Server
Konfigurace běžného SQL Server má koncový bod naslouchat na portu 1433:

![SQL Server koncový bod][SqlServerEndpoint]

Existují dva přístupy k omezení provozu do tohoto koncového bodu:

* [Seznamy síťových Access Control][NetworkAccessControlLists] (seznamy ACL sítě)
* [Network Security Groups (Skupiny zabezpečení sítě)][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Omezení přístupu pomocí seznamu ACL sítě
Port 1433 se dá zabezpečit pomocí seznamu řízení přístupu k síti.  Následující příklad přidá k oprávněním pro přiřazení adresy klientů, které pocházejí z virtuální sítě, a zablokuje přístup všem ostatním klientům.

![Příklad seznamu Access Control sítě][NetworkAccessControlListExample]

Všechny aplikace, které běží v App Service Environment, se ve stejné virtuální síti jako SQL Server můžou připojit k instanci SQL Server. Pro virtuální počítač SQL Server použijte interní IP adresu **virtuální** sítě.  

Vzorový připojovací řetězec níže odkazuje na SQL Server pomocí jeho privátní IP adresy.

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

I když má virtuální počítač také veřejný koncový bod, budou se pokusy o připojení k použití veřejné IP adresy odmítat z důvodu seznamu ACL sítě. 

## <a name="restricting-access-with-a-network-security-group"></a>Omezení přístupu pomocí skupiny zabezpečení sítě
Alternativním přístupem k zabezpečení přístupu je skupina zabezpečení sítě.  Skupiny zabezpečení sítě je možné použít pro jednotlivé virtuální počítače nebo pro podsíť obsahující virtuální počítače.

Nejdřív budete muset vytvořit skupinu zabezpečení sítě:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Omezení přístupu pouze k internímu provozu virtuální sítě je jednoduché pro skupinu zabezpečení sítě.  Výchozí pravidla ve skupině zabezpečení sítě povolují přístup jenom z jiných síťových klientů ve stejné virtuální síti.

V důsledku toho je uzamčení přístupu k SQL Server jednoduché. Stačí použít skupinu zabezpečení sítě s výchozími pravidly buď na virtuální počítače se systémem SQL Server, nebo na podsíť obsahující virtuální počítače.

Následující ukázka aplikuje skupinu zabezpečení sítě na nadřazenou podsíť:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

Konečný výsledek je sada pravidel zabezpečení, která blokují externí přístup, a současně umožňuje interní přístup k virtuální síti:

![Výchozí pravidla zabezpečení sítě][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Začínáme
Pokud chcete začít pracovat se App Service prostředími, přečtěte si téma [Úvod do App Service Environment][IntroToAppServiceEnvironment]

Podrobnosti o řízení příchozího provozu na App Service Environment najdete v tématu [řízení příchozího provozu do App Service Environment][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png