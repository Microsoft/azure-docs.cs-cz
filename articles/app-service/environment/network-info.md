---
title: Důležité informace o sítích s App Service Environment – Azure
description: Vysvětluje síťový provoz služby ASE a jak nastavit skupiny zabezpečení sítě a trasy definované uživatelem s vaší služby ASE
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
ms.date: 05/31/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3f80f3c6be747cf84aa9d8b2c386c0568a7511ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069382"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Důležité informace o sítích pro službu App Service Environment #

## <a name="overview"></a>Přehled ##

 Azure [služby App Service Environment] [ Intro] je nasazení služby Azure App Service do podsítě ve službě Azure virtual network (VNet). Existují dva typy nasazení pro App Service environment (ASE):

- **Externí služby ASE**: Poskytuje hostované služby ASE aplikace na IP adresu přístupné z Internetu. Další informace najdete v tématu [vytvoření externí služby ASE][MakeExternalASE].
- **SLUŽBA ASE S ILB**: Poskytuje služby ASE hostované aplikace na IP adresu uvnitř virtuální sítě. Vnitřní koncový bod je interní nástroj pro vyrovnávání zatížení (ILB), což je důvod, proč se používá označení službu ASE. Další informace najdete v tématu [vytvoření a použití prostředí ILB ASE][MakeILBASE].

Všechny služby ase, externí a ILB, mají veřejná virtuální IP adresy, který se používá k řízení příchozích přenosů, jako z adresy při volání ze služby ASE k Internetu. Volání ze služby ASE, která se připojuje k Internetu, ponechejte virtuální sítě pomocí virtuální IP adresy přiřazené pro danou službu ASE. Veřejnou IP Adresou této virtuálních IP adres je zdrojová IP adresa pro všechna volání ze služby ASE, které připojuje k Internetu. Pokud aplikace ve vaší službě ASE provést volání k prostředkům ve vaší virtuální síti nebo přes síť VPN, Zdrojová IP adresa je jednou z IP adresy v podsíti používaným vaší službou ASE. Vzhledem k tomu, že služba ASE je v rámci virtuální sítě, dostanete také prostředky v rámci virtuální sítě bez jakékoli dodatečné konfigurace. Pokud virtuální síť je připojená k vaší místní síti, aplikace ve vaší službě ASE také mají přístup k prostředkům existuje bez další konfigurace.

![Externí služby ASE][1] 

Pokud už máte externí služby ASE, je koncový bod, který vaše aplikace služby ASE přeložit pro také veřejných virtuálních IP adres:

* HTTP/S 
* FTP/S
* Nasazení webu
* Vzdálené ladění

![SLUŽBA ASE S ILB][2]

Pokud máte službu ASE, je adresa ILB adresy koncových bodů HTTP/S, FTP/S, nasazení webu a vzdálené ladění.

## <a name="ase-subnet-size"></a>Velikost podsítě služby ASE ##

Po nasazení služby ASE se nedá změnit velikost podsítě používané k hostování služby ASE.  Služby ASE používá adresu pro každou roli infrastruktury stejně jako u každé instance plánu izolované služby App Service.  Kromě toho jsou pět adres používané sítí Azure pro každou podsíť, která je vytvořena.  Služba ASE se žádné plány služby App Service vůbec používat 12 adres před vytvořením aplikace.  Pokud službu ASE, pak použije 13 adresy předtím, než vytvoříte aplikaci v této službě ASE. Jak škálovat svoji službu ASE, infrastrukturu role jsou přidány každý násobky 15 až 20 instancí plánu služby App Service.

   > [!NOTE]
   > V podsíti, ale služba ASE může být nic dalšího. Je potřeba zvolit adresní prostor, který umožňuje na budoucí růst. Toto nastavení později nejde změnit. Doporučujeme velikost `/24` s 256 adres.

Když škálujete směrem nahoru nebo dolů, se přidají nové role odpovídající velikost a pak se vaše úlohy migrují z aktuální velikost pro cílovou velikost. Původní virtuální počítače odebrány až po migraci úloh. Pokud máte službu ASE s 100 instancí ASP, bude tečku tam, kde potřebujete double počet virtuálních počítačů.  Je proto doporučujeme použití lomítkem (/ 24) tak, aby vyhovovaly všechny změny, které můžete potřebovat.  

