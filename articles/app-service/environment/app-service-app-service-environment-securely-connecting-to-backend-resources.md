---
title: Připojení k back-endu v1
description: Přečtěte si, jak se bezpečně připojit k back-endu prostředků z prostředí služby App Service. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 03f773e286697a12188f238cf2f422a18a20054f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687299"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Bezpečné připojení k back-endprostředkům z prostředí služby App Service
Vzhledem k tomu, že prostředí služby App Service se vždy vytváří **ve** virtuální síti Azure Resource Manager **nebo** ve [virtuální síti][virtualnetwork]klasického modelu nasazení , odchozí připojení z prostředí služby App Service do jiných back-endových prostředků můžou proudit výhradně přes virtuální síť.  S nedávnou změnou provedenou v červnu 2016 lze ases také nasadit do virtuálních sítí, které používají rozsahy veřejných adres nebo adresní prostory RFC1918 (tj. soukromé adresy).  

Například může být SQL Server spuštěn v clusteru virtuálních počítačů s portem 1433 uzamčen.  Koncový bod může být ACLd povolit pouze přístup z jiných prostředků ve stejné virtuální síti.  

Jako další příklad citlivé koncové body může běžet místně a být připojen k Azure prostřednictvím [site-to-site][SiteToSite] nebo [Azure ExpressRoute][ExpressRoute] připojení.  V důsledku toho budou mít přístup k místním koncovým bodům pouze prostředky ve virtuálních sítích připojených k tunelům Site-to-Site nebo ExpressRoute.

Pro všechny tyto scénáře aplikace spuštěné v prostředí služby App Service se budou moci bezpečně připojit k různým serverům a prostředkům.  Odchozí provoz z aplikací spuštěné v prostředí služby App Service do soukromých koncových bodů ve stejné virtuální síti (nebo připojené ke stejné virtuální síti) bude proudit jenom přes virtuální síť.  Odchozí provoz do soukromých koncových bodů nebude tok přes veřejný Internet.

Jedna námitka se vztahuje na odchozí provoz z prostředí služby App Service na koncové body v rámci virtuální sítě.  Prostředí služby App Service nemůže dosáhnout koncových bodů virtuálních počítačů umístěných **ve stejné** podsíti jako prostředí služby App Service.  To by obvykle neměl být problém, pokud prostředí služby App Service jsou nasazeny do podsítě vyhrazené pro výhradní použití pouze prostředí služby App Service.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Odchozí připojení a požadavky na DNS
Aby prostředí služby App Service fungovalo správně, vyžaduje odchozí přístup k různým koncovým bodům. Úplný seznam externích koncových bodů používaných službou ASE je v části Požadované připojení k síti v článku [Konfigurace sítě pro expressroute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

Prostředí služby App Service vyžadují platnou infrastrukturu DNS nakonfigurovanou pro virtuální síť.  Pokud se z nějakého důvodu po vytvoření prostředí služby App Service změní konfigurace DNS, vývojáři mohou vynutit, aby prostředí služby App Service vyzvedlo novou konfiguraci DNS.  Spuštění postupného restartování prostředí pomocí ikony "Restartovat" umístěné v horní části okna správy prostředí služby App Service na portálu způsobí, že prostředí vyzvedne novou konfiguraci DNS.

Doporučuje se také, aby všechny vlastní servery DNS ve virtuální síti byly nastaveny předem před vytvořením prostředí služby App Service.  Pokud se při vytváření prostředí služby App Service změní konfigurace DNS virtuální sítě, bude to mít za následek selhání procesu vytváření prostředí služby App Service.  V podobném hlediska, pokud vlastní server DNS existuje na druhém konci brány VPN a server DNS je nedostupný nebo není k dispozici, proces vytváření prostředí služby App Service se také nezdaří.

## <a name="connecting-to-a-sql-server"></a>Připojení k serveru SQL Server
Běžná konfigurace serveru SQL Server má koncový bod naslouchání na portu 1433:

![Koncový bod serveru SQL Server][SqlServerEndpoint]

Existují dva přístupy pro omezení provozu na tento koncový bod:

* [Seznamy řízení přístupu k síti][NetworkAccessControlLists] (seznamy ACL sítě)
* [Skupiny zabezpečení sítě][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Omezení přístupu pomocí síťového přístupu
Port 1433 lze zabezpečit pomocí seznamu řízení přístupu k síti.  Níže uvedený příklad whitelists klientské adresy pocházející z uvnitř virtuální sítě a blokuje přístup ke všem ostatním klientům.

![Příklad seznamu řízení přístupu k síti][NetworkAccessControlListExample]

Všechny aplikace spuštěné v prostředí App Service Ve stejné virtuální síti jako SQL Server se budou moct připojit k instanci SERVERU SQL Server pomocí interní IP adresy **virtuální sítě** pro virtuální počítač SQL Server.  

Příklad připojovacího řetězce níže odkazuje na SQL Server pomocí své privátní IP adresy.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Přestože virtuální počítač má také veřejný koncový bod, pokusy o připojení pomocí veřejné IP adresy budou odmítnuty z důvodu síťového přístupového bodu. 

## <a name="restricting-access-with-a-network-security-group"></a>Omezení přístupu skupinou zabezpečení sítě
Alternativní přístup pro zabezpečení přístupu je se skupinou zabezpečení sítě.  Skupiny zabezpečení sítě lze použít pro jednotlivé virtuální počítače nebo pro podsíť obsahující virtuální počítače.

Nejprve je třeba vytvořit skupinu zabezpečení sítě:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Omezení přístupu pouze na interní provoz virtuální sítě je velmi jednoduché se skupinou zabezpečení sítě.  Výchozí pravidla ve skupině zabezpečení sítě umožňují přístup pouze od jiných síťových klientů ve stejné virtuální síti.

V důsledku uzamčení přístupu k SERVERU SQL Server je stejně jednoduché jako použití skupiny zabezpečení sítě s výchozími pravidly pro virtuální počítače se systémem SQL Server nebo podsíť obsahující virtuální počítače.

Následující ukázka aplikuje skupinu zabezpečení sítě na obsahující podsíť:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Konečným výsledkem je sada pravidel zabezpečení, která blokují externí přístup a současně umožňují interní přístup virtuální sítě:

![Výchozí pravidla zabezpečení sítě][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Začínáme
Pokud chcete začít s prostředím služby App Service, [přečtěte si informace o úvodu do prostředí služby App Service][IntroToAppServiceEnvironment]

Podrobnosti o řízení příchozího provozu do prostředí služby App Service najdete [v tématu Řízení příchozího provozu do prostředí služby App Service][ControlInboundASE]

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
