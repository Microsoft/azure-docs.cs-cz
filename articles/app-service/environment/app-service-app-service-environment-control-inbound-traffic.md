---
title: Řízení příchozího provozu do služby App Service environment – Azure
description: Další informace o tom, jak nakonfigurovat pravidla zabezpečení sítě k řízení příchozího provozu do služby App Service Environment.
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
ms.custom: seodec18
ms.openlocfilehash: 84575dcb67845a074ce19cf9d819e1dda3f90e20
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271952"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Řízení příchozího provozu do služby App Service Environment
## <a name="overview"></a>Přehled
Služby App Service Environment je možné vytvořit v **buď** virtuální sítí Azure Resource Manageru **nebo** modelu nasazení classic [virtuální sítě] [ virtualnetwork].  Nová virtuální síť a novou podsíť lze definovat v době vytvoření služby App Service Environment.  Alternativně lze vytvořit služby App Service Environment v existující virtuální sítě a již existující podsíti.  Změny provedené v červnu 2016 je možné také nasadit služby ase do virtuální sítě, které používají rozsahy adres veřejné nebo definice RFC1918 adresní prostory (tj. privátní adresy).  Další podrobnosti o vytvoření služby App Service Environment najdete [vytvoření služby App Service Environment][HowToCreateAnAppServiceEnvironment].

Služby App Service Environment se musí vždycky vytvořit v podsíti, protože podsíť poskytuje ohraničení sítě, které je možné zamezit příchozímu přenosu za upstreamovými zařízeními a službami tak, že je přijatelný jenom přenosy HTTP i HTTPS z konkrétní nadřazeného IP adresy.

Příchozí a odchozí síťový provoz v podsíti je řízen pomocí [skupinu zabezpečení sítě][NetworkSecurityGroups]. Řízení příchozího provozu vyžaduje vytváření pravidel zabezpečení sítě ve skupině zabezpečení sítě a poté přiřazováním zabezpečení sítě seskupit podsítě obsahující služby App Service Environment.

Po přiřazení skupiny zabezpečení sítě k podsíti, příchozí provoz do aplikace ve službě App Service Environment je povolen nebo zablokován na základě povolit a odepřít pravidel definovaných ve skupině zabezpečení sítě.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Příchozí síťových portů používaných ve službě App Service Environment
Před uzamčením příchozího provozu sítě s použitím skupiny zabezpečení sítě, je důležité znát sadu povinné a nepovinné síťových portů používaných ve službě App Service Environment.  Náhodně zavření mimo provoz do některé porty může vést ke ztrátě funkčnosti ve službě App Service Environment.

Následuje seznam portů používaných ve službě App Service Environment. Všechny porty jsou **TCP**, pokud není uvedeno jinak jasně:

* 454:  **Vyžaduje port** používá infrastrukturu Azure pro správu a údržbu služby App Service Environment přes SSL.  Neblokujete provoz na tento port.  Tento port je vždy vázány na veřejných virtuálních IP adres službě ASE.
* 455:  **Vyžaduje port** používá infrastrukturu Azure pro správu a údržbu služby App Service Environment přes SSL.  Neblokujete provoz na tento port.  Tento port je vždy vázány na veřejných virtuálních IP adres službě ASE.
* 80:  Výchozí port pro příchozí provoz protokolu HTTP pro aplikace běžící v plánech služby App Service ve službě App Service Environment.  Ve službě ASE s ILB povolena je tento port vázán na adresu ILB ASE.
* 443: Výchozí port pro příchozí provoz protokolu SSL pro aplikace běžící v plánech služby App Service ve službě App Service Environment.  Ve službě ASE s ILB povolena je tento port vázán na adresu ILB ASE.
* 21:  Řídicí kanál pro službu FTP.  Tento port může bezpečně zablokovat, pokud se nepoužívá FTP.  Ve službě ASE s ILB povoleno mohou být vázány na adresu ILB tento port pro službu ASE.
* 990:  Řídicí kanál pro FTPS.  Tento port může bezpečně zablokovat, pokud se nepoužívá FTPS.  Ve službě ASE s ILB povoleno mohou být vázány na adresu ILB tento port pro službu ASE.
* 10001-10020: Datové kanály pro službu FTP.  Stejně jako řídicí kanál, tyto porty může bezpečně zablokovat, pokud se nepoužívá FTP.  Ve službě ASE s ILB povoleno mohou být tento port vázány na adresu ILB ASE pro.
* 4016: Používá se pro vzdálené ladění pomocí sady Visual Studio 2012.  Tento port může bezpečně zablokovat, pokud se nepoužívá funkci.  Ve službě ASE s ILB povolena je tento port vázán na adresu ILB ASE.
* 4018: Používá se pro vzdálené ladění pomocí sady Visual Studio 2013.  Tento port může bezpečně zablokovat, pokud se nepoužívá funkci.  Ve službě ASE s ILB povolena je tento port vázán na adresu ILB ASE.
* 4020: Používá se pro vzdálené ladění pomocí sady Visual Studio 2015.  Tento port může bezpečně zablokovat, pokud se nepoužívá funkci.  Ve službě ASE s ILB povolena je tento port vázán na adresu ILB ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Odchozí připojení a požadavky na DNS
Pro službu App Service Environment fungovala správně vyžaduje také odchozí přístup do různých koncových bodů. Úplný seznam externích koncových bodů použitých ve službě ASE je v části "Vyžaduje připojení k síti" [konfiguraci sítě pro ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) článku.