## <a name="ase-dependencies"></a>Závislostí služby ASE ##

### <a name="ase-inbound-dependencies"></a>Služba ASE příchozí závislosti ###

Jenom pro službu ASE k provozu služby ASE vyžaduje následující porty otevřené:

| Použití | Z | Do |
|-----|------|----|
| Správa | Adresy pro správu aplikace app Service | Podsíti služby ASE: 454, 455 |
|  Interní komunikace služby ASE | Podsíti služby ASE: Všechny porty | Podsíti služby ASE: Všechny porty
|  Povolit nástroji Azure load balancer příchozí | Nástroj pro vyrovnávání zatížení Azure | Podsíti služby ASE: 16001

Existují 2 porty, které můžete zobrazit jako otevřený na portu kontroly 7654 a 1221. Tyto odpovídají IP adresu a nic víc. Může být blokované v případě potřeby. 

Řízení příchozích přenosů poskytuje příkazy a ovládání služby ase kromě systému sledování. Zdrojové adresy pro tento provoz jsou uvedeny v [adresy služby ASE správu] [ ASEManagement] dokumentu. Konfigurace zabezpečení sítě je potřeba povolit přístup z adresy pro správu služby ASE na portech 454 a 455. Pokud zablokujete přístup z těchto adres, vaše služba ASE přestane není v pořádku a pak zablokuje. Provoz TCP, který je k dispozici ve na portech 454 a 455 musí vracet ze stejné virtuální IP adresy nebo je nutné kvůli problému asymetrického směrování. 

V podsíti služby ASE jsou mnoho portech používaných ke komunikaci interní komponenty a můžete změnit. To vyžaduje všechny porty v podsíti služby ASE byla přístupná z podsítě služby ASE. 

Minimální porty, které musí být otevřené pro komunikaci mezi Azure load balancer a podsíti služby ASE jsou 454 a 455 16001. 16001 port je používán pro keep alive přenosy mezi nástroje pro vyrovnávání zatížení a služby ASE. Pokud používáte službu ASE, pak můžete uzamknout provoz na právě 454, 455, 16001 porty.  Pokud používáte externí služby ASE, budete muset vzít v úvahu přístupové porty normální aplikace.  

Ostatní porty budete muset sami se týkají jsou porty aplikace:

| Použití | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio vzdálené ladění  |  4020, 4022, 4024 |
|  Webová služba pro nasazení | 8172 |

Pokud zablokujete porty aplikací, vaše služba ASE může i nadále fungovat, ale vaše aplikace nemusí.  Pokud používáte aplikace přiřazené IP adresy s externí služby ASE, budete muset povolit přenosy z IP adres přiřazených k vašim aplikacím k podsíti služby ASE na porty uvedené na portálu služby ASE > stránky IP adresy.

### <a name="ase-outbound-dependencies"></a>Odchozí závislostí služby ASE ###

Pro odchozí přístup k službě ASE závisí na více externích systémů. Mnohé z těchto systémové závislosti jsou definovány s názvy DNS a nejsou mapovány na pevnou sadu IP adres. Služba ASE vyžaduje, aby odchozí přístup z podsítě služby ASE pro všechny externí IP adresy na různých portů. 

Služba ASE komunikuje navýšení kapacity k internetovým adresám přístupné na následující porty:

| Využití | Porty |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| 8CRL Windows, Linux závislosti, aktualizace služby Azure | 80/443 |
| Azure SQL | 1433 | 
| Monitorování | 12000 |

Odchozí závislosti jsou uvedené v tomto dokumentu, který popisuje [omezovat se jenom odchozí provoz služby App Service Environment](./firewall-integration.md). Pokud službu ASE ztratí přístup k jeho závislostí, přestane fungovat. Pokud k tomu dojde dostatečně dlouho, služba ASE je pozastaveno. 

