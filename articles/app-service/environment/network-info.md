---
title: Aspekty sítí
description: Přečtěte si informace o síťovém provozu ase a o tom, jak pomocí rozhraní ASE nastavit skupiny zabezpečení sítě a uživatelem definované trasy.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 01/24/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4aec7fa78292f224952dd2ae929d2b8bfd97ab9b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477684"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Důležité aspekty sítí pro službu App Service Environment #

## <a name="overview"></a>Přehled ##

 [Azure App Service Environment][Intro] je nasazení služby Azure App Service do podsítě ve vaší virtuální síti Azure (VNet). Existují dva typy nasazení pro prostředí služby App Service (ASE):

- **Externí služba ASE**: Zpřístupňuje aplikace hostované službou ASE na ip adrese přístupné z internetu. Další informace naleznete [v tématu Vytvoření externí hospo-][MakeExternalASE]
- **Služba ASE ILB**: Zpřístupňuje aplikace hostované službou ASE na adrese IP uvnitř virtuální sítě. Interní koncový bod je interní vyrovnávání zatížení (ILB), což je důvod, proč se nazývá ASE ILB. Další informace naleznete v [tématu Vytvoření a použití služby ASE služby ILB][MakeILBASE].

Všechny ASS, Externí a ILB, mají veřejné VIP, který se používá pro příchozí provoz správy a jako adresa při volání ze služby ASE na internet. Volání ze seznamu se spouštění a sek, které přejdou na internet opustit virtuální síť prostřednictvím virtuální sítě přiřazené pro ase. Veřejná IP adresa tohoto VIP je zdrojová IP adresa pro všechny hovory ze seznamu ASE, které přejdou na internet. Pokud aplikace ve službě ASE volání prostředků ve vaší virtuální síti nebo přes VPN, zdrojová IP adresa je jedním z IP adres v podsíti používané službou ASE. Vzhledem k tomu, že služba ASE je v rámci virtuální sítě, může také přistupovat k prostředkům v rámci virtuální sítě bez jakékoli další konfigurace. Pokud je virtuální síť připojená k vaší místní síti, aplikace ve vaší službě ASE mají také přístup k prostředkům bez další konfigurace.

![Externí ase][1] 

Pokud máte externí službu ASE, veřejná virtuální ip adresa je také koncový bod, který vaše aplikace služby ASE vyřešit pro:

* Protokol HTTP/S 
* FTP/S
* Nasazení webu
* Vzdálené ladění

![ILB ASE][2]

Pokud máte službu ASE ILB, adresa ILB je koncovým bodem pro HTTP/S, FTP/S, nasazení webu a vzdálené ladění.

## <a name="ase-subnet-size"></a>Velikost podsítě ase ##

Velikost podsítě používané k hostování služby ASE nelze po nasazení služby ASE změnit.  Služba ASE používá adresu pro každou roli infrastruktury i pro každou instanci plánu izolované služby App Service.  Kromě toho existuje pět adres používaných Azure Networking pro každou podsíť, která je vytvořena.  Služba ASE bez plánů služby App Service použije před vytvořením aplikace 12 adres.  Pokud se jedná o službu ASE ILB, použije před vytvořením aplikace v této službu ASE 13 adres. Při horizontálním navýšení kapacity služby ASE se role infrastruktury přidávají každý násobek 15 a 20 instancí plánu služby App Service.

   > [!NOTE]
   > Nic jiného nemůže být v podsíti, ale ase. Ujistěte se, že jste zvolili adresní prostor, který umožňuje budoucí růst. Toto nastavení nelze později změnit. Doporučujeme velikost `/24` s 256 adresami.

Při škálování nahoru nebo dolů, nové role odpovídající velikosti jsou přidány a pak vaše úlohy se migrují z aktuální velikosti do cílové velikosti. Původní virtuální chody odebrané pouze po migraci úloh. Pokud jste měli službu ASE se 100 instancemi ASP, bude existovat období, ve kterém budete potřebovat dvojnásobný počet virtuálních discích.  Z tohoto důvodu doporučujeme použít '/24' přizpůsobit všechny změny, které by mohly vyžadovat.  

## <a name="ase-dependencies"></a>Závislosti služby ASE ##

### <a name="ase-inbound-dependencies"></a>Příchozí závislosti služby ASE ###

Pouze pro fungování ase, ASE vyžaduje následující porty, které mají být otevřené:

| Použití | From | Akce |
|-----|------|----|
| Správa | Adresy správy služby App Service | Podsíť ASE: 454, 455 |
|  Interní komunikace ASE | Podsíť ASE: Všechny porty | Podsíť ASE: Všechny porty
|  Povolit příchozí vyrovnávání zatížení Azure | Nástroj pro vyrovnávání zatížení Azure | Podsíť ASE: 16001

