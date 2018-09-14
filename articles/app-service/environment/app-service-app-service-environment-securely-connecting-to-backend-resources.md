---
title: Bezpečné připojení k back-Endovým prostředkům ze služby App Service Environment
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
ms.openlocfilehash: 1732e6778febac60a25da74c330cb3d3da94154d
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580038"
---
# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Bezpečné připojení k back-Endovým prostředkům ze služby App Service Environment
## <a name="overview"></a>Přehled
Protože služba App Service Environment se vždy vytvářejí ve **buď** virtuální sítí Azure Resource Manageru **nebo** modelu nasazení classic [virtuální sítě] [ virtualnetwork], odchozí připojení ze služby App Service Environment do dalších back-endovým prostředkům může probíhat výhradně prostřednictvím virtuální sítě.  Nedávné změny provedené v červnu 2016 je možné také nasadit služby ase do virtuální sítě, které používají rozsahy adres veřejné nebo definice RFC1918 adresní prostory (tj. privátní adresy).  

Například může být SQL Server běžící v clusteru virtuálních počítačů s port 1433 uzamčen.  Koncový bod může být ACLd k povolení přístupu jenom z dalších prostředků ve stejné virtuální síti.  

Další příklad – citlivých koncových bodů může spustit v místním a být připojení k Azure prostřednictvím [Site-to-Site] [ SiteToSite] nebo [Azure ExpressRoute] [ ExpressRoute] připojení.  Jen k prostředkům ve virtuální sítě připojené k Site-to-Site nebo ExpressRoute tunely v důsledku toho budou mít přístup k místní koncové body.

U všech těchto scénářích aplikace běžící na App Service Environment bude možné pro zabezpečené připojení k různým servery a prostředky.  Odchozí provoz z aplikací běžících ve službě App Service Environment do privátní koncových bodů ve stejné virtuální síti (nebo připojeny ke stejné virtuální síti), bude pouze flow přes virtuální síť.  Odchozí provoz do koncových bodů privátní nebude flow přes veřejný Internet.

Jeden výstrahou platí pro odchozí provoz ze služby App Service Environment do koncových bodů v rámci virtuální sítě.  App Service Environment nemůže kontaktovat koncové body virtuálních počítačů umístěných v **stejné** podsítě jako služba App Service Environment.  To obvykle by neměl být problém tak dlouho, dokud App Service Environment se nasazuje do podsítě vyhrazené pro výhradní použití App Service Environment.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Odchozí připojení a požadavky na DNS
Pro službu App Service Environment fungovala správně vyžaduje odchozí přístup do různých koncových bodů. Úplný seznam externích koncových bodů použitých ve službě ASE je v části "Vyžaduje připojení k síti" [konfiguraci sítě pro ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) článku.

Služby App Service Environment vyžaduje platnou konfiguraci pro virtuální síť infrastruktury služby DNS.  Pokud z nějakého důvodu dojde ke změně konfigurace DNS po vytvoření služby App Service Environment, vývojáři můžete vynutit službu App Service Environment, aby se získaly novou konfiguraci DNS.  Aktivuje se zajištěním provozu restartování prostředí pomocí ikony "Restartovat" umístěný v horní části okna správy služby App Service Environment na portálu způsobí, že prostředí tak, aby získaly novou konfiguraci DNS.

Doporučuje se také, že všechny vlastní servery DNS ve virtuální síti se instalační program předem před vytvořením služby App Service Environment.  Pokud při vytváření služby App Service Environment se změnila konfigurace DNS virtuální sítě, které způsobí selhání procesu vytvoření služby App Service Environment.  V podobných souvislosti Pokud vlastní server DNS existuje na druhém konci bránu sítě VPN a DNS server je nedostupná nebo není k dispozici, proces vytváření služby App Service Environment se také nezdaří.

## <a name="connecting-to-a-sql-server"></a>Připojení k SQL serveru
Běžné konfigurace systému SQL Server má naslouchat na portu 1433 koncový bod:

![Koncový bod SQL serveru][SqlServerEndpoint]

Existují dvě metody pro omezujete provoz směřující do tohoto koncového bodu:

* [Sítě seznamy řízení přístupu][NetworkAccessControlLists] (sítě ACL)
* [Skupiny zabezpečení sítě][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Omezení přístupu k síti seznamu ACL
Port 1433 je možné zabezpečit pomocí seznamu řízení přístupu síť.  V příkladu níže klienta seznamů povolených adres pocházející z ve virtuální síti a blokuje přístup na všechny ostatní klienty.

![Příklad seznamu řízení přístupu sítě][NetworkAccessControlListExample]

Všechny aplikace spuštěné ve službě App Service Environment ve stejné virtuální síti jako SQL Server bude moct připojit k instanci systému SQL Server pomocí **interní virtuální síti** IP adresu pro virtuální počítač s SQL serverem.  

Příklad připojovacího řetězce následující odkazuje na SQL serveru pomocí jeho privátní IP adresu.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

I když má virtuální počítač také veřejný koncový bod, pokusy o připojení pomocí veřejné IP adresy odmítne kvůli sítě ACL. 

## <a name="restricting-access-with-a-network-security-group"></a>Omezení přístupu se skupinou zabezpečení sítě
Alternativním přístupem k zabezpečení přístupu se skupina zabezpečení sítě.  Skupiny zabezpečení sítě můžete použít k jednotlivým virtuální počítačům nebo podsíť obsahující virtuální počítače.

Skupina zabezpečení sítě je nejprve potřeba vytvořit:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Omezení přístupu k jen vnitřní provoz virtuální sítě je velmi jednoduchý se skupinou zabezpečení sítě.  Výchozí pravidla skupiny zabezpečení sítě povolení přístupu jenom z jiných klientů sítě ve stejné virtuální síti.

V důsledku zablokujete přístup k systému SQL Server je jednoduché, stačí použít skupinu zabezpečení sítě s jeho výchozí pravidla buď na virtuální počítače s SQL serverem nebo podsíť obsahující virtuální počítače.

Následující ukázka platí skupinu zabezpečení sítě pro podsíť obsahující:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Konečný výsledek je sada pravidel zabezpečení, která blokovat externí přístup zároveň vám umožní přístup k interní virtuální síť:

![Výchozí pravidla zabezpečení sítě][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Začínáme
Začínáme s App Service Environment najdete v článku [Úvod do služby App Service Environment][IntroToAppServiceEnvironment]

Podrobnosti o řízení příchozího provozu do služby App Service Environment, naleznete v tématu [řízení příchozího provozu do služby App Service Environment][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