Služby App Service Environment vyžaduje platnou konfiguraci pro virtuální síť infrastruktury služby DNS.  Pokud z nějakého důvodu dojde ke změně konfigurace DNS po vytvoření služby App Service Environment, vývojáři můžete vynutit službu App Service Environment, aby se získaly novou konfiguraci DNS.  Aktivuje se zajištěním provozu restartování prostředí pomocí ikony "Restart" umístěný v horní části okna správy služby App Service Environment [webu Azure portal] [ NewPortal] způsobí, že prostředí tak, aby získaly nové DNS konfigurace.

Doporučuje se také, že všechny vlastní servery DNS ve virtuální síti se instalační program předem před vytvořením služby App Service Environment.  Pokud při vytváření služby App Service Environment se změnila konfigurace DNS virtuální sítě, které způsobí selhání procesu vytvoření služby App Service Environment.  V podobných souvislosti Pokud vlastní server DNS existuje na druhém konci bránu sítě VPN a DNS server je nedostupná nebo není k dispozici, proces vytváření služby App Service Environment se také nezdaří.

## <a name="creating-a-network-security-group"></a>Vytváří se skupina zabezpečení sítě
Všechny podrobnosti o tom, jak pracovních skupin zabezpečení sítě najdete v následujících [informace][NetworkSecurityGroups].  Azure Service Management, které se týká níže uvedený příklad ukazuje skupin zabezpečení sítě, se zaměřením na konfiguraci a použití skupinu zabezpečení sítě pro podsíť, která obsahuje službu App Service Environment.

**Poznámka:** Skupiny zabezpečení sítě můžete konfigurovat pomocí graficky [webu Azure Portal](https://portal.azure.com) nebo prostřednictvím Azure Powershellu.

Skupiny zabezpečení sítě jsou nejprve vytvořit jako samostatný entity spojené s předplatným. Vzhledem k tomu, že skupiny zabezpečení sítě se vytvoří v oblasti Azure, ujistěte se, že se vytvoří skupina zabezpečení sítě ve stejné oblasti jako služba App Service Environment.

Následující příklad ukazuje vytvoření skupiny zabezpečení sítě:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Po vytvoření skupiny zabezpečení sítě, jeden nebo více pravidel zabezpečení sítě jsou přidány do něj.  Vzhledem k tomu, že sadu pravidel, která může v průběhu času měnit, doporučujeme místo si číslování schéma používané pro pravidlo priority snadno vložit další pravidla v čase.

Následující příklad ukazuje pravidlo, které se explicitně udělí přístup k portům pro správu potřebné infrastruktury Azure ke správě a údržbě služby App Service Environment.  Všimněte si, že veškerý provoz správy toků přes protokol SSL a je zabezpečen pomocí klientských certifikátů, tak i v případě, že jsou otevřené porty, budou pro entitu než infrastruktury pro správu Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Pokud zablokujete přístup k portu 80 a 443 "schovat" App Service Environment za upstreamovými zařízeními nebo služby, musíte znát nadřazeného IP adresu.  Například, pokud používáte firewall webových aplikací (WAF), WAF bude mít svou vlastní IP adresy (nebo adresy), které používá při provozu využívání proxy serveru pro příjem dat App Service Environment.  Budete muset použít tuto IP adresu v *hodnotu SourceAddressPrefix* parametr pravidla zabezpečení sítě.

V následujícím příkladu je explicitně povolen příchozí provoz z nadřazeného konkrétní IP adresu.  Adresa *1.2.3.4* slouží jako zástupný symbol pro IP adresu z nadřazeného WAF.  Změňte hodnotu tak, aby odpovídaly je adresa použitá pro nadřazený zařízení nebo službou.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Pokud se požaduje podpory protokolu FTP, následující pravidla může sloužit jako šablonu k udělení přístupu k port řízení FTP a datový kanál porty.  Protože stavový protokol FTP, nemusí být schopny směrovat přenosy FTP přes tradiční zařízení brány firewall nebo proxy server HTTP/HTTPS.  V tomto případě budete muset nastavit *hodnotu SourceAddressPrefix* na jinou hodnotu – například rozsahu IP adres vývojářské nebo nasazení počítačů, na které FTP klientských počítačích. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Poznámka:** rozsah portů datového kanálu se může změnit během období preview.)