Existují 2 další porty, které se mohou zobrazit jako otevřené na skenování portu, 7654 a 1221. Oni odpoví s IP adresou a nic víc. Mohou být blokovány v případě potřeby. 

Příchozí provoz správy poskytuje příkaz a řízení a řízení ase kromě monitorování systému. Zdrojové adresy pro tento provoz jsou uvedeny v dokumentu [Adresy správy systému ASE.][ASEManagement] Konfigurace zabezpečení sítě musí umožnit přístup z adres správy služby ASE na portech 454 a 455. Pokud zablokujete přístup z těchto adres, vaše ase se stane není v pořádku a pak se pozastaví. Přenosy TCP, které přicházejí na portech 454 a 455, musí být zpět ze stejného virtuálního protokolu VIP, jinak budete mít problém s asymetrickým směrováním. 

V podsíti služby ASE existuje mnoho portů používaných pro komunikaci s interními součástmi a mohou se změnit. To vyžaduje, aby všechny porty v podsíti ase přístupné z podsítě ase. 

Pro komunikaci mezi Azure vyrovnávání zatížení a podsítě ASE minimální porty, které musí být otevřené jsou 454, 455 a 16001. Port 16001 se používá pro udržování provozu mezi vyvykladač emituje a seřízení systému Řízení rizik. Pokud používáte službu ASE ilb, můžete uzamknout provoz pouze na porty 454, 455, 16001.  Pokud používáte externí službu ASE, je třeba vzít v úvahu normální porty pro přístup k aplikacím.  

Další porty, které potřebujete, aby se týkají sami s jsou porty aplikace:

| Použití | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Vzdálené ladění sady Visual Studio  |  4020, 4022, 4024 |
|  Služba nasazení webu | 8172 |

Pokud zablokujete porty aplikace, vaše ase může stále fungovat, ale vaše aplikace nemusí.  Pokud používáte ip adresy přiřazené aplikacím s externí službou ASE, budete muset povolit provoz z IP adres přiřazených vašim aplikacím do podsítě služby ASE na portech zobrazených na portálu služby ASE > stránce IP adresy.

### <a name="ase-outbound-dependencies"></a>Odchozí závislosti služby ASE ###

Pro odchozí přístup závisí systém Řízení přístupu na více externích systémech. Mnohé z těchto systémových závislostí jsou definovány názvy DNS a nemapují se na pevnou sadu adres IP. ASE tedy vyžaduje odchozí přístup z podsítě ASE ke všem externím IP adresy napříč různými porty. 

ASE komunikuje na internetové přístupné adresy na následujících portech:

| Použití | Porty |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| SEZNAM CRL, aktualizace Systému Windows, závislosti na Linuxu, služby Azure | 80/443 |
| Azure SQL | 1433 | 
| Monitorování | 12000 |

Odchozí závislosti jsou uvedeny v dokumentu, který popisuje [uzamčení prostředí App Service Odchozí provoz](./firewall-integration.md). Pokud služby ASE ztratí přístup ke svým závislostem, přestane fungovat. Když se to stane dost dlouho, ase je pozastavena. 

### <a name="customer-dns"></a>DNS zákazníka ###

Pokud je virtuální síť nakonfigurovaná se zákazníkem definovaným serverem DNS, použijí ji úlohy klienta. Služba Řízení řízení služeb používá Azure DNS pro účely správy. Pokud je virtuální síť nakonfigurovaná se serverem DNS vybraným zákazníkem, musí být server DNS dostupný z podsítě, která obsahuje službu ASE.

Chcete-li otestovat rozlišení DNS z webové aplikace, můžete použít příkaz *konzoly překladač*. Přejděte do okna ladění na webu scm pro vaši aplikaci nebo přejděte do aplikace na portálu a vyberte konzolu. Z řádku prostředí můžete vydat příkaz *nameresolver* spolu s názvem DNS, který chcete vyhledat. Výsledek, který získáte zpět, je stejný jako to, co by vaše aplikace získala při stejném vyhledávání. Pokud použijete nslookup, budete místo toho provést vyhledávání pomocí Azure DNS.

Pokud změníte nastavení DNS virtuální sítě, ve které se vaše služba Se senachází, budete muset restartovat službu ASE. Chcete-li se vyhnout restartování služby ASE, důrazně doporučujeme před vytvořením služby ASE nakonfigurovat nastavení DNS pro virtuální síť.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Závislosti portálu ##

Kromě funkční závislosti služby ASE existuje několik dalších položek souvisejících s prostředí portálu. Některé funkce na webu Azure portal závisí na přímém přístupu k _webu SCM_. Pro každou aplikaci ve službě Azure App Service existují dvě adresy URL. První adresa URL je pro přístup k vaší aplikaci. Druhá adresa URL je pro přístup k webu SCM, který se také nazývá _konzole Kudu_. Mezi funkce, které používají web SCM, patří:

-   Web Jobs
-   Functions
-   Protokolovat streamování
-   Kudu
-   Rozšíření
-   Průzkumník procesů
-   Konzola

Při použití služby ASE ILB není web SCM přístupný mimo virtuální síť. Některé funkce nebudou fungovat z portálu aplikace, protože vyžadují přístup k webu SCM aplikace. Můžete se připojit k webu SCM přímo namísto použití portálu. 

Pokud je vaše služba ASE ILB název domény *contoso.appserviceenvironment.net* a název aplikace je *testapp*, aplikace je dosaženo na *testapp.contoso.appserviceenvironment.net*. SCM stránky, které jde s ním je dosaženo na *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>Adresy IP ase ##

ASE má několik IP adres, které je třeba znát. Jsou to tyto:

- **Veřejná příchozí IP adresa**: Používá se pro provoz aplikací v externí ase a provoz správy v externí služby ASE a služby ASE ILB.
- **Odchozí veřejná IP adresa**: Používá se jako "z" IP pro odchozí připojení ze služby ASE, které opouštějí virtuální síť, které nejsou směrovány dolů VPN.
- **IP adresa ILB**: Ip adresa ILB existuje pouze ve službách ASE ILB.
- **Adresy SSL založené na protokolu IP přiřazené aplikací :** Možné pouze s externí službou ASE a při konfiguraci protokolu SSL založeného na protokolu IP.

Všechny tyto IP adresy jsou viditelné na webu Azure portal z uhlavního počítače Služby ASE. Pokud máte službu ASE ILB, je uvedena adresa IP pro službu ILB.

   > [!NOTE]
   > Tyto IP adresy se nezmění, dokud vaše ase zůstane v provozu.  Pokud se vaše ase stane pozastavena a obnovena, adresy používané vaší aSE se změní. Normální příčinou pro službu ASE, aby se stala pozastavena, je, pokud zablokujete přístup k příchozí správě nebo blokujete přístup k závislosti služby ASE. 

![IP adresy][3]

### <a name="app-assigned-ip-addresses"></a>IP adresy přiřazené aplikacím ###

Pomocí externí služby ASE můžete jednotlivým aplikacím přiřadit IP adresy. To nemůžete udělat se službou ASE ILB. Další informace o tom, jak nakonfigurovat aplikaci tak, aby měla vlastní IP adresu, najdete v [tématu Zabezpečení vlastního názvu DNS s vazbou TLS/SSL ve službě Azure App Service](../configure-ssl-bindings.md).

Pokud má aplikace vlastní ssl adresu založenou na protokolu IP, systém ASE si rezervuje dva porty, které se budou mapovat na tuto ADRESU IP. Jeden port je pro přenos protokolu HTTP a druhý port je pro protokol HTTPS. Tyto porty jsou uvedeny v unovém uj.a. ase v části IP adresy. Provoz musí být schopen dosáhnout těchto portů z programu VIP nebo jsou aplikace nepřístupné. Tento požadavek je důležité mít na paměti při konfiguraci skupin zabezpečení sítě (NSGs).

## <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě) ##

[Skupiny zabezpečení sítě][NSGs] poskytují možnost řídit přístup k síti v rámci virtuální sítě. Při použití portálu, je implicitní odepřít pravidlo na nejnižší prioritu odepřít všechno. To, co sestavíte, jsou vaše pravidla povolení.

V ase nemáte přístup k virtuálním sobě používaným k hostování samotné hospo- hostitele. Jsou v předplatném spravovaném microsoftem. Pokud chcete omezit přístup k aplikacím ve službě ASE, nastavte skupiny nsg v podsíti služby ASE. Přitom věnujte pozornost závislostem služby ASE. Pokud zablokujete všechny závislosti, služby ASE přestane fungovat.

NsGs lze nakonfigurovat prostřednictvím portálu Azure nebo prostřednictvím PowerShellu. Informace zde zobrazuje portál Azure. Skupiny nsg na portálu můžete vytvářet a spravovat jako prostředek nejvyšší úrovně v části **Síť**.

Požadované položky v nsg, pro aSE fungovat, jsou povolit přenosy:

**Příchozí**
* z ip servisního štítku AppServiceManagement na portech 454 455
* z vykladače zatížení na portu 16001
* z podsítě ASE do podsítě ASE na všech portech

**Odchozí**
* všem IP adresy na portu 123
* všem IP adresy na portech 80, 443
* k ip servisníznačce AzureSQL na portech 1433
* všem IP adresy na portu 12000
* do podsítě ASE na všech portech

Port DNS nemusí být přidán, protože provoz na dns není ovlivněn pravidly sítě zabezpečení sítě. Tyto porty neobsahují porty, které vaše aplikace vyžadují pro úspěšné použití. Normální porty pro přístup k aplikacím jsou:

| Použití | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Vzdálené ladění sady Visual Studio  |  4020, 4022, 4024 |
|  Služba nasazení webu | 8172 |

Při příchozí a odchozí požadavky jsou vzaty v úvahu, nsGs by měl vypadat podobně jako nsGs uvedené v tomto příkladu. 

![Příchozí pravidla zabezpečení][4]

Výchozí pravidlo umožňuje IP adresy ve virtuální síti pro čtení do podsítě ASE. Jiné výchozí pravidlo umožňuje nástroj pro vyrovnávání zatížení, označovaný také jako veřejná virtuální ip, komunikovat se sesporecku se sestavou Se. Chcete-li zobrazit výchozí pravidla, vyberte **možnost Výchozí pravidla** vedle ikony **Přidat.** Pokud před výchozí pravidla uvězníte pravidlo odepřít vše ostatní, zabráníte přenosu mezi virtuální ip a sese. Chcete-li zabránit přenosu přicházejícím z vnitřní sítě, přidejte vlastní pravidlo, které povolí příchozí. Použijte zdroj rovnající se AzureLoadBalancer **Any** s cílem Any **\*** a rozsah portů . Vzhledem k tomu, že pravidlo skupiny síťových účelů je použito v podsíti ase, nemusíte být v cílovém umístění konkrétní.

Pokud jste aplikaci přiřadili IP adresu, ujistěte se, že máte porty otevřené. Chcete-li zobrazit porty, vyberte IP adresy **prostředí** > **služby**App Service .  

Jsou potřeba všechny položky zobrazené v následujících odchozích pravidlech, s výjimkou poslední položky. Umožňují přístup k síti závislosti služby ASE, které byly zaznamenány dříve v tomto článku. Pokud některý z nich zablokujete, vaše ase přestane fungovat. Poslední položka v seznamu umožňuje vaší sekatou ke komunikaci s jinými prostředky ve vaší virtuální síti.

![Odchozí pravidla zabezpečení][5]

Po definování skupin nsg je přiřaďte k podsíti, ve které je vaše sestava se křovinatá. Pokud si nepamatujete virtuální síť ase nebo podsíť, uvidíte ji na stránce portálu ASE. Chcete-li přiřadit skupinu nsg k podsíti, přejděte do unového rozhraní podsítě a vyberte skupinu nsg.

## <a name="routes"></a>Trasy ##

Vynucené tunelování je při nastavení tras ve virtuální síti, aby odchozí provoz nepřešel přímo na internet, ale někam jinam, jako je brána ExpressRoute nebo virtuální zařízení.  Pokud potřebujete nakonfigurovat službu ASE takovým způsobem, přečtěte si dokument o [konfiguraci prostředí služby App Service pomocí vynuceného tunelového propojení][forcedtunnel].  Tento dokument vám řekne možnosti, které jsou k dispozici pro práci s ExpressRoute a vynucené tunelové propojení.

Při vytváření ase na portálu také vytvoříme sadu směrovacích tabulek v podsíti, která je vytvořena pomocí ase.  Tyto trasy jednoduše říkají, že mají posílat odchozí provoz přímo na internet.  
Chcete-li vytvořit stejné trasy ručně, postupujte takto:

1. Přejděte na web Azure Portal. Vyberte **možnost Síťové** > **tabulky směrování**.

2. Vytvořte novou směrovací tabulku ve stejné oblasti jako virtuální síť.

3. V ui. směrovací tabulky vyberte **Přidat trasy** > **.**

4. Nastavte **další typ směrování** na **Internet** a **předponu Adresa** na **0.0.0.0/0**. Vyberte **Uložit**.

    Pak vidíte něco jako následující:

    ![Funkční trasy][6]

5. Po vytvoření nové tabulky tras přejděte do podsítě, která obsahuje vaši ase. Vyberte směrovací tabulku ze seznamu na portálu. Po uložení změny byste se měli zobrazit skupiny nsg a trasy uvedené v podsíti.

    ![NSG a trasy][7]

## <a name="service-endpoints"></a>Koncové body služby ##

Koncové body služby umožňují omezit přístup k víceklientským službám na sadu virtuálních sítí a podsítí Azure. Další informace o koncových bodech služby najdete v dokumentaci pro [koncové body služby pro virtuální síť][serviceendpoints]. 

Když pro prostředek povolíte koncové body služby, vytvoří se trasy s vyšší prioritou než všechny ostatní trasy. Pokud používáte koncové body služby na jakékoli službě Azure, s vynuceným tunelovým propojením služby ASE, provoz na tyto služby nebude vynuceně tunelové propojení. 

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
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
