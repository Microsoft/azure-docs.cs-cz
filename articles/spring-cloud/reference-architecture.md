---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: kriation
title: Referenční architektura pro jarní cloudy Azure
ms.author: akaleshian
ms.service: spring-cloud
description: Tato referenční architektura je základem pro používání Azure jarního cloudu pomocí typického podnikového centra a návrhu paprsků.
ms.openlocfilehash: 74183ca2decf8487e5c41cf36d5784538021077f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877870"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Referenční architektura pro jarní cloudy Azure

Tato referenční architektura je základem pro používání Azure jarního cloudu pomocí typického podnikového centra a návrhu paprsků. V tomto návrhu je Azure jaře Cloud nasazený v jednom paprsku, který je závislý na sdílených službách hostovaných v centru. Architektura je sestavená s komponentami, aby principy v rozhraní [Microsoft Azure Well-Architected Framework][16].

Implementaci této architektury najdete v úložišti [referenčních architektur Azure jarních cloudů][10] na GitHubu.

Mezi možnosti nasazení této architektury patří Azure Resource Manager (ARM), Terraformu a Azure CLI. Artefakty v tomto úložišti poskytují základ, který můžete přizpůsobit pro vaše prostředí. Prostředky, například Azure Firewall nebo Application Gateway, můžete seskupit do různých skupin prostředků nebo předplatných. Toto seskupení pomáhá udržet různé funkce oddělené, například IT infrastruktura, zabezpečení, týmy pro obchodní aplikace atd.

## <a name="planning-the-address-space"></a>Plánování adresního prostoru

Jarní cloud Azure vyžaduje dvě vyhrazené podsítě:

* Modul runtime služby
* Aplikace pro spouštění pružiny

Každá z těchto podsítí vyžaduje vyhrazený cluster. Více clusterů nemůže sdílet stejné podsítě. Minimální velikost každé podsítě je/28. Počet instancí aplikace, které může Azure jaře Cloud podporovat, se liší v závislosti na velikosti podsítě. Podrobné požadavky na Virtual Network (VNET) najdete v části požadavky na [virtuální síť][11] v tématu [nasazení Azure jarního cloudu ve virtuální síti][17].

> [!WARNING]
> Vybraná velikost podsítě se nemůže překrývat s existujícím adresním prostorem virtuální sítě a neměla by se překrývat s žádnými rozsahy adres partnerských uzlů nebo místních podsítí.

## <a name="use-cases"></a>Případy použití

Obvyklá využití pro tuto architekturu:

* Interní aplikace nasazené v hybridních cloudových prostředích
* Externě přístupné aplikace

Tyto případy použití jsou podobné s výjimkou pravidel zabezpečení a provozu sítě. Tato architektura je navržena tak, aby podporovala drobné odlišnosti každého.

## <a name="private-applications"></a>Soukromé aplikace

Následující seznam popisuje požadavky na infrastrukturu pro privátní aplikace. Tyto požadavky jsou typické ve vysoce regulovaných prostředích.

* Žádný přímý výstup na veřejný Internet, s výjimkou provozu řídicích rovin.
* Odchozí přenosy by se měly procházet přes virtuální síťové zařízení (síťové virtuální zařízení) centrální sítě (například Azure Firewall).
* Data v klidovém stavu by měla být zašifrovaná.
* Přenášená data by měla být zašifrovaná.
* Kanály nasazení DevOps se dají použít (například Azure DevOps) a vyžadují síťové připojení k jarnímu cloudu Azure.
* Přístup k zabezpečení nulové důvěryhodnosti od Microsoftu vyžaduje, aby byly tajné klíče, certifikáty a přihlašovací údaje uložené v zabezpečeném trezoru. Doporučená služba je Azure Key Vault.
* Záznamy DNS (Domain Name Service) hostitele aplikace by měly být uložené v Azure Privátní DNS.
* Překlad názvů hostitelů místně a v cloudu by měl být obousměrný.
* Musí se vyhovět aspoň jednomu bezpečnostnímu srovnávacímu testu.
* Závislosti služby Azure by měly komunikovat prostřednictvím koncových bodů služby nebo privátního odkazu.
* Skupiny prostředků spravované nasazením pro jarní Cloud v Azure se nesmí upravovat.
* Podsítě spravované nasazením pro jarní cloudové prostředí Azure se nesmí upravovat.
* Podsíť musí mít jenom jednu instanci jarního cloudu Azure.
* Pokud se k načtení vlastností konfigurace z úložiště používá [Azure jaře Cloud config server][8] , musí být úložiště privátní.

