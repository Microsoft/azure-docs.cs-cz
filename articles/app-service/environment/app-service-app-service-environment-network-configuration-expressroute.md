---
title: Podrobnosti o konfiguraci sítě pro práci se službou Express Route
description: Podrobnosti o konfiguraci sítě pro spouštění služby App Service Environment ve virtuálních sítích připojené k okruhu ExpressRoute.
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
ms.openlocfilehash: 6c9dcf5812d1d8efe7adbadc3647085664093bb1
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969188"
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Podrobnosti o konfiguraci sítě pro službu App Service Environment používající ExpressRoute
## <a name="overview"></a>Přehled
Zákazníci mohou připojit [Azure ExpressRoute] [ ExpressRoute] okruh ke své virtuální síťové infrastruktury, tak rozšířit jejich místní síť do Azure.  Služby App Service Environment je možné vytvořit v této podsíti [virtuální sítě] [ virtualnetwork] infrastruktury.  Aplikace běžící na App Service Environment pak můžete navazovat zabezpečená připojení k back endové prostředky, které jsou přístupné pouze prostřednictvím připojení ExpressRoute.  

Služby App Service Environment je možné vytvořit v **buď** virtuální sítí Azure Resource Manageru **nebo** virtuální síť modelu nasazení classic.  Nedávné změny provedené v červnu 2016 služby ase můžete také nasadit do virtuální sítě, které používají rozsahy adres veřejné nebo definice RFC1918 adresní prostory (tj. privátní adresy). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Vyžaduje síťové připojení
Existují požadavky síťového připojení pro služby App Service Environment, která nemusí být splněny zpočátku do virtuální sítě připojené k ExpressRoute.  Služby App Service Environment vyžadovat mohl správně fungovat všechny z následujících akcí:

* Odchozího síťového připojení k Azure Storage koncových bodů po celém světě na oba porty 80 a 443.  Jedná se o koncových bodech, které jsou umístěné ve stejné oblasti jako služba App Service Environment, stejně jako koncové body úložiště nachází v **jiných** oblastí Azure.  Koncové body služby Azure Storage vyřešit podle následujících domén DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* a *file.core.windows.net*.  
* Odchozího síťového připojení ke službě soubory Azure na portu 445.
* Odchozí síťové připojení ke koncovým bodům Sql DB umístěný ve stejné oblasti jako služba App Service Environment.  Koncové body SQL DB řešení v rámci následující domény: *database.windows.net*.  To vyžaduje přístup k portům 1433, 11000 11999 a 14000 14999 otevření.  Další podrobnosti najdete v tématu [Tento článek na použití portů Sql Database V12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Odchozí síťové připojení ke koncovým bodům roviny správy Azure (ASM a ARM koncových bodů).  Jedná se o odchozí připojení k oběma *management.core.windows.net* a *management.azure.com*. 
* Odchozího síťového připojení k *ocsp.msocsp.com*, *mscrl.microsoft.com* a *crl.microsoft.com*.  To je potřeba pro podporu funkcí protokolu SSL.
* Konfigurace DNS pro virtuální síť musí být schopné řeší všechny koncové body a domén, které jsou uvedené v předchozích bodů.  Pokud tyto koncové body nelze vyřešit, se nezdaří pokusy o vytvoření služby App Service Environment a stávající služby App Service Environment se označí jako špatný.
* Odchozí přístup na port 53, je nutné pro komunikaci se servery DNS.
* Pokud vlastní server DNS existuje na druhém konci bránu VPN, musí být dostupný z podsítě služby App Service Environment obsahující DNS server. 
* Odchozí síťová cesta nemůže přecházet mezi interní podnikové proxy servery ani nemůže být vynuceným tunelovým propojením do místní.  Efektivní adresa NAT odchozích síťových přenosů tím změní ze služby App Service Environment.  Změna adres NAT odchozího provozu sítě služby App Service Environment způsobí selhání připojení k několika koncových bodů uvedených výše.  To má za následek neúspěšné pokusy o vytvoření služby App Service Environment, stejně jako dříve v pořádku App Service Environment se označí jako špatný.  
* Příchozí síťový přístup k požadované porty pro App Service Environment musí být povoleno, jak je popsáno v tomto [článku][requiredports].

Mohou být splněny požadavky na DNS tím, že zajišťuje platný infrastruktury služby DNS je konfiguraci a údržbu pro virtuální síť.  Pokud z nějakého důvodu dojde ke změně konfigurace DNS po vytvoření služby App Service Environment, vývojáři můžete vynutit službu App Service Environment, aby se získaly novou konfiguraci DNS.  Aktivuje se zajištěním provozu restartování prostředí pomocí ikony "Restart" umístěný v horní části okna správy služby App Service Environment [webu Azure portal] [ NewPortal] způsobí, že prostředí tak, aby získaly nové DNS konfigurace.

Mohou být splněny požadavky na přístup příchozí síťová konfigurace [skupinu zabezpečení sítě] [ NetworkSecurityGroups] v podsíti služby App Service Environment umožňující požadovaný přístup, jak je popsáno v tomto [ článek][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Povolení odchozího síťového připojení pro služby App Service Environment
Ve výchozím nastavení nově vytvořený okruh ExpressRoute inzeruje výchozí trasu, která umožňuje odchozí připojení k Internetu.  S touto konfigurací bude moct připojit k jiné koncové body Azure App Service Environment.

Běžnou konfigurací zákazníků je ale definovat vlastní výchozí trasa (0.0.0.0/0), která vynutí odchozí internetový provoz místo toho tok místní.  Tento tok provozu vždy přeruší App Service Environment, protože odchozí provoz je blokované v místním nebo NAT by nerozpoznatelný sadu adresy, které přestane fungovat v různých koncových bodů Azure.

Toto řešení je definování (nejméně) trasy definované uživatelem (udr) na podsíť, která obsahuje služby App Service Environment.  Trasu UDR definuje konkrétní podsítě tras, které bude použito místo výchozí trasu.

Pokud je to možné doporučuje se použijte následující konfiguraci:

* Konfigurace ExpressRoute inzeruje 0.0.0.0/0 a ve výchozím nastavení vynucené tunelů všechny odchozí provoz do místní.
* Uživatelem definovaná TRASA použitá na podsíť obsahující služby App Service Environment definuje 0.0.0.0/0 s dalším segmentem směrování typu Internet (příkladem je mimo provoz dále v tomto článku).

Celkové požadavky z těchto kroků je, že úrovni podsítě uživatelem definovaná TRASA bude mít přednost přes ExpressRoute vynucené tunelování, čímž zajišťuje odchozí internetový přístup ze služby App Service Environment.

> [!IMPORTANT]
> Trasy definované v trase UDR **musí** být dost konkrétní, aby přednost před všemi trasami inzerovanými konfigurací ExpressRoute.  Následující příklad používá široký rozsah adres 0.0.0.0/0 a proto může nechtěně dojít k jeho podle inzerování tras pomocí konkrétnější rozsahy adres.
> 
> App Service Environment nejsou podporované v konfiguracích ExpressRoute, který **trasy z cesty veřejného partnerského vztahu pro cestou soukromého partnerského vztahu mezi inzerovat**.  Konfigurace ExpressRoute, které mají veřejné partnerské vztahy nakonfigurované, bude přijímají inzerci tras od Microsoftu pro velkou sadu rozsahů adres IP adres Microsoft Azure.  Pokud tyto rozsahy adres křížová inzerce na cestou soukromého partnerského vztahu, konečný výsledek je, že všechny odchozí síťové pakety z podsítě služby App Service Environment bude platnost s děleným tunelovým propojením do zákazníka místní síťové infrastruktury.  App Service Environment v současné době nepodporuje tento tok sítí.  Jedním řešením tohoto problému je ukončit křížovou inzerci tras z cesty veřejného partnerského vztahu pro cestou soukromého partnerského vztahu.
> 
> 

Základní informace o uživatelsky definované trasy je k dispozici v tomto [přehled][UDROverview].  

Podrobnosti o vytvoření a konfigurace trasy definované uživatelem je k dispozici v tomto [jak pro průvodce][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Příklad konfigurace uživatelem definovaná TRASA pro služby App Service Environment
**Předpoklady**

1. Nainstalovat Azure Powershell z [Azure stáhne stránky][AzureDownloads] (s datem červen 2015 nebo novější).  V části "Nástroje příkazového řádku" je odkazem pro "Instalaci" v části "Windows Powershell", který nainstaluje nejnovější rutiny prostředí Powershell.
2. Doporučuje se, že jedinečnou podsíť se vytvoří pro výhradní použití ve službě App Service Environment.  Tím se zajistí, že použije na podsíť trasy definované uživatelem se otevře, jenom odchozí provoz pro App Service Environment.
3. **Důležité**: nezvolíte nasazení do služby App Service Environment **po** následovaných následující kroky konfigurace.  Tím se zajistí, že odchozího síťového připojení je k dispozici před pokusem o nasazení služby App Service Environment.

**Krok 1: Vytvoření pojmenovaných směrovací tabulky**

Následující fragment kódu vytvoří směrovací tabulku nazývá "DirectInternetRouteTable" v oblasti Azure USA – západ:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Krok 2: Vytvořte jeden nebo několik tras ve směrovací tabulce**

Je potřeba přidat jeden nebo více trasy do směrovací tabulky, chcete-li povolit odchozí internetový přístup.  

Doporučený postup pro konfiguraci odchozí přístup k Internetu je definovat trasu pro 0.0.0.0/0, jak je znázorněno níže.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Mějte na paměti, že 0.0.0.0/0 je rozsah adres široké a jako takové, budou přepsaná identifikátorem konkrétnější rozsahy adres inzerovat pomocí ExpressRoute.  K iteraci znovu předchozí doporučení, trasu UDR s trasy 0.0.0.0/0 byste měli použít ve spojení s konfigurací ExpressRoute, který pouze zajistí inzerci také 0.0.0.0/0. 

Jako alternativu můžete stáhnout kompletní a aktualizovaný seznam rozsahy CIDR používaných službou Azure.  Soubor Xml obsahující všechny rozsahy adres IP adres Azure je k dispozici [Microsoft Download Center][DownloadCenterAddressRanges].  

Nezapomeňte, že tyto rozsahy v průběhu času měnit, takže vyžadovala pravidelné ruční aktualizace trasy definované uživatelem pro synchronizaci.  Také protože výchozí horní limit 100 tras v jedné uživatelem definovaná TRASA, budete muset "shrnout" rozsahy Azure IP adres, aby vyhovovaly limitu 100 trasy, dodržujte při tom, že směrování definovaného uživatelem definované trasy musí být konkrétnější než trasy inzerované podle vaší ExpressRo ustit.  

**Krok 3: Přidružení směrovací tabulky pro podsíť obsahující služby App Service Environment**

Posledním krokem konfigurace je přidružení směrovací tabulky k podsíti nasazená služba App Service Environment.  Následující příkaz přiřadí "DirectInternetRouteTable" k "ASESubnet", která bude obsahovat nakonec služby App Service Environment.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Krok 4: Závěrečné kroky**

Jakmile směrovací tabulka je vázána k podsíti, se doporučuje nejprve otestovat a potvrďte zamýšlený efekt.  Například nasazení virtuálního počítače do podsítě a ujistěte se, že:

* Odchozí provoz do Azure i mimo Azure koncových bodů uvedených výše v tento článek je **není** toku okruhu ExpressRoute.  Je velmi důležité ověřit toto chování, protože pokud odchozího provozu z podsítě je stále vynuceným tunelovým propojením selže vždy vytvoření služby App Service Environment v místním. 
* Vyhledávání DNS pro koncové body již bylo zmíněno dříve všechny řeší správně. 

Jakmile jsou potvrzeny výše uvedené kroky, musíte odstranit virtuální počítač, protože podsíť musí být "prázdný" v době vytvoření služby App Service Environment.

Potom pokračujte ve vytváření služby App Service Environment!

## <a name="getting-started"></a>Začínáme
Začínáme s App Service Environment najdete v článku [Úvod do služby App Service Environment][IntroToAppServiceEnvironment]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