### <a name="customer-dns"></a>Zákazník DNS ###

Pokud virtuální síť nakonfigurována se serverem DNS definované zákazníkem, úlohy klientů použít. Služby ASE používá službu Azure DNS pro účely správy. Pokud virtuální síť má nakonfigurovanou zákazníka vybraný server DNS, DNS server musí být dostupný z podsítě, která obsahuje službu ASE.

Otestujte překlad DNS vaší webové aplikace, můžete příkaz konzoly *nameresolver*. Přejděte na okno ladění na vašem webu scm pro vaši aplikaci nebo přejít do aplikace na portálu a vyberte konzoly. Z příkazového řádku prostředí můžete vydat příkaz *nameresolver* spolu s názvem DNS, kterou chcete vyhledat. Které získáte zpět výsledek je stejný jako co by vaše aplikace získá při nastavování stejné vyhledávání. Pokud používáte nslookup, provede vyhledávání pomocí služby Azure DNS místo toho.

Pokud změníte nastavení serveru DNS virtuální sítě, vaše služba ASE je v, musíte restartovat službu ASE. Abyste se vyhnuli restartování vaší služby ASE, důrazně doporučujeme konfigurovat nastavení DNS pro vaši virtuální síť, před vytvořením služby ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Závislosti portálu ##

Kromě funkční závislostí služby ASE se několik další položky týkající se práce s portálem. Některé funkce na webu Azure Portal závisí na přímý přístup k _web SCM_. Pro každou aplikaci ve službě Azure App Service jsou dvě adresy URL. První adresa URL je přístup k vaší aplikaci. Je druhý adresa URL pro přístup k webu SCM, což se označuje taky jako _konzola Kudu_. Funkce, které používají web SCM:

-   Webové úlohy
-   Funkce
-   Streamování protokolů
-   Kudu
-   Rozšíření
-   Průzkumník procesů
-   Konzola

Pokud používáte službu ASE, web SCM není dostupné z oblasti mimo virtuální síť. Některé funkce nebudou fungovat z portálu pro aplikace, protože vyžadují přístup k webu SCM aplikace. Můžete připojit k webu SCM přímo namísto použití na portálu. 

Pokud vaše služba ASE s ILB je název domény *contoso.appserviceenvironnment.net* a je název vaší aplikace *testapp*, aplikace je dosažena *testapp.contoso.appserviceenvironment.net*. Správce řízení služeb webu, která jde s ním je dosažena *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>Služba ASE IP adresy ##

Služba ASE má několik IP adres je potřeba vědět. Jsou to tyto:

- **Veřejnou IP adresu příchozího**: Používá se pro aplikaci externí službě ase a provoz správy v externí službě ASE a službu ASE.
- **Odchozí veřejnou IP adresu**: Použít jako IP adresa "od" pro odchozí připojení ze služby ASE, které ponechejte virtuální sítě, které nejsou směrovány dolů sítě VPN.
- **Adresa ILB IP**: ILB IP adresu existuje pouze ve službě ASE s ILB.
- **Aplikace přiřazená SSL na základě IP adresy**: Provádět jen s externí služby ASE, a pokud je nakonfigurovaný protokol SSL na základě IP adresy.

Tyto IP adresy jsou viditelné na webu Azure Portal v uživatelském rozhraní služby ASE. Pokud máte službu ASE, je uvedena IP adresa pro ILB.

   > [!NOTE]
   > Tyto IP adresy se nezmění, tak dlouho, dokud vaše služba ASE zůstane zprovozněný.  Pokud vaše služba ASE bude pozastavit a obnovit, adresy používané službou ASE se změní. Běžné příčiny službu ase k zablokuje je-li blokovat přístup k řízení příchozích nebo blokovat přístup k závislostí služby ASE. 

![IP adresy][3]

### <a name="app-assigned-ip-addresses"></a>Aplikace přiřazená IP adresy ###

S externí služby ASE můžete přiřadit IP adresy pro jednotlivé aplikace. Nemůžete udělat s ILB ASE. Další informace o tom, jak nakonfigurovat aplikaci tak, aby mít svou vlastní IP adresu najdete v tématu [vytvoření vazby existujícího vlastního certifikátu SSL do služby Azure App Service](../app-service-web-tutorial-custom-ssl.md).

