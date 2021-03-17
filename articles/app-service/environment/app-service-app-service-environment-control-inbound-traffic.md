---
title: Řízení příchozího provozu v1
description: Naučte se řídit příchozí provoz do App Service Environment. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fe9326ea9ebd5afe981b7ba6c34b1a5d51e084b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962056"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Řízení příchozího provozu na App Service Environment
## <a name="overview"></a>Přehled
App Service Environment se dá vytvořit **buď** v Azure Resource Manager virtuální síti, **nebo** ve [virtuální síti][virtualnetwork]modelu nasazení Classic.  Novou virtuální síť a novou podsíť lze definovat v době, kdy je vytvořena App Service Environment. Místo toho je možné vytvořit App Service Environment v existující virtuální síti a v již existující podsíti.  Od června 2016 lze služby ASE také nasadit do virtuálních sítí, které používají buď rozsahy veřejných adres, nebo RFC1918 adresních prostorů (privátní adresy).  Další informace najdete v tématu [vytvoření App Service Environment][HowToCreateAnAppServiceEnvironment].

Vždy vytvořte App Service Environment v rámci podsítě. Podsíť poskytuje hranici sítě, která se dá použít k uzamknutí příchozího provozu za vysílání zařízení a služeb. Tato instalace umožňuje přijímat přenosy HTTP a HTTPS jenom pro konkrétní nadřazené IP adresy.

Příchozí a odchozí síťový provoz v podsíti se řídí pomocí [skupiny zabezpečení sítě][NetworkSecurityGroups]. Pro řízení příchozího provozu vytvořte pravidla zabezpečení sítě ve skupině zabezpečení sítě. Pak skupině zabezpečení sítě přiřaďte podsíť obsahující App Service Environment.

Po přiřazení skupiny zabezpečení sítě k podsíti se příchozí provoz do aplikací v App Service Environment povoluje nebo zablokuje na základě pravidel povolit a odepřít, která jsou definovaná ve skupině zabezpečení sítě.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Příchozí síťové porty používané v App Service Environment
Před uzamčením příchozího síťového provozu pomocí skupiny zabezpečení sítě si poznáte sadu požadovaných a volitelných síťových portů používaných App Service Environment.  Náhodné ukončení provozu na některých portech může způsobit ztrátu funkčnosti v App Service Environment.

Následující seznam obsahuje porty používané App Service Environment. Všechny porty jsou **TCP**, pokud není výslovně uvedeno jinak:

* 454:  **požadovaný port** používaný infrastrukturou Azure pro správu a údržbu App Servicech prostředí prostřednictvím protokolu TLS.  Neblokovat provoz na tento port.  Tento port je vždycky vázaný na veřejnou virtuální IP adresu pomocného mechanismu.
* 455:  **požadovaný port** používaný infrastrukturou Azure pro správu a údržbu App Servicech prostředí prostřednictvím protokolu TLS.  Neblokovat provoz na tento port.  Tento port je vždycky vázaný na veřejnou virtuální IP adresu pomocného mechanismu.
* 80: výchozí port pro příchozí přenos HTTP do aplikací spuštěných v App Service plánuje v App Service Environment.  Na přihlašování s povoleným interního nástroje je tento port vázán na interního nástroje adresu mechanismu řízení.
* 443: výchozí port pro příchozí přenos TLS do aplikací, které běží v App Service plánuje v App Service Environment.  Na přihlašování s povoleným interního nástroje je tento port vázán na interního nástroje adresu mechanismu řízení.
* 21: řídicí kanál pro FTP.  Pokud se server FTP nepoužívá, můžete tento port bezpečně zablokovat.  Na interního nástroje přihlašování s povolenými možnostmi je možné tento port svázat s interního nástroje adresou pro pomocného mechanismu řízení.
* 990: řídicí kanál pro FTPS.  Pokud se FTPS nepoužívá, můžete tento port bezpečně zablokovat.  Na interního nástroje přihlašování s povolenými možnostmi je možné tento port svázat s interního nástroje adresou pro pomocného mechanismu řízení.
* 10001-10020: datové kanály pro FTP.  Stejně jako u řídicího kanálu můžou být tyto porty bezpečně blokované, pokud se server FTP nepoužívá.  Na interního nástroje přihlašování s povolenými možnostmi je možné tento port svázat s interního nástroje adresou pro pomocného mechanismu.
* 4016: používá se pro vzdálené ladění pomocí sady Visual Studio 2012.  Pokud se funkce nepoužívá, můžete tento port bezpečně zablokovat.  Na přihlašování s povoleným interního nástroje je tento port vázán na interního nástroje adresu mechanismu řízení.
* 4018: používá se pro vzdálené ladění pomocí Visual Studio 2013.  Pokud se funkce nepoužívá, můžete tento port bezpečně zablokovat.  Na přihlašování s povoleným interního nástroje je tento port vázán na interního nástroje adresu mechanismu řízení.
* 4020: používá se pro vzdálené ladění pomocí sady Visual Studio 2015.  Pokud se funkce nepoužívá, můžete tento port bezpečně zablokovat.  Na přihlašování s povoleným interního nástroje je tento port vázán na interního nástroje adresu mechanismu řízení.

