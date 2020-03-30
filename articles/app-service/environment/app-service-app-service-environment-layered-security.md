---
title: Zabezpečení vrstvené v1
description: Zjistěte, jak implementovat architekturu zabezpečení vrstvené ve vašem prostředí služby App Service. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: a8920e97d315dc7bfd0ba22386b8b637afb7c05e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688801"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementace architektury zabezpečení vrstvené s prostředími služby App Service
Vzhledem k tomu, že prostředí služby App Service poskytují izolované prostředí runtime nasazené do virtuální sítě, vývojáři mohou vytvořit architekturu zabezpečení s vrstvami, která poskytuje různé úrovně přístupu k síti pro každou fyzickou aplikační vrstvu.

Běžnou touhou je skrýt back-endy rozhraní API před obecným přístupem k Internetu a povolit volání rozhraní API pouze prostřednictvím webových aplikací pro upstream.  [Skupiny zabezpečení sítě (NSGs)][NetworkSecurityGroups] lze použít v podsítích obsahujících prostředí služby App Service k omezení veřejného přístupu k aplikacím rozhraní API.

Následující diagram znázorňuje ukázkovou architekturu s aplikací založenou na rozhraní WebAPI nasazenou v prostředí služby App Service.  Tři samostatné instance webových aplikací, nasazené ve třech samostatných prostředích služby App Service, uskutečují back-endová volání do stejné aplikace WebAPI.

![Koncepční architektura][ConceptualArchitecture] 

Zelené znaménko plus označují, že skupina zabezpečení sítě v podsíti obsahující "apiázu" umožňuje příchozí volání z upstream webových aplikací, stejně jako volání ze sebe sama.  Stejná skupina zabezpečení sítě však výslovně odepře přístup k obecnému příchozímu provozu z Internetu. 

Zbývající část tohoto článku prochází kroky potřebné ke konfiguraci skupiny zabezpečení sítě v podsíti obsahující "apiázy".

## <a name="determining-the-network-behavior"></a>Určení chování sítě
Chcete-li zjistit, jaká pravidla zabezpečení sítě jsou potřeba, musíte určit, kteří síťoví klienti budou moci dosáhnout prostředí služby App Service obsahujícího aplikaci rozhraní API a kteří klienti budou blokováni.

Vzhledem k tomu, [že skupiny zabezpečení sítě (NSG)][NetworkSecurityGroups] se používají pro podsítě a prostředí služby App Service se nasazují do podsítí, pravidla obsažená v souboru zabezpečení sítě se vztahují na **všechny** aplikace spuštěné v prostředí služby App Service.  Pomocí ukázkové architektury pro tento článek, jakmile je skupina zabezpečení sítě použita pro podsíť obsahující "apiázu", budou všechny aplikace spuštěné v prostředí služby App Service "apiase" chráněny stejnou sadou pravidel zabezpečení. 

* **Určete odchozí IP adresu volajících upstream:**  Jaká je IP adresa nebo adresy volajících upstream?  Tyto adresy budou muset být explicitně povolen přístup v souboru nsg.  Vzhledem k tomu, že volání mezi prostředími služby App Service jsou považována za "internetová" volání, odchozí IP adresa přiřazená každému ze tří prostředí služby App Service musí být povolen přístup v podsíti "apiázy".   Další informace o určení odchozí IP adresy pro aplikace spuštěné v prostředí služby App Service najdete v článku Přehled [síťové architektury.][NetworkArchitecture]
* **Bude aplikace back-end API muset volat sama sebe?**  Někdy přehlížena a subtilní bod je scénář, kde back-end aplikace potřebuje volat sám.  Pokud aplikace rozhraní API back-end v prostředí služby App Service potřebuje volat sám sebe, je také považována za volání "Internet".  V ukázkové architektuře to vyžaduje povolení přístupu z odchozí IP adresy prostředí služby App Service apiase.

## <a name="setting-up-the-network-security-group"></a>Nastavení skupiny zabezpečení sítě
Jakmile je známa sada odchozích adres IP, je dalším krokem vytvoření skupiny zabezpečení sítě.  Skupiny zabezpečení sítě lze vytvořit jak pro virtuální sítě založené na Resource Manageru, tak pro klasické virtuální sítě.  Níže uvedené příklady ukazují vytvoření a konfiguraci síťového připojení k síti v klasické virtuální síti pomocí prostředí Powershell.

Pro ukázkovou architekturu jsou prostředí umístěna v usa – –střed usa, takže v této oblasti je vytvořen prázdný soubor nSG:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Nejprve se přidá explicitní pravidlo povolení pro infrastrukturu správy Azure, jak je uvedeno v článku o [příchozím provozu][InboundTraffic] pro prostředí služby App Service.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Dále jsou přidána dvě pravidla, která umožňují volání HTTP a HTTPS z prvního prostředí služby upstream App Service ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Opláchněte a opakujte pro druhé a třetí upstream App Service prostředí ("fe2ase" a "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Nakonec udělte přístup k odchozí IP adrese prostředí služby App Service rozhraní API back-endu, aby bylo možné volat zpět do sebe.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Nejsou vyžadována žádná další pravidla zabezpečení sítě, protože každý soubor zabezpečení sítě má ve výchozím nastavení sadu výchozích pravidel, která blokují příchozí přístup z Internetu.

Úplný seznam pravidel ve skupině zabezpečení sítě je uveden níže.  Všimněte si, jak poslední pravidlo, které je zvýrazněno, blokuje příchozí přístup od všech volajících, než volající, kterým byl explicitně udělen přístup.

![Konfigurace nsg][NSGConfiguration] 

Posledním krokem je použití skupiny sítě sítě pro podsíť, která obsahuje prostředí služby App Service "apiase".

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

S nsg použít pro podsíť, pouze tři upstream App Service prostředí a app service prostředí obsahující back-end rozhraní API, mohou volat do prostředí "apiázy".

## <a name="additional-links-and-information"></a>Další odkazy a informace
Informace o [skupinách zabezpečení sítě](../../virtual-network/security-overview.md).

Principy [odchozích IP adres][NetworkArchitecture] a prostředí služby App Service.

[Síťové porty][InboundTraffic] používané prostředími služby App Service.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
