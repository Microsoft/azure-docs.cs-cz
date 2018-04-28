---
title: Podrobnosti konfigurace sítě pro práci s Express Route
description: Podrobnosti konfigurace sítě pro spuštění prostředí App Service ve virtuální sítě připojen k okruhu ExpressRoute.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.openlocfilehash: fcb9fa9004039205fa49f63c50d5907a8029a079
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Podrobnosti o konfiguraci sítě pro službu App Service Environment používající ExpressRoute
## <a name="overview"></a>Přehled
Zákazníci mohou připojit [Azure ExpressRoute] [ ExpressRoute] okruhu jejich infrastruktury virtuální sítě, tak rozšířit jejich místní sítě do Azure.  Služby App Service Environment lze vytvořit v této podsíti [virtuální sítě] [ virtualnetwork] infrastruktury.  Aplikace běžící na App Service Environment pak může navázat zabezpečené připojení k back endové prostředky, které jsou přístupné pouze prostřednictvím připojení ExpressRoute.  

Služby App Service Environment se dají vytvářet v **buď** virtuální síť Azure Resource Manager **nebo** virtuální síť modelu nasazení classic.  Nedávné změny provedené v června 2016, ASEs také teď můžou být nasazené do virtuální sítě, které používají rozsahů veřejných adres nebo RFC1918 adresní prostory (tj. privátní adresy). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Vyžaduje síťové připojení
Existují požadavky na síťové připojení pro prostředí App Service, která nemusí být splněna původně ve virtuální síti připojené k ExpressRoute.  Služby App Service Environment vyžadují ke správnému všechny z následujících:

* Odchozí síťové připojení k Azure Storage koncových bodů po celém světě na oba porty 80 a 443.  To zahrnuje koncové body umístěné ve stejné oblasti jako služba App Service Environment, stejně jako koncové body úložiště, které jsou umístěné v **jiných** oblastech Azure.  Koncové body Azure úložiště vyřešit v rámci z následujících domén DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* a *file.core.windows.net*.  
* Odchozí síťové připojení ke službě Azure soubory na port 445.
* Odchozí síťové připojení k databázi Sql koncové body umístěné ve stejné oblasti jako služba App Service Environment.  Koncové body SQL DB vyřešit pod následující domény: *database.windows.net*.  To vyžaduje přístup k portům 1433, 11000 11999 a 14000 14999 otevření.  Podrobnosti najdete v části [Tento článek na použití portu Sql Database verze 12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Odchozí síťové připojení ke koncovým bodům roviny Azure management (ASM i ARM koncových bodů).  To zahrnuje odchozí připojení pro obě *management.core.windows.net* a *management.azure.com*. 
* Odchozí síťové připojení k *ocsp.msocsp.com*, *mscrl.microsoft.com* a *crl.microsoft.com*.  To je potřeba k podpoře funkce SSL.
* Konfiguraci DNS pro virtuální síť musí být schopen překladu všechny koncové body a domény uvedený v dřívějších bodů.  Pokud tyto koncové body nelze přeložit, se nezdaří pokusy o vytvoření služby App Service Environment a budou označeny stávající prostředí App Service není v pořádku.
* Je nutný pro komunikaci se servery DNS odchozí přístup na port 53.
* Pokud na druhém konci brány VPN existuje vlastního serveru DNS, DNS server musí být dostupný z podsítě obsahující App Service Environment. 
* Odchozí síťovou cestu nelze procházet i podnikové interní proxy, ani lze vynutit tunelovým propojením místně.  Díky tomu změní adresu efektivní NAT odchozího provozu z App Service Environment.  Změna adres NAT odchozího provozu služby App Service Environment způsobí selhání připojení k mnoha koncových bodů uvedené výše.  Výsledkem neúspěšných pokusech o vytvoření služby App Service Environment, stejně jako dříve pořádku prostředí App Service bude označena jako není v pořádku.  
* Příchozí síťový přístup k požadované porty pro prostředí App Service musí být povoleno, jak je popsáno v tomto [článku][requiredports].

Požadavky na DNS můžete splnit zajištěním platný infrastruktura DNS je nakonfigurován a udržuje pro virtuální síť.  Pokud z nějakého důvodu dojde ke změně konfigurace DNS po vytvoření služby App Service Environment, vývojáři vynutit služby App Service Environment načíst novou konfiguraci DNS.  Aktivuje restartu postupného prostředí pomocí ikony "Restartovat" nachází v horní části okna Správa služby App Service Environment ve [portál Azure] [ NewPortal] způsobí, že prostředí tak, aby se vyzvedávat novou konfiguraci DNS.

Nelze splnit požadavky na přístup příchozích síťových konfigurací [skupinu zabezpečení sítě] [ NetworkSecurityGroups] v podsíti App Service Environment umožňující požadovaný přístup, jak je popsáno v tomto [článku][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Povolení odchozího síťového připojení pro služby App Service Environment
Ve výchozím nastavení nově vytvořený okruh ExpressRoute inzeruje výchozí trasu, která umožňuje odchozí připojení k Internetu.  Pomocí této konfigurace bude moci připojit k jiné koncové body Azure App Service Environment.

Obvyklé konfigurace zákazníka je ale definovat vlastní výchozí trasa (0.0.0.0/0), který vynutí odchozí přenosy z Internetu do místo toku místní.  Tento tok provozu vždy dělí prostředí App Service, protože odchozí provoz je blokované místně nebo NAT by nerozpoznatelný sadu adresy, které přestane fungovat v různých koncové body Azure.

Řešení je definovat jeden (nebo více) trasy definované uživatelem (udr) v podsíti, který obsahuje App Service Environment.  UDR definuje tras konkrétní podsítě, které bude dodržení místo výchozí trasu.

Pokud je to možné doporučujeme použít následující konfigurace:

* Konfigurace ExpressRoute inzeruje 0.0.0.0/0 a ve výchozím nastavení vynucené tunelových propojení všechny odchozí přenosy na místě.
* UDR použije na podsíť obsahující App Service Environment definuje 0.0.0.0/0 s další typ směrování Internet (příkladem je dále dolů v tomto článku).

Celkové požadavky tyto kroky je, že úrovni podsítě UDR bude mít přednost přes ExpressRoute vynucené tunelování, čímž zajišťuje odchozí přístup k Internetu z App Service Environment.

> [!IMPORTANT]
> Trasy definované v UDR **musí** být dost konkrétní, aby mají přednost před všechny trasy inzerované konfigurace ExpressRoute.  Následující příklad používá rozsah adres široký 0.0.0.0/0 a jako takový se dá potenciálně omylem přepsat inzerování tras pomocí podrobnější rozsahy adres.
> 
> Prostředí App Service nejsou podporovány s konfigurací ExpressRoute, **mezi Inzerovat trasy z cesty veřejného partnerského vztahu k cestou soukromého partnerského vztahu**.  Konfigurace ExpressRoute, které mají veřejné partnerské vztahy nakonfigurované, obdrží inzerování trasy od společnosti Microsoft pro velké sady rozsahů adres Microsoft Azure IP.  Pokud tyto rozsahy adres ohlášené mezi na cestou soukromého partnerského vztahu, konečným výsledkem je, že všechny odchozí síťových paketů z podsítě App Service Environment bude tunelovým propojením force zákazníka místní síťové infrastruktuře.  Tento tok sítě se aktuálně nepodporuje s prostředí App Service.  Jedno řešení tohoto problému je zastavit směrování mezi – reklamu z cesty veřejného partnerského vztahu cestou soukromého partnerského vztahu.
> 
> 

Základní informace o trasy definované uživatelem je k dispozici v tomto [přehled][UDROverview].  

Informace o vytváření a konfigurace trasy definované uživatelem je k dispozici v tomto [jak pro průvodce][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Ukázková konfigurace UDR služby App Service Environment
**Předpoklady**

1. Nainstalovat Azure Powershell z [Azure stáhne stránky][AzureDownloads] (s datem červen 2015 nebo novější).  V části "Nástroje příkazového řádku" existuje propojení "Instalace" v části "Windows Powershell", který bude instalovat nejnovější rutiny prostředí Powershell.
2. Doporučuje se, že jedinečnou podsíť je vytvořený pro výhradní použití služby App Service Environment.  To zaručuje, že udr použije na podsíť pouze otevřít odchozí přenosy App Service Environment.
3. **Důležité**: nenasazujte App Service Environment, dokud **po** dodržíte následující kroky konfigurace.  To zajišťuje, že odchozí síťové připojení k dispozici před pokusem o nasazení služby App Service Environment.

**Krok 1: Vytvoření tabulky pojmenovanou trasu**

Následující fragment kódu vytvoří směrovací tabulku v oblasti Azure USA – Západ jako "DirectInternetRouteTable":

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Krok 2: Vytvoření jednu nebo víc tras ve směrovací tabulce**

Musíte přidat jeden nebo více trasy do směrovací tabulky. Chcete-li povolit odchozí přístup k Internetu.  

Doporučený postup pro konfiguraci odchozí přístup k Internetu je definovat trasu pro 0.0.0.0/0, jak je uvedeno níže.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Mějte na paměti, že 0.0.0.0/0 je adresa široký rozsah a jako takový bude přepsáno podrobnější rozsahy adres inzerovaných ExpressRoute.  Znovu iteraci starší doporučení, UDR s trasou 0.0.0.0/0 by měl používat ve spojení s konfigurací ExressRoute, pouze inzeruje 0.0.0.0/0 také. 

Jako alternativu můžete si stáhnout seznam rozsahy CIDR používá Azure komplexní a aktualizované.  Soubor Xml obsahující všechny rozsahy Azure IP adres je k dispozici z [Microsoft Download Center][DownloadCenterAddressRanges].  

Všimněte si ale, že tyto rozsahy časem změnit, proto očekávání pravidelné ruční aktualizace trasy definované uživatelem pro synchronizaci.  Navíc vzhledem k tomu, že je výchozí maximální limit je 100 trasy v jednom UDR, budete muset "shrnout" rozsahů adres Azure IP nevejde se do limit 100 trasy, pamatujte, že UDR definované trasy musejí být konkrétnější než trasy inzerované podle vaší ExpressRoute.  

**Krok 3: Přidružení tabulku směrování pro podsíť obsahující App Service Environment**

Poslední krok konfigurace je přidružit k podsíti směrovací tabulka, kde bude nasazen App Service Environment.  Následující příkaz přidruží "DirectInternetRouteTable" na "ASESubnet", bude obsahovat nakonec služby App Service Environment.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Krok 4: Závěrečné kroky**

Jakmile směrovací tabulka je vázána k podsíti, doporučujeme nejprve testování a potvrďte zamýšlený účinek.  Například nasazení virtuálního počítače do podsítě a ověřte, že:

* Odchozí přenosy na Azure a koncové body mimo Azure uvedených výše v otázce Tento článek je **není** toku okruhu ExpressRoute.  Je velmi důležité ověřit toto chování vzhledem k tomu, že pokud odchozí provoz z podsítě je stále vynucené tunelovým propojením na místních počítačích App Service Environment vytvoření vždycky selže. 
* Vyhledávání DNS pro koncové body již bylo zmíněno dříve všechny řešíme správně. 

Jakmile se potvrzují výše uvedené kroky, musíte odstranit virtuální počítač, protože podsíť musí být v době, kdy se vytvoří služba App Service Environment "prázdný".

Potom pokračujte ve vytváření služby App Service Environment!

## <a name="getting-started"></a>Začínáme
Chcete-li začít pracovat s prostředí App Service, přečtěte si téma [Úvod do služby App Service Environment][IntroToAppServiceEnvironment]

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[AzureDownloads]: http://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