Pokud aplikace má vlastní SSL založené na protokolu IP adresu, službu ASE rezervuje dva porty pro mapování na tuto IP adresu. Jeden port je pro provoz protokolu HTTP a je jiný port pro protokol HTTPS. Tyto porty jsou uvedené v uživatelském rozhraní služby ASE v části IP adresy. Provoz musí být schopen kontaktovat tyto porty z virtuální IP adresy nebo aplikace nejsou dostupné. Tento požadavek je důležité si pamatovat, když konfigurujete skupiny zabezpečení sítě (Nsg).

## <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě) ##

[Skupiny zabezpečení sítě] [ NSGs] poskytnout možnost řídit přístup k síti v rámci virtuální sítě. Při použití na portálu se s nejnižší prioritou na Zamítnout vše, co pravidlo odepřít implicitní. Co je vytvořit jsou vaše pravidla povolit.

Ve službě ASE nemáte přístup k virtuálním počítačům, které používají k hostování služby ASE, samotného. Jsou to v rámci předplatného spravovaných microsoftem. Pokud chcete omezit přístup k aplikacím služby ase, nastavte skupiny zabezpečení sítě v podsíti služby ASE. Přitom platíte důkladnou pozornost závislostí služby ASE. Pokud zablokujete všechny závislosti, že služba ASE přestane fungovat.

Skupiny zabezpečení sítě se dá nakonfigurovat na webu Azure portal nebo přes PowerShell. Zde uvedené informace se zobrazí na webu Azure portal. Vytvoření a Správa skupin zabezpečení sítě na portálu jako prostředek nejvyšší úrovně v rámci **sítě**.

Požadované položky v skupinu zabezpečení sítě pro službu ASE na funkci, jsou pro povolení provozu:

**Příchozí**
* značka AppServiceManagement na portech 454,455 služby z IP adresa
* z nástroje pro vyrovnávání zatížení na portu 16001
* z podsítě služby ASE k podsíti služby ASE na všech portech

**Odchozí**
* pro všechny IP adresy na port 123
* pro všechny IP adresy na porty 80 a 443
* značka AzureSQL porty 1433 na IP adresu služby
* pro všechny IP adresy na port 12000
* k podsíti služby ASE na všech portech

DNS port není nutné přidat, protože přenosy do DNS nemá vliv pravidla skupiny zabezpečení sítě. Tyto porty nezahrnují porty, které vaše aplikace potřebuje pro úspěšné používání. Běžná aplikace přístupové porty jsou:

| Použití | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio vzdálené ladění  |  4020, 4022, 4024 |
|  Webová služba pro nasazení | 8172 |

Pokud příchozí a odchozí požadavky jsou vzít v úvahu, vypadat podobně jako na skupiny zabezpečení sítě v tomto příkladu skupiny zabezpečení sítě. 

![Příchozí pravidla zabezpečení][4]

Výchozí pravidlo umožňuje IP adresy ve virtuální síti komunikovat s podsíti služby ASE. Další výchozí pravidlo umožňuje nástroje pro vyrovnávání zatížení, označované také jako veřejnou virtuální IP adresy, ke komunikaci se službou ASE. Pokud chcete zobrazit výchozí pravidla, vyberte **výchozí pravidla** vedle **přidat** ikonu. Když vložíte odepřít všechno ostatní pravidla než výchozí pravidla, zabránit provoz mezi virtuální IP adresy a služby ASE. Chcete-li zabránit provoz přicházející z uvnitř virtuální sítě, přidejte vlastní pravidlo, které povolí příchozí. Použít prostředek rovna AzureLoadBalancer s cílovou **jakékoli** a rozsah portů **\*** . Vzhledem k tomu, že pravidlo NSG, je použita na podsíti služby ASE, nemusíte být konkrétní v cílovém umístění.

Pokud jste IP adresy přiřazené vaší aplikaci, ujistěte se, že můžete ponechat porty otevřené. Pokud chcete zobrazit porty, vyberte **služby App Service Environment** > **IP adresy**.  

Všechny položky uvedené v následující odchozí pravidla jsou potřeba, s výjimkou poslední položky. Umožňují přístup k síti závislostí služby ASE, které jste si poznamenali dříve v tomto článku. Pokud některý z nich, vaše služba ASE přestane fungovat. Poslední položku v seznamu umožňuje vaší služby ASE ke komunikaci s ostatními prostředky ve virtuální síti.

![Odchozí pravidla zabezpečení][5]

Jakmile vaše skupiny zabezpečení sítě jsou definovány, je přiřadíte k podsíti, ke které vaše služba ASE je v. Pokud si nepamatujete ASE virtuální síť nebo podsíť, zobrazí se na stránce portálu služby ASE. Přiřazení skupiny zabezpečení sítě k podsíti služby, přejděte k podsíti uživatelského rozhraní a vyberte skupiny zabezpečení sítě.

## <a name="routes"></a>Trasy ##

Vynucené tunelování znamená nastavit trasy ve virtuální síti, odchozí provoz nemá přejít přímo na Internetu, ale někde jinde jako bránu ExpressRoute nebo virtuální zařízení.  Pokud je potřeba nakonfigurovat službu ASE takovým způsobem, pak si přečtěte dokument [konfigurace služby App Service Environment s vynuceným tunelovým propojením][forcedtunnel].  Tento dokument vám sdělí možnosti dostupné pro práci s ExpressRoute a vynucené tunelování.

Při vytváření služby ASE na portálu také vytvoříme sadu směrovací tabulky podsítě, který je vytvořen pomocí služby ASE.  Tyto trasy se jednoduše Řekněme, že odesílání odchozí provoz přímo k Internetu.  
Ruční vytvoření tras, postupujte podle těchto kroků:

1. Přejděte na web Azure Portal. Vyberte **sítě** > **směrovací tabulky**.

2. Vytvoření nové směrovací tabulky ve stejné oblasti jako virtuální síť.

3. Ve směrovací tabulce uživatelského rozhraní, **trasy** > **přidat**.

4. Nastavte **typem dalšího segmentu směrování** k **Internet** a **předponu adresy** k **0.0.0.0/0**. Vyberte **Uložit**.

    Zobrazí se přibližně takto:

    ![Funkční trasy][6]

5. Po vytvoření nové směrovací tabulky, přejděte na podsíť, která obsahuje vaše služba ASE. Směrovací tabulka vyberte ze seznamu na portálu. Po uložení změn, měli byste vidět pak skupin zabezpečení sítě a tras, které jsou uvedené s vaší podsítě.

    ![Skupiny Nsg a trasy][7]

## <a name="service-endpoints"></a>Koncové body služeb ##

Koncové body služby umožňují omezit přístup k víceklientským službám na sadu virtuálních sítí a podsítí Azure. Další informace o koncových bodech služby najdete v dokumentaci pro [koncové body služby pro virtuální síť][serviceendpoints]. 

Když pro prostředek povolíte koncové body služby, vytvoří se trasy s vyšší prioritou než všechny ostatní trasy. Pokud použijete koncové body služby pro libovolnou službu Azure, pomocí vynuceného tunelového propojení ASE, provoz do těchto služeb nebudou vynuceného tunelového propojení. 

Pokud jsou koncové body služby povolené v podsíti s instancí SQL Azure, musí mít koncové body služby povolené i všechny instance SQL Azure, ke kterým se z této podsítě připojuje. Pokud chcete ze stejné podsítě přistupovat k několika instancím SQL Azure, není možné povolit koncové body služby v jedné instanci SQL Azure a v jiné ne. Žádné další služby Azure se chová jako SQL Azure s ohledem na koncové body služby. Když povolíte koncové body služby se službou Azure Storage, uzamknete přístup k danému prostředku z vaší podsítě, ale stále budete mít přístup k ostatním účtům služby Azure Storage, a to i v případě, že nemají povolené koncové body služby.  

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
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