Následující seznam obsahuje komponenty, které tvoří návrh:

* Místní síť
  * Služba DNS (Domain Name Service)
  * brána
* Předplatné centra
  * Azure Firewall podsíť
  * Application Gateway podsíť
  * Podsíť sdílených služeb
* Připojené předplatné
  * Virtual Network partnerský uzel
  * Podsíť Azure bastionu

Následující seznam popisuje služby Azure v této referenční architektuře:

* [Jarní cloud Azure][1]: spravovaná služba, která je navržená a optimalizovaná speciálně pro aplikace pro pružinové spouštění založené na jazyce Java a. [Steeltoe][9] aplikace založené na síti.

* [Azure Key Vault][2]: Služba pro správu přihlašovacích údajů se správou hardwaru, která má úzkou integraci se službami Microsoft identity a výpočetními prostředky.

* [Azure monitor][3]: pro aplikace, které nasazují v Azure i v místním prostředí, zahrnuje sadu monitorovacích služeb.

* [Azure Security Center][4]: jednotnou správu zabezpečení a systém ochrany před internetovými útoky pro úlohy v místním prostředí, několika cloudech a Azure.

* [Azure Pipelines][5]: plně vybavená služba pro průběžnou integraci nebo průběžné vývojové prostředí (CI/CD), která dokáže automaticky nasazovat aktualizované aplikace pro jarní spouštění do Azure Pramenitého cloudu.

Následující diagram představuje návrh dobře navrženého centra a paprsků, který řeší výše uvedené požadavky:

![Diagram referenční architektury pro privátní aplikace](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>Veřejné aplikace

Následující seznam popisuje požadavky infrastruktury pro veřejné aplikace. Tyto požadavky jsou typické ve vysoce regulovaných prostředích.

* Příchozí přenosy by se měly spravovat minimálně pomocí Application Gateway nebo front Azure.
* Měla by být povolená Azure DDoS Protection Standard.
* Žádný přímý výstup na veřejný Internet, s výjimkou provozu řídicích rovin.
* Výstupní přenos by měl procházet virtuálním síťovým zařízením (síťové virtuální zařízení) centrální sítě (například Azure Firewall).
* Data v klidovém stavu by měla být zašifrovaná.
* Přenášená data by měla být zašifrovaná.
* Kanály nasazení DevOps se dají použít (například Azure DevOps) a vyžadují síťové připojení k jarnímu cloudu Azure.
* Přístup k zabezpečení nulové důvěryhodnosti od Microsoftu vyžaduje, aby byly tajné klíče, certifikáty a přihlašovací údaje uložené v zabezpečeném trezoru. Doporučená služba je Azure Key Vault.
* Záznamy DNS hostitele aplikace by měly být uložené v Azure Privátní DNS.
* Internetové adresy směrování by se měly ukládat ve veřejné službě DNS Azure.
* Překlad názvů hostitelů místně a v cloudu by měl být obousměrný.
* Musí se vyhovět aspoň jednomu bezpečnostnímu srovnávacímu testu.
* Závislosti služby Azure by měly komunikovat prostřednictvím koncových bodů služby nebo privátního odkazu.
* Skupiny prostředků spravované nasazením pro jarní Cloud v Azure se nesmí upravovat.
* Podsítě spravované nasazením pro jarní cloudové prostředí Azure se nesmí upravovat.
* Podsíť musí mít jenom jednu instanci jarního cloudu Azure.

Následující seznam obsahuje komponenty, které tvoří návrh:

* Místní síť
  * Služba DNS (Domain Name Service)
  * brána
* Předplatné centra
  * Azure Firewall podsíť
  * Application Gateway podsíť
  * Podsíť sdílených služeb
* Připojené předplatné
  * Virtual Network partnerský uzel
  * Podsíť Azure bastionu

Následující seznam popisuje služby Azure v této referenční architektuře:

* [Jarní cloud Azure][1]: spravovaná služba, která je navržená a optimalizovaná speciálně pro aplikace pro pružinové spouštění založené na jazyce Java a. [Steeltoe][9] aplikace založené na síti.

* [Azure Key Vault][2]: Služba pro správu přihlašovacích údajů se správou hardwaru, která má úzkou integraci se službami Microsoft identity a výpočetními prostředky.

* [Azure monitor][3]: pro aplikace, které nasazují v Azure i v místním prostředí, zahrnuje sadu monitorovacích služeb.

* [Azure Security Center][4]: jednotnou správu zabezpečení a systém ochrany před internetovými útoky pro úlohy v místním prostředí, několika cloudech a Azure.

* [Azure Pipelines][5]: plně vybavená služba pro průběžnou integraci nebo průběžné vývojové prostředí (CI/CD), která dokáže automaticky nasazovat aktualizované aplikace pro jarní spouštění do Azure Pramenitého cloudu.

* [Azure Application Gateway][6]: Nástroj pro vyrovnávání zatížení zodpovědný za provoz aplikací s snižováním zátěže TLS (Transport Layer Security) pracuje ve vrstvě 7.

* [Azure Application firewall][7]: funkce Azure Application Gateway, která poskytuje centralizovanou ochranu aplikací před běžnými zneužitím a ohrožením zabezpečení.

Následující diagram představuje návrh dobře navrženého centra a paprsků, který řeší výše uvedené požadavky:

![Diagram referenční architektury pro veřejné aplikace](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Místní připojení k jarnímu cloudu Azure

Aplikace běžící v Azure jaře cloudu můžou komunikovat s různými prostředky Azure, místními počítači a externími prostředky. Pomocí návrhu centrálního centra a paprsků můžou aplikace směrovat provoz externě nebo do místní sítě pomocí expresního směrování nebo virtuální privátní sítě (VPN) typu Site-to-site.

## <a name="azure-well-architected-framework-considerations"></a>Předpoklady pro Azure Well-Architected Framework

[Azure Well-Architected Framework][16] je sada identifikátorů GUID principy, která se bude řídit při vytváření silné základní infrastruktury. Rozhraní obsahuje následující kategorie: optimalizaci nákladů, provozní náklady, efektivita výkonu, spolehlivost a zabezpečení.

### <a name="cost-optimization"></a>Optimalizace nákladů

Vzhledem k povaze distribuovaného systémového návrhu je infrastruktura neustálému zvětšování realitou. Výsledkem této reality jsou neočekávané a neovladatelné náklady. Jarní cloud Azure je sestaven pomocí komponent, které se škálují, aby splňoval požadavky a optimalizoval náklady. Základem této architektury je služba Azure Kubernetes (AKS). Služba je navržená tak, aby snižovala složitost a provozní režii správy Kubernetes, která zahrnuje efektivitu za provozní náklady clusteru.

Můžete nasadit různé aplikace a typy aplikací do jediné instance služby Azure jaře Cloud. Služba podporuje automatické škálování aplikací aktivovaných metrikami nebo plány, které můžou zlepšit využití a cenovou efektivitu.

Ke snížení provozních nákladů můžete použít také Application Insights a Azure Monitor. Díky viditelnosti poskytované komplexním řešením protokolování můžete implementovat automatizaci, která umožňuje škálovat součásti systému v reálném čase. Můžete také analyzovat data protokolu a odhalit tak neefektivitu v kódu aplikace, který můžete řešit, abyste vylepšili celkové náklady a výkon systému.

### <a name="operational-excellence"></a>Efektivita provozu

Azure jaře Cloud řeší více aspektů provozní kvality. Tyto aspekty můžete kombinovat, abyste zajistili, že služba bude efektivně běžet v produkčním prostředí, jak je popsáno v následujícím seznamu:

* Azure Pipelines můžete použít k zajištění spolehlivého a konzistentního nasazení a zároveň vám pomůže vyhnout se lidské chybě.
* K ukládání dat protokolů a telemetrie můžete použít Azure Monitor a Application Insights.
  Můžete vyhodnotit shromážděná data protokolu a metriky a zajistit tak stav a výkon vašich aplikací. Funkce Application Performance Monitoring (APM) je plně integrovaná do služby prostřednictvím agenta Java. Tento agent nabízí přehled o všech nasazených aplikacích a závislostech bez nutnosti dalšího kódu. Další informace najdete v blogovém příspěvku bez snadného [monitorování aplikací a závislostí ve jarním cloudu Azure][15].
* Pomocí Azure Security Center můžete zajistit, aby aplikace udržovaly zabezpečení tím, že poskytují platformu pro analýzu a vyhodnocení poskytnutých dat.
* Služba podporuje různé vzory nasazení. Další informace najdete v tématu [Nastavení přípravného prostředí v Azure jaře cloudu][14].

### <a name="reliability"></a>Spolehlivost

Jarní cloud Azure je navržený s AKS jako základní komponentou. I když AKS poskytuje úroveň odolnosti prostřednictvím clusteringu, tato referenční architektura zahrnuje služby a požadavky na architekturu, aby se zvýšila dostupnost aplikace, pokud dojde k selhání komponenty.

Základem této architektury je, že při sestavování přesně definovaného centra a hvězdicové konstrukce zajišťuje, že ho můžete nasadit do několika oblastí. V případě použití privátní aplikace architektura používá Azure Privátní DNS k zajištění nepřetržité dostupnosti během geografické chyby. Pro případ použití veřejné aplikace je k dispozici přední vrátka Azure a Azure Application Gateway k zajištění dostupnosti.

### <a name="security"></a>Zabezpečení

Zabezpečení této architektury je řešeno jejich přistoupením k oborovým ovládacím prvkům a srovnávacím testům. Ovládací prvky v této architektuře jsou z [matrice řízení cloudu][19] (ccm) od služby [Cloud Security Alliance][18] (CSA) [Microsoft Azure a srovnávacího][20] MAFB (Foundation test) od [centra pro Internet Security][21] (CIS). V použitých ovládacích prvcích se zaměřujete na primární zásady návrhu zabezpečení zásad správného řízení, sítě a zabezpečení aplikací. Máte zodpovědnost za zpracování principů návrhu identity, správy přístupu a úložiště, protože se vztahují k vaší cílové infrastruktuře.

#### <a name="governance"></a>Zásady správného řízení

Primární aspekt zásad správného řízení, který tato architektura řeší, je oddělení síťových prostředků. V nástroji CCM se na řadičích domény – 08 doporučuje řízení vstupu a výstupu pro datacentrum. Pro uspokojení ovládacího prvku používá architektura návrh rozbočovače a paprsku pomocí skupin zabezpečení sítě (skupin zabezpečení sítě) k filtrování provozu v západním západním provozu mezi prostředky. Architektura také filtruje provoz mezi centrálními službami v centru a prostředcích na paprske. Architektura používá instanci Azure Firewall ke správě provozu mezi Internetem a prostředky v rámci architektury.

Následující seznam obsahuje ovládací prvek, který řeší zabezpečení datového centra v tomto odkazu:

| CSA – ID ovládacího prvku CCM | Doména ovládacího prvku CSA CCM |
| :----------------- | :----------------------|
| ŘADIČE DOMÉNY – 08 | Záznam oprávněných osob v zabezpečení datového centra |

#### <a name="network"></a>Síť

Návrh sítě podporující tuto architekturu je odvozený od tradičního modelu hvězdicového a paprskového modelu. Toto rozhodnutí zajistí, že je izolace sítě základní konstrukcí. Ideographic ovládacího prvku CCM – 06 doporučuje, aby se provoz mezi sítěmi a virtuálními počítači omezil a kontroloval mezi důvěryhodnými a nedůvěryhodnými prostředími. Tato architektura přijímá kontrolu implementací skupin zabezpečení sítě pro provoz na východ a západním provozu a Azure Firewall pro přenos v severním jižním provozu. IPY ovládacího prvku CCM – 04 doporučuje, aby infrastruktura používala zabezpečené síťové protokoly pro výměnu dat mezi službami. Služby Azure, které podporují tuto architekturu, využívají standardní zabezpečené protokoly, jako je třeba TLS pro HTTP a SQL.

Následující seznam obsahuje ovládací prvky CCM, které řeší zabezpečení sítě v tomto odkazu:

| CSA – ID ovládacího prvku CCM | Doména ovládacího prvku CSA CCM |
| :----------------- | :----------------------|
| IPY – 04             | Síťové protokoly      |
| IDEOGRAPHIC-06             | Zabezpečení sítě       |

Implementace sítě je dále zabezpečena definováním ovládacích prvků z MAFB. Ovládací prvky zajišťují, že provoz do prostředí je omezen z veřejného Internetu.

V následujícím seznamu jsou uvedeny ovládací prvky CIS, které řeší zabezpečení sítě v tomto odkazu:

| ID ovládacího prvku SNS | Popis ovládacího prvku CIS |
| :------------- | :---------------------- |
| 6,2 | Zajistěte, aby přístup přes protokol SSH byl omezen z Internetu. |
| 6.3 | Zajistěte, aby žádné databáze SQL nepovolovaly příchozí přenosy 0.0.0.0/0 (jakákoli IP adresa). |
| 6.5 | Zajistěte, aby byla Network Watcher povolena. |
| 6.6 | Zajistěte, aby příchozí přenos přes protokol UDP byl omezený z Internetu. |

V případě nasazení v zabezpečeném prostředí vyžaduje přenos dat ze služby Azure jaře Cloud z Azure. Aby to bylo možné, musíte pro síť a aplikace zobrazit pravidla uvedená v části [zodpovědnosti zákazníka za provozování jarního cloudu Azure ve virtuální](./spring-cloud-vnet-customer-responsibilities.md)síti.

#### <a name="application-security"></a>Zabezpečení aplikací

Tento objekt návrhu pokrývá základní součásti identity, ochrany dat, správy klíčů a konfigurace aplikací. Aplikace nasazená v Azure jaře cloudu je navržena s nejnižším oprávněním vyžadovaným pro funkci. Sada autorizačních ovládacích prvků je přímo v souvislosti s ochranou dat při používání služby. Správa klíčů posiluje tento přístup k vrstvené aplikaci na úrovni zabezpečení.

Následující seznam obsahuje ovládací prvky CCM, které řeší správu klíčů v tomto odkazu:

| CSA – ID ovládacího prvku CCM | Doména ovládacího prvku CSA CCM |
| :----------------- | :--------------------- |
| EKM-01 | Oprávnění k šifrování a správě klíčů |
| EKM-02 | Šifrování a generování klíče správy klíčů |
| EKM – 03 | Šifrování a ochrana citlivých dat správy klíčů |
| EKM – 04 | Šifrování a úložiště správy klíčů a přístup |

Pro správu klíčů a používání šifrovacích protokolů k ochraně citlivých dat doporučují zásady a postupy pro CCM, EKM-02 a EKM-03. EKM-01 doporučuje, aby všechny kryptografické klíče měly identifikovatelné vlastníky, aby je bylo možné spravovat. EKM-04 doporučuje použití standardních algoritmů.

V následujícím seznamu jsou uvedeny ovládací prvky CIS, které řeší správu klíčů v tomto odkazu:

| ID ovládacího prvku SNS | Popis ovládacího prvku CIS |
| :------------- | :---------------------- |
| 8.1 | Zajistěte, aby bylo datum vypršení platnosti nastaveno u všech klíčů. |
| 8.2 | Zajistěte, aby bylo datum vypršení platnosti nastaveno u všech tajných kódů. |
| 8.4 | Ujistěte se, že je Trezor klíčů obnovitelný. |

Ovládací prvky CIS 8,1 a 8,2 doporučují, aby byla pro zajištění prosazování rotace nastavena data vypršení platnosti pro přihlašovací údaje. Řízení CIS 8,4 zajišťuje, aby bylo možné obnovit obsah trezoru klíčů, aby se zachovala Kontinuita podnikových aplikací.

Aspekty zabezpečení aplikací nastavily základ pro použití této referenční architektury pro podporu jarních úloh v Azure.

## <a name="next-steps"></a>Další kroky

Prozkoumejte tuto referenční architekturu prostřednictvím nasazení ARM, Terraformu a Azure CLI, která jsou k dispozici v úložišti [referenčních cloudů Azure jaře][10] .

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./spring-cloud-tutorial-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./spring-cloud-howto-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/