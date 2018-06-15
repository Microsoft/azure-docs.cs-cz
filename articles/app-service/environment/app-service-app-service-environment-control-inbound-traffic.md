---
title: Tom, jak řídit příchozí přenosy do služby App Service Environment
description: Další informace o tom, jak nakonfigurovat pravidla zabezpečení sítě k řízení příchozí přenosy do služby App Service Environment.
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: ''
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.openlocfilehash: ed72bf3202d6cb2d2161bc0df693d3e6a1fc58ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836921"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Tom, jak řídit příchozí přenosy do služby App Service Environment
## <a name="overview"></a>Přehled
Služby App Service Environment se dají vytvářet v **buď** virtuální síť Azure Resource Manager **nebo** modelu nasazení classic [virtuální sítě][virtualnetwork].  V době, kdy se vytvoří služby App Service Environment lze definovat nové virtuální sítě a novou podsíť.  Služby App Service Environment Alternativně lze vytvořit v existující virtuální síť a existující podsíť.  S změny provedené v červnu 2016 ASEs také lze nasadit do virtuálních sítí, které používají rozsahů veřejných adres nebo RFC1918 adresní prostory (tj. privátní adresy).  Podrobnosti o vytvoření služby App Service Environment najdete v části [postup vytvoření služby App Service Environment][HowToCreateAnAppServiceEnvironment].

Služby App Service Environment musí vždy vytvořit v rámci jedné podsítě, protože podsíť poskytuje hranici sítě, který můžete použít zamknout příchozí provoz za nadřazeného zařízení a služeb tak, aby přenosů dat HTTP a HTTPS je pouze přijímán z konkrétní nadřazeného IP adresy.

Příchozí a odchozí síťový provoz na podsíť je řízena pomocí [skupinu zabezpečení sítě][NetworkSecurityGroups]. Řízení příchozí provoz vyžaduje vytvoření pravidla zabezpečení sítě ve skupině zabezpečení sítě a pak přiřazení zabezpečení sítě skupiny podsíť obsahující App Service Environment.

Jakmile skupinu zabezpečení sítě je přiřazen k podsíti, příchozí přenosy na aplikace v App Service Environment je povolené nebo blokované podle povolit a odepřít pravidla definovaná v skupinu zabezpečení sítě.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Příchozí síťové porty používané v prostředí služby App Service
Před zamykání příchozí síťový provoz s skupinu zabezpečení sítě, je důležité vědět, sadu požadované a volitelné síťové porty používané služby App Service Environment.  Omylem zavřením mimo provoz na některé porty může dojít ke ztrátě funkcí ve službě App Service Environment.

Následuje seznam portů používaných služby App Service Environment. Všechny porty jsou **TCP**, pokud není uvedeno jinak jasně:

* 454: **požadované port** používá infrastrukturu Azure pro správu a údržbu prostředí App Service přes SSL.  Nebrání provoz na tento port.  Tento port je vždy vázána veřejné VIP App Service Environment.
* 455: **požadované port** používá infrastrukturu Azure pro správu a údržbu prostředí App Service přes SSL.  Nebrání provoz na tento port.  Tento port je vždy vázána veřejné VIP App Service Environment.
* 80: výchozí port pro příchozí provoz protokolu HTTP pro aplikace běžící v plány služby App Service ve službě App Service Environment.  Na povoleno ILB App Service Environment tento port je vázána na adresu ILB App Service Environment.
* 443: výchozí port pro příchozí provoz protokolu SSL pro aplikace běžící v plány služby App Service ve službě App Service Environment.  Na povoleno ILB App Service Environment tento port je vázána na adresu ILB App Service Environment.
* 21: řídicí kanál pro službu FTP.  Tento port můžete bezpečně zablokuje, pokud není používán FTP.  Na povoleno ILB App Service Environment tento port vázat na adresu ILB pro App Service Environment.
* 990: řídicí kanál pro FTPS.  Tento port můžete bezpečně zablokuje, pokud není používán FTPS.  Na povoleno ILB App Service Environment tento port vázat na adresu ILB pro App Service Environment.
* 10001-10020: datové kanály pro službu FTP.  Stejně jako u řídicí kanál, tyto porty lze bezpečně blokovat Pokud není používán FTP.  Na povoleno ILB App Service Environment tento port vázat na adresu ILB App Service Environment.
* 4016: používá pro vzdálené ladění pomocí sady Visual Studio 2012.  Tento port můžete bezpečně zablokuje, pokud se nepoužívají funkci.  Na povoleno ILB App Service Environment tento port je vázána na adresu ILB App Service Environment.
* 4018: používá pro vzdálené ladění pomocí sady Visual Studio 2013.  Tento port můžete bezpečně zablokuje, pokud se nepoužívají funkci.  Na povoleno ILB App Service Environment tento port je vázána na adresu ILB App Service Environment.
* 4020: používá pro vzdálené ladění pomocí sady Visual Studio 2015.  Tento port můžete bezpečně zablokuje, pokud se nepoužívají funkci.  Na povoleno ILB App Service Environment tento port je vázána na adresu ILB App Service Environment.