## <a name="outbound-connectivity-and-dns-requirements"></a>Odchozí připojení a požadavky na DNS
Aby App Service Environment správně fungovalo, vyžaduje také odchozí přístup k různým koncovým bodům. Úplný seznam externích koncových bodů používaných pomocným mechanismem najdete v části "požadované připojení k síti" v článku [Konfigurace sítě pro ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service prostředí vyžadují pro virtuální síť platnou infrastrukturu DNS.  Pokud se konfigurace DNS po vytvoření App Service Environment změní, mohou vývojáři vynutit App Service Environment, aby si vybrali novou konfiguraci DNS.  Pokud aktivujete restartování prostředí pro prostředí pomocí ikony **restartování** , prostředí zabere novou konfiguraci DNS. (Ikona **restartování** se nachází v horní části okna správy App Service Environment v [Azure Portal][NewPortal].)

Před vytvořením App Service Environment taky doporučujeme, aby se všechny vlastní servery DNS ve virtuální síti nastavily předem.  Pokud se konfigurace DNS virtuální sítě během vytváření App Service Environment změnila, proces vytváření App Service Environment se nezdaří.  Podobně platí, že pokud je k dispozici vlastní server DNS, který je nedosažitelný nebo nedostupný na druhém konci brány sítě VPN, proces vytváření App Service Environment se také nezdaří.

## <a name="creating-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Úplné podrobnosti o tom, jak fungují skupiny zabezpečení sítě, najdete v následujících [informacích][NetworkSecurityGroups].  Níže uvedený příklad správy služeb Azure se dotýká nejdůležitějších skupin zabezpečení sítě. V příkladu se nakonfiguruje a použije skupina zabezpečení sítě pro podsíť, která obsahuje App Service Environment.

**Poznámka:** Skupiny zabezpečení sítě je možné konfigurovat graficky pomocí [Azure Portal](https://portal.azure.com) nebo prostřednictvím Azure PowerShell.

Skupiny zabezpečení sítě se nejdřív vytvoří jako samostatná entita přidružená k předplatnému. Vzhledem k tomu, že skupiny zabezpečení sítě se vytvářejí v oblasti Azure, vytvořte skupinu zabezpečení sítě ve stejné oblasti jako App Service Environment.

Následující příkaz ukazuje vytvoření skupiny zabezpečení sítě:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Po vytvoření skupiny zabezpečení sítě se do ní přidá nejméně jedno pravidlo zabezpečení sítě.  Vzhledem k tomu, že se sada pravidel může v průběhu času měnit, měli byste místo na začátku použít pro priority pravidel. Tento postup usnadňuje vkládání dalších pravidel v průběhu času.

V následujícím příkladu pravidlo explicitně udělí přístup k portům pro správu, které potřebuje infrastruktura Azure ke správě a údržbě App Service Environment.  Všechna přenosová data pro správu se využívají přes protokol TLS a jsou zabezpečená klientskými certifikáty. I když jsou porty otevřené, nejsou dostupné pro žádnou jinou entitu než infrastrukturu pro správu Azure.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Když uzamknete přístup k portům 80 a 443 na "Skrýt" App Service Environment za nadřazenými zařízeními nebo službami, zapamatujte si IP adresu nadřazeného objektu.  Pokud například používáte bránu firewall webových aplikací (WAF), bude mít WAF svou vlastní IP adresu nebo adresy. WAF je používá při proxy provozu do App Service Environment pro příjem dat.  Tuto IP adresu budete muset použít v parametru *SourceAddressPrefix* pravidla zabezpečení sítě.

V následujícím příkladu je příchozí přenos z konkrétní IP adresy pro odesílání výslovně povolen.  Adresa *1.2.3.4* se používá jako zástupný symbol pro IP adresu nadřazeného WAFu.  Změňte hodnotu tak, aby odpovídala adrese používané pro vaše nadřazené zařízení nebo službu.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Pokud je žádoucí podpora FTP, použijte následující pravidla jako šablonu, která udělí přístup k portům ovládacího prvku FTP a portům datového kanálu.  Vzhledem k tomu, že FTP je stavový protokol, možná nebudete moci směrovat přenos FTP prostřednictvím tradiční brány firewall HTTP/HTTPS nebo proxy zařízení.  V takovém případě budete muset nastavit *SourceAddressPrefix* na jinou hodnotu, třeba na rozsah IP adres počítačů pro vývojáře nebo nasazení, na kterých běží klienti FTP. 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

(**Poznámka:**  rozsah portů datového kanálu se může během období Preview změnit.)

Pokud se používá vzdálené ladění pomocí sady Visual Studio, následující pravidla demonstrují, jak udělit přístup.  Pro každou podporovanou verzi sady Visual Studio existuje samostatné pravidlo, protože každá verze používá jiný port pro vzdálené ladění.  Stejně jako při přístupu k FTP nemusí vzdálené ladění provozu bez problémů přesměrovat prostřednictvím tradičního WAF nebo proxy zařízení.  *SourceAddressPrefix* je možné nastavit na rozsah IP adres počítačů vývojářů se sadou Visual Studio.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Přiřazení skupiny zabezpečení sítě k podsíti
Skupina zabezpečení sítě má výchozí pravidlo zabezpečení, které odepře přístup ke všem externím přenosům. Když zkombinujete toto pravidlo s pravidly zabezpečení sítě, bude moct odesílat data do aplikací spuštěných v App Service Environment jenom přenosy ze zdrojových rozsahů adres, které jsou přidružené k akci *Povolení* .

Po naplnění skupiny zabezpečení sítě pomocí pravidel zabezpečení ji přiřaďte k podsíti obsahující App Service Environment.  Příkaz přiřazení odkazuje na dva názvy: název virtuální sítě, ve které je App Service Environment, a název podsítě, ve které byla App Service Environment vytvořena.  

Následující příklad ukazuje skupinu zabezpečení sítě, která je přiřazena k podsíti a virtuální síti:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

Přiřazení je dlouhotrvající operace a dokončení může trvat několik minut. Po úspěšném přiřazení skupiny zabezpečení sítě budou aplikace v App Service Environment úspěšně dostupné jenom příchozí přenosy, které odpovídají pravidlům *Povolení* .

V případě úplnosti ukazuje následující příklad, jak odebrat a zrušit přidružení skupiny zabezpečení sítě k podsíti:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>Zvláštní požadavky na explicitní IP-SSL
Pokud je aplikace nakonfigurovaná s explicitní adresou IP-SSL (platí *jenom* pro služby ASE, která má veřejnou virtuální IP adresu), místo použití výchozí IP adresy App Service Environment přenos přes protokol HTTP i HTTPS do podsítě přes jiné porty než porty 80 a 443.

Pokud chcete najít jednotlivé dvojice portů, které používá každá adresa IP-SSL, přečtěte si na portálu a podívejte se na okno UX s podrobnostmi o App Service Environment.  Vyberte **všechna nastavení**  >  **IP adresy**.  V okně **IP adresy** se zobrazí tabulka všech explicitně nakonfigurovaných adres IP-SSL pro App Service Environment. V okně se zobrazí také speciální dvojice portů, která se používá ke směrování provozu HTTP a HTTPS přidružených ke každé adrese IP-SSL.  Tuto dvojici portů použijte pro parametry DestinationPortRange při konfiguraci pravidel ve skupině zabezpečení sítě.

Když je aplikace v pomocném okně nakonfigurovaná k používání IP-SSL, externí zákazníci neuvidí nebo nebudou muset dělat starosti s mapováním speciálního páru portů.  Provoz do aplikací bude normálně tok nakonfigurované IP adresy SSL.  Překlad speciální dvojice portů automaticky probíhá interně během poslední fáze směrování provozu do podsítě, která obsahuje pomocného mechanismu řízení. 

## <a name="getting-started"></a>Začínáme
Pokud chcete začít pracovat s App Service prostředími, přečtěte si téma [Úvod do App Service Environment][IntroToAppServiceEnvironment].

Další informace najdete v tématu [zabezpečené připojení k back-endu prostředkům z App Service Environment][SecurelyConnecttoBackend].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->