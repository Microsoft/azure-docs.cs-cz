---
title: Řízení příchozího provozu v1
description: Přečtěte si, jak řídit příchozí provoz do prostředí služby App Service. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 857b2b00aadced567bc8ac191cdd9908f7bea7a3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804397"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Jak řídit příchozí provoz do prostředí služby App Service
## <a name="overview"></a>Přehled
Prostředí služby App Service lze vytvořit **ve** virtuální síti Azure Resource Manager **nebo** ve [virtuální síti][virtualnetwork]modelu klasického nasazení .  Novou virtuální síť a novou podsíť lze definovat v době, kdy je vytvořeno prostředí služby App Service.  Případně prostředí služby App Service lze vytvořit v již existující virtuální síti a již existující podsíti.  Se změnou provedenou v červnu 2016 lze ases také nasadit do virtuálních sítí, které používají rozsahy veřejných adres nebo adresní prostory RFC1918 (tj. soukromé adresy).  Další podrobnosti o vytvoření prostředí služby App Service najdete v [tématu Jak vytvořit prostředí služby App Service][HowToCreateAnAppServiceEnvironment].

Prostředí služby App Service musí být vždy vytvořeno v rámci podsítě, protože podsíť poskytuje hranici sítě, kterou lze použít k uzamčení příchozího provozu za upstream ovými zařízeními a službami, takže přenosy HTTP a HTTPS jsou přijímány pouze z konkrétních upstream IP adres.

Příchozí a odchozí síťový provoz v podsíti je řízen pomocí [skupiny zabezpečení sítě][NetworkSecurityGroups]. Řízení příchozího provozu vyžaduje vytvoření pravidel zabezpečení sítě ve skupině zabezpečení sítě a následné přiřazení skupiny zabezpečení sítě k podsíti obsahující prostředí služby App Service.

Jakmile je skupina zabezpečení sítě přiřazena k podsíti, příchozí provoz do aplikací v prostředí služby App Service je povolen/blokován na základě pravidel povolení a zamítnutí definovaných ve skupině zabezpečení sítě.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Příchozí síťové porty používané v prostředí služby App Service
Před uzamčením příchozího síťového provozu se skupinou zabezpečení sítě je důležité znát sadu požadovaných a volitelných síťových portů používaných prostředím služby App Service.  Náhodné ukončení provozu na některé porty může mít za následek ztrátu funkčnosti v prostředí služby App Service.

Následuje seznam portů používaných prostředím služby App Service. Všechny porty jsou **TCP**, pokud není jasně uvedeno jinak:

* 454: **Požadovaný port** používaný infrastrukturou Azure pro správu a údržbu prostředí služby App Service prostřednictvím tls.  Neblokujte provoz na tomto portu.  Tento port je vždy vázán na veřejné VIP ase.
* 455: **Požadovaný port** používaný infrastrukturou Azure pro správu a údržbu prostředí služby App Service prostřednictvím tls.  Neblokujte provoz na tomto portu.  Tento port je vždy vázán na veřejné VIP ase.
* 80: Výchozí port pro příchozí provoz HTTP do aplikací spuštěné v plánech služby App Service v prostředí služby App Service.  V ase s povolenou službou ILB je tento port vázán na adresu ILB služby ASE.
* 443: Výchozí port pro příchozí přenos tls do aplikací spuštěných v plánech služby App Service v prostředí služby App Service.  V ase s povolenou službou ILB je tento port vázán na adresu ILB služby ASE.
* 21: Řídící kanál pro FTP.  Tento port lze bezpečně zablokovat, pokud není používán ftp.  Ve službě ASE s povolenou službou ILB může být tento port vázán na adresu ILB pro službu ASE.
* 990: Řídící kanál pro FTPS.  Tento port lze bezpečně zablokovat, pokud není používán FTPS.  Ve službě ASE s povolenou službou ILB může být tento port vázán na adresu ILB pro službu ASE.
* 10001-10020: Datové kanály pro FTP.  Stejně jako u řídicího kanálu mohou být tyto porty bezpečně blokovány, pokud není používán FTP.  V ase s povolenou službou ILB může být tento port vázán na adresu ILB služby ASE.
* 4016: Používá se pro vzdálené ladění s Visual Studio 2012.  Tento port lze bezpečně zablokovat, pokud není funkce používána.  V ase s povolenou službou ILB je tento port vázán na adresu ILB služby ASE.
* 4018: Používá se pro vzdálené ladění s Visual Studio 2013.  Tento port lze bezpečně zablokovat, pokud není funkce používána.  V ase s povolenou službou ILB je tento port vázán na adresu ILB služby ASE.
* 4020: Používá se pro vzdálené ladění s Visual Studio 2015.  Tento port lze bezpečně zablokovat, pokud není funkce používána.  V ase s povolenou službou ILB je tento port vázán na adresu ILB služby ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Odchozí připojení a požadavky na DNS
Pro prostředí služby App Service správně fungovat, vyžaduje také odchozí přístup k různým koncovým bodům. Úplný seznam externích koncových bodů používaných službou ASE je v části Požadované připojení k síti v článku [Konfigurace sítě pro expressroute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

Prostředí služby App Service vyžadují platnou infrastrukturu DNS nakonfigurovanou pro virtuální síť.  Pokud se z nějakého důvodu po vytvoření prostředí služby App Service změní konfigurace DNS, vývojáři mohou vynutit, aby prostředí služby App Service vyzvedlo novou konfiguraci DNS.  Spuštění postupného restartování prostředí pomocí ikony "Restart" umístěné v horní části okna správy prostředí služby App Service na [portálu Azure][NewPortal] způsobí, že prostředí vyzvedne novou konfiguraci DNS.

Doporučuje se také, aby všechny vlastní servery DNS ve virtuální síti byly nastaveny předem před vytvořením prostředí služby App Service.  Pokud se při vytváření prostředí služby App Service změní konfigurace DNS virtuální sítě, bude to mít za následek selhání procesu vytváření prostředí služby App Service.  V podobném hlediska, pokud vlastní server DNS existuje na druhém konci brány VPN a server DNS je nedostupný nebo není k dispozici, proces vytváření prostředí služby App Service se také nezdaří.

## <a name="creating-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Podrobné informace o tom, jak skupiny zabezpečení sítě fungují, naleznete v následujících [informacích][NetworkSecurityGroups].  Následující příklad správy služeb Azure se dotýká zvýraznění skupin zabezpečení sítě se zaměřením na konfiguraci a použití skupiny zabezpečení sítě v podsíti, která obsahuje prostředí služby App Service.

**Poznámka:** Skupiny zabezpečení sítě lze nakonfigurovat graficky pomocí [portálu Azure Portal](https://portal.azure.com) nebo prostřednictvím Azure PowerShellu.

Skupiny zabezpečení sítě jsou nejprve vytvořeny jako samostatná entita přidružená k předplatnému. Vzhledem k tomu, že skupiny zabezpečení sítě jsou vytvořeny v oblasti Azure, ujistěte se, že skupina zabezpečení sítě je vytvořenve stejné oblasti jako prostředí služby App Service.

Vytvoření skupiny zabezpečení sítě ukazuje následující:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Po vytvoření skupiny zabezpečení sítě je do ní přidáno jedno nebo více pravidel zabezpečení sítě.  Vzhledem k tomu, že sada pravidel se může v průběhu času měnit, doporučujeme umístit schéma číslování používané pro priority pravidel, aby bylo snadné vkládat další pravidla v průběhu času.

Následující příklad ukazuje pravidlo, které explicitně uděluje přístup k portům pro správu, které infrastruktura Azure potřebuje ke správě a údržbě prostředí služby App Service.  Všimněte si, že všechny toky provozu správy přes TLS a je zabezpečen klientských certifikátů, takže i když jsou porty otevřené, jsou nepřístupné žádné jiné entity než infrastruktury správy Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Při uzamčení přístupu k portu 80 a 443 "skrýt" app service prostředí za upstream zařízení nebo služby, budete potřebovat znát upstream IP adresu.  Například pokud používáte bránu firewall webové aplikace (WAF), WAF bude mít vlastní IP adresu (nebo adresy), které používá při proxy provozu na navazující prostředí služby App Service.  Tuto adresu IP budete muset použít v parametru *SourceAddressPrefix* pravidla zabezpečení sítě.

V níže uvedeném příkladu je výslovně povolen příchozí provoz z určité adresy IP proti proudu.  Adresa *1.2.3.4* se používá jako zástupný symbol pro IP adresu waf proti proudu.  Změňte hodnotu tak, aby odpovídala adrese používané vaším upstream zařízením nebo službou.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Pokud je požadována podpora FTP, následující pravidla lze použít jako šablonu pro udělení přístupu k portu řízení FTP a portům datového kanálu.  Vzhledem k tomu, že protokol FTP je stavový protokol, nemusí být možné směrovat přenosy FTP prostřednictvím tradiční brány firewall HTTP/HTTPS nebo proxy zařízení.  V takovém případě budete muset nastavit *SourceAddressPrefix* na jinou hodnotu – například rozsah IP adres vývojářských nebo nasazovacích počítačů, na kterých jsou spuštěni klienti FTP. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

**(Poznámka:** Rozsah portů datového kanálu se může během období náhledu měnit.)

Pokud se používá vzdálené ladění pomocí sady Visual Studio, následující pravidla ukazují, jak udělit přístup.  Pro každou podporovanou verzi sady Visual Studio existuje samostatné pravidlo, protože každá verze používá pro vzdálené ladění jiný port.  Stejně jako u přístupu FTP nemusí vzdálený přenos ladění správně procházet tradičním zařízením WAF nebo proxy zařízením.  *SourceAddressPrefix* lze místo toho nastavit na rozsah IP adres vývojářských počítačů se spuštěnou sadou Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Přiřazení skupiny zabezpečení sítě k podsíti
Skupina zabezpečení sítě má výchozí pravidlo zabezpečení, které odepře přístup ke všem externím přenosům.  Výsledkem kombinace výše popsaných pravidel zabezpečení sítě a výchozího pravidla zabezpečení blokujícího příchozí provoz je, že pouze přenosy z rozsahů zdrojových adres přidružené k akci *Povolit* budou moci odesílat provoz aplikacím spuštěným v prostředí služby App Service.

Jakmile je skupina zabezpečení sítě naplněna pravidly zabezpečení, musí být přiřazena k podsíti obsahující prostředí služby App Service.  Příkaz přiřazení odkazuje jak na název virtuální sítě, ve které se nachází prostředí služby App Service, tak na název podsítě, kde bylo prostředí služby App Service vytvořeno.  

Následující příklad ukazuje skupinu zabezpečení sítě přiřazenou k podsíti a virtuální síti:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Jakmile je přiřazení skupiny zabezpečení sítě úspěšné (přiřazení je dlouhotrvající operace a může trvat několik minut na dokončení), pouze příchozí přenosy odpovídající *Povolit* pravidla se úspěšně dostanou k aplikacím v prostředí služby App Service.

Pro úplnost následující příklad ukazuje, jak odebrat a tedy zrušit přidružení skupiny zabezpečení sítě z podsítě:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Zvláštní aspekty explicitního protokolu IP-SSL
Pokud je aplikace nakonfigurovaná s explicitní adresou IP-SSL (použitelnou *pouze* pro služby ASS, které mají veřejnou virtuální ip adresu), namísto použití výchozí IP adresy prostředí služby App Service, přeteče přenosy HTTP i HTTPS do podsítě přes jinou sadu portů než porty 80 a 443.

Jednotlivé dvojice portů používaných každou adresou IP-SSL najdete v uživatelském rozhraní portálu z okna ux podrobností prostředí služby App Service.  Vyberte možnost "Všechna nastavení" --> "IP adresy".  Okno "IP adresy" zobrazuje tabulku všech explicitně nakonfigurovaných adres IP-SSL pro prostředí služby App Service spolu se speciálním párem portů, který se používá ke směrování přenosů HTTP a HTTPS spojených s každou adresou IP-SSL.  Je to tento pár portů, který je třeba použít pro parametry DestinationPortRange při konfiguraci pravidel ve skupině zabezpečení sítě.

Když je aplikace ve službě ASE nakonfigurována pro použití protokolu IP-SSL, externí zákazníci neuvidí a nemusí se starat o mapování dvojice speciálních portů.  Provoz do aplikací bude normálně přetékat na nakonfigurovanou adresu IP-SSL.  Překlad do dvojice speciálních portů se automaticky stane interně během poslední části směrování provozu do podsítě obsahující službu ASE. 

## <a name="getting-started"></a>Začínáme
Pokud chcete začít s prostředím služby App Service, [přečtěte si informace o úvodu do prostředí služby App Service][IntroToAppServiceEnvironment]

Podrobnosti o aplikacích, které se bezpečně připojují k prostředkům back-endu z prostředí služby App Service, najdete v [tématu Bezpečné připojení k prostředkům back-endu z prostředí služby App Service][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

