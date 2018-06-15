---
title: Architektura vrstveného zabezpečení pomocí služby App Service Environment
description: Implementace architektura vrstveného zabezpečení s prostředí App Service.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.openlocfilehash: 29c928c7d81eb3a2532f735be9132b49db5da373
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356201"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementace architektura vrstveného zabezpečení pomocí služby App Service Environment
## <a name="overview"></a>Přehled
Vzhledem k tomu, že prostředí App Service poskytuje izolovaném prostředí nasadit do virtuální sítě, vývojáři můžou vytvářet architektura vrstveného zabezpečení poskytuje různé úrovně přístupu k síti pro každý fyzický aplikační vrstvě.

Společné přání je skrýt rozhraní API back EndY z obecné přístup k Internetu a Povolit jenom rozhraní API, která se má volat nadřazeného webové aplikace.  [Skupin zabezpečení (Nsg) sítě] [ NetworkSecurityGroups] lze použít na podsítě, který obsahuje prostředí App Service omezit veřejný přístup k aplikacím API.

Následující diagram ukazuje příklad architekturu a WebAPI na základě aplikaci nasadit na služby App Service Environment.  Tři instance aplikace samostatný webový nasazené na tři samostatné prostředí App Service, volat back-end ke stejné aplikaci WebAPI.

![Konceptuální architektura][ConceptualArchitecture] 

Zeleného znaménka plus indikovat, skupiny zabezpečení sítě na podsíť obsahující "apiase" umožňuje příchozí volání z nadřazeného webové aplikace, jako dobře volání ze sebe samotné.  Ale stejnou skupinu zabezpečení sítě výslovně odepře přístup k obecné příchozí provoz z Internetu. 

Zbývající část tohoto článku vás provede kroky potřebné ke konfiguraci skupinu zabezpečení sítě na podsíť obsahující "apiase."

## <a name="determining-the-network-behavior"></a>Určení chování sítě
Chcete-li vědět, jaká pravidla zabezpečení sítě jsou potřeba, budete muset určit, kteří klienti sítě se bude moct připojit obsahující aplikace API App Service Environment a které klienti budou Blokovaní.

Vzhledem k tomu [skupin zabezpečení (Nsg) sítě] [ NetworkSecurityGroups] se použijí k podsítím a prostředí App Service jsou nasazené do podsítí, platí pravidla obsažené v skupinu NSG k **všechny** aplikace běžící na služby App Service Environment.  Použití ukázkové architektury pro tohoto článku, jakmile skupinu zabezpečení sítě se použije na podsíť obsahující "apiase", budou všechny aplikace běžící na App Service Environment "apiase" chráněné stejná sada pravidel zabezpečení. 

* **Odchozí IP adresu nadřazeného volajícím určit:** co je IP adresa nebo adresy nadřazeného volající?  Tyto adresy bude muset být explicitně povolené přístup v této skupině.  Vzhledem k tomu, že volání mezi prostředí App Service jsou považovány za "Internet" volání, IP adresu odchozí přiřazeni k jednotlivým tři nadřazeného prostředí App Service musí mít přístup povolený skupina NSG pro podsíť "apiase".   Další informace o určení odchozí IP adresu pro aplikace běžící ve službě App Service Environment, najdete v článku [síťovou architekturu] [ NetworkArchitecture] článek s přehledem.
* **Bude potřeba back-end aplikace API volat sám sebe?**  Někdy přehlíženým a jemně bod je tento scénář, kde back-end aplikace musí volat sám sebe.  Pokud aplikace API back-end na služby App Service Environment musí volat sám sebe, je také považovat za volání "Internet".  Ukázková architektura to vyžaduje povolení přístupu z odchozí adresy IP "apiase" App Service Environment také.

## <a name="setting-up-the-network-security-group"></a>Nastavení skupinu zabezpečení sítě
Jakmile jsou známé sadu odchozí IP adres, dalším krokem je vytvořit skupinu zabezpečení sítě.  Skupiny zabezpečení sítě lze vytvořit pro virtuální sítě, jakož i klasické virtuální sítě na základě i Resource Manager.  Následující příklady ukazují, vytvoření a konfiguraci skupiny NSG na klasické virtuální sítě pomocí prostředí Powershell.

Pro ukázkové architektury prostředí se nacházíte, Jižní střední USA, aby prázdné skupiny NSG se vytvoří v této oblasti:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Nejprve explicitního Povolit pravidlo je přidáno pro správu Azure infrastrukturu, jak je uvedeno v následujícím článku na [příchozí provoz] [ InboundTraffic] pro prostředí App Service.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

V dalším kroku dvě pravidla jsou přidány do povolte volání protokolu HTTP a HTTPS z první nadřazeného App Service Environment ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Vypláchněte a opakujte pro druhý a třetí nadřazeného prostředí App Service ("fe2ase" a "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Nakonec udělte přístup k odchozí IP adresu rozhraní API back-end App Service Environment, aby můžete volat zpět do sebe sama.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Žádná další pravidla zabezpečení sítě jsou povinné, protože obsahuje sadu výchozích pravidel, která blokují příchozí přístup z Internetu, ve výchozím nastavení každých NSG.

Níže jsou uvedeny úplný seznam pravidel ve skupině zabezpečení sítě.  Všimněte si, jak poslední pravidlo, které zvýrazní, blokuje příchozí přístup ze všech volající, než volajícím, kteří mají výslovně udělil přístup.

![Konfigurace skupiny NSG][NSGConfiguration] 

Posledním krokem je použít NSG k podsíti, který obsahuje "apiase" App Service Environment.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

S NSG použije na podsíť jsou povoleny pouze tři nadřazeného prostředí App Service a služba App Service Environment obsahující rozhraní API back-end, provést volání do prostředí "apiase".

## <a name="additional-links-and-information"></a>Další odkazy a informace
Informace o [skupin zabezpečení sítě](../../virtual-network/security-overview.md).

Principy [odchozí IP adresy] [ NetworkArchitecture] a prostředí App Service.

[Síťové porty] [ InboundTraffic] používá prostředí App Service.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
