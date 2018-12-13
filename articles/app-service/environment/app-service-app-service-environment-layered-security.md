---
title: Vrstvy architektury zabezpečení pomocí služby App Service Environment – Azure
description: Implementace vrstvené architektury zabezpečení pomocí služby App Service Environment.
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
ms.custom: seodec18
ms.openlocfilehash: 5e25de1ad2042ac978c3698165b9d9baba20e816
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274153"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementace vrstvené architektury zabezpečení pomocí služby App Service Environment
## <a name="overview"></a>Přehled
Protože App Service Environment poskytuje izolovaném prostředí nasazený do virtuální sítě, umožňuje vývojářům vytvářet vrstvené architektury zabezpečení poskytují různé úrovně přístupu k síti pro jednotlivé vrstvy fyzické aplikace.

Běžné tím pádem si je skrýt rozhraní API back EndY z obecné přístup k Internetu a povolit pouze rozhraní API, které jsou volány aplikací nadřazený web apps.  [Skupiny zabezpečení sítě (Nsg)] [ NetworkSecurityGroups] je možné omezit veřejný přístup k aplikacím API na podsítěmi, které obsahují služby App Service Environment.

Následující diagram znázorňuje příklad architekturu s využitím aplikace na základě WebAPI nasazené ve službě App Service Environment.  Tři samostatné webové aplikaci, nasazené instance na tři samostatné služby App Service Environment, volání back-end pro stejnou aplikaci WebAPI.

![Konceptuální architektura][ConceptualArchitecture] 

Zeleného znaménka plus znamenat, že skupina zabezpečení sítě na podsíť obsahující "apiase" umožňuje příchozí volání z nadřazeného webových aplikací jako zásobník volání od sebe sama.  Ale stejnou skupinu zabezpečení sítě výslovně odepře přístup k obecné příchozí provoz z Internetu. 

Zbývající část tohoto článku vás provede kroky potřebné ke konfiguraci skupiny zabezpečení sítě na podsíť obsahující "apiase."

## <a name="determining-the-network-behavior"></a>Sledování chování sítě
Pokud chcete zjistit, jaká pravidla zabezpečení sítě jsou potřeba, budete muset určit, kteří klienti sítě se bude moct dosáhnout obsahující aplikaci rozhraní API služby App Service Environment a které klienti budou Blokovaní.

Protože [skupiny zabezpečení sítě (Nsg)] [ NetworkSecurityGroups] se použijí k podsítím a App Service Environment se nasazuje do podsítě, pravidla, které jsou součástí skupiny NSG se vztahují na **všechny** aplikace spuštění ve službě App Service Environment.  Pomocí skupiny zabezpečení sítě je použitá na podsíť obsahující "apiase" ukázkové architektury pro účely tohoto článku, budou chráněné všechny aplikace běžící na App Service Environment "apiase" stejná sada pravidel zabezpečení. 

* **Zjistit odchozí IP adresa nadřazeného volající:**  Co je IP adresa nebo adresy nadřazeného volající?  Tyto adresy se musí explicitně povolit přístup v této skupině.  Protože volání mezi App Service Environment jsou považovány za volání "Internet", odchozí IP adresa přiřadí každé tři nadřazeného prostředí App Service musí mít přístup povolený v NSG pro podsíť "apiase".   Další informace o určení odchozí IP adresa pro aplikace běžící ve službě App Service Environment najdete v tématu [Síťová architektura] [ NetworkArchitecture] přehledovém článku.
* **Bude potřeba back endové aplikace API volat sám sebe?**  Někdy přehlíženým a drobným bod je scénář, kde je potřeba volat sám sebe back endové aplikace.  Pokud je back endové aplikace API v App Service Environment je potřeba volat sám sebe, je také zpracováván jako volání rozhraní "Internet".  V ukázkové architektury vyžaduje to povolení přístupu z odchozí IP adresy "apiase" App Service Environment také.

## <a name="setting-up-the-network-security-group"></a>Nastavení skupiny zabezpečení sítě
Jakmile sadu odchozí IP adresy jsou známé, dalším krokem je vytvoření skupiny zabezpečení sítě.  Pro obě Resource Manager podle virtuálních sítí, jakož i klasické virtuální sítě je možné vytvořit skupiny zabezpečení sítě.  Následující příklady ukazují vytváření a konfiguraci skupiny NSG na klasické virtuální sítě pomocí prostředí Powershell.

Pro ukázkové architektury prostředí se nacházejí v střed USA – Jih, takže prázdná skupina zabezpečení sítě se vytvoří v dané oblasti:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Nejdřív explicitně povolit je přidáno pravidlo pro Azure správu infrastruktury, jak je uvedeno v následujícím článku na [příchozí provoz] [ InboundTraffic] pro App Service Environment.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Dále jsou přidány dvě pravidla povolit volání HTTP a HTTPS z prvního nadřazeného služby App Service Environment ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Zasažení vodou a opakovat druhý a třetí nadřazeného App Service Environment ("fe2ase" a "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

A konečně udělte přístup k odchozí IP adresa back endového rozhraní API služby App Service Environment tak, že může volat zpět do sebe samé.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Žádná další pravidla zabezpečení sítě jsou povinné, protože každé skupiny zabezpečení sítě obsahuje sadu výchozích pravidel, která blokují příchozí přístup z Internetu, ve výchozím nastavení.

Níže se zobrazují na seznam všech pravidel ve skupině zabezpečení sítě.  Všimněte si, jak poslední pravidlo, které je zvýrazněn, blokuje příchozí přístup ze všech volajících než volajícím, které výslovně udělil přístup.

![Konfigurace skupiny zabezpečení sítě][NSGConfiguration] 

Posledním krokem je použití NSG pro podsíť, která obsahuje "apiase" App Service Environment.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

S skupina NSG použitá na podsíť jsou povoleny pouze tři nadřazeného App Service Environment a App Service Environment, který obsahuje rozhraní API back endu, provést volání do prostředí "apiase".

## <a name="additional-links-and-information"></a>Další odkazy a informace
Informace o [skupiny zabezpečení sítě](../../virtual-network/security-overview.md).

Principy [odchozí IP adresy] [ NetworkArchitecture] a App Service Environment.

[Síťové porty] [ InboundTraffic] používá služba App Service Environment.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
