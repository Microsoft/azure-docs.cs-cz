---
title: Aspekty sítí
description: Přečtěte si informace o síťovém provozu s MECHANISMem zabezpečení a o tom, jak nastavit skupiny zabezpečení sítě a uživatelem definované trasy pomocí pomocného mechanismu
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 07/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 91b6134e7c809a8af75aa1cf23523e352e0a1a0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997337"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Důležité aspekty sítí pro službu App Service Environment #

## <a name="overview"></a>Přehled ##

 Azure [App Service Environment][Intro] je nasazení Azure App Service do podsítě ve službě Azure Virtual Network (VNET). K dispozici jsou dva typy nasazení App Service prostředí (pomocného mechanismu):

- **Externí** přístupový modul pro čtení: zpřístupňuje aplikace hostované pro pomocného objektu na IP adrese přístupné pro Internet. Další informace najdete v tématu [Vytvoření externího POmocného mechanismu služby][MakeExternalASE].
- **Interního nástroje POmocného mechanismu**: zpřístupňuje aplikace hostované pro pomocného objektu na IP adrese v rámci vaší virtuální sítě. Interním koncovým bodem je interní nástroj pro vyrovnávání zatížení (interního nástroje), což je důvod, proč se nazývá pomocného mechanismu interního nástroje. Další informace najdete v tématu [Vytvoření a použití POmocného interního nástrojeu][MakeILBASE].

Všechny služby ASE, externí a interního nástroje mají veřejnou virtuální IP adresu, která se používá pro příchozí provoz správy a jako adresa od v případě volání z pomocného mechanismu pro Internet. Volání z pomocného modulu pro pořízení, který přejde na Internet, opustí virtuální síť prostřednictvím VIP přiřazené k tomuto pomocnému uživateli. Veřejná IP adresa tohoto VIP je zdrojová IP adresa pro všechna volání z pomocného mechanismu, která se nachází na internetu. V případě, že aplikace ve vaší službě pomocného mechanismu volání vyvolají prostředky ve vaší virtuální síti nebo v síti VPN, je zdrojová IP adresa jednou z IP adres v podsíti používané vaším pomocným mechanismem řízení. Vzhledem k tomu, že je přístupový modul pro přístup ve virtuální síti, může také přistupovat k prostředkům v rámci virtuální sítě bez jakékoli další konfigurace. Pokud je virtuální síť připojená k vaší místní síti, aplikace ve vašem přihlašování k prostředkům mají také přístup k prostředkům, které nemají žádnou další konfiguraci.

![Externí pomocného mechanismu][1] 

Pokud máte externí pomocný objekt pro pořízení, veřejná VIP je zároveň koncovým bodem, na který aplikace pomocného mechanismu přikládá:

* HTTP/S 
* FTP/S
* Nasazení webu
* Vzdálené ladění

![INTERNÍHO NÁSTROJE POMOCNÉHO MECHANISMU][2]

Pokud máte interního nástroje pomocného programu pro zápis, adresa interního nástroje adresy je koncový bod pro HTTP/S, FTP/S, nasazení webu a vzdálené ladění.

## <a name="ase-subnet-size"></a>Velikost podsítě pomocného mechanismu ##

Velikost podsítě, která se používá k hostování pomocného mechanismu řízení, se po nasazení pomocného mechanismu nemůže změnit.  Nástroj pro pomocné služby používá adresu pro každou roli infrastruktury a také pro každou izolovanou instanci plánu App Service.  K dispozici je také pět adres používaných službou Azure Networking pro každou vytvořenou podsíť.  Pomocného programu, který nemá žádné plány App Service, bude používat 12 adres ještě před vytvořením aplikace.  Pokud se jedná o interního nástroje pomocného mechanismu, pak bude používat 13 adres ještě před tím, než vytvoříte aplikaci v tomto pomocném objektu pro vytváření. Při horizontálním navýšení kapacity se přidávají role infrastruktury každé násobky 15 a 20 instancí App Service plánu.

   > [!NOTE]
   > V podsíti není nic jiného, ale pomocného mechanismu. Nezapomeňte zvolit adresní prostor, který umožňuje budoucí nárůst. Toto nastavení nemůžete později změnit. Doporučujeme velikost `/24` s 256 adresami.

Při horizontálním navýšení nebo snížení kapacity se přidají nové role příslušné velikosti a pak se z aktuální velikosti migrují vaše úlohy na cílovou velikost. Původní virtuální počítače se odebraly až po migraci úloh. Pokud jste nastavili pomocného programu s 100 instancemi ASP, nastane doba, kdy potřebujete zdvojnásobit počet virtuálních počítačů.  Z tohoto důvodu doporučujeme použití "/24" k přizpůsobení jakýchkoli změn, které byste mohli potřebovat.  

## <a name="ase-dependencies"></a>Závislosti pomocného mechanismu ##

### <a name="ase-inbound-dependencies"></a>Příchozí závislosti na pomocném mechanismu ###

K fungování pomocného mechanismu pro zpracování vyžaduje jenom následující porty, které se dají otevřít:

| Použití | Z | Záměr |
|-----|------|----|
| Správa | Adresy pro správu App Service | Podsíť pomocného mechanismu: 454, 455 |
|  Interní komunikace prostřednictvím pomocného mechanismu | Podsíť pomocného mechanismu: všechny porty | Podsíť pomocného mechanismu: všechny porty
|  Povolit příchozí službu Azure Load Balancer | Nástroj pro vyrovnávání zatížení Azure | Podsíť pomocného mechanismu: 16001

K dispozici jsou dva další porty, které mohou být zobrazeny jako otevřené na prověřování portů 7654 a 1221. Odpoví s IP adresou a nic dalšího. V případě potřeby je můžete zablokovat. 

Příchozí provoz správy poskytuje kromě monitorování systému také příkaz a řízení pomocného mechanismu řízení. Zdrojové adresy tohoto provozu jsou uvedené v dokumentu adresy pro [správu pomocného mechanismu][ASEManagement] . Konfigurace zabezpečení sítě musí umožňovat přístup z adres správy pomocného mechanismu přístupu na portech 454 a 455. Pokud zablokujete přístup z těchto adres, váš přístupový stav se změní na není v pořádku a pak se pozastaví. Provoz TCP, který je součástí portů 454 a 455, se musí vrátit ze stejné virtuální IP adresy nebo dojde k potížím s asymetrickým směrováním. 

V rámci podsítě služby přihlášeného programu je k dispozici mnoho portů pro interní komunikaci komponent a může se změnit. K tomu je potřeba, aby všechny porty v podsíti služby přihlašování byly dostupné z podsítě pomocného mechanismu. 

Pro komunikaci mezi nástrojem pro vyrovnávání zatížení Azure a podsítí pomocného mechanismu musí být minimální porty, které je potřeba otevřít, 454, 455 a 16001. Port 16001 se používá pro udržování provozu mezi nástrojem pro vyrovnávání zatížení a pomocným mechanismem řízení. Pokud používáte interního nástroje pomocného mechanismu, můžete provoz uzamknout až do pouhých 454, 455, 16001 portů.  Pokud používáte externí přístupový modul pro přístup k datům, musíte vzít v úvahu normální porty pro přístup k aplikacím.  

K dalším portům, se kterými se budete muset zabývat, patří porty aplikace:

| Použití | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Vzdálené ladění sady Visual Studio  |  4020, 4022, 4024 |
|  Služba Nasazení webu | 8172 |

Pokud zablokujete porty aplikace, může váš přihlašovat stále fungovat, ale vaše aplikace nemusí.  Pokud používáte IP adresy přiřazené aplikacím s externím pomocným mechanismem řízení, bude potřeba, abyste povolili provoz z IP adres přiřazených k vašim aplikacím do podsítě pro pomocné služby na portech, které jsou zobrazené na portálu pomocného mechanismu pro > IP adres.

### <a name="ase-outbound-dependencies"></a>Odchozí závislosti na pomocném mechanismu ###

Pro odchozí přístup závisí přístupový objekt na více externích systémech. Mnohé z těchto systémových závislostí jsou definované s názvy DNS a nemapují se na pevně danou sadu IP adres. Proto pomocného mechanismu vyžaduje odchozí přístup z podsítě pro pomocné služby na všechny externí IP adresy v různých portech. 

Služba pomocného mechanismu oznamuje přístup k Internetu adres na těchto portech:

| Použití | Porty |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL, aktualizace Windows, závislosti Linux, služby Azure | 80/443 |
| Azure SQL | 1433 | 
| Monitorování | 12000 |

Odchozí závislosti jsou uvedené v dokumentu, který popisuje [uzamykání App Service Environment odchozích přenosů](./firewall-integration.md). Pokud přístupový modul pro přístup ztratí přístup k jeho závislostem, přestane fungovat. Pokud k tomu dojde dostatečně dlouho, pozastaví se pomocného mechanismu. 

### <a name="customer-dns"></a>DNS zákazníka ###

Pokud je virtuální síť nakonfigurovaná pomocí serveru DNS definovaného zákazníkem, použije se k tomu zatížení klienta. Správce služby používá Azure DNS pro účely správy. Pokud je virtuální síť nakonfigurovaná pomocí serveru DNS vybraného zákazníkem, musí být server DNS dosažitelný z podsítě, která obsahuje pomocného uživatele.

K otestování překladu DNS z vaší webové aplikace můžete použít příkaz konzoly *nameresolver*. Pro vaši aplikaci přejdete do okna ladění na webu SCM nebo na portálu přejdete na aplikaci a vyberte Konzola. Z příkazového řádku prostředí můžete zadat *nameresolver* příkazu spolu s názvem DNS, který chcete vyhledat. Výsledek, který vrátíte, je stejný jako to, co by vaše aplikace získala při stejném vyhledávání. Pokud použijete nástroj nslookup, provedete místo toho vyhledávání pomocí Azure DNS.

Pokud změníte nastavení DNS virtuální sítě, ve které je váš přihlášený, budete muset restartovat službu pomocného mechanismu. Abyste se vyhnuli restartování vašeho pomocného mechanismu, důrazně doporučujeme nakonfigurovat nastavení DNS pro virtuální síť předtím, než vytvoříte správce přidaných mechanismů.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Závislosti portálu ##

Kromě funkčních závislostí pomocného mechanismu je několik dalších položek, které se týkají prostředí portálu. Některé funkce v Azure Portal závisí na přímém přístupu k _webu SCM_. Pro každou aplikaci v Azure App Service jsou k dispozici dvě adresy URL. První adresa URL má přístup k vaší aplikaci. Druhá adresa URL má přístup k webu SCM, který se také nazývá _Konzola Kudu_. K funkcím, které používají web SCM, patří:

-   Web Jobs
-   Functions
-   Streamování protokolů
-   Kudu
-   Rozšíření
-   Průzkumník procesů
-   Konzola

Když použijete interního nástroje pomocného mechanismu, web SCM není přístupný mimo virtuální síť. Některé možnosti nebudou fungovat z portálu aplikace, protože vyžadují přístup k webu SCM aplikace. K webu SCM se můžete připojit přímo místo používání portálu. 

Pokud je vaším pomocným mechanismem interního nástroje název domény *contoso.appserviceenvironment.NET* a název vaší aplikace je *TestApp*, aplikace se dorazí na *TestApp.contoso.appserviceenvironment.NET*. Web SCM, který se s ním doprovází, se dosáhne na *TestApp.SCM.contoso.appserviceenvironment.NET*.

## <a name="ase-ip-addresses"></a>IP adresy pomocného mechanismu ##

Pomocného programu má na paměti několik IP adres. Jsou to tyto:

- **Veřejná příchozí IP adresa**: používá se pro přenosy aplikací v externím pomocném mechanismu řízení a přenos pro správu v externím pomocném mechanismu práv i v interního nástroje.
- **Odchozí veřejná IP adresa**: používá se jako IP adresa "od" pro odchozí připojení z pomocného mechanismu, která opouští virtuální síť, která není směrována do sítě VPN.
- **Interního nástroje IP adresa**: IP adresa interního nástroje existuje pouze v pomocném mechanismu pro interního nástroje.
- **Adresy SSL na základě IP adres přiřazené aplikacím**: možné jenom s externím pomocným mechanismem zabezpečení a při konfiguraci SSL založeného na protokolu IP.

Všechny tyto IP adresy jsou viditelné v Azure Portal v uživatelském rozhraní služby Řízení uživatelských mechanismů. Pokud máte interního nástroje pomocného nástroje, zobrazí se v seznamu IP adresa pro interního nástroje.

   > [!NOTE]
   > Tyto IP adresy se nezmění, pokud váš správce služby zůstane v provozu.  Pokud dojde k pozastavení a obnovení vašeho pomocného mechanismu, adresy používané vaším pomocným mechanismem změn se změní. Normální příčinou pozastaveného pomocného mechanismu řízení je, že Pokud zablokujete příchozí přístup pro správu nebo zablokuje přístup k závislosti na pomocném mechanismu 

![IP adresy][3]

### <a name="app-assigned-ip-addresses"></a>IP adresy přiřazené aplikacím ###

Pomocí externího pomocného uživatele můžete přiřadit IP adresy jednotlivým aplikacím. Nemůžete to udělat pomocí pomocného programu interního nástroje. Další informace o tom, jak nakonfigurovat aplikaci tak, aby měla svou vlastní IP adresu, najdete [v tématu zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](../configure-ssl-bindings.md).

Když má aplikace svoji vlastní adresu SSL založenou na IP adresách, pomocného mechanismu pro mapování na tuto IP adresu rezervuje dva porty. Jeden port je pro přenosy HTTP a druhý port pro protokol HTTPS. Tyto porty jsou uvedené v uživatelském rozhraní pomocného mechanismu v části IP adresy. Provoz musí být schopný získat přístup k těmto portům z virtuální IP adresy nebo jsou aplikace nedostupné. Tento požadavek je důležité pamatovat při konfiguraci skupin zabezpečení sítě (skupin zabezpečení sítě).

## <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě) ##

[Skupiny zabezpečení sítě][NSGs] poskytují možnost řídit přístup k síti v rámci virtuální sítě. Když použijete portál, existuje implicitní pravidlo odepření s nejnižší prioritou pro zamítnutí všeho. To, co sestavíte, jsou vaše pravidla povolení.

V pomocném mechanismu přístupu nemáte přístup k virtuálním počítačům použitým k hostování samotného mechanismu. Jsou v rámci předplatného spravovaného společností Microsoft. Pokud chcete omezit přístup k aplikacím v pomocném mechanismu přístupu, nastavte skupin zabezpečení sítě v podsíti pro pomocného mechanismu. V takovém případě věnujte pečlivou pozornost závislostem pomocného mechanismu. Pokud zablokujete jakékoli závislosti, přestane fungovat.

Skupin zabezpečení sítě se dá nakonfigurovat přes Azure Portal nebo přes PowerShell. Zde najdete informace o Azure Portal. Skupin zabezpečení sítě vytvoříte a spravujete na portálu jako prostředek nejvyšší úrovně v části **sítě**.

Požadované položky v NSG, aby mohl funkce pomocného mechanismu provozu fungovat:

**Příchozí**
* TCP ze značky služby IP AppServiceManagement na portech 454 455
* TCP z nástroje pro vyrovnávání zatížení na portu 16001
* z podsítě služby přihlašování do podsítě pomocného mechanismu na všech portech

**Odchozí**
* UDP na všechny IP adresy na portu 53
* UDP na všechny IP adresy na portu 123
* TCP na všechny IP adresy na portech 80, 443
* Na portech 1433 se TCP pro značku služby IP `Sql`
* TCP na všechny IP adresy na portu 12000
* do podsítě pomocného mechanismu pro všechny porty

Tyto porty nezahrnují porty, které vaše aplikace vyžadují pro úspěšné použití. Vaše aplikace může například potřebovat zavolat Server MySQL na portu 3306. Protokol NTP (Network Time Protocol) na portu 123 je protokol synchronizace času používaný operačním systémem. Koncové body NTP nejsou specifické pro App Services, se můžou lišit v závislosti na operačním systému a nejsou v dobře definovaném seznamu adres. Aby nedocházelo k problémům s synchronizací času, je nutné povolený provoz UDP na všechny adresy na portu 123. Odchozí přenos TCP na port 12000 je určen pro podporu a analýzu systému. Koncové body jsou dynamické a nejsou v dobře definované sadě adres.

Standardní porty pro přístup k aplikacím:

| Použití | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Vzdálené ladění sady Visual Studio  |  4020, 4022, 4024 |
|  Služba Nasazení webu | 8172 |

Při zohlednění vstupních a odchozích požadavků by měl skupin zabezpečení sítě vypadat podobně jako skupin zabezpečení sítě v tomto příkladu. 

![Příchozí pravidla zabezpečení][4]

Výchozí pravidlo povoluje, aby IP adresy ve virtuální síti komunikovaly s podsítí pomocného mechanismu. Další výchozí pravidlo povolí službě Vyrovnávání zatížení, která se označuje jako veřejná VIP, ke komunikaci s pomocným mechanismem řízení. Pokud chcete zobrazit výchozí pravidla, vyberte **výchozí pravidla** vedle ikony **Přidat** . Pokud před výchozími pravidly vložíte pravidlo Odepřít vše jiného, zabráníte tak provozu mezi VIP a pomocným mechanismem řízení. Pokud chcete zabránit provozu v rámci virtuální sítě, přidejte vlastní pravidlo, které povolí příchozí. Použijte **zdroj, který** se rovná AzureLoadBalancer, s cílovým umístěním a rozsahem portů **\*** . Vzhledem k tomu, že pravidlo NSG se používá pro podsíť pomocného mechanismu, nemusíte být v cíli specifická.

Pokud jste aplikaci přiřadili IP adresu, zajistěte, aby byly porty otevřené. Porty zobrazíte tak, že vyberete **App Service Environment**  >  **IP adresy**.  

Všechny položky zobrazené v následujících odchozích pravidlech jsou potřeba s výjimkou poslední položky. Umožňují síťovému přístupu ke závislostem pomocného mechanismu, které byly popsány dříve v tomto článku. Pokud je některý z nich zablokujete, váš pomocného programu přestane fungovat. Poslední položka v seznamu umožňuje vašemu přihlášenému uživatelům komunikovat s ostatními prostředky ve vaší virtuální síti.

![Odchozí pravidla zabezpečení][5]

Po definování skupin zabezpečení sítě je přiřaďte k podsíti, ve které je váš pomocným mechanismem řízení. Pokud si nepamatujete virtuální síť nebo podsíť pomocného mechanismu, můžete ji zobrazit na stránce portálu pomocného mechanismu. Pokud chcete přiřadit NSG k vaší podsíti, otevřete to tak, že přejdete do uživatelského rozhraní podsítě a vyberete NSG.

## <a name="routes"></a>Trasy ##

Vynucené tunelování je při nastavování tras ve virtuální síti, aby odchozí přenosy nepřešly přímo na Internet, ale někde jinde jako brána ExpressRoute nebo virtuální zařízení.  Pokud potřebujete nějakým způsobem nakonfigurovat pomocného správce, přečtěte si dokument týkající se [konfigurace App Service Environment s vynuceným tunelovým propojením][forcedtunnel].  Tento dokument vám sdělí možnosti dostupné pro práci s ExpressRoute a vynuceným tunelovým propojením.

Při vytváření pomocného mechanismu na portálu vytvoříme také sadu směrovacích tabulek v podsíti, která je vytvořená pomocí pomocného mechanismu služby.  Tyto trasy jednoduše říkají odeslání odchozího provozu přímo na Internet.  
Chcete-li vytvořit stejné trasy ručně, postupujte podle následujících kroků:

1. Přejděte na Azure Portal. Vyberte **sítě**  >  **směrovací tabulky**.

2. Vytvořte novou směrovací tabulku ve stejné oblasti, ve které je vaše virtuální síť.

3. V uživatelském rozhraní směrovací tabulky vyberte **trasy**  >  **Přidat**.

4. Nastavte **typ dalšího segmentu směrování** na **Internet** a **předponu adresy** na **0.0.0.0/0**. Vyberte **Uložit**.

    Pak se zobrazí něco podobného následujícímu:

    ![Funkční trasy][6]

5. Až vytvoříte novou směrovací tabulku, přejdete do podsítě, která obsahuje váš pomocným mechanismem řízení. Vyberte směrovací tabulku ze seznamu na portálu. Po uložení změn byste měli vidět skupin zabezpečení sítě a trasy zaznamenané ve vaší podsíti.

    ![Skupin zabezpečení sítě a trasy][7]

## <a name="service-endpoints"></a>Koncové body služby ##

Koncové body služby umožňují omezit přístup k víceklientským službám na sadu virtuálních sítí a podsítí Azure. Další informace o koncových bodech služby najdete v dokumentaci pro [koncové body služby pro virtuální síť][serviceendpoints]. 

Když pro prostředek povolíte koncové body služby, vytvoří se trasy s vyšší prioritou než všechny ostatní trasy. Pokud používáte koncové body služby v jakékoli službě Azure s vynuceným pomocným mechanismem řízení, nebude přenos do těchto služeb vynucený tunelování. 

Pokud jsou koncové body služby povolené v podsíti s instancí SQL Azure, musí mít koncové body služby povolené i všechny instance SQL Azure, ke kterým se z této podsítě připojuje. Pokud chcete ze stejné podsítě přistupovat k několika instancím SQL Azure, není možné povolit koncové body služby v jedné instanci SQL Azure a v jiné ne. Žádná jiná služba Azure se nechová jako Azure SQL s ohledem na koncové body služby. Když povolíte koncové body služby se službou Azure Storage, uzamknete přístup k danému prostředku z vaší podsítě, ale stále budete mít přístup k ostatním účtům služby Azure Storage, a to i v případě, že nemají povolené koncové body služby.  

![Koncové body služby][8]

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
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md