---
title: Řízení příchozího provozu v1
description: Seznamte se s postupy pro řízení příchozího provozu do App Service Environment. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 857b2b00aadced567bc8ac191cdd9908f7bea7a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804397"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Řízení příchozího provozu na App Service Environment
## <a name="overview"></a>Přehled
App Service Environment se dá vytvořit **buď** v Azure Resource Manager virtuální síti, **nebo** ve [virtuální síti][virtualnetwork]modelu nasazení Classic.  Novou virtuální síť a novou podsíť lze definovat v době, kdy je vytvořena App Service Environment.  Alternativně můžete App Service Environment vytvořit v existující virtuální síti a již existující podsíti.  Po změně provedené v červnu 2016 lze služby ASE také nasadit do virtuálních sítí, které používají buď rozsahy veřejných adres, nebo RFC1918 adresní prostory (tj. soukromé adresy).  Další informace o vytváření App Service Environment najdete v tématu [jak vytvořit App Service Environment][HowToCreateAnAppServiceEnvironment].

V rámci podsítě se musí vždy vytvořit App Service Environment, protože podsíť poskytuje hranici sítě, která se dá použít k uzamknutí příchozího provozu za provozu zařízení a služeb tak, aby přenosy HTTP a HTTPS byly přijímány jenom z konkrétních IP adres nadřazeného objektu.

Příchozí a odchozí síťový provoz v podsíti se řídí pomocí [skupiny zabezpečení sítě][NetworkSecurityGroups]. Řízení příchozího provozu vyžaduje vytvoření pravidel zabezpečení sítě ve skupině zabezpečení sítě a přiřazení skupiny zabezpečení sítě k podsíti obsahující App Service Environment.

Po přiřazení skupiny zabezpečení sítě k podsíti se příchozí provoz do aplikací v App Service Environment povoluje nebo zablokuje na základě pravidel povolení a odmítnutí definovaných ve skupině zabezpečení sítě.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Příchozí síťové porty používané v App Service Environment
Před uzamčením příchozího síťového provozu pomocí skupiny zabezpečení sítě je důležité znát sadu vyžadovaných a volitelných síťových portů používaných App Service Environment.  Náhodné ukončení provozu na některých portech může způsobit ztrátu funkčnosti v App Service Environment.

Následuje seznam portů používaných App Service Environment. Všechny porty jsou **TCP**, pokud není výslovně uvedeno jinak:

* 454: **požadovaný port** používaný infrastrukturou Azure pro správu a údržbu App Servicech prostředí prostřednictvím protokolu TLS.  Neblokovat provoz na tento port.  Tento port je vždycky vázaný na veřejnou virtuální IP adresu pomocného mechanismu.
* 455: **požadovaný port** používaný infrastrukturou Azure pro správu a údržbu App Servicech prostředí prostřednictvím protokolu TLS.  Neblokovat provoz na tento port.  Tento port je vždycky vázaný na veřejnou virtuální IP adresu pomocného mechanismu.
* 80: výchozí port pro příchozí přenos HTTP do aplikací spuštěných v App Service plánuje v App Service Environment.  Na přihlašování s povoleným interního nástroje je tento port vázán na interního nástroje adresu mechanismu řízení.
* 443: výchozí port pro příchozí přenos TLS do aplikací spuštěných v App Service plánuje v App Service Environment.  Na přihlašování s povoleným interního nástroje je tento port vázán na interního nástroje adresu mechanismu řízení.
* 21: řídicí kanál pro FTP.  Pokud se FTP nepoužívá, můžete tento port bezpečně zablokovat.  Na interního nástroje přihlašování s povolenými možnostmi je možné tento port svázat s interního nástroje adresou pro pomocného mechanismu řízení.
* 990: řídicí kanál pro FTPS.  Pokud se FTPS nepoužívá, můžete tento port bezpečně zablokovat.  Na interního nástroje přihlašování s povolenými možnostmi je možné tento port svázat s interního nástroje adresou pro pomocného mechanismu řízení.
* 10001-10020: datové kanály pro FTP.  Stejně jako u řídicího kanálu můžou být tyto porty bezpečně blokované, pokud se FTP nepoužívá.  Na interního nástroje přihlašování s povolenými možnostmi je možné tento port svázat s interního nástroje adresou pro pomocného mechanismu.
* 4016: používá se pro vzdálené ladění pomocí sady Visual Studio 2012.  Pokud se funkce nepoužívá, můžete tento port bezpečně zablokovat.  Na přihlašování s povoleným interního nástroje je tento port vázán na interního nástroje adresu mechanismu řízení.
* 4018: používá se pro vzdálené ladění pomocí Visual Studio 2013.  Pokud se funkce nepoužívá, můžete tento port bezpečně zablokovat.  Na přihlašování s povoleným interního nástroje je tento port vázán na interního nástroje adresu mechanismu řízení.
* 4020: používá se pro vzdálené ladění pomocí sady Visual Studio 2015.  Pokud se funkce nepoužívá, můžete tento port bezpečně zablokovat.  Na přihlašování s povoleným interního nástroje je tento port vázán na interního nástroje adresu mechanismu řízení.

## <a name="outbound-connectivity-and-dns-requirements"></a>Odchozí připojení a požadavky na DNS
Aby App Service Environment správně fungovalo, vyžaduje také odchozí přístup k různým koncovým bodům. Úplný seznam externích koncových bodů používaných pomocným mechanismem najdete v části "požadované připojení k síti" v článku [Konfigurace sítě pro ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service prostředí vyžadují pro virtuální síť platnou infrastrukturu DNS.  Pokud z nějakého důvodu dojde ke změně konfigurace DNS po vytvoření App Service Environment, můžou vývojáři vynutit App Service Environment, aby si vybrali novou konfiguraci DNS.  Aktivuje se restartování prostředí za běhu pomocí ikony "restartovat" v horní části okna správy App Service Environment v [Azure Portal][NewPortal] způsobí, že prostředí vybere novou konfiguraci DNS.

Před vytvořením App Service Environment taky doporučujeme, aby všechny vlastní servery DNS ve virtuální síti byly napřed času.  Pokud dojde ke změně konfigurace DNS virtuální sítě během vytváření App Service Environment, což způsobí selhání procesu vytváření App Service Environment.  Pokud v podobném přístupnosti existuje vlastní server DNS na druhém konci brány VPN a server DNS je nedosažitelný nebo nedostupný, proces vytváření App Service Environment se také nezdaří.

## <a name="creating-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Úplné podrobnosti o tom, jak fungují skupiny zabezpečení sítě, najdete v následujících [informacích][NetworkSecurityGroups].  Níže uvedený příklad správy služeb Azure se zaměřuje na nejdůležitější skupiny zabezpečení sítě. Zaměřte se na konfiguraci a použití skupiny zabezpečení sítě pro podsíť, která obsahuje App Service Environment.

**Poznámka:** Skupiny zabezpečení sítě je možné konfigurovat graficky pomocí webu [Azure Portal](https://portal.azure.com) nebo prostřednictvím Azure PowerShell.

Skupiny zabezpečení sítě se nejdřív vytvoří jako samostatná entita přidružená k předplatnému. Vzhledem k tomu, že skupiny zabezpečení sítě se vytvářejí v oblasti Azure, ujistěte se, že je skupina zabezpečení sítě vytvořená ve stejné oblasti jako App Service Environment.

Následující příklad ukazuje, jak vytvořit skupinu zabezpečení sítě:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Po vytvoření skupiny zabezpečení sítě se do ní přidá nejméně jedno pravidlo zabezpečení sítě.  Vzhledem k tomu, že se sada pravidel může v průběhu času měnit, doporučujeme, abyste si vyčerpali schéma číslování používané pro priority pravidel, abyste mohli snadno vkládat další pravidla v průběhu času.

Následující příklad ukazuje pravidlo, které explicitně udělí přístup k portům pro správu, které potřebuje infrastruktura Azure ke správě a údržbě App Service Environment.  Mějte na paměti, že všechny přenosy správy přecházejí přes protokol TLS a jsou zabezpečené klientskými certifikáty, takže i když jsou porty otevřené, jsou nepřístupné jinou entitou než infrastrukturou pro správu Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Když zamknete přístup k portům 80 a 443 na "Skrýt" App Service Environment za nadřazenými zařízeními nebo službami, budete muset znát IP adresu nadřazeného objektu.  Pokud například používáte bránu firewall webových aplikací (WAF), bude mít WAF svou vlastní IP adresu (nebo adresy), kterou používá při proxy provozu do App Service Environment pro příjem dat.  Tuto IP adresu budete muset použít v parametru *SourceAddressPrefix* pravidla zabezpečení sítě.

V následujícím příkladu je příchozí přenos z konkrétní IP adresy pro odesílání výslovně povolen.  Adresa *1.2.3.4* se používá jako zástupný symbol pro IP adresu nadřazeného WAFu.  Změňte hodnotu tak, aby odpovídala adrese používané pro vaše nadřazené zařízení nebo službu.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Pokud se požaduje podpora FTP, můžete použít následující pravidla jako šablonu k udělení přístupu k portům ovládacího prvku FTP a k portům datových kanálů.  Vzhledem k tomu, že FTP je stavový protokol, možná nebudete moci směrovat přenos FTP prostřednictvím tradiční brány firewall HTTP/HTTPS nebo proxy zařízení.  V takovém případě budete muset nastavit *SourceAddressPrefix* na jinou hodnotu – například rozsah IP adres počítačů pro vývojáře nebo nasazení, na kterých běží klienti FTP. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Poznámka:** rozsah portů datového kanálu se může během období Preview změnit.)

Pokud se používá vzdálené ladění pomocí sady Visual Studio, následující pravidla demonstrují, jak udělit přístup.  Pro každou podporovanou verzi sady Visual Studio existuje samostatné pravidlo, protože každá verze používá jiný port pro vzdálené ladění.  Stejně jako při přístupu k FTP nemusí vzdálené ladění provozu bez problémů přesměrovat prostřednictvím tradičního WAF nebo proxy zařízení.  *SourceAddressPrefix* je možné nastavit na rozsah IP adres počítačů vývojářů se sadou Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Přiřazení skupiny zabezpečení sítě k podsíti
Skupina zabezpečení sítě má výchozí pravidlo zabezpečení, které odepře přístup ke všem externím přenosům.  Výsledkem kombinace pravidel zabezpečení sítě popsaných výše a výchozího pravidla zabezpečení blokujícího příchozího provozu je, že přenos dat do aplikací spuštěných v App Service Environment bude umožňovat pouze provoz ze zdrojových rozsahů adres spojených s akcí *Povolit* .

Po naplnění skupiny zabezpečení sítě pomocí pravidel zabezpečení je potřeba ji přiřadit k podsíti obsahující App Service Environment.  Příkaz přiřazení odkazuje na název virtuální sítě, ve které se App Service Environment nachází, a na název podsítě, ve které byla App Service Environment vytvořena.  

Následující příklad ukazuje skupinu zabezpečení sítě, která je přiřazena k podsíti a virtuální síti:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Po úspěšném přiřazení skupiny zabezpečení sítě (přiřazení je dlouhotrvající operace a dokončení může trvat několik minut), jenom příchozí přenosy, které odpovídají pravidlům pro *Povolení* , budou úspěšně kontaktovat aplikace v App Service Environment.

V případě úplnosti následující příklad ukazuje, jak odebrat a tedy dis – přidružit skupinu zabezpečení sítě z podsítě:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Zvláštní požadavky na explicitní IP-SSL
Pokud je aplikace nakonfigurovaná s explicitní adresou IP-SSL (platí *jenom* pro služby ASE, která má veřejnou virtuální IP adresu), místo použití výchozí IP adresy App Service Environment přenos přes protokol HTTP i HTTPS do podsítě přes jinou sadu jiných portů než porty 80 a 443.

Jednotlivé páry portů používané jednotlivými adresami IP-SSL najdete v okně uživatelské rozhraní portálu z okna s podrobnostmi o App Service Environment.  Vyberte všechna nastavení--> "IP adresy".  V okně "IP adresy" se zobrazí tabulka všech explicitně nakonfigurovaných adres IP-SSL pro App Service Environment spolu se speciální dvojicí portů, která se používá ke směrování přenosů HTTP a HTTPS přidružených ke každé adrese IP-SSL.  Je to dvojice portů, kterou je třeba použít pro parametry DestinationPortRange při konfiguraci pravidel ve skupině zabezpečení sítě.

Když je aplikace na pomocném panelu nakonfigurovaná pro používání IP-SSL, externí zákazníci se neuvidí a nemusíte si dělat starosti s mapováním speciálního páru portů.  Provoz do aplikací bude normálně tok nakonfigurované IP adresy SSL.  Překlad na speciální dvojici portů automaticky probíhá interně během poslední fáze směrování provozu do podsítě obsahující pomocného mechanismu řízení. 

## <a name="getting-started"></a>Začínáme
Pokud chcete začít pracovat se App Service prostředími, přečtěte si téma [Úvod do App Service Environment][IntroToAppServiceEnvironment]

Podrobnosti o aplikacích, které se bezpečně připojují k back-endu prostředku z App Service Environment, najdete v tématu [zabezpečené připojení k back-endu prostředkům z App Service Environment][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

