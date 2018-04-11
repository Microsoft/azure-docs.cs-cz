---
title: Aspekty sítě pomocí služby Azure App Service environment
description: Vysvětluje App Service Environment síťový provoz a jak nastavit skupiny Nsg a udr s vaší App Service Environment
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: ccompy
ms.openlocfilehash: 54257ae3e02a00c5097aa7880fa356da3bc0ecce
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Aspekty sítě služby App Service Environment #

## <a name="overview"></a>Přehled ##

 Azure [App Service Environment] [ Intro] nasazení služby Azure App Service na podsíť ve virtuální síti Azure (VNet). Existují dva typy nasazení pro služby App Service environment (App Service Environment):

- **Externí App Service Environment**: zpřístupní aplikace app Service Environment hostované na IP adresu přístupné z Internetu. Další informace najdete v tématu [vytvořit externí App Service Environment][MakeExternalASE].
- **App Service Environment ILB**: zpřístupní aplikace app Service Environment hostované na IP adresu ve virtuální síti. Vnitřní koncový bod je k pro vyrovnávání interní zatížení (ILB), proto volal ILB App Service Environment. Další informace najdete v tématu [vytvoření a použití App Service Environment ILB][MakeILBASE].

Existují dvě verze služby App Service Environment: ASEv1 a ASEv2. Informace o ASEv1 najdete v tématu [Úvod do služby App Service Environment v1][ASEv1Intro]. ASEv1 se dá nasadit v klasický nebo virtuální sítě Resource Manageru. ASEv2 lze nasadit pouze do virtuální sítě Resource Manageru.

Všechna volání ze App Service Environment, které připojuje k Internetu ponechte virtuální sítě pomocí virtuální IP adresu přiřazené App Service Environment. Veřejné IP adresy z této VIP je pak zdrojové IP adresy pro všechna volání z App Service Environment, které připojuje k Internetu. Pokud aplikace, které vaše App Service Environment provádět volání k prostředkům ve vaší virtuální síti nebo přes síť VPN, je jedním z IP adresy v podsíti používané vaší App Service Environment zdrojové IP adresy. Protože App Service Environment není v rámci virtuální sítě, ho také přístup k prostředkům v rámci virtuální sítě bez jakékoli dodatečné konfigurace. Pokud virtuální sítě připojen k síti na pracovišti, aplikací ve vaší App Service Environment také mít přístup k prostředkům existuje. Nemusíte konfigurovat App Service Environment nebo aplikace pro všechny další.

![Externí App Service Environment][1] 

Pokud máte externí App Service Environment, veřejné VIP je také koncového bodu, který aplikace app Service Environment řešení pro:

* HTTP/S. 
* FTP/S. 
* Nasazení webu.
* Vzdálené ladění.

![ILB APP SERVICE ENVIRONMENT][2]

Pokud máte App Service Environment ILB, IP adresa ILB je koncový bod pro protokol HTTP/S, FTP/S, nasazení webu a vzdálené ladění.

Normální aplikace přístupové porty jsou:

| Použití | Od | Akce |
|----------|---------|-------------|
|  HTTP/HTTPS  | Konfigurovatelná uživatelem |  80, 443 |
|  FTP/FTPS    | Konfigurovatelná uživatelem |  21, 990, 10001-10020 |
|  Visual Studio vzdálené ladění  |  Konfigurovatelná uživatelem |  4016, 4018, 4020, 4022 |

To platí, pokud jste na externí App Service Environment nebo ILB App Service Environment. Pokud jste na externí App Service Environment, stiskněte tlačítko tyto porty na veřejných virtuálních IP adres. Pokud jste v App Service Environment ILB, stiskněte tlačítko tyto porty na ILB. Pokud se uzamknout port 443, může být dopad na některé funkce umístěné na portálu. Další informace najdete v tématu [portál závislosti](#portaldep).

## <a name="ase-subnet-size"></a>Velikost podsítě App Service Environment ##

Velikost podsítě používané k hostování App Service Environment nelze změnit po nasazení App Service Environment.  App Service Environment používá adresu pro každou infrastruktury roli stejně jako u každé instance plán izolované aplikační služby.  Kromě toho jsou 5 adresy používané pro každou podsíť, která je vytvořena sítí Azure.  App Service Environment se žádné plány služby App Service na všech použije 12 adresy před vytvořením aplikace.  Pokud je App Service Environment ILB pak použije 13 adresy předtím, než vytvoříte aplikaci v této App Service Environment. Jako škálovat plánu aplikace služby bude vyžadovat další adresy pro každý front-end, který je přidán.  Ve výchozím nastavení jsou servery front-end přidány pro každých 15 celkový počet instancí plánu služby App Service. 

   > [!NOTE]
   > V podsíti, ale App Service Environment může být nic jiného. Je třeba zvolit adresní prostor, který umožňuje růstem do budoucna. Nelze změnit, tato nastavení později. Doporučujeme velikost `/25` adresy 128.

## <a name="ase-dependencies"></a>Závislosti App Service Environment ##

App Service Environment příchozí přístup závislostí je:

| Použití | Od | Akce |
|-----|------|----|
| Správa | Adresy pro správu služby aplikace | Podsíť App Service Environment: 454, 455 |
|  Interní komunikaci App Service Environment | Podsíť App Service Environment: všechny porty | Podsíť App Service Environment: všechny porty
|  Povolit pro vyrovnávání zatížení Azure příchozí | Nástroj pro vyrovnávání zatížení Azure | Podsíť App Service Environment: všechny porty
|  Aplikace přiřazené IP adresy | Aplikace, které jsou přiřazeny adresy | Podsíť App Service Environment: všechny porty

Příchozí provoz poskytuje příkazy a ovládání App Service Environment kromě sledování systému. Zdroj IP adres pro tyto přenosy dat jsou uvedené v [adresy App Service Environment správu] [ ASEManagement] dokumentu. Konfigurace zabezpečení sítě je potřeba povolit přístup ze všech IP adres na portech 454 a 455.

V rámci podsítě App Service Environment nejsou mnoho porty používané pro interní součást komunikace a můžete změnit.  To vyžaduje všechny porty v podsíti App Service Environment, aby mohl být dostupný z podsítě App Service Environment. 

Pro komunikaci mezi nástroje pro vyrovnávání zatížení Azure a podsíť App Service Environment jsou minimální porty, které musí být otevřený 454 a 455 16001. 16001 port se používá pro zachování připojení provoz mezi nástroje pro vyrovnávání zatížení a App Service Environment. Pokud používáte App Service Environment ILB pak zamknete provoz dolů právě 454, 455, 16001 porty.  Pokud používáte externí App Service Environment budete muset vzít v úvahu přístupové porty normální aplikace.  Pokud používáte aplikaci přiřazenou adresy budete muset otevřít na všechny porty.  Jakmile bude přiřazena adresa pro konkrétní aplikaci, nástroj pro vyrovnávání zatížení bude používat porty, které nejsou známé z předem odeslat HTTP a HTTPS provozy App Service Environment.

Pokud používáte aplikace přiřazené IP adresy, budete muset povolit přenosy z přiřazené vašich aplikací App Service Environment podsítě IP adres.

Pro odchozí přístup App Service Environment závisí na několika externími systémy. Tyto závislosti systému jsou definovány s názvy DNS a nejsou mapovány na sadu pevné IP adresy. Proto App Service Environment vyžaduje přístup z podsítě App Service Environment všechny externí IP adres napříč různými porty. App Service Environment obsahuje následující odchozí závislosti:

| Použití | Od | Akce |
|-----|------|----|
| Azure Storage | Podsíť App Service Environment | Table.Core.Windows.NET, blob.core.windows.net, queue.core.windows.net, file.core.windows.net: 80, 443, 445 (445 je potřeba jenom pro ASEv1.) |
| Azure SQL Database | Podsíť App Service Environment | Database.Windows.NET: 1433, 11000 11999, 14000 14999 (Další informace najdete v tématu [využití portu SQL Database verze 12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).)|
| Správa Azure | Podsíť App Service Environment | management.core.windows.net, management.azure.com: 443 
| Ověření certifikátu SSL |  Podsíť App Service Environment            |  ocsp.msocsp.com, mscrl.microsoft.com, crl.microsoft.com: 443
| Azure Active Directory        | Podsíť App Service Environment            |  Internet: 443
| Správa služby App Service        | Podsíť App Service Environment            |  Internet: 443
| Azure DNS                     | Podsíť App Service Environment            |  Internet: 53
| Interní komunikaci App Service Environment    | Podsíť App Service Environment: všechny porty |  Podsíť App Service Environment: všechny porty

Pokud App Service Environment ztratí přístup k tyto závislosti, přestane fungovat. Pokud k tomu dojde dost dlouho, je pozastaven App Service Environment.

### <a name="customer-dns"></a>Zákazník DNS ###

Pokud jsou virtuální síť nakonfigurované zákazník definovaný server DNS, úlohy klientů ji použít. App Service Environment se stále musí komunikovat s Azure DNS pro účely správy. 

Pokud jsou virtuální síť nakonfigurované zákazník DNS na druhé straně sítě VPN, musí být dostupný z podsítě, která obsahuje App Service Environment serverem DNS.

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portál závislosti ##

Kromě funkční závislosti App Service Environment existuje několik další položky týkající se práce s portálem. Některé z možností na portálu Azure závisí na přímý přístup k _SCM lokality_. Pro každou aplikaci ve službě Azure App Service existují dvě adresy URL. První adresa URL je přístup k vaší aplikaci. Druhý adresa URL je pro přístup k webu SCM, což se označuje taky jako _Kudu konzoly_. Funkce, které používají SCM lokality:

-   Webové úlohy
-   Funkce
-   Streamování protokolů
-   Kudu
-   Rozšíření
-   Průzkumník procesů
-   Konzola

Pokud používáte App Service Environment ILB, lokalita SCM není dostupné z oblasti mimo virtuální sítě internet. Pokud vaše aplikace je hostitelem App Service Environment ILB, některé funkce nebudou fungovat z portálu.  

Mnoho z těchto možností, které závisí na webu SCM jsou k dispozici přímo v konzole Kudu. Můžete se připojit k němu přímo, a nikoli pomocí portálu. Pokud je vaše aplikace hostovaná v App Service Environment ILB, používáte pro přihlášení vaše přihlašovací údaje pro publikování. Adresa URL pro přístup k webu SCM aplikace hostované v App Service Environment ILB má následující formát: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Pokud vaše App Service Environment ILB je název domény *contoso.net* a je název vaší aplikace *testapp*, aplikace je k dispozici na adrese *testapp.contoso.net*. SCM webu, která jde s ním je k dispozici na adrese *testapp.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>Funkce a webové úlohy ##

Funkce a webové úlohy závisí na webu SCM, ale jsou podporovány pro použití na portálu, i v případě, že vaše aplikace nacházely ve ILB App Service Environment, dokud bude prohlížeč dosáhnout SCM lokality.  Pokud používáte certifikát podepsaný svým držitelem s vaší App Service Environment ILB, musíte povolit prohlížeč důvěřovat certifikátu.  Pro aplikaci Internet Explorer a Microsoft Edge, tzn. certifikát musí být v úložišti počítače vztah důvěryhodnosti.  Pokud používáte Chrome, pak to znamená, že jste dříve přijali certifikát v prohlížeči zasažení pravděpodobně z důvodu webu scm přímo.  Nejlepším řešením je použití komerční certifikát, který je v prohlížeči řetěz certifikátů.  

## <a name="ase-ip-addresses"></a>App Service Environment IP adresy ##

App Service Environment má několik IP adres znát. Jsou to tyto:

- **Veřejná IP adresa příchozí**: použít pro provoz aplikace v externím App Service Environment a přenosy správy v App Service Environment ILB i externí App Service Environment.
- **Odchozí veřejnou IP adresu**: používá jako IP adresa "od" pro odchozí připojení z App Service Environment která opouští virtuální sítě, které nejsou směrovány dolů sítě VPN.
- **ILB IP adresu**: Pokud používáte ILB App Service Environment.
- **Přiřazené aplikace založená na IP adresy**: jedinou možnou s externí App Service Environment a je nakonfigurovaný na základě IP protokol SSL.

Tyto IP adresy jsou snadno viditelná v ASEv2 na portálu Azure z rozhraní App Service Environment. Pokud máte App Service Environment ILB, IP adresu pro ILB je uvedený.

![IP adresy][3]

### <a name="app-assigned-ip-addresses"></a>Aplikace přiřazené IP adresy ###

S externí App Service Environment můžete přiřadit IP adresy pro jednotlivé aplikace. Nejde udělat s ILB App Service Environment. Další informace o tom, jak nakonfigurovat aplikaci, aby měla vlastní IP adresu, najdete v části [vazby stávající vlastní certifikát SSL pro službu Azure web apps](../app-service-web-tutorial-custom-ssl.md).

Pokud aplikace má vlastní založená na IP adresu, App Service Environment si vyhrazuje dva porty pro mapování na tuto IP adresu. Je jeden port pro přenos HTTP a další port, který je pro protokol HTTPS. Tyto porty jsou uvedeny v uživatelském rozhraní App Service Environment v části IP adresy. Přenosy musí být schopný dosáhnout tyto porty z virtuální IP adresu nebo aplikace jsou nedostupné. Tento požadavek je důležité si pamatovat při konfiguraci skupin zabezpečení sítě (Nsg).

## <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě) ##

[Skupin zabezpečení sítě] [ NSGs] umožňují řídit přístup k síti v rámci virtuální sítě. Pokud používáte portál, je na nejnižší prioritu tak, aby odepřel všechno, co pravidlo implicitní odepřít. Můžete vytvořit jsou vaše povolí pravidla.

V App Service Environment nemáte přístup k virtuálním počítačům použity k hostování App Service Environment, sám sebe. Jsou v předplatném spravovaný společností Microsoft. Pokud chcete omezit přístup k aplikacím na App Service Environment, nastavte skupiny Nsg na podsítě App Service Environment. Při tom věnujte pozornost pečlivě závislosti App Service Environment. Pokud zablokujete všechny závislosti, App Service Environment přestane fungovat.

Skupiny Nsg se dá nakonfigurovat pomocí portálu Azure nebo pomocí prostředí PowerShell. Zde uvedené informace zobrazí na portálu Azure. Vytvořit a spravovat skupiny Nsg na portálu jako prostředek nejvyšší úrovně v rámci **sítě**.

Pokud jsou požadavky na příchozí a odchozí vzít v úvahu, by mělo vypadat jako skupiny Nsg uvedeno v tomto příkladu skupin Nsg. Rozsah adres virtuální sítě je _192.168.250.0/23_, a podsíť, který je App Service Environment ve _192.168.251.128/25_.

První dva příchozí požadavky pro App Service Environment funkce se zobrazí v horní části seznamu v tomto příkladu. Jejich umožňuje správu App Service Environment a App Service Environment pro komunikaci se sám sebe. Jiné položky jsou všechny konfigurovatelné klienta a můžou řídit přístup k síti pro aplikace spouštěné v App Service Environment. 

![Příchozí pravidla zabezpečení][4]

Výchozí pravidlo umožňuje IP adresy ve virtuální síti, aby komunikoval s App Service Environment podsítě. Jiné výchozí pravidlo umožňuje Vyrovnávání zatížení, také známé jako veřejné VIP, ke komunikaci s App Service Environment. Pokud chcete zobrazit výchozí pravidla, vyberte **výchozí pravidla** vedle **přidat** ikonu. Když vložíte odepření všechno ostatní pravidla po pravidla NSG uvedené, abyste zabránili provoz mezi virtuální IP adresu a App Service Environment. Pokud chcete zabránit provoz přicházející ze uvnitř virtuální sítě, přidáte vlastní pravidlo povolující příchozí. Použít zdroj rovna AzureLoadBalancer s cílovým serverem **žádné** a rozsah portů **\***. Protože pravidla NSG se použije k podsíti App Service Environment, nemusíte být v cílovém specifické.

Pokud jste přiřadili IP adresu do vaší aplikace, zajistěte, aby že nechat otevřené porty. Pokud chcete zobrazit porty, vyberte **App Service Environment** > **IP adresy**.  

Všechny položky, které jsou uvedené v následující odchozí pravidla, je potřeba, s výjimkou poslední položky. Umožňují přístup k síti do app Service Environment závislosti, které jste si poznamenali dříve v tomto článku. Pokud některý z nich zablokujete, vaše App Service Environment přestane fungovat. Poslední položky v seznamu umožňuje vaší App Service Environment ke komunikaci s další prostředky ve virtuální síti.

![Odchozí pravidla zabezpečení][5]

Po skupin Nsg jsou definovány, přiřadíte k podsíti, který vaše App Service Environment je na. Pokud si nepamatujete App Service Environment virtuální síť nebo podsíť, uvidíte na stránce portálu App Service Environment. Chcete-li přiřadit NSG pro podsíť, přejděte k podsíti uživatelského rozhraní a vyberte NSG.

## <a name="routes"></a>Trasy ##

Vynucené tunelování je nastavena tras ve vaší virtuální síti, nebude odchozí přenosy přejděte přímo k Internetu, ale jinde jako bránu ExpressRoute nebo virtuální zařízení.  Pokud potřebujete nakonfigurovat vaše App Service Environment takovým způsobem, pak dokument číst na [konfigurace služby App Service Environment se vynucené tunelování][forcedtunnel].  Tento dokument vás bude informovat možnosti práce s ExpressRoute a vynucené tunelování.

Při vytváření App Service Environment portálu jsme také vytvořit sadu směrovací tabulky na podsíť, která je vytvořena pomocí App Service Environment.  Tyto trasy, že jednoduše odeslat odchozí přenos přímo k Internetu.  
Ruční vytvoření tras, postupujte takto:

1. Přejděte na portálu Azure. Vyberte **sítě** > **směrovacích tabulek**.

2. Vytvořte novou tabulku směrování ve stejné oblasti jako virtuální síť.

3. V rámci tabulky tras uživatelského rozhraní, vyberte **trasy** > **přidat**.

4. Nastavte **typ dalšího směrování** k **Internet** a **předpona adresy** k **0.0.0.0/0**. Vyberte **Uložit**.

    Zobrazí se přibližně takto:

    ![Funkční trasy][6]

5. Když vytvoříte nový směrovací tabulka, přejděte na podsíť, která obsahuje vaše App Service Environment. Vyberte směrovací tabulku ze seznamu na portálu. Po uložení změn, měli byste vidět pak skupiny Nsg a trasy zaznamenán s podsíť.

    ![Skupiny Nsg a trasy][7]

## <a name="service-endpoints"></a>Koncové body služeb ##

Koncové body služby umožňují omezit přístup k víceklientským službám na sadu virtuálních sítí a podsítí Azure. Další informace o koncových bodech služby najdete v dokumentaci pro [koncové body služby virtuální sítě][serviceendpoints]. 

Když pro prostředek povolíte koncové body služby, vytvoří se trasy s vyšší prioritou než všechny ostatní trasy. Pokud použijete koncové body služby se službou ASE s vynuceným tunelováním, nebude se vynucovat tunelování provozu správy SQL Azure a služby Azure Storage. 

Pokud jsou koncové body služby povolené v podsíti s instancí SQL Azure, musí mít koncové body služby povolené i všechny instance SQL Azure, ke kterým se z této podsítě připojuje. Pokud chcete ze stejné podsítě přistupovat k několika instancím SQL Azure, není možné povolit koncové body služby v jedné instanci SQL Azure a v jiné ne. Služba Azure Storage se nechová stejně jako SQL Azure. Když povolíte koncové body služby se službou Azure Storage, uzamknete přístup k danému prostředku z vaší podsítě, ale stále budete mít přístup k ostatním účtům služby Azure Storage, a to i v případě, že nemají povolené koncové body služby.  

![Koncové body služeb][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