## <a name="outbound-connectivity-and-dns-requirements"></a>Odchozí připojení a požadavky na DNS
Pro služby App Service Environment ke správnému fungování vyžaduje také odchozí přístup k různým koncové body. Úplný seznam externí koncové body používané App Service Environment je v části "Vyžaduje připojení k síti" [konfiguraci sítě pro ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) článku.

Prostředí služby App Service vyžaduje platnou konfiguraci pro virtuální síť infrastruktury služby DNS.  Pokud z nějakého důvodu dojde ke změně konfigurace DNS po vytvoření služby App Service Environment, vývojáři vynutit služby App Service Environment načíst novou konfiguraci DNS.  Aktivuje restartu postupného prostředí pomocí ikony "Restartovat" nachází v horní části okna Správa služby App Service Environment ve [portál Azure] [ NewPortal] způsobí, že prostředí tak, aby se vyzvedávat novou konfiguraci DNS.

Dále je doporučeno, aby žádné vlastní servery DNS na virtuální sítě se instalační program dopředu před vytvořením služby App Service Environment.  Pokud je při vytváření služby App Service Environment se změnila konfigurace DNS virtuální sítě, které způsobí selhání procesu vytvoření služby App Service Environment.  V souvislosti podobnou Pokud na druhém konci brány VPN existuje vlastního serveru DNS a serveru DNS je nedostupná nebo není k dispozici, procesu vytváření služby App Service Environment se také nezdaří.

## <a name="creating-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Úplné podrobnosti o tom, jak fungují skupiny zabezpečení sítě najdete v následujících [informace][NetworkSecurityGroups].  Pro správu služby Azure, které příklad dotykem na označuje skupin zabezpečení sítě, se zaměřením na konfiguraci a použití skupinu zabezpečení sítě pro podsíť, která obsahuje služby App Service Environment.

**Poznámka:** skupin zabezpečení sítě pomocí se dají konfigurovat graficky [portálu Azure](https://portal.azure.com) nebo pomocí prostředí Azure PowerShell.

Skupiny zabezpečení sítě jsou nejprve vytvořit jako samostatné entity přidružené předplatnému. Vzhledem k tomu, že skupiny zabezpečení sítě je vytvořeno v oblasti Azure, zajistěte, aby skupina zabezpečení sítě je vytvořená ve stejné oblasti jako služba App Service Environment.

Následující část ukazuje vytvoření skupiny zabezpečení sítě:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Po vytvoření skupiny zabezpečení sítě je jeden nebo více pravidel zabezpečení sítě se přidají k němu.  Vzhledem k tomu, že sadu pravidel v průběhu času mění, doporučujeme místo out používané pro priority pravidel snadno vložit další pravidla časem číslování schéma.

Následující příklad ukazuje pravidlo, které explicitně udělí přístup k portům správy vyžaduje infrastrukturu Azure ke správě a údržbě služby App Service Environment.  Všimněte si, že veškerý provoz správy probíhá přes protokol SSL a je zabezpečená službou klientské certifikáty, takže i když jsou otevřené porty budou nepřístupné všechny entita než infrastruktury pro správu Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Při zamykání přístup k portu 80 a 443 pro "schovat" služby App Service Environment za nadřazeného zařízení nebo služby, musíte znát nadřazeného IP adresu.  Například pokud používáte brány firewall webových aplikací (firewall webových aplikací), firewall webových aplikací bude mít svůj vlastní IP adresu (nebo adresy), které používá při provozu připojení přes proxy server pro příjem dat App Service Environment.  Budete muset použít tuto IP adresu v *SourceAddressPrefix* parametr pravidla zabezpečení sítě.

V následujícím příkladu je výslovně povoleno příchozí provoz z konkrétní IP adresu nadřazený.  Adresa *1.2.3.4* slouží jako zástupný symbol pro IP adresu nadřazenému firewall webových aplikací.  Změňte hodnotu tak, aby odpovídaly je adresa použitá nadřazeného zařízení nebo službou.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

V případě potřeby podpora FTP následující pravidla můžou sloužit jako šablona pro udělení přístupu k řízení port protokolu FTP a data kanál porty.  Vzhledem k tomu, že FTP je stavová protokol, nemusí být schopny směrovat přenos FTP prostřednictvím tradiční zařízení brány firewall nebo proxy protokolu HTTP nebo HTTPS.  V takovém případě budete muset nastavit *SourceAddressPrefix* na jinou hodnotu – například IP adresa rozsahu vývojáře nebo nasazení počítačů, na které FTP klientských počítačích. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Poznámka:** rozsah portů datového kanálu se může změnit během období preview.)

Pokud se používá vzdálené ladění pomocí sady Visual Studio, následující pravidla ukazují, jak k udělení přístupu.  Vzhledem k tomu, že každou verzi používá jiný port pro vzdálené ladění je samostatné pravidlo pro všechny podporované verze sady Visual Studio.  Stejně jako u přístup FTP, nemusí vzdálené ladění provoz procházet skrz tradiční firewall webových aplikací nebo zařízení proxy správně.  *SourceAddressPrefix* místo toho může být nastaven na rozsah IP adres počítačů vývojáře Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Přiřazení skupiny zabezpečení sítě k podsíti
Skupina zabezpečení sítě má výchozí pravidlo zabezpečení, čímž se odmítne přístup k externí veškerý provoz.  Výsledek z kombinace výše popsané pravidla zabezpečení sítě a zabezpečení výchozí pravidlo blokuje příchozí přenos, je tento jenom přenosy z rozsahů adres zdroje přidružené *povolit* akce bude moct posílat přenosy na aplikace běžící ve službě App Service Environment.

Po naplnění skupinu zabezpečení sítě s pravidla zabezpečení se musí přiřadit k podsíti obsahující App Service Environment.  Příkaz přiřazení odkazuje na název virtuální sítě, které se nachází App Service Environment, jak název podsítě, které bylo vytvořeno App Service Environment.  

Následující příklad ukazuje skupinu zabezpečení sítě nejsou přiřazeny virtuální sítě a podsítě:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Po úspěšném přiřazení skupiny zabezpečení sítě (přiřazení je dlouhotrvající operace a může trvat několik minut na dokončení), pouze příchozí provoz odpovídající *povolit* pravidla se úspěšně připojit aplikace v App Service Environment.

Pro úplnost následující příklad ukazuje, jak odebrat a proto dis-přidružte skupinu zabezpečení sítě z podsítě:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Zvláštní upozornění pro explicitní IP SSL
Pokud aplikace je konfigurován s adresou IP SSL explicitní (použít *pouze* k ASEs, které mají veřejné VIP), místo použití výchozí IP adresu App Service Environment, provoz protokolu HTTP a HTTPS, pokračovat v podsíti přes jinou sadu jiné porty než porty 80 a 443.

Jednotlivé dvojice porty používané každou adresu IP SSL naleznete v portálu uživatelského rozhraní v okně UX podrobnosti App Service Environment.  Vyberte možnost "všechna nastavení"--> "IP adresy".  V okně "IP adresy" je příklad všechny explicitně nakonfigurovanou IP SSL adresy pro App Service Environment, společně s pár speciální port, který slouží ke směrování HTTP a HTTPS provozy, které jsou spojené s každou IP SSL adresu.  Je tento pár port, který se musí použít pro parametry DestinationPortRange při konfiguraci pravidel ve skupině zabezpečení sítě.

Když aplikace v App Service Environment je nakonfigurovaný na použití IP-SSL, externí zákazníky, se nezobrazí a nemusíte se starat o mapování pár speciální portů.  Provoz aplikace bude procházet obvykle nakonfigurované IP SSL adresu.  Překlad na pár speciální port automaticky provede interně při konečné fáze směrování provozu do podsíť obsahující App Service Environment. 

## <a name="getting-started"></a>Začínáme
Chcete-li začít pracovat s prostředí App Service, přečtěte si téma [Úvod do služby App Service Environment][IntroToAppServiceEnvironment]

Podrobnosti ohledně aplikace zabezpečené připojení k back-end prostředků ze služby App Service Environment najdete v tématu [bezpečně připojování k back-endové prostředky ze služby App Service Environment][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

