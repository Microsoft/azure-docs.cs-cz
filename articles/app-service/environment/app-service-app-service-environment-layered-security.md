---
title: Vrstvené zabezpečení v1
description: Naučte se implementovat vícevrstvou architekturu zabezpečení v prostředí App Service. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: e9a776aad966bb74e22f6d7fb8d66530e4b4db92
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91255192"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementace vrstvené architektury zabezpečení s App Service prostředími
Vzhledem k tomu, že prostředí App Service poskytují izolované běhové prostředí nasazené ve virtuální síti, můžou vývojáři vytvořit vrstvenou architekturu zabezpečení, která poskytuje různé úrovně síťového přístupu pro každou fyzickou aplikační vrstvu.

Běžným přáním je skrýt back-endy rozhraní API z obecného přístupu k Internetu a jenom v případě, že jsou rozhraní API zavolána pomocí nadřazeného webového aplikace.  [Skupiny zabezpečení sítě (skupin zabezpečení sítě)][NetworkSecurityGroups] se dají použít na podsítích, které obsahují App Service prostředí, aby se omezil veřejný přístup k aplikacím API.

Následující diagram znázorňuje ukázkovou architekturu s aplikací založenou na WebAPI, která je nasazená na App Service Environment.  Tři samostatné instance webových aplikací nasazené ve třech různých App Service prostředích, které mají back-end volání do stejné aplikace WebAPI.

![Koncepční architektura][ConceptualArchitecture] 

Zelené znaménko plus značí, že skupina zabezpečení sítě v podsíti, která obsahuje "apiase", umožňuje příchozí volání z nadřazených webových aplikací, stejně jako volání samotného.  Stejná skupina zabezpečení sítě ale explicitně odepře přístup k obecnému příchozímu provozu z Internetu. 

Zbývající část tohoto článku vás provede kroky potřebnými ke konfiguraci skupiny zabezpečení sítě v podsíti obsahující "apiase".

## <a name="determining-the-network-behavior"></a>Určení chování sítě
Aby bylo možné zjistit, jaká pravidla zabezpečení sítě jsou potřebná, musíte určit, kteří síťoví klienti budou mít povolený přístup k App Service Environment, který obsahuje aplikaci API, a kteří klienti budou zablokováni.

Vzhledem k tomu, že se [skupiny zabezpečení sítě (skupin zabezpečení sítě)][NetworkSecurityGroups] používají pro podsítě a App Service prostředí se nasazují do podsítí, pravidla obsažená v NSG se vztahují na **všechny** aplikace spuštěné v App Service Environment.  Po použití ukázkové architektury tohoto článku se po použití skupiny zabezpečení sítě pro podsíť obsahující "apiase" budou všechny aplikace běžící v App Service Environment apiase chránit stejnou sadou pravidel zabezpečení. 

* **Určete odchozí IP adresu nadřazených volajících:**  Jaké jsou IP adresy nebo adresy nadřazených volajících?  Tyto adresy budou muset explicitně povolený přístup v NSG.  Vzhledem k tomu, že se volání mezi App Service prostředími považují za volání "Internet", odchozí IP adresa přiřazená ke každému ze tří nadřazených App Service prostředí musí mít povolený přístup v NSG pro podsíť "apiase".   Další informace o určení odchozí IP adresy pro aplikace běžící v App Service Environment najdete v článku Přehled [architektury sítě][NetworkArchitecture] .
* **Bude aplikace API back-endu muset volat sám sebe?**  Někdy přebývající a jemný bod je scénář, ve kterém back-end aplikace musí volat sám sebe.  Pokud aplikace back-end API na App Service Environment musí volat sám sebe, je také považována za volání "Internet".  V ukázkové architektuře to vyžaduje i povolení přístupu z odchozí IP adresy App Service Environment apiase.

## <a name="setting-up-the-network-security-group"></a>Nastavení skupiny zabezpečení sítě
Po známé sadě odchozích IP adres je dalším krokem vytvoření skupiny zabezpečení sítě.  Skupiny zabezpečení sítě je možné vytvořit pro virtuální sítě založené na Správce prostředků i v klasických virtuálních sítích.  Níže uvedené příklady ukazují vytváření a konfiguraci NSG v klasické virtuální síti pomocí PowerShellu.

V případě ukázkové architektury se prostředí nacházejí v Střed USA – jih, takže se v této oblasti vytvoří prázdná NSG:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" 
-Label "Only allow web frontend and loopback traffic"
```

Nejdřív se přidá pravidlo explicitního povolení pro infrastrukturu pro správu Azure, jak je uvedeno v článku o [příchozím provozu][InboundTraffic] pro App Service prostředí.

```azurepowershell-interactive
#Open ports for access by Azure management infrastructure
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" 
-Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Dále jsou přidána dvě pravidla, která povolují volání HTTP a HTTPS z prvního nadřazeného App Service Environment ("fe1ase").

```azurepowershell-interactive
#Grant access to requests from the first upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" 
-Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" 
-Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Vypláchněte a zopakujte pro druhý a třetí App Service prostředí pro odesílání dat ("fe2ase" a "fe3ase").

```azurepowershell-interactive
#Grant access to requests from the second upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" 
-Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" 
-Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

#Grant access to requests from the third upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" 
-Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" 
-Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Nakonec udělte přístup k odchozí IP adrese App Service Environment back-endu rozhraní API, aby se mohl volat zpátky sám.

```azurepowershell-interactive
#Allow apps on the apiase environment to call back into itself
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" 
-Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" 
-Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Žádná další pravidla zabezpečení sítě se nevyžadují, protože každá NSG má sadu výchozích pravidel, která blokují příchozí přístup z Internetu, a to ve výchozím nastavení.

Úplný seznam pravidel ve skupině zabezpečení sítě je uvedený níže.  Všimněte si, jak poslední zvýrazněné pravidlo blokuje příchozí přístup ze všech volajících, kromě volajících, kterým byl explicitně udělen přístup.

![Konfigurace NSG][NSGConfiguration] 

Posledním krokem je použití NSG pro podsíť, která obsahuje App Service Environment "apiase".

```azurepowershell-interactive
#Apply the NSG to the backend API subnet
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet 
-VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'
```

U NSG, který se používá pro podsíť, se můžou do prostředí "apiase" volat jenom tři App Serviceová prostředí pro odesílání a App Service Environment obsahující back-end rozhraní API.

## <a name="additional-links-and-information"></a>Další odkazy a informace
Informace o [skupinách zabezpečení sítě](../../virtual-network/security-overview.md).

Principy [odchozích IP adres][NetworkArchitecture] a App Service prostředí.

[Síťové porty][InboundTraffic] používané App Service prostředími.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