Pokud se používá vzdálené ladění pomocí sady Visual Studio, následující pravidla ukazují, jak udělit přístup.  Neexistuje samostatné pravidlo pro každou podporovanou verzi sady Visual Studio, protože každá verze používá jiný port pro vzdálené ladění.  Stejně jako u přístup pomocí protokolu FTP, nemusí správně tok provozu vzdálené ladění pomocí tradiční WAF nebo zařízení proxy serveru.  *Hodnotu SourceAddressPrefix* můžete místo toho nastavit pro rozsah IP adres vývojářských počítačů se systémem Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Přiřazení skupiny zabezpečení sítě k podsíti
Skupina zabezpečení sítě má výchozí pravidlo zabezpečení, které odepře přístup do všech externích přenosů.  Výsledkem kombinace výše popsaná pravidla zabezpečení sítě a výchozího pravidla zabezpečení blokuje příchozí provoz, je tento pouze provoz ze zdrojových rozsahů adres přidružené *povolit* akce bude moct posílat přenosy dat pro aplikace běžící ve službě App Service Environment.

Po naplnění skupiny zabezpečení sítě s pravidly zabezpečení je potřeba přiřadit podsíť obsahující služby App Service Environment.  Přiřazení příkazu odkazuje na název virtuální sítě, ve které se nachází služba App Service Environment, i název podsítě, ve kterém byla vytvořena služby App Service Environment.  

Následující příklad ukazuje skupinu zabezpečení sítě přiřazovaný proměnné podsítě a virtuální sítě:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Po úspěšném přiřazení skupiny zabezpečení sítě (přiřazení je dlouho běžící operace a může trvat několik minut), pouze příchozí provoz odpovídající *povolit* pravidla úspěšně dosáhne aplikace ve službě App Service Prostředí.

Pro úplnost následující příklad ukazuje, jak odebrat a proto dis-přidružení skupiny zabezpečení sítě z podsítě:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Zvláštní upozornění pro explicitní IP SSL
Pokud aplikace má nakonfigurovanou explicitní adresa IP SSL (příslušné *pouze* do služby ase, které mají veřejnou virtuální IP adresy), nemusíte používat výchozí IP adresu služby App Service Environment, provoz protokolu HTTP a HTTPS toků do podsítě přes jinou sadu jiné porty než porty 80 a 443.

Jednotlivé dvojice portů používaných každou adresu IP SSL najdete v portálu uživatelského rozhraní v okně App Service Environment podrobnosti uživatelského prostředí.  Vyberte nastavení "veškerá nastavení"--> "IP adresy".  "IP adresy" okno tabulky všechny explicitně nakonfigurované adresy IP SSL pro App Service Environment, společně s pár speciální port, který se používá ke směrování přenosů dat HTTP a HTTPS spojené s každou adresu IP SSL.  Je tento port pár, který je potřeba použít pro parametry DestinationPortRange při konfiguraci pravidla skupiny zabezpečení sítě.

Pokud aplikaci ve službě ASE je nakonfigurovaná pro použití protokolu IP SSL, externí zákazníky nezobrazí a není nutné se starat o pár mapování speciální portů.  Provoz do aplikace se obvykle směrovat do nakonfigurovaných adres IP SSL.  Překlad na spárování speciální port automaticky probíhá interně při do konečné fáze směrování provozu do podsítě obsahující službu ASE. 

## <a name="getting-started"></a>Začínáme
Začínáme s App Service Environment najdete v článku [Úvod do služby App Service Environment][IntroToAppServiceEnvironment]

Podrobnosti o aplikace se bezpečně připojuje k back-endový prostředek ze služby App Service Environment, naleznete v tématu [zabezpečené připojení k back-Endovým prostředkům ze služby App Service Environment][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

