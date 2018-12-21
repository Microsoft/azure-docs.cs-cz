---
title: Spuštění clusteru Cassandra v Linuxu v Azure z Node.js
description: Jak spustit Cassandra cluster z aplikace Node.js v Linuxu ve službě Azure Virtual Machines
services: virtual-machines-linux
documentationcenter: nodejs
author: craigshoemaker
manager: routlaw
editor: ''
tags: azure-service-management
ms.assetid: 30de1f29-e97d-492f-ae34-41ec83488de0
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: cshoe
ms.openlocfilehash: 3f7b216be79be1307a5668d6686fd73a27ae5574
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237855"
---
# <a name="run-a-cassandra-cluster-on-linux-in-azure-with-nodejs"></a>Spuštění clusteru Cassandra v Linuxu v Azure pomocí Node.js

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Šablony Resource Manageru pro [Datastax Enterprise](https://azure.microsoft.com/documentation/templates/datastax) a [podnítit clusteru a Cassandra na CentOS](https://azure.microsoft.com/documentation/templates/spark-and-cassandra-on-centos/).

## <a name="overview"></a>Přehled
Microsoft Azure je otevřený Cloudová platforma, na kterém běží Microsoft i jiných společností než Microsoft software, který obsahuje operační systémy, aplikační servery, zasílání zpráv middleware, jakož i databází SQL a NoSQL z obou modelů obchodních a open source. Vytváření odolných služeb na veřejné cloudy včetně Azure vyžaduje pečlivé plánování a rozhodnout vědomě a záměrně architektury pro oba servery aplikace jako vrstvy kontejneru úložiště. Cassandra v distribuovaném úložišti architektura přirozeně pomáhá vytvářet vysoce dostupné systémy, které jsou odolné proti chybám pro selhání clusteru. Cassandra je cloudové škálování databází NoSQL udržována cassandra.apache.org Apache Software Foundation. Cassandra je napsána v jazyce Java. Proto poběží na platformách Windows a Linux.

Hlavním cílem tohoto článku je ukázat nasazení databáze Cassandra jako cluster Centrum jednoho a víc dat, která používá Azure Virtual Machines a virtuálních sítí na Ubuntu. Nasazení clusteru pro úlohy v produkčním prostředí optimalizované sahá nad rámec tohoto článku vyžaduje konfiguraci vícediskového uzlu, příslušný kanál topologie návrh a modelování pro podporu potřebné replikace, konzistenci dat, propustnosti a vysoce dat požadavky na dostupnost.

Tento článek má základní přístup k zobrazení, co je součástí vytváření clusteru Cassandra porovnání Dockeru, Chef nebo Puppet, které můžete provést nasazení infrastruktury mnohem snazší.  

## <a name="the-deployment-models"></a>Modely nasazení
Síťových služeb Microsoft Azure umožňuje nasazení izolovaná privátní clustery přístupu, které je možné omezit na dosažení zabezpečení podrobné sítě.  Tento článek je o zobrazení nasazení databáze Cassandra na základní úrovni, není zaměřit na úrovně konzistence a návrh úložiště optimální propustnosti. Tady je seznam požadavky na síť pro hypotetické clusteru:

* Externí systémy nelze získat přístup k databázi Cassandra z v rámci nebo mimo Azure
* Musí být za nástroj pro vyrovnávání zatížení pro provoz thrift clusteru Cassandra
* Nasazení Cassandra uzly ve dvou skupinách v každé datové centrum pro rozšířené clusteru skupiny dostupnosti
* Uzamknout clusteru tak, která jen pro serverovou farmu aplikace má přístup k databázi přímo
* Žádné veřejné koncových bodech sítě než SSH
* Každý uzel Cassandra vyžaduje pevnou interní IP adresu

Cassandra je možné nasadit do jedné oblasti Azure nebo do více oblastí podle distribuovaná povaha zatížení. Můžete použít model nasazení ve více oblastech poskytovat služby koncovým uživatelům blíže do určité zeměpisné oblasti přes stejnou infrastrukturu Cassandra. Cassandra pro předdefinovaný uzel replikace přebírá péče o synchronizaci více hlavních databází zapíše pocházející z několika datových centrech a poskytuje konzistentní zobrazení dat do aplikace. Nasazení v různých oblastech může také pomoct s zmírnění rizik z širší výpadek služeb Azure. V souladu s rozdílnými potřebami cíle bodu obnovení aplikací pomáhá společnosti Cassandra laditelnou konzistenci a topologii replikace.

### <a name="single-region-deployment"></a>Nasazení v jedné oblasti
Pojďme začít s nasazením v jedné oblasti a získejte poznatky o při vytváření modelu ve více oblastech. Virtuální sítě Azure slouží k vytvoření podsítě izolované tak, že výše uvedené požadavky zabezpečení sítě mohou být splněny.  Postup při vytváření nasazení jedné oblasti používá Ubuntu 14.04 LTS a Cassandra 2.08. Proces však můžete snadno přijímají na další varianty Linuxu. Následují některé systémové vlastnosti nasazení jedné oblasti.  

**Vysoká dostupnost:** Cassandra uzly zobrazené na obrázku 1 jsou nasazené na dvě skupiny dostupnosti tak, aby uzly jsou rozloženy mezi několik domén selhání pro zajištění vysoké dostupnosti. Virtuální počítače s poznámkami s každou skupinu dostupnosti je namapována na 2 domén selhání. Azure používá koncept doména selhání ke správě neplánovaným výpadkům (například selhání hardwaru nebo softwaru). Koncept upgradovací doméně (například hostitel nebo opravy operačního systému hosta/upgradu, upgrady aplikací) se používá pro správu naplánované časové prodlevy. Podrobnosti najdete na [zotavení po havárii a vysoká dostupnost pro aplikace Azure](https://msdn.microsoft.com/library/dn251004.aspx) pro roli chybových nebo upgradovacích doménách v dosažení vysoké dostupnosti.

![Nasazení v jedné oblasti](./media/cassandra-nodejs/cassandra-linux1.png)

Obrázek 1: Nasazení jedné oblasti

Všimněte si, že v době psaní tohoto návodu, Azure neumožňuje mít explicitní mapování skupinu virtuálních počítačů k doméně selhání konkrétní; v důsledku toho i s modelem nasazení znázorňuje obrázek 1 je statisticky pravděpodobné, že všechny virtuální počítače mohou být namapovány na dvě domény selhání místo čtyř.

**Provoz Thrift služby Vyrovnávání zatížení:** Thrift klientské knihovny uvnitř webový server připojení ke clusteru prostřednictvím interní load balancer. To vyžaduje proces přidávání interního nástroje pro podsíť "data" (viz obrázek 1) v rámci cloudové služby, který je hostitelem clusteru Cassandra. Po definování interní služby load balancer každý uzel vyžaduje koncový bod s vyrovnáváním zatížení přidávané s poznámkami sadu s vyrovnáváním zatížení s názvem nástroje pro vyrovnávání zatížení dříve definované. Zobrazit [interního Vyrovnávání zatížení Azure ](../../../load-balancer/load-balancer-internal-overview.md)další podrobnosti.

**Cluster rychlosti:** je důležité vybrat Většina uzlů s vysokou dostupností pro rychlosti, protože nové uzly komunikovat s počáteční uzly ke zjišťování topologie clusteru. Jeden uzel v každé skupině dostupnosti je určený jako počáteční uzly, aby jediný bod selhání.

**Faktor replikace a úrovně konzistence:** Cassandra sestavení se změnami a data s vysokou dostupností odolnosti je charakterizovaná faktor replikace (RF – počet kopií každého řádku uložené v clusteru) a úroveň konzistence (počet replik být načtených/zapsaných před vrácením výsledku do volajícího). Faktor replikace specifikovaném během vytvoření prostoru KLÍČŮ (podobně jako relační databáze), zatímco úroveň konzistence je určena při vydání dotazů CRUD. Dokumentaci k Cassandra [konfigurací pro zajištění konzistence](https://docs.datastax.com/en/cassandra/3.0/cassandra/dml/dmlConfigConsistency.html) podrobnosti konzistence a vzorce pro výpočty kvora.

Cassandra podporuje dva typy modelů integritu dat – konzistencí a konzistencí; Faktor replikace a úroveň konzistence společně určují, pokud data jsou konzistentní vzhledem k aplikacím, jako operace zápisu označena jako dokončená nebo konečnou konzistenci. Například zadání KVORA podle úrovně konzistentnosti vždy zajišťuje konzistenci dat při libovolné úrovně konzistence, nižší než počet replik k zapsání podle potřeby dosáhnout KVORA (například jeden) výsledky v datech se nakonec konzistentní.

8 uzlů clusteru s faktorem replikace 3 a KVORA vidíte výše, (2 uzly jsou čteným nebo zapsaným konzistence) úrovně konzistence čtení a zápis, přežijí teoretické ztráty nanejvýš 1 uzel na skupinu replikace před sledováním spuštění aplikace došlo k chybě. Předpokladem je, že všechno, co mají klíče prostory dobře s vyrovnáváním čtení/zápisu požadavků.  Následují parametry použité v nasazeném clusteru:

Konfigurace clusteru Cassandra jedné oblasti:

| Parametr clusteru | Hodnota | Poznámky |
| --- | --- | --- |
| Počet uzlů (N) |8 |Celkový počet uzlů v clusteru |
| Faktor replikace (RF) |3 |Počet replik zadaného řádku |
| Úroveň konzistence (zápis) |QUORUM[(RF/2) +1) = 2] je výsledkem vzorce se zaokrouhlí směrem dolů |Zapíše nanejvýš 2 repliky před odesláním odpověď na volajícího 3 repliky se zapisují konečnou konzistenci způsobem. |
| Úroveň konzistence (čtení) |KVORA [(RF/2) + 1 = 2] je zaokrouhlena výsledek vzorce |Přečte 2 repliky před odesláním odpověď na volajícího. |
| Strategie replikace |Viz NetworkTopologyStrategy [replikace dat](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archDataDistributeAbout.html) v dokumentaci k Cassandra Další informace |Topologie nasazení rozumí a umístí repliky na uzly, tak, aby všechny repliky není ukládaly na stejné rack |
| Snitch |Viz GossipingPropertyFileSnitch [přepínače](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archSnitchesAbout.html) v dokumentaci k Cassandra Další informace |NetworkTopologyStrategy používá koncept snitch k pochopení topologie. GossipingPropertyFileSnitch poskytuje lepší kontrolu při mapování každý uzel do datového centra a stojanu. Cluster použije stálém kontaktu k šíření těchto informací. To je mnohem jednodušší v vzhledem k PropertyFileSnitch dynamické nastavení protokolu IP |

**Azure důležité informace pro Cassandra Cluster:** možnost Microsoft Azure Virtual Machines využívá úložiště objektů Blob v Azure pro trvalost disku; Azure Storage ukládá tří replik každého disku tím se dosahuje vysoké odolnosti. To znamená, že každý řádek dat, vložen do tabulky Cassandra jsou už uložená v tří replik. Takže konzistence dat je už se postaral o i v případě, že faktor replikace (RF) je 1. Hlavní problém se faktor replikace, přičemž 1 je, že aplikace i v případě selhání jednoho uzlu Cassandra vyskytne výpadek. Nicméně pokud uzel je mimo provoz kvůli problémům (například hardware, software selhání systému) společnost kontroler prostředků infrastruktury Azure, zřídí nový uzel na příslušné místo použití stejné jednotky úložiště. Zřízení nového uzlu do nahradí to původní může trvat několik minut.  Podobně jako na aktivity plánované údržby, jako jsou změny operačního systému hosta, upgraduje Cassandra a změny aplikace kontroler prostředků infrastruktury Azure provádí postupné upgrady uzlů v clusteru.  Postupné upgrady také může trvat dolů několika uzlů najednou, a proto clusteru může docházet k krátký výpadek pro několik oddílů. Data nejsou však ztraceno v důsledku vestavěná redundance služby Azure Storage.  

Pro systémy, které jsou nasazené do Azure, která nevyžaduje vysokou dostupnost (třeba přibližně 99,9 což je totéž jako 8.76 hodin a rok; viz [vysoké dostupnosti](http://en.wikipedia.org/wiki/High_availability) podrobnosti) je možné spustit s RF = 1 a úroveň konzistence = jedné.  Pro aplikace s požadavky na vysokou dostupnost, RF = 3 a úroveň konzistence = KVORA toleruje výpadkům jeden z uzlů z replik. RF = 1 v tradiční nasazení (například v místním prostředí) nelze použít kvůli ztrátě dat vyplývající z problémy, jako je selhání disku.   

## <a name="multi-region-deployment"></a>Nasazení v různých oblastech
Model pro replikaci a konzistence datového centra s deklaracemi Cassandra je popsané výše pomáhá bez nutnosti jakékoli externího nástroje pro nasazení ve více oblastech pomocí. Tím se liší od tradičních relačních databází, ve kterém Instalační program pro zrcadlení databáze pro více hlavních zápisy může být složité. Cassandra v instalačním programu ve více oblastech můžou pomoct s scénáře použití, včetně scénářů:

**Blízkosti nasazení založené na:** aplikací s více tenanty, Vymazat mapování uživatelů tenanta-k-oblasti, můžete prospěch podle cluster pro více oblastí s nízkou latencí. Například učení systémy pro správu pro vzdělávací instituce můžou nasadit cluster distribuované v oblastech USA – východ a USA – západ k poskytování odpovídajících kampusy pro transakční a analýzy. Data může být místně konzistentní vzhledem k aplikacím na čas čtení a zápisy a může být konzistentní v obou oblastech. Existují jiné příklady jako je distribuce multimédií, elektronického obchodování a veškerých kódů a všechno, co se uživatel geograficky koncentrované slouží základní je vhodné využít případu pro tento model nasazení.

**Vysoká dostupnost:** zálohování je klíčovým faktorem k dosažení vysoké dostupnosti hardwaru a softwaru; podrobnosti najdete v části vytváření spolehlivých cloudových systémů na Microsoft Azure. V Microsoft Azure je jenom spolehlivě dosažení skutečnou redundanci nasazení clusteru ve více oblastech. Aplikace je možné nasadit v režimu aktivní aktivní nebo aktivní pasivní a je-li jednu z oblastí je vypnutý, Azure Traffic Manageru můžete přesměrování provozu na aktivní oblast.  Při nasazení jedné oblasti, pokud je dostupnost 99,9, nasazení se dvěma oblastmi dosáhnout dostupnosti 99.9999 vypočítanou ze vzorce: (1-(1-0.999) * (1-0,999)) * 100); viz výše papíru podrobnosti.

**Zotavení po havárii:** clusteru Cassandra ve více oblastech, pokud správně navržená dokázal dat center výpadků. Pokud jedna oblast je vypnutý, aplikace nasazená do jiných oblastí začít obsluhující koncovým uživatelům. Stejně jako jakékoli jiné obchodní kontinuity podnikových procesů implementace aplikace musí být odolný vůči chybám pro ztrátu některých dat. vyplývající z dat v asynchronní kanálu. Ale Cassandra díky obnovení mnohem rychlejší, než doba, za kterou procesy obnovení databází. Obrázek 2 ukazuje typické nasazení ve více oblastech model s osmi uzlů v jednotlivých oblastech. Obě oblasti jsou zrcadlových obrazech vzájemně pro stejný symetrie; návrhy reálného světa, závisí na typu úlohy (například transakční a analytické), cíle bodu obnovení, RTO, konzistenci dat a požadavky na dostupnost.

![Nasazení s více oblastmi](./media/cassandra-nodejs/cassandra-linux2.png)

Obrázek 2: Nasazení ve více oblastech Cassandra

### <a name="network-integration"></a>Integrace sítě
Sady virtuálních počítačů nasazených na soukromých sítích na dvě oblasti komunikuje mezi sebou pomocí tunelového připojení sítě VPN. Tunelového připojení sítě VPN připojení dvou bran softwaru zřídit během procesu nasazení sítě. Obě oblasti mají podobné síťové architektury z hlediska podsítě "www" a "data"; Sítě Azure umožňuje vytvářet libovolný počet podsítí podle potřeby a použít seznamy řízení přístupu podle potřeby zabezpečení sítě. Při navrhování topologie clusteru, mezi virtuálními sítěmi latence při komunikaci datového centra a ekonomický dopad provozu sítě se potřeba považovat za.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Konzistence dat pro nasazení více datového centra
Distribuované nasazení musí být vědomi clusteru topologie dopad na propustnost a vysokou dostupnost. RF a úroveň konzistence musí být vybrán tak, že kvora nejsou závislé na dostupnosti datových centrech.
Pro systém, který potřebuje vysokou konzistence LOCAL_QUORUM pro úroveň konzistence (pro čtení a zápisy) zajišťuje, že místní operací čtení a zápisu jsou obslouženy z místní uzlů během data asynchronně replikují do vzdálené datových center.  Tabulka 2 shrnuje podrobnosti o konfiguraci pro cluster pro více oblastí uvedených dále v zápisu nahoru.

**Konfigurace clusteru Cassandra dvěma oblastmi**

| Parametr clusteru | Hodnota | Poznámky |
| --- | --- | --- |
| Počet uzlů (N) |8 + 8 |Celkový počet uzlů v clusteru |
| Faktor replikace (RF) |3 |Počet replik zadaného řádku |
| Úroveň konzistence (zápis) |LOCAL_QUORUM [(sum(RF)/2) +1) = 4] výsledek vzorce se zaokrouhlí směrem dolů |2 uzly se zapisují do prvního datového centra synchronně, hodnota Další 2 uzly, které jsou potřebné pro kvora se asynchronně zapíše do 2. datové centrum. |
| Úroveň konzistence (čtení) |LOCAL_QUORUM ((RF/2) + 1) = 2, výsledek vzorce se zaokrouhlí směrem dolů |Žádosti o čtení jsou obslouženy z pouze jedna oblast; 2 uzly jsou čtení, před odesláním odpověď zpět klientovi. |
| Strategie replikace |Viz NetworkTopologyStrategy [replikace dat](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archDataDistributeAbout.html) v dokumentaci k Cassandra Další informace |Topologie nasazení rozumí a umístí repliky na uzly, tak, aby všechny repliky není ukládaly na stejné rack |
| Snitch |Viz GossipingPropertyFileSnitch [Snitches](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archSnitchesAbout.html) v dokumentaci k Cassandra Další informace |NetworkTopologyStrategy používá koncept snitch k pochopení topologie. GossipingPropertyFileSnitch poskytuje lepší kontrolu při mapování každý uzel do datového centra a stojanu. Cluster použije stálém kontaktu k šíření těchto informací. To je mnohem jednodušší v vzhledem k PropertyFileSnitch dynamické nastavení protokolu IP |

## <a name="the-software-configuration"></a>KONFIGURACE SOFTWARU
Během nasazení se používají následujícími verzemi softwaru:

<table>
<tr><th>Software</th><th>Zdroj</th><th>Verze</th></tr>
<tr><td>PROSTŘEDÍ JRE    </td><td>[PROSTŘEDÍ JRE 8](https://aka.ms/azure-jdks) </td><td>8U5</td></tr>
<tr><td>JNA    </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu    </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Zjednodušuje nasazení, stáhněte si potřebný software na plochu. Nahrajte ho do image Ubuntu šablony vytvořit jako předpokladem nasazení clusteru.

Stáhněte si výše uvedený software do adresáře dobře známé stahování (například %TEMP%/downloads na Windows nebo ~/Downloads na většině distribucí systému Linux nebo Mac) v místním počítači.

### <a name="create-ubuntu-vm"></a>VYTVOŘENÍ VIRTUÁLNÍHO POČÍTAČE S UBUNTU
V tomto kroku procesu vytvoření Ubuntu image pomocí požadovaného softwaru tak, že image lze opětovně použít pro zřizování několik uzlů Cassandra.  

#### <a name="step-1-generate-ssh-key-pair"></a>Krok 1: Generování páru klíčů SSH
Azure potřebuje X509 veřejný klíč, který je PEM nebo DER kódování při jeho zřizování. Vygenerujte pár veřejného a privátního klíče pomocí pokynů v tom, jak použít SSH s Linuxem v Azure. Pokud budete chtít použít putty.exe jako klienta SSH buď na Windows nebo Linux, budete muset převést kódovaný PEM privátního klíče RSA PPK formátu s využitím puttygen.exe. Pokyny k tomuto najdete na webové stránce výše.

#### <a name="step-2-create-ubuntu-template-vm"></a>Krok 2: Vytvoření šablony virtuálního počítače se systémem Ubuntu
Vytvoření šablony virtuálního počítače, přihlaste se na webu Azure portal a použijte následující posloupnost: klikněte na tlačítko Nový, výpočetní prostředky, virtuální počítač, z GALERIE, UBUNTU, Ubuntu Server 14.04 LTS a potom klikněte na šipku vpravo. Kurz popisuje, jak vytvořit virtuální počítač s Linuxem najdete v tématu vytvořit s Linuxem spuštěný virtuální počítač.

Na obrazovce "Konfigurace virtuálního počítače" #1 zadejte následující informace:

<table>
<tr><th>NÁZEV POLE              </td><td>       HODNOTA POLE               </td><td>         POZNÁMKY                </td><tr>
<tr><td>DATUM VYDÁNÍ VERZE    </td><td> Vyberte datum z rozevíracího seznamu</td><td></td><tr>
<tr><td>NÁZEV VIRTUÁLNÍHO POČÍTAČE    </td><td> servery CAS template                   </td><td> Toto je název hostitele virtuálního počítače </td><tr>
<tr><td>ÚROVEŇ                     </td><td> STANDARD                           </td><td> Ponechte výchozí nastavení              </td><tr>
<tr><td>VELIKOST                     </td><td> A1                              </td><td>Vyberte virtuální počítač, na základě potřeb vstupně-výstupní operace; pro tento účel ponechte výchozí nastavení </td><tr>
<tr><td> NOVÉ UŽIVATELSKÉ JMÉNO             </td><td> localadmin                       </td><td> "admin" je jako vyhrazené uživatelské jméno v Ubuntu xx 12. a po</td><tr>
<tr><td> OVĚŘOVÁNÍ         </td><td> Klikněte na zaškrtávací políčko                 </td><td>Zkontrolujte, jestli chcete zabezpečit pomocí klíče SSH </td><tr>
<tr><td> CERTIFIKÁT             </td><td> Název souboru certifikátu veřejného klíče </td><td> Použít veřejný klíč dříve generované</td><tr>
<tr><td> Nové heslo    </td><td> silné heslo </td><td> </td><tr>
<tr><td> Potvrzení hesla    </td><td> silné heslo </td><td></td><tr>
</table>

Na obrazovce "Konfigurace virtuálního počítače" #2 zadejte následující informace:

<table>
<tr><th>NÁZEV POLE             </th><th> HODNOTA POLE                       </th><th> POZNÁMKY                                 </th></tr>
<tr><td> CLOUDOVÉ SLUŽBY    </td><td> Vytvořit novou cloudovou službu    </td><td>Cloudová služba je kontejneru výpočetní prostředky, jako jsou virtuální počítače</td></tr>
<tr><td> NÁZEV CLOUDOVÉ SLUŽBY DNS    </td><td>ubuntu-template.cloudapp.net    </td><td>Pojmenujte nástroje pro vyrovnávání zatížení nezávislá počítače</td></tr>
<tr><td> OBLAST NEBO SKUPINA VZTAHŮ/VIRTUÁLNÍ SÍTĚ </td><td>    Západní USA    </td><td> Vyberte oblast, ze kterého vaše webové aplikace přístup ke clusteru Cassandra</td></tr>
<tr><td>ÚČET ÚLOŽIŠTĚ </td><td>    Použijte výchozí.    </td><td>Použít výchozí účet úložiště nebo předem vytvořený účet úložiště v konkrétní oblasti</td></tr>
<tr><td>SKUPINA DOSTUPNOSTI </td><td>    Žádný </td><td>    Ponechte prázdné</td></tr>
<tr><td>KONCOVÉ BODY    </td><td>Použijte výchozí. </td><td>    Použít výchozí konfiguraci SSH </td></tr>
</table>

Klikněte na šipku vpravo, nechat výchozí hodnoty na obrazovce #3. Klikněte na tlačítko "kontrola" pro dokončení procesu zřizování virtuálních počítačů. Po několika minutách by měly mít virtuální počítač s názvem "ubuntu-template" stavu "spuštěno".

### <a name="install-the-necessary-software"></a>NAINSTALOVAT NEZBYTNÝ SOFTWARE
#### <a name="step-1-upload-tarballs"></a>Krok 1: Uložení tarballs
Pomocí spojovacího bodu služby nebo pscp, zkopírujte softwaru dříve staženy do adresáře ~/downloads formátu následující příkaz:

##### <a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp server-jre-8u5-linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz
Opakujte výše uvedeného příkazu pro prostředí JRE také jako Cassandra bits.

#### <a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>Krok 2: Příprava adresářovou strukturu a extrahovat archivy
Přihlášení k virtuálnímu počítači a vytvářet adresářovou strukturu a extrahujte softwaru jako superuživatele, pomocí níže uvedeného skriptu bash:

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


Pokud vložíte tento skript do okna vim, ujistěte se, že chcete odebrat návratu na začátek ("\r") pomocí následujícího příkazu:

    tr -d '\r' <infile.sh >outfile.sh

#### <a name="step-3-edit-etcprofile"></a>Krok 3: Úprava etc/profile
Připojte na konec:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

#### <a name="step-4-install-jna-for-production-systems"></a>Krok 4: Instalace JNA pro produkční systémy.
Použijte následující sekvence příkazů: následující příkaz nainstaluje jna 3.2.7.jar a platforma 3.2.7.jar jna /usr/share.java directory sudo apt-Get nainstalovat libjna – java

Vytvořte symbolické odkazy v adresáři $CASS_HOME/lib, aby spouštěcí skript Cassandra najdete tyto kromě souborů JAR:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

#### <a name="step-5-configure-cassandrayaml"></a>Krok 5: Konfigurace cassandra.yaml
Upravte cassandra.yaml na každý virtuální počítač tak, aby odrážely konfiguraci potřebné pro všechny virtuální počítače [nemůžete upravit název této konfigurace během zřizování skutečné]:

<table>
<tr><th>Název pole   </th><th> Hodnota  </th><th>    Poznámky </th></tr>
<tr><td>název_clusteru </td><td>    "CustomerService"    </td><td> Použijte název, který odráží vaše nasazení</td></tr>
<tr><td>listen_address    </td><td>[pole ponechte prázdné]    </td><td> Odstranit "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[pole ponechte prázdné]    </td><td> Odstranit "localhost" </td></tr>
<tr><td>rychlosti    </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"    </td><td>Seznam všech IP adres, které jsou určené jako rychlosti.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> To NetworkTopologyStrateg používá pro odvození datového centra a rack virtuálního počítače</td></tr>
</table>

#### <a name="step-6-capture-the-vm-image"></a>Krok 6: Zachycení image virtuálního počítače
Přihlášení k virtuálnímu počítači pomocí názvu hostitele (hk template.cloudapp.net certifikační autority) a privátní klíč SSH předtím vytvořili. V tématu Jak použít SSH s Linuxem v Azure pro podrobnosti o tom, jak se přihlásit pomocí příkazu ssh nebo putty.exe.

Spusťte následující posloupnost akcí k zachycení bitové kopie:

##### <a name="1-deprovision"></a>1. Zrušení zřízení
Pomocí příkazu "sudo waagent – zrušení zřízení + uživatel" Odebrat informace o konkrétní instanci virtuálního počítače. Naleznete v tématu [jak zachytit virtuální počítač s Linuxem](capture-image-classic.md) pro použití jako šablony další podrobnosti o procesu zachycení bitové kopie.

##### <a name="2-shut-down-the-vm"></a>2: vypnout virtuální počítač
Ujistěte se, že virtuální počítač je zvýrazněn a klikněte na odkaz vypnutí na panelu příkazů dole.

##### <a name="3-capture-the-image"></a>3: zachycení bitové kopie
Ujistěte se, že virtuální počítač je zvýrazněn a klikněte na odkaz zachycení na panelu příkazů dole. V další obrazovku, poskytněte název IMAGE, která je (například hk-cas-2-08-ub-14-04-2014071), příslušný popis OBRÁZKU a klikněte na tlačítko "" zaškrtnutí dokončete proces zachycení.

Tento proces trvá několik sekund a na obrázku by měla být k dispozici v části Moje Image z Galerie imagí. Zdrojový virtuální počítač se automaticky odstraní po úspěšně zachycením image. 

## <a name="single-region-deployment-process"></a>Proces nasazení jedné oblasti
**Krok 1: Vytvoření virtuální sítě** Přihlaste se k webu Azure portal a vytvoření virtuální sítě (klasické) s atributy uvedené v následující tabulce. Zobrazit [vytvořit virtuální síť (classic) pomocí webu Azure portal](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) podrobné pokyny procesu.      

<table>
<tr><th>Atribut název virtuálního počítače</th><th>Hodnota</th><th>Poznámky</th></tr>
<tr><td>Název</td><td>vnet-cass-west-us</td><td></td></tr>
<tr><td>Oblast</td><td>Západní USA</td><td></td></tr>
<tr><td>Servery DNS</td><td>Žádný</td><td>Tuto zprávu ignorovat, protože nepoužíváme DNS Server</td></tr>
<tr><td>Adresní prostor</td><td>10.1.0.0/16</td><td></td></tr>    
<tr><td>Počáteční IP</td><td>10.1.0.0</td><td></td></tr>    
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Přidejte následující podsítě:

<table>
<tr><th>Název</th><th>Počáteční IP</th><th>CIDR</th><th>Poznámky</th></tr>
<tr><td>web</td><td>10.1.1.0</td><td>/24 (251)</td><td>Podsítě webové farmy</td></tr>
<tr><td>data</td><td>10.1.2.0</td><td>/24 (251)</td><td>Podsíť pro uzly databáze</td></tr>
</table>

Data a Web podsítě se dají chránit pomocí skupin zabezpečení sítě pokrytí je mimo rozsah pro účely tohoto článku.  

**Krok 2: Zřizování virtuálních počítačů** pomocí bitové kopie, které jste vytvořili dříve, můžete vytvořit následující virtuální počítače v cloudovými servery "hk-c-svc západní" a svázat ho s příslušnými podsítěmi, jak je znázorněno níže:

<table>
<tr><th>Název počítače    </th><th>Podsíť    </th><th>IP adresa    </th><th>Skupina dostupnosti</th><th>DC/stojan</th><th>Počáteční hodnoty?</th></tr>
<tr><td>hk-c1-west-us    </td><td>data    </td><td>10.1.2.4    </td><td>hk-c-aset-1    </td><td>DC = WESTUS rack = rack1 </td><td>Ano</td></tr>
<tr><td>hk-c2-west-us    </td><td>data    </td><td>10.1.2.5    </td><td>hk-c-aset-1    </td><td>DC = WESTUS rack = rack1    </td><td>Ne </td></tr>
<tr><td>hk-c3-west-us    </td><td>data    </td><td>10.1.2.6    </td><td>hk-c-aset-1    </td><td>DC = WESTUS rack = rack2    </td><td>Ano</td></tr>
<tr><td>hk-c4-west-us    </td><td>data    </td><td>10.1.2.7    </td><td>hk-c-aset-1    </td><td>DC = WESTUS rack = rack2    </td><td>Ne </td></tr>
<tr><td>hk-c5-west-us    </td><td>data    </td><td>10.1.2.8    </td><td>hk-c-aset-2    </td><td>DC = WESTUS rack = rack3    </td><td>Ano</td></tr>
<tr><td>hk-c6-west-us    </td><td>data    </td><td>10.1.2.9    </td><td>hk-c-aset-2    </td><td>DC = WESTUS rack = rack3    </td><td>Ne </td></tr>
<tr><td>hk-c7-west-us    </td><td>data    </td><td>10.1.2.10    </td><td>hk-c-aset-2    </td><td>DC = WESTUS rack = rack4    </td><td>Ano</td></tr>
<tr><td>hk-c8-west-us    </td><td>data    </td><td>10.1.2.11    </td><td>hk-c-aset-2    </td><td>DC = WESTUS rack = rack4    </td><td>Ne </td></tr>
<tr><td>hk-w1-west-us    </td><td>web    </td><td>10.1.1.4    </td><td>hk-w-aset-1    </td><td>                       </td><td>neuvedeno</td></tr>
<tr><td>hk-w2-west-us    </td><td>web    </td><td>10.1.1.5    </td><td>hk-w-aset-1    </td><td>                       </td><td>neuvedeno</td></tr>
</table>

Vytvoření seznamu virtuálních počítačů vyžaduje následující proces:

1. Vytvoření prázdné cloudové služby v konkrétní oblasti
2. Vytvoření virtuálního počítače z dříve zaznamenané bitové kopie a připojit ho k virtuální síti vytvořené dříve; Tento postup opakujte pro všechny virtuální počítače
3. Přidat interního nástroje pro cloudovou službu a připojí k podsíti "data"
4. Pro každý virtuální počítač předtím vytvořili přidáte koncový bod s vyrovnáváním zatížení pro provoz thrift přes sadu s vyrovnáváním zatížení připojený dříve vytvořeného interního nástroje load balancer

Výše uvedené proces lze provést pomocí webu Azure portal; použití počítače s Windows (použití virtuálního počítače v Azure, pokud nemáte přístup k počítači s Windows), pomocí následujícího skriptu prostředí PowerShell ke zřízení všech 8 virtuálních počítačů automaticky.

**Seznam 1: Skript prostředí PowerShell pro zřizování virtuálních počítačů**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. create a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Krok 3: Konfigurace Cassandra na každém virtuálním počítači**

Přihlášení k virtuálnímu počítači a proveďte následující kroky:

* Upravte $CASS_HOME/conf/cassandra-rackdc.properties zadávat data center a rack vlastnosti:
  
       dc =EASTUS, rack =rack1
* Upravte cassandra.yaml nakonfigurovat počáteční uzly, jak je uvedeno níže:
  
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Krok 4: Spuštění virtuálních počítačů a test clusteru**

Přihlaste se do jednoho z uzlů (například hk-c1 – USA – západ-) a spusťte následující příkaz a zjistit stav clusteru:

       nodetool –h 10.1.2.4 –p 7199 status

Měli byste vidět zobrazení podobně jako se podobá tomuhle 8 uzlů clusteru:

<table>
<tr><th>Status</th><th>Adresa    </th><th>Načtení    </th><th>Tokeny    </th><th>Vlastní </th><th>ID hostitele    </th><th>Stojan</th></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.4     </td><td>87.81 KB    </td><td>256    </td><td>38.0%    </td><td>Identifikátor GUID (odebrání).</td><td>rack1</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.5     </td><td>41.08 KB    </td><td>256    </td><td>68.9%    </td><td>Identifikátor GUID (odebrání).</td><td>rack1</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.6     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrání).</td><td>rack2</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.7     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrání).</td><td>rack2</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.8     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrání).</td><td>rack3</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.9     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrání).</td><td>rack3</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.10     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrání).</td><td>rack4</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.11     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrání).</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Testování jedné oblasti clusteru
K otestování clusteru, postupujte následovně:

1. Pomocí rutiny Get-AzureInternalLoadbalancer příkaz prostředí Powershell, získejte IP adresu interního nástroje (například 10.1.2.101). Syntaxe příkazu je uveden níže: Get-AzureLoadbalancer – ServiceName "hk-c-svc – USA – západ –" [zobrazují podrobnosti o nástroje pro vyrovnávání zatížení interní spolu s jeho IP adresu.]
2. Přihlásit se do webové farmy virtuálních počítačů (například hk-w1 – USA – západ –) pomocí Putty nebo ssh
3. Spustit $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4. Pomocí následujících příkazů CQL ověřte, zda funguje clusteru:
   
     VYTVOŘENÍ prostoru KLÍČŮ customers_ks s REPLIKACE = {'class': 'SimpleStrategy", 'replication_factor': 3};   POUŽITÍ customers_ks;   Vytvoření tabulky Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Vložit do VALUES(1, 'John', 'Doe') Customers(customer_id, firstname, lastname);   Vložit do Customers(customer_id, firstname, lastname) hodnoty (2, "Jana", "Doe");
   
     Vybrat * z zákazníkům;

By měl vypadat přibližně jako následující výsledky:

<table>
  <tr><th> customer_id </th><th> jméno </th><th> Příjmení </th></tr>
  <tr><td> 1 </td><td> Jan </td><td> Doe </td></tr>
  <tr><td> 2 </td><td> Jana </td><td> Doe </td></tr>
</table>

Prostor klíčů vytvořili v kroku 4 používá SimpleStrategy s replication_factor 3. SimpleStrategy se doporučuje pro jednoho datového centra nasazení že NetworkTopologyStrategy pro více data center nasazení. Replication_factor 3 poskytuje vůči chybám pro selhání uzlů.

## <a id="tworegion"> </a>Proces nasazení ve více oblastech
Využijte jedné oblasti nasazení bylo dokončeno a opakujte stejný postup k instalaci druhé oblasti. Nastavení tunelového propojení sítě VPN pro komunikaci mezi oblastmi; je klíčovým rozdílem mezi nasazení jednotlivých i několika oblastmi začít s instalací sítě, zřídit virtuální počítače a nakonfigurujte Cassandra.

### <a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Krok 1: Vytvořte virtuální síť v oblasti 2.
Přihlaste se na webu Azure portal a vytvoření virtuální sítě s atributy zobrazeno v tabulce. Zobrazit [konfigurace virtuální sítě Cloud-Only na webu Azure Portal](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) podrobné pokyny procesu.      

<table>
<tr><th>Název atributu    </th><th>Hodnota    </th><th>Poznámky</th></tr>
<tr><td>Název    </td><td>vnet-cass-east-us</td><td></td></tr>
<tr><td>Oblast    </td><td>USA – východ</td><td></td></tr>
<tr><td>Servery DNS        </td><td></td><td>Tuto zprávu ignorovat, protože nepoužíváme DNS Server</td></tr>
<tr><td>Konfigurace point-to-site VPN</td><td></td><td>        Tuto zprávu ignorovat</td></tr>
<tr><td>Konfigurace sítě Site-to-Site VPN</td><td></td><td>        Tuto zprávu ignorovat</td></tr>
<tr><td>Adresní prostor    </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Počáteční IP    </td><td>10.2.0.0    </td><td></td></tr>
<tr><td>CIDR    </td><td>/16 (65531)</td><td></td></tr>
</table>

Přidejte následující podsítě:

<table>
<tr><th>Název    </th><th>Počáteční IP    </th><th>CIDR    </th><th>Poznámky</th></tr>
<tr><td>web    </td><td>10.2.1.0    </td><td>/24 (251)    </td><td>Podsítě webové farmy</td></tr>
<tr><td>data    </td><td>10.2.2.0    </td><td>/24 (251)    </td><td>Podsíť pro uzly databáze</td></tr>
</table>


### <a name="step-2-create-local-networks"></a>Krok 2: Vytvoření místní sítě
Místní síť do virtuální sítě Azure se proxy adresní prostor, který se mapuje na vzdálený web, včetně privátního cloudu nebo jiné oblasti Azure. Tento adresní prostor proxy je vázán na vzdálenou bránu pro směrování sítě do správné síťové umístění. Zobrazit [konfigurace virtuální sítě připojení mezi virtuálními sítěmi](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) pokyny k vytvoření připojení VNET-to-VNET.

Vytvořte dvě místní sítě, za následující podrobnosti:

| Síťový název | Adresa brány VPN | Adresní prostor | Poznámky |
| --- | --- | --- | --- |
| hk-lnet-map-to-east-us |23.1.1.1 |10.2.0.0/16 |Při vytváření místní sítě poskytují zástupný symbol adresu brány. Po vytvoření brány naplní adresy skutečné brány. Ujistěte se, že přesně odpovídá příslušné vzdálené virtuální sítě, adresní prostor v tomto případě virtuální sítě vytvořené v oblasti USA – východ. |
| hk-lnet-map-to-west-us |23.2.2.2 |10.1.0.0/16 |Při vytváření místní sítě poskytují zástupný symbol adresu brány. Po vytvoření brány naplní adresy skutečné brány. Ujistěte se, že přesně odpovídá příslušné vzdálené virtuální sítě, adresní prostor v tomto případě virtuální sítě vytvořené v oblasti západní USA. |

### <a name="step-3-map-local-network-to-the-respective-vnets"></a>Krok 3: Připojit "Local" síťovou příslušnými virtuálními sítěmi
Na webu Azure Portal vyberte každé virtuální síti, klikněte na možnost "Configure", zaškrtněte možnost připojit k místní síti,"a vyberte místní sítě, za následující podrobnosti:

| Virtual Network | Místní sítě |
| --- | --- |
| hk-vnet-west-us |hk-lnet-map-to-east-us |
| hk-vnet-east-us |hk-lnet-map-to-west-us |

### <a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Krok 4: Vytvoření brány sítě VNET1 a VNET2
Na řídicím panelu virtuální sítě klikněte na tlačítko Vytvořit BRÁNU pro aktivaci procesu zřizování brány sítě VPN. Po několika minutách by se na řídicím panelu pro každou virtuální síť zobrazit adresy skutečné brány.

### <a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Krok 5: Aktualizace "Místní" sítě s adresami v příslušných "Brána"
Upravte místní sítě chcete nahradit zástupnou IP adresu brány skutečné IP adresu jenom zřízené brány. Použijte následující mapování:

<table>
<tr><th>Místní sítě    </th><th>Brána virtuální sítě</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>Brána hk-vnet – USA – západ</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>Brána hk – připojení typu vnet východ-nás</td></tr>
</table>

### <a name="step-6-update-the-shared-key"></a>Krok 6: Aktualizace sdílený klíč
Pomocí následujícího skriptu prostředí Powershell se aktualizovat klíč IPSec z každé brány VPN gateway [použít saké klíč pro obě brány]: hk-lnet-map-to-west-us LocalNetworkSiteName – v sadě AzureVNetGatewayKey - VNetName hk – připojení typu vnet východ nám - SharedKey D9E76BKK Set-AzureVNetGatewayKey - VNetName hk-vnet – USA – západ – - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

### <a name="step-7-establish-the-vnet-to-vnet-connection"></a>Krok 7: Vytvoření připojení VNET-to-VNET
Na webu Azure Portal použijte nabídku "Panel" virtuálních sítí k navázání připojení brány brány. Pomocí položky nabídky "Připojit" v dolní panel nástrojů. Po několika minutách by měl řídicí panel obsahovat podrobnosti o připojení graficky.

### <a name="step-8-create-the-virtual-machines-in-region-2"></a>Krok 8: Vytvoření virtuálních počítačů v oblasti #2
Vytvořit Ubuntu image, jak je popsáno v nasazení v oblasti #1 podle stejné kroky nebo zkopírujte soubor bitové kopie virtuálního pevného disku do účtu úložiště Azure se nachází v oblasti #2 a image. Použít tuto bitovou kopii a vytvořte následující seznam virtuálních počítačů do nové cloudové službě, hk-c-svc Východ USA:

| Název počítače | Podsíť | IP adresa | Skupina dostupnosti | DC/stojan | Počáteční hodnoty? |
| --- | --- | --- | --- | --- | --- |
| hk-c1-east-us |data |10.2.2.4 |hk-c-aset-1 |DC = EASTUS rack = rack1 |Ano |
| HK-c2-Východ USA |data |10.2.2.5 |hk-c-aset-1 |DC = EASTUS rack = rack1 |Ne |
| HK-c3-Východ USA |data |10.2.2.6 |hk-c-aset-1 |DC = EASTUS rack = rack2 |Ano |
| HK-c5-Východ USA |data |10.2.2.8 |hk-c-aset-2 |DC = EASTUS rack = rack3 |Ano |
| HK-c6-Východ USA |data |10.2.2.9 |hk-c-aset-2 |DC = EASTUS rack = rack3 |Ne |
| HK-kompatibilní s c7 – Východ USA |data |10.2.2.10 |hk-c-aset-2 |DC = EASTUS rack = rack4 |Ano |
| HK-c8-Východ USA |data |10.2.2.11 |hk-c-aset-2 |DC = EASTUS rack = rack4 |Ne |
| HK-w1 – Východ USA |web |10.2.1.4 |hk-w-aset-1 |neuvedeno |neuvedeno |
| HK-w2 – Východ USA |web |10.2.1.5 |hk-w-aset-1 |neuvedeno |neuvedeno |

Použijte stejné pokyny jako oblast #1, ale používá 10.2.xxx.xxx adresních prostorů.

### <a name="step-9-configure-cassandra-on-each-vm"></a>Krok 9: Konfigurace Cassandra na každém virtuálním počítači
Přihlášení k virtuálnímu počítači a proveďte následující kroky:

1. Upravit $CASS_HOME/conf/cassandra-rackdc.properties zadejte vlastnosti centra a rack data ve formátu: dc = EASTUS rack = rack1
2. Upravit cassandra.yaml nakonfigurovat počáteční uzly: rychlosti: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

### <a name="step-10-start-cassandra"></a>Krok 10: Spuštění Cassandra
Přihlaste se do každého virtuálního počítače a spuštěním následujícího příkazu spustí na pozadí Cassandra: $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>Testovací Cluster pro více oblastí
Nyní Cassandra byla nasazena do 16 uzlů s 8 uzly v každé oblasti Azure. Tyto uzly jsou ve stejném clusteru tím, že běžný název clusteru a počáteční konfigurace uzlu. K otestování clusteru, použijte následující postup:

### <a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Krok 1: Získání IP interní služby load balancer pro obě oblasti pomocí Powershellu
* Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-west-us"
* Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-east-us"  
  
    Zapište si IP adresy (pro příklad západ - 10.1.2.101, východ - 10.2.2.101) zobrazí.

### <a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Krok 2: Po přihlášení hk-w1 – USA – západ – spuštěním následujících v oblasti Západ
1. Spustit $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2. Spusťte následující příkazy CQL:
   
     VYTVOŘENÍ prostoru KLÍČŮ customers_ks s REPLIKACE = {'class': 'NetworkToplogyStrategy', 'WESTUS': 3, "EASTUS': 3};   POUŽITÍ customers_ks;   Vytvoření tabulky Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Vložit do VALUES(1, 'John', 'Doe') Customers(customer_id, firstname, lastname);   Vložit do Customers(customer_id, firstname, lastname) hodnoty (2, "Jana", "Doe");   Vybrat * z zákazníkům;

Měli byste vidět zobrazení jako je následující:

| customer_id | jméno | Příjmení |
| --- | --- | --- |
| 1 |Jan |Doe |
| 2 |Jana |Doe |

### <a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Krok 3: Spusťte následující v oblasti east po přihlášení hk-w1 – východ – nás:
1. Spustit $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2. Spusťte následující příkazy CQL:
   
     POUŽITÍ customers_ks;   Vytvoření tabulky Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Vložit do VALUES(1, 'John', 'Doe') Customers(customer_id, firstname, lastname);   Vložit do Customers(customer_id, firstname, lastname) hodnoty (2, "Jana", "Doe");   Vybrat * z zákazníkům;

Jak je vidět pro oblast západní byste měli vidět stejný zobrazení:

| customer_id | jméno | Příjmení |
| --- | --- | --- |
| 1 |Jan |Doe |
| 2 |Jana |Doe |

Spustit několik vloží další a podívejte, ty se replikují do – západ-nám součástí clusteru.

## <a name="test-cassandra-cluster-from-nodejs"></a>Test Cluster Cassandra z Node.js
Pomocí některého z virtuálních počítačů s Linuxem vytvořili na úrovni "web" dříve, můžete provést jednoduchý skript Node.js k načtení dříve vložených dat

**Krok 1: Instalace klient Cassandra a Node.js**

1. Nainstalovat Node.js a npm
2. Uzel balíčku "cassandra klienta" nainstalovat pomocí npm
3. Spusťte následující skript příkazového řádku prostředí, které zobrazí řetězce json objem načtených data:
   
        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };
   
        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }
   
        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }
   
        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);
   
           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }
   
        //update also inserts the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }
   
        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }
   
        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)

## <a name="conclusion"></a>Závěr
Microsoft Azure je flexibilní platforma, která umožňuje spouštění Microsoftu i open source softwaru, jak je ukázáno v tomto cvičení. Clustery s vysokou dostupností Cassandra můžete nasadit na jednoho datového centra prostřednictvím rozšíření uzly clusteru napříč několika doménami selhání. Cassandra clustery je možné nasadit taky ve víc oblastech Azure geograficky vzdáleném důkazu systémů po havárii. Azure a Cassandra společně umožňují procesu vytváření vysoce škálovatelných a vysoce dostupné a obnovitelná cloudové služby po havárii potřeby prostřednictvím Internetu dnešní škálování služeb.  

## <a name="references"></a>Odkazy
* [http://cassandra.apache.org](http://cassandra.apache.org)
* [http://www.datastax.com](http://www.datastax.com)
* [http://www.nodejs.org](http://www.nodejs.org)

