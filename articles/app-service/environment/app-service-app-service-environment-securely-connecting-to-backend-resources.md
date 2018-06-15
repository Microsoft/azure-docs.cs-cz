---
title: Bezpečné připojení k back-endové prostředky ze služby App Service Environment
description: Další informace o tom, jak bezpečně připojit back-endové prostředky ze služby App Service Environment.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: a43d88d64710b95dd753c19f02582f22accac8b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836522"
---
# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Bezpečné připojení k back-endové prostředky ze služby App Service Environment
## <a name="overview"></a>Přehled
Vzhledem k tomu, že služby App Service Environment se vždy vytvoří v **buď** virtuální síť Azure Resource Manager **nebo** modelu nasazení classic [virtuální sítě] [ virtualnetwork], odchozí připojení ze služby App Service Environment jiných back-endové prostředky můžete toku výhradně přes virtuální síť.  Nedávné změny provedené v června 2016 ASEs také lze nasadit do virtuálních sítí, které používají rozsahů veřejných adres nebo RFC1918 adresní prostory (tj. privátní adresy).  

Například může být SQL Server běžící na clusteru s podporou virtuálních počítačů s port 1433 uzamčené.  Koncový bod může být ACLd pouze povolit přístup z jiných zdrojů ve stejné virtuální síti.  

Další příklad citlivé koncových bodů může spustit místně a připojit k Azure přes buď [Site-to-Site] [ SiteToSite] nebo [Azure ExpressRoute] [ ExpressRoute] připojení.  V důsledku toho pouze prostředky ve virtuálních sítích, které jsou připojené k Site-to-Site nebo ExpressRoute tunely bude mít přístup k místní koncové body.

Pro všechny z těchto scénářů bude aplikace běžící na služby App Service Environment navázat zabezpečené připojení k různým serverům a prostředky.  Odchozí provoz z aplikace běžící ve službě App Service Environment privátní koncových bodů ve stejné virtuální síti (nebo připojené ke stejné virtuální síti), bude pouze toku přes virtuální síť.  Odchozí přenosy na privátní koncové body nebude toku prostřednictvím veřejného Internetu.

Jeden přímý přístup paměti platí pro odchozí provoz ze služby App Service Environment do koncových bodů v rámci virtuální sítě.  Prostředí App Service se nemůže připojit koncové body virtuálních počítačů, které jsou umístěné v **stejné** podsíti jako App Service Environment.  Za normálních okolností nemělo by se jednat problém, dokud prostředí App Service jsou nasazeny do podsítě vyhrazené pro výhradní použití App Service Environment.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Odchozí připojení a požadavky na DNS
Pro služby App Service Environment ke správnému fungování vyžaduje odchozí přístup k různým koncové body. Úplný seznam externí koncové body používané App Service Environment je v části "Vyžaduje připojení k síti" [konfiguraci sítě pro ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) článku.

Prostředí služby App Service vyžaduje platnou konfiguraci pro virtuální síť infrastruktury služby DNS.  Pokud z nějakého důvodu dojde ke změně konfigurace DNS po vytvoření služby App Service Environment, vývojáři vynutit služby App Service Environment načíst novou konfiguraci DNS.  Aktivuje restartu postupného prostředí pomocí ikonu "Restartovat" se nachází v horní části okna Správa služby App Service Environment na portálu způsobí, že prostředí tak, aby se vyzvedávat novou konfiguraci DNS.

Dále je doporučeno, aby žádné vlastní servery DNS na virtuální sítě se instalační program dopředu před vytvořením služby App Service Environment.  Pokud je při vytváření služby App Service Environment se změnila konfigurace DNS virtuální sítě, které způsobí selhání procesu vytvoření služby App Service Environment.  V souvislosti podobnou Pokud na druhém konci brány VPN existuje vlastního serveru DNS a serveru DNS je nedostupná nebo není k dispozici, procesu vytváření služby App Service Environment se také nezdaří.

## <a name="connecting-to-a-sql-server"></a>Připojení k serveru SQL
Obvyklé konfigurace systému SQL Server má koncový bod naslouchání na portu 1433:

![Koncový bod serveru SQL][SqlServerEndpoint]

Existují dva přístupy pro provoz směřující do tohoto koncového bodu:

* [Sítě seznamy řízení přístupu][NetworkAccessControlLists] (sítě ACL)
* [Skupiny zabezpečení sítě][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Omezení přístupu k síti seznamu ACL
Port 1433 lze zabezpečit pomocí seznamu řízení přístupu síti.  V příkladu níže povolených programů klienta adresy pocházející z uvnitř virtuální sítě a blokuje přístup pro všechny ostatní klienty.

![Příklad seznamu řízení přístupu sítě][NetworkAccessControlListExample]

Všechny aplikace běžící v App Service Environment ve stejné virtuální síti jako SQL Server bude moci připojit k instanci SQL serveru pomocí **interní virtuální síť** IP adresu pro virtuální počítač systému SQL Server.  

Příklad připojovacího řetězce níže odkazuje na SQL Server pomocí jeho privátní IP adresy.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

I když virtuální počítač obsahuje také veřejný koncový bod, pokusy o připojení pomocí veřejnou IP adresu se odmítne kvůli sítě ACL. 

## <a name="restricting-access-with-a-network-security-group"></a>Omezení přístupu ke skupině zabezpečení sítě
Alternativní způsob pro přístup k zabezpečení je skupina zabezpečení sítě.  Skupiny zabezpečení sítě můžete použít pro jednotlivé virtuální počítače, nebo podsíť obsahující virtuální počítače.

Nejprve je potřeba vytvořit skupinu zabezpečení sítě:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Omezení přístupu k jenom interní provoz VNet je velmi jednoduchý ke skupině zabezpečení sítě.  Výchozí pravidla ve skupině zabezpečení sítě povolí přístup pouze z jiných klientů sítě ve stejné virtuální síti.

V důsledku zablokujete přístup k systému SQL Server je jednoduché, použití skupinu zabezpečení sítě s jeho výchozí pravidla buď na virtuální počítače s SQL serverem, nebo podsíť obsahující virtuální počítače.

Následující ukázka platí skupinu zabezpečení sítě pro podsíť obsahující:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Konečný výsledek je sada pravidel zabezpečení, která blokují externí přístup při povolení přístupu interní virtuální sítě:

![Výchozí pravidla zabezpečení sítě][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Začínáme
Chcete-li začít pracovat s prostředí App Service, přečtěte si téma [Úvod do služby App Service Environment][IntroToAppServiceEnvironment]

Podrobnosti ohledně ovládání příchozí přenosy do služby App Service Environment najdete v tématu [řízení příchozí přenosy do služby App Service Environment][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
