---
title: Spusťte Cassandra clusteru v systému Linux v Azure z Node.js
description: Jak spustit Cassandra clusteru v systému Linux v Azure Virtual Machines z aplikace Node.js
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
ms.openlocfilehash: b1945c68f0e320c834ae93a590f420403263a0fd
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098936"
---
# <a name="run-a-cassandra-cluster-on-linux-in-azure-with-nodejs"></a>Spusťte Cassandra clusteru v systému Linux v Azure pomocí Node.js

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zobrazit šablony Resource Manageru pro [Datastax Enterprise](https://azure.microsoft.com/documentation/templates/datastax) a [Spark clusteru a Cassandra na CentOS](https://azure.microsoft.com/documentation/templates/spark-and-cassandra-on-centos/).

## <a name="overview"></a>Přehled
Microsoft Azure je platforma otevřete cloudu, která spouští společnosti Microsoft a jiných společností než Microsoft software, který zahrnuje operační systémy, aplikační servery, zasílání zpráv middleware, jakož i databází SQL a NoSQL z obou modelů komerční s otevřeným zdrojem. Vytváření odolné služeb na veřejných cloudů, včetně Azure vyžaduje pečlivé plánování a architektura záměrné pro oba servery aplikace jako dobře úložiště vrstvy. Architektura distribuovaného úložiště pro Cassandra přirozeně pomáhá při vytváření vysoce dostupných systémy, které jsou odolné proti chybám pro selhání clusteru. Cassandra je cloudové škálování databáze NoSQL udržovat Apache Software Foundation cassandra.apache.org. Cassandra je napsán v jazyce Java. Proto běží na obou na platformách systému Windows a Linux.

Cílem tohoto článku je zobrazit Cassandra nasazení na Ubuntu jako jeden a více data center clusteru, který používá virtuálních počítačích Azure a virtuální sítě. Nasazení clusteru pro úlohy v produkčním prostředí optimalizované je mimo rámec tohoto článku jako vyžaduje konfigurace uzlu více disku, příslušné kruhová topologie návrh a modelování na podporu potřebné replikace, konzistenci dat, propustnosti a vysokou dat požadavky na dostupnosti.

Tento článek má základní přístup, zobrazit, co je součástí vytváření clusteru Cassandra porovná Docker, Chef nebo Puppet, které můžete provést nasazení infrastruktury bylo mnohem snazší.  

## <a name="the-deployment-models"></a>Modely nasazení
Microsoft Azure sítě umožňuje nasazení izolované privátní clusterů přístupu, které může být s omezeným přístupem, aby bylo možné zabezpečení podrobné sítě.  Vzhledem k tomu, že tento článek se týká zobrazující Cassandra nasazení na základní úrovni, není soustředí na úroveň konzistence a návrh úložiště optimální propustnosti. Tady je seznam sítí požadavky pro hypotetické clusteru:

* Externí systémy nelze získat přístup k Cassandra databáze z uvnitř nebo vně Azure
* Cassandra clusteru musí být za službou Vyrovnávání zatížení pro provoz thrift
* Nasazení Cassandra uzly ve dvou skupinách v každé datové centrum pro rozšířené clusteru dostupnosti
* Zamknout clusteru, který jen aplikace serveru farmy, měl přístup k databázi přímo
* Žádné veřejné sítě koncové body než SSH
* Každý uzel Cassandra musí pevnou interní IP adresu

Cassandra lze nasadit do jedné oblasti Azure nebo do několika oblastí podle distribuovaná povaha zatížení. Model nasazení s více oblasti můžete použít k obsluze koncovým uživatelům blíže konkrétní geografický prostřednictvím stejné infrastruktury, Cassandra. Cassandra pro předdefinovaný uzel replikace trvá stará se o synchronizaci s více hlavní zapíše pocházející z několika datových centrech a představuje konzistentní zobrazení dat do aplikace. Nasazení s více oblast může také pomoci s zmírnění rizik širší výpadků služby Azure. Na Cassandra přizpůsobitelné konzistence a replikační topologie pomáhá pokrývat různých plánovaný bod obnovení z aplikace.

### <a name="single-region-deployment"></a>Nasazení jedné oblasti
Pojďme začínat nasazení jedné oblasti a shromažďovat learnings při vytváření více oblast modelu. Azure virtuální síť se používá k vytvoření izolované podsítě tak, aby lze splnit požadavky na zabezpečení sítě uvedených výše.  Proces popsaný v vytvořením nasazení jedné oblasti používá Ubuntu 14.04 LTS a Cassandra 2.08. Proces však můžete snadno přijímají na jiné Linux varianty. Následují některé systémové charakteristiky nasazení jedné oblasti.  

**Vysoká dostupnost:** Cassandra uzly na obrázku 1 se nasadí do dvou sad dostupnosti tak, aby uzly se šíří mezi více domén selhání pro zajištění vysoké dostupnosti. Poznámky s každou skupinu dostupnosti virtuálních počítačů je namapována na 2 domén selhání. Azure používá koncept doména selhání ke správě neplánované výpadek (například selhání hardwaru nebo softwaru). Koncept upgradovací doméně (například hostitel nebo opravy hostovaný operační systém a upgrady, upgrady aplikací) se používá pro správu naplánované výpadek. Najdete v tématu [zotavení po havárii a vysoká dostupnost pro aplikací Azure](http://msdn.microsoft.com/library/dn251004.aspx) pro roli selhání a upgradu domén v dosažení vysoké dostupnosti.

![Nasazení jedné oblasti](./media/cassandra-nodejs/cassandra-linux1.png)

Obrázek 1: Nasazení jedné oblasti

Všimněte si, že v době psaní tohoto textu, Azure neumožňuje explicitní mapování skupiny virtuálních počítačů k doméně konkrétní chyby; v důsledku toho i s modelem nasazení na obrázku 1 je statisticky pravděpodobné, že všechny virtuální počítače mohou být namapovány na dvě domény selhání místo čtyři.

**Přenosy Thrift Vyrovnávání zatížení:** Thrift klientské knihovny uvnitř webový server připojení ke clusteru prostřednictvím interní nástroj. To vyžaduje proces přidávání nástroje pro vyrovnávání zatížení pro vnitřní k podsíti "data" (viz obrázek 1) v rámci cloudové služby hostování Cassandra clusteru. Jakmile je definována pro vyrovnávání zatížení interní, vyžaduje každý uzel koncový bod skupinu s vyrovnáváním zatížení přidávaného s poznámky skupinu s vyrovnáváním zatížení s názvem služby Vyrovnávání zatížení dříve definovaném. V tématu [interní Vyrovnávání zatížení Azure ](../../../load-balancer/load-balancer-internal-overview.md)další podrobnosti.

**Cluster semen:** je důležité vybrat Většina uzlů vysoce dostupný pro semena jako nové uzly komunikaci s uzly počáteční hodnoty pro zjišťování topologie clusteru. Jeden uzel z každé skupiny dostupnosti je určený jako uzly počáteční hodnoty předejdete jediný bod selhání.

**Zařízení a úroveň konzistence replikace:** Cassandra na sestavení v vysokou dostupnost a data odolnost je charakterizovaná replikace faktor (RF - počet kopií každého řádku uložené v clusteru) a úroveň konzistence (počet replik být číst nebo zapisovat před vrácením výsledku volající). Faktor replikace je zadán během vytváření KEYSPACE (podobně jako relační databáze), zatímco úroveň konzistence je zadána při vydání CRUD dotaz. Naleznete v dokumentaci k Cassandra v [konfigurace pro konzistence](https://docs.datastax.com/en/cassandra/3.0/cassandra/dml/dmlConfigConsistency.html) konzistence podrobnosti a vzorec pro výpočty kvora.

Cassandra podporuje dva typy modelů integrity dat – konzistence a konzistence typu případné; Faktor replikace a úroveň konzistence společně určují, pokud je data konzistentní při operaci zápisu označena jako dokončená nebo nakonec byl konzistentní. Například zadání KVORA jako úroveň konzistence vždy zajišťuje konzistenci dat při jakékoli úrovní konzistence, menší než počet replik k zapsání tak, aby bylo možné KVORA (například jeden) výsledkem data se nakonec byl konzistentní.

8 uzlů clusteru s faktorem replikace 3 a KVORA zobrazeno výše, (2 uzly jsou číst nebo zapisovat konzistenci) pro čtení a zápis úroveň konzistence, přežijí teoretické ztrátu nejvýše 1 uzel na skupinu replikace před vašeho povšimnutí aplikace spustit došlo k chybě. Předpokladem je, že všechny klíče prostory mají dobře vyrovnáváním čtení/zápisu požadavků.  Tady jsou použité pro nasazené cluster parametry:

Konfigurace clusteru Cassandra jedné oblasti:

| Parametr clusteru | Hodnota | Poznámky |
| --- | --- | --- |
| Počet uzlů (ne) |8 |Celkový počet uzlů v clusteru |
| Replikace faktor (RF) |3 |Počet replik daného řádku |
| Úroveň konzistence (zápisu) |QUORUM[(RF/2) +1) = 2] výsledek vzorec se zaokrouhlí směrem dolů |Zapíše maximálně 2 repliky předtím, než odešle odpověď na volajícího; 3. replika se zapíše nakonec byl konzistentní způsobem. |
| Úroveň konzistence (čtení) |KVORA [(RF/2) + 1 = 2] výsledek vzorec se zaokrouhlí směrem dolů |Přečte 2 repliky před odesláním odpověď na volajícího. |
| Strategie replikace |Viz NetworkTopologyStrategy [replikaci dat](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archDataDistributeAbout.html) v dokumentaci k Cassandra pro další informace |Jste srozuměni s tím topologie nasazení a umístí repliky na uzlech, aby všechny repliky není skončili na stejné racku |
| Snitch |Viz GossipingPropertyFileSnitch [přepínače](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archSnitchesAbout.html) v dokumentaci k Cassandra pro další informace |NetworkTopologyStrategy používá koncept snitch zjistit topologii. GossipingPropertyFileSnitch poskytuje lepší kontrolu mapování každý uzel datového centra a racku. Cluster použije povídání potřebný k šíření tyto informace. To je mnohem jednodušší v dynamické nastavení protokolu IP relativně k PropertyFileSnitch |

**Azure aspekty pro Cassandra Cluster:** funkce Microsoft Azure Virtual Machines používá úložiště objektů Blob v Azure disku trvalosti; Úložiště Azure uloží tři repliky každého disku pro vysokou odolnost. To znamená, že každý řádek dat vloženy do tabulky Cassandra je již uložen v tři repliky. Proto konzistenci dat je již postaráno i v případě, že replikace faktor (RF) je 1. Hlavní problém s Multi-Factor replikace se 1 je, že aplikace i v případě selhání jednoho uzlu Cassandra vyskytne výpadku. Ale pokud uzel je vypnutý problémy (například hardwaru, softwaru selhání systému) rozpoznáno Kontroleru prostředků infrastruktury Azure, zřídí nového uzlu na jeho místo pomocí stejné jednotky úložiště. Zřizování nový uzel nahradit starou může trvat několik minut.  Pro plánované údržby činnosti, jako například změny hostovaného operačního systému, podobně jako Cassandra upgraduje a změny aplikace Kontroleru prostředků infrastruktury Azure provádí vrácení upgrady z uzlů v clusteru.  Vrácení upgradu také může trvat dolů několika uzlů najednou a proto clusteru setkat s krátkou prodlevou mezi migrací pro několik oddílů. Data však není ztraceno v důsledku vestavěná redundance úložiště Azure.  

Pro systémy nasadit do Azure, která nevyžaduje vysokou dostupnost (třeba přibližně 99,9 což je totéž jako 8.76 hodin/rok, zjistit [vysokou dostupnost](http://en.wikipedia.org/wiki/High_availability) podrobnosti) může být možné ji spustit s RF = 1 a úroveň konzistence = jeden.  Pro aplikace s požadavky na vysokou dostupnost, RF = 3 a úroveň konzistence = KVORA toleruje výpadkům jednoho z uzlů z replik. RF = 1 v tradiční nasazení (například místní) nelze použít kvůli ztrátě dat. výsledkem problémy, jako je selhání disku.   

## <a name="multi-region-deployment"></a>Nasazení s více oblast
Model pro replikaci a konzistence datového centra s deklaracemi na Cassandra je popsané výše pomáhá při nasazení několika oblast bez nutnosti žádné externí nástroje. To se liší od tradičních relačních databází, kde může být složité nastavení pro zrcadlení databáze pro více hlavních zápisy. Cassandra v instalačním programu více oblast můžou pomoct s scénáře použití, včetně scénáře:

**Bezkontaktní komunikace na základě nasazení:** víceklientským aplikacím s zrušte mapování uživatelů klienta-na-oblast, můžete prospěch ve více oblastech clusteru nízkou latenci. Například učení systémy správy pro vzdělávací instituce můžete nasadit cluster distribuované v oblasti Východ USA a západní USA k obsluze příslušných univerzity pro transakční i analytics. Data lze místně konzistentní čas čtení a zápisu a může být v obou oblastech nakonec byl konzistentní. Existují další příklady jako distribuční média, elektronické obchodování a nic a všechno, co se uživatel geograficky soustředí slouží základní je případ vhodné využít pro tento model nasazení.

**Vysoká dostupnost:** redundance je klíčovým faktorem k dosažení vysoké dostupnosti softwaru a hardwaru; podrobnosti najdete v části vytváření spolehlivé cloudové systémy v Microsoft Azure. Pouze spolehlivé způsobem dosáhnout true redundance v Microsoft Azure, je nasazení clusteru s podporou více oblast. Mohou být aplikace nasazeny v režimu aktivní aktivní nebo aktivní – pasivní, a pokud jeden z oblastí je vypnutý, můžete Azure Traffic Manager přesměrování provozu na aktivní oblasti.  Při nasazení jedné oblasti dostupnost 99,9, je-li dva oblast nasazení můžete zajistit s dostupností 99.9999 vypočítanou ze vzorce: (1-(1-0.999) * (1-0,999)) * 100); viz výše dokumentu podrobnosti.

**Zotavení po havárii:** Cassandra více oblast clusteru, pokud správně navržená tak, odolat závažné data center výpadků. Pokud jedné oblasti je vypnutý, můžete aplikaci nasadit do jiných oblastí spustit obsluhující koncoví uživatelé. Podobně jako jakékoli jiné firmy kontinuity implementace aplikace musí být odolný vůči chybám pro ztrátu dat, která je výsledkem dat v asynchronní kanál. Ale Cassandra umožňuje obnovení mnohem rychlejší, než doba, za kterou procesy tradiční databáze obnovení. Obrázek 2 ukazuje model typické nasazení s více oblasti s osmi uzlů v každé oblasti. Obě oblasti jsou Image zrcadlení vzájemně pro stejný symetrie; návrhy skutečných závisí na typu zatížení (například transakcí nebo analytical), plánovaný bod obnovení, RTO, konzistenci dat a požadavky dostupnosti.

![Nasazení více oblast](./media/cassandra-nodejs/cassandra-linux2.png)

Obrázek 2: Nasazení s více oblast Cassandra

### <a name="network-integration"></a>Integrace sítě
Nastaví virtuální počítače nasazené na soukromých sítích, které se nachází na dvou oblastí komunikuje se navzájem pomocí tunelového připojení sítě VPN. Tunelové propojení VPN se připojí dvě brány softwaru zřízenou během procesu nasazení sítě. Obě oblasti mají podobné síťovou architekturu z hlediska podsítě "web" a "data"; Azure sítě umožňuje vytvoření tolik podsítě podle potřeby a použít seznamy řízení přístupu podle potřeb zabezpečení sítě. Při navrhování topologie clusteru mimo latence při komunikaci datového centra a hospodářského dopad považovat za nutnost provozu sítě.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Konzistence dat pro nasazení s více datového centra
Distribuované nasazení je potřeba mít na paměti clusteru topologie dopad na propustnost a vysokou dostupnost. RF a úroveň konzistence musí být vybrána tak, že kvora nezávisí na dostupnost datových centrech.
Pro systém, který potřebuje vysokou konzistence LOCAL_QUORUM pro úroveň konzistence (pro čtení a zápisy) je zajištěno, že místní čtení a zápisu jsou splněny z místní uzlů při data se replikují asynchronně vzdálené datových střediscích.  Tabulka 2 shrnuje podrobnosti konfigurace clusteru více oblast uvedených v zápisu až později.

**Konfigurace clusteru Cassandra dva oblast**

| Parametr clusteru | Hodnota | Poznámky |
| --- | --- | --- |
| Počet uzlů (ne) |8 + 8 |Celkový počet uzlů v clusteru |
| Replikace faktor (RF) |3 |Počet replik daného řádku |
| Úroveň konzistence (zápisu) |LOCAL_QUORUM [(sum(RF)/2) +1) = 4] výsledek vzorec se zaokrouhlí směrem dolů |2 uzly se zapíše do první datového centra synchronně; Další 2 uzly, které jsou potřebné pro kvora se asynchronně zapíše na 2. datové centrum. |
| Úroveň konzistence (čtení) |LOCAL_QUORUM ((RF/2) + 1) = 2, výsledek vzorec se zaokrouhlí směrem dolů |Požadavky pro čtení jsou splnit v pouze jedna oblast; 2 uzly se čtou předtím, než odešle odpověď zpět klientovi. |
| Strategie replikace |Viz NetworkTopologyStrategy [replikaci dat](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archDataDistributeAbout.html) v dokumentaci k Cassandra pro další informace |Jste srozuměni s tím topologie nasazení a umístí repliky na uzlech, aby všechny repliky není skončili na stejné racku |
| Snitch |Viz GossipingPropertyFileSnitch [Snitches](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archSnitchesAbout.html) v dokumentaci k Cassandra pro další informace |NetworkTopologyStrategy používá koncept snitch zjistit topologii. GossipingPropertyFileSnitch poskytuje lepší kontrolu mapování každý uzel datového centra a racku. Cluster použije povídání potřebný k šíření tyto informace. To je mnohem jednodušší v dynamické nastavení protokolu IP relativně k PropertyFileSnitch |

## <a name="the-software-configuration"></a>KONFIGURACE SOFTWARU
Během nasazení se používají následující verze softwaru:

<table>
<tr><th>Software</th><th>Zdroj</th><th>Verze</th></tr>
<tr><td>PROSTŘEDÍ JRE    </td><td>[PROSTŘEDÍ JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA    </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu    </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Je ručně musí přijmout tyto licenční Oracle, když si stáhnete prostředí JRE. Ano zjednodušit nasazení, stáhněte veškerý požadovaný software na ploše. Nahrajte ho do image šablony Ubuntu vytvořit jako předchůdcem nasazení clusteru služby.

Stáhněte si výše uvedený software do adresáře dobře známé stahování (například %TEMP%/downloads v systému Windows nebo ~/Downloads na Linuxových distribucích většiny Mac) v místním počítači.

### <a name="create-ubuntu-vm"></a>VYTVOŘENÍ VIRTUÁLNÍHO POČÍTAČE S UBUNTU
V tomto kroku procesu vytvoření bitové kopie Ubuntu s požadovaného softwaru tak, aby bitovou kopii můžete znovu použít pro několik uzlů Cassandra zřizování.  

#### <a name="step-1-generate-ssh-key-pair"></a>Krok 1: Vygenerovat pár klíčů SSH
Azure potřebuje X509 veřejný klíč, který je PEM nebo DER kódovaný během zřizování. Generování páru veřejného a privátního klíče RSA podle pokynů v tom, jak použití SSH se systémem Linux v Azure. Pokud budete chtít použít putty.exe jako klienta SSH buď na systému Windows nebo Linux, je nutné převést PEM kódovaný privátního klíče RSA PPK formátu s využitím puttygen.exe. Pokyny k tomuto naleznete na webové stránce nahoře.

#### <a name="step-2-create-ubuntu-template-vm"></a>Krok 2: Vytvoření Ubuntu šablony virtuálních počítačů
Vytvořit šablonu virtuálního počítače, přihlaste se k portálu Azure a použijte následující posloupnost: klikněte na tlačítko Nový, výpočetní, virtuální počítač, FROM GALERIE, UBUNTU a Ubuntu Server 14.04 LTS a klikněte na šipku vpravo. Kurz, který popisuje, jak vytvořit virtuální počítač s Linuxem, najdete v části vytvořit virtuální počítač systémem Linux.

Na obrazovce "Konfigurace virtuálního počítače" #1 zadejte následující informace:

<table>
<tr><th>NÁZEV POLE              </td><td>       HODNOTA POLE               </td><td>         POZNÁMKY                </td><tr>
<tr><td>DATUM VYDÁNÍ VERZE    </td><td> Vyberte datum z rozevírací nabídky</td><td></td><tr>
<tr><td>NÁZEV VIRTUÁLNÍHO POČÍTAČE    </td><td> Cass šablony.                   </td><td> Toto je název hostitele virtuálního počítače </td><tr>
<tr><td>VRSTVY                     </td><td> STANDARD                           </td><td> Ponechte výchozí nastavení              </td><tr>
<tr><td>VELIKOST                     </td><td> A1                              </td><td>Vyberte virtuální počítač, na základě potřeb vstupně-výstupní operace; pro tento účel ponechte výchozí nastavení </td><tr>
<tr><td> NOVÉ UŽIVATELSKÉ JMÉNO             </td><td> localadmin                       </td><td> "admin" je vyhrazené uživatelské jméno ve 12. xx Ubuntu a</td><tr>
<tr><td> OVĚŘOVÁNÍ         </td><td> Klikněte na zaškrtávací políčko                 </td><td>Zkontrolujte, zda chcete zabezpečit pomocí klíče SSH </td><tr>
<tr><td> CERTIFIKÁT             </td><td> Název souboru certifikátu veřejného klíče </td><td> Použijte veřejný klíč vygenerovaný dříve</td><tr>
<tr><td> Nové heslo    </td><td> silné heslo </td><td> </td><tr>
<tr><td> Potvrzení hesla    </td><td> silné heslo </td><td></td><tr>
</table>

Na obrazovce "Konfigurace virtuálního počítače" #2 zadejte následující informace:

<table>
<tr><th>NÁZEV POLE             </th><th> HODNOTA POLE                       </th><th> POZNÁMKY                                 </th></tr>
<tr><td> CLOUDOVÉ SLUŽBY    </td><td> Vytvořte novou cloudovou službu    </td><td>Cloudová služba je kontejner výpočetní prostředky, a to jako virtuální počítače</td></tr>
<tr><td> NÁZEV CLOUDOVÉ SLUŽBY DNS    </td><td>ubuntu-template.cloudapp.net    </td><td>Pojmenujte nástroje pro vyrovnávání zatížení lhostejné počítače</td></tr>
<tr><td> OBLASTI NEBO SKUPINY VZTAHŮ NEBO VIRTUÁLNÍ SÍTĚ </td><td>    Západní USA    </td><td> Vyberte oblast, ve kterém webových aplikací přístup ke clusteru Cassandra</td></tr>
<tr><td>ÚČET ÚLOŽIŠTĚ </td><td>    Použijte výchozí.    </td><td>Použít výchozí účet úložiště nebo předem vytvořený účet úložiště v určité oblasti.</td></tr>
<tr><td>SADY DOSTUPNOSTI. </td><td>    Žádný </td><td>    Ponechat prázdné</td></tr>
<tr><td>KONCOVÉ BODY    </td><td>Použijte výchozí. </td><td>    Použít výchozí konfiguraci SSH </td></tr>
</table>

Klikněte na šipku vpravo, ponechte výchozí nastavení na obrazovce #3. Klikněte na tlačítko "kontrola" k dokončení procesu zřizování virtuálních počítačů. Po několika minutách by měly mít virtuální počítač s názvem "ubuntu šablona" stavu "spuštění".

### <a name="install-the-necessary-software"></a>NAINSTALUJTE POTŘEBNÝ SOFTWARE
#### <a name="step-1-upload-tarballs"></a>Krok 1: Nahrávání tarballs
Spojovací bod služby nebo pscp používáte, zkopírujte dříve stažené softwaru ~/downloads adresář v následujícím formátu příkaz:

##### <a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp server-jre-8u5-linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz
Zopakujte výše uvedeném příkazu pro prostředí JRE také jako Cassandra bits.

#### <a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>Krok 2: Příprava strukturu adresáře a extrahovat archivu
Přihlaste se k virtuálnímu počítači a vytvořte strukturu adresáře a extrahovat softwaru jako superuživatele pomocí skriptu bash níže:

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


Pokud tento skript můžete vložit do okna vim, nezapomeňte odebrat návratu na začátek ('\r ") pomocí následujícího příkazu:

    tr -d '\r' <infile.sh >outfile.sh

#### <a name="step-3-edit-etcprofile"></a>Krok 3: Úprava atd nebo profil
Připojte následující na konci:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

#### <a name="step-4-install-jna-for-production-systems"></a>Krok 4: Instalace JNA pro produkční systémy.
Použijte následující sekvence příkazů: Tento příkaz nainstaluje jna 3.2.7.jar a platforma 3.2.7.jar jna /usr/share.java directory sudo výstižný-Get nainstalujte libjna java

Vytvořte symbolické odkazy v adresáři CASS_HOME/lib $, aby tyto JAR najít Cassandra spouštěcí skript:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

#### <a name="step-5-configure-cassandrayaml"></a>Krok 5: Konfigurace cassandra.yaml
Upravte cassandra.yaml na každý virtuální počítač tak, aby odrážela konfigurace potřebné pro všechny virtuální počítače [můžete upravit tuto konfiguraci při zřizování skutečné]:

<table>
<tr><th>Název pole   </th><th> Hodnota  </th><th>    Poznámky </th></tr>
<tr><td>název_clusteru </td><td>    "CustomerService"    </td><td> Použijte název, který se vztahuje k nasazení</td></tr>
<tr><td>listen_address    </td><td>[necháte prázdné]    </td><td> Odstranit "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[necháte prázdné]    </td><td> Odstranit "localhost" </td></tr>
<tr><td>semen    </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"    </td><td>Seznam všech adres IP, které jsou určené jako vstupní.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Toto je používáno NetworkTopologyStrateg pro odvození datového centra a racku virtuálního počítače</td></tr>
</table>

#### <a name="step-6-capture-the-vm-image"></a>Krok 6: Zachycení bitové kopie virtuálního počítače
Přihlaste se k virtuálnímu počítači pomocí názvu hostitele (hk template.cloudapp.net certifikační autority) a privátní klíč SSH vytvořili. Najdete v části Postup použití SSH se systémem Linux v Azure podrobnosti o tom, jak se přihlásit pomocí příkazu ssh nebo putty.exe.

Spusťte následující posloupnost akcí k zachycení bitové kopie:

##### <a name="1-deprovision"></a>1. Deprovision
Použijte příkaz "sudo příkaz waagent – deprovision + uživatele" odebrat konkrétní informace o instanci virtuálního počítače. Zobrazit [jak zachytit virtuální počítač s Linuxem](capture-image-classic.md) chcete použít jako šablonu podrobnosti na proces zachycení bitové kopie.

##### <a name="2-shut-down-the-vm"></a>2: vypnutí virtuálního počítače
Ujistěte se, že virtuální počítač je označený a klikněte na odkaz vypnutí z řádku nabídek dolní.

##### <a name="3-capture-the-image"></a>3: zachycení bitové kopie
Ujistěte se, že virtuální počítač je označený a klikněte na odkaz zachycení z panelu dolní příkazů. Další obrazovka, udělte název bitové kopie (například hk-cas-2-08-ub-14-04-2014071), odpovídající popis bitové kopie, a klikněte na označit "kontrola" ukončíte proces zachycení.

Tento proces trvat několik sekund a bitovou kopii, která by měla být k dispozici v části Moje Image z Galerie obrázků. Zdrojový virtuální počítač je automaticky odstraněna po úspěšně zachycením image. 

## <a name="single-region-deployment-process"></a>Proces nasazení jedné oblasti
**Krok 1: Vytvoření virtuální sítě** Přihlaste se k portálu Azure a vytvoření virtuální sítě (klasické) s atributy uvedené v následující tabulce. V tématu [vytvoření virtuální sítě (klasické) pomocí portálu Azure](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) podrobný popis kroků procesu.      

<table>
<tr><th>Atribut název virtuálního počítače.</th><th>Hodnota</th><th>Poznámky</th></tr>
<tr><td>Název</td><td>vnet-cass-west-us</td><td></td></tr>
<tr><td>Oblast</td><td>Západní USA</td><td></td></tr>
<tr><td>Servery DNS</td><td>Žádný</td><td>Toto ignorovat, protože jsme nejsou pomocí serveru DNS</td></tr>
<tr><td>Adresní prostor</td><td>10.1.0.0/16</td><td></td></tr>    
<tr><td>Počáteční IP adresu</td><td>10.1.0.0</td><td></td></tr>    
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Přidejte následující podsítě:

<table>
<tr><th>Název</th><th>Počáteční IP adresu</th><th>CIDR</th><th>Poznámky</th></tr>
<tr><td>web</td><td>10.1.1.0</td><td>/24 (251)</td><td>Podsíť pro webové farmy</td></tr>
<tr><td>data</td><td>10.1.2.0</td><td>/24 (251)</td><td>Podsíť pro databázové uzly</td></tr>
</table>

Data a webové podsítě se dají chránit pomocí skupin zabezpečení sítě pokrytí sahá nad rámec tohoto článku.  

**Krok 2: Zřizování virtuálních počítačů** pomocí bitové kopie předtím vytvořili, vytvořte následující virtuální počítače v cloudu serveru "(Hong Kong) c-svc západ" a vazby do příslušných podsítí, jak je uvedeno níže:

<table>
<tr><th>Název počítače    </th><th>Podsíť    </th><th>IP adresa    </th><th>Skupina dostupnosti</th><th>DC/Rack</th><th>Počáteční hodnoty?</th></tr>
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

Vytvoření seznamu virtuálních počítačů, vyžaduje následující proces:

1. Vytvoření prázdné cloudové služby v určité oblasti.
2. Vytvoření virtuálního počítače z dříve zaznamenané bitové kopie a jeho připojení k virtuální síti vytvořené dříve; Tento postup opakujte pro všechny virtuální počítače
3. Přidejte interní nástroj do cloudové služby a jeho připojení k podsíti "data.
4. Pro každý virtuální počítač předtím vytvořili přidáte koncový bod Vyrovnávání zatížení pro provoz thrift prostřednictvím připojený ke službě Vyrovnávání zatížení dříve vytvořenou interní skupinu s vyrovnáváním zatížení

Výše uvedené proces lze provést pomocí portálu Azure; počítače s Windows (použití a virtuálních počítačů v Azure, pokud nemáte přístup k počítači s Windows), použít následující skript prostředí PowerShell pro zřízení všech 8 virtuálních počítačů automaticky.

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
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Krok 3: Konfigurace Cassandra na jednotlivé virtuální počítače**

Přihlaste se k virtuálnímu počítači a proveďte následující:

* Upravte $CASS_HOME/conf/cassandra-rackdc.properties k určení datového centra a rack vlastností:
  
       dc =EASTUS, rack =rack1
* Upravte cassandra.yaml konfigurace uzly počáteční hodnoty jako níže:
  
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Krok 4: Spuštění virtuální počítače a otestovat clusteru**

Přihlaste se do jednoho z uzlů (například hk-c1-západní us) a spusťte následující příkaz a zjistit stav clusteru:

       nodetool –h 10.1.2.4 –p 7199 status

Měli byste vidět zobrazení podobné níže 8 uzlů clusteru:

<table>
<tr><th>Status</th><th>Adresa    </th><th>Načtení    </th><th>Tokeny    </th><th>Vlastní </th><th>ID hostitele    </th><th>Stojan</th></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.4     </td><td>87.81 KB    </td><td>256    </td><td>38.0%    </td><td>Identifikátor GUID (odebrat)</td><td>rack1</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.5     </td><td>41.08 KB    </td><td>256    </td><td>68.9%    </td><td>Identifikátor GUID (odebrat)</td><td>rack1</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.6     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrat)</td><td>rack2</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.7     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrat)</td><td>rack2</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.8     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrat)</td><td>rack3</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.9     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrat)</td><td>rack3</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.10     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrat)</td><td>rack4</td></tr>
<tr><th>ZRUŠENÍ    </td><td>10.1.2.11     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identifikátor GUID (odebrat)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Testování jedné oblasti clusteru
Použijte následující postup k testování clusteru:

1. Pomocí příkazu Get-AzureInternalLoadbalancer prostředí PowerShell., získejte IP adresu služby Vyrovnávání zatížení pro vnitřní (například 10.1.2.101). Syntaxe příkazu je zobrazena níže: Get-AzureLoadbalancer – ServiceName "(Hong Kong) c-svc západní USA" [zobrazí podrobnosti o vyrovnávání zatížení pro vnitřní společně s jeho IP adresy]
2. Přihlaste se k webové farmy virtuálního počítače (například hk-w1 – západní us) pomocí klienta Putty ssh nebo
3. Spuštění $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4. Chcete-li ověřit, zda je cluster funkční, použijte následující příkazy CQL:
   
     Vytvoření KEYSPACE customers_ks REPLIKACE s = {'class': 'SimpleStrategy', 'replication_factor': 3};   POUŽITÍ customers_ks;   Vytvoření tabulky Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Vložit do VALUES(1, 'John', 'Doe') Customers(customer_id, firstname, lastname);   Vložit do Customers(customer_id, firstname, lastname) hodnoty (2, 'Jana', 'Doe');
   
     Vyberte * od zákazníků;

Měli byste vidět něco podobného jako následující výsledky:

<table>
  <tr><th> customer_id </th><th> FirstName </th><th> Příjmení </th></tr>
  <tr><td> 1 </td><td> Jan </td><td> Doe </td></tr>
  <tr><td> 2 </td><td> Jana </td><td> Doe </td></tr>
</table>

Keyspace vytvořili v kroku 4 používá SimpleStrategy s replication_factor 3. SimpleStrategy se doporučuje pro jednoho datového centra nasazení zatímco NetworkTopologyStrategy pro více data center nasazení. Replication_factor 3 poskytuje odolnost proti selhání uzlu.

## <a id="tworegion"> </a>Proces nasazení s více oblast
Využívejte nasazení jedné oblasti bylo úspěšně dokončeno a opakujte stejný postup pro instalaci druhé oblast. Klíčovým rozdílem mezi jednou či více oblastí nasazení je nastavení tunelové propojení VPN pro komunikaci mezi oblast; začít s instalací sítě, zřídíte virtuální počítače a nakonfigurujete Cassandra.

### <a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Krok 1: Vytvoření virtuální sítě v oblasti 2.
Přihlaste se k portálu Azure a vytvořit virtuální síť s atributy zobrazení v tabulce. V tématu [konfigurace virtuální sítě Cloud-Only na portálu Azure](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) podrobný popis kroků procesu.      

<table>
<tr><th>Název atributu    </th><th>Hodnota    </th><th>Poznámky</th></tr>
<tr><td>Název    </td><td>vnet-cass-east-us</td><td></td></tr>
<tr><td>Oblast    </td><td>Východ USA</td><td></td></tr>
<tr><td>Servery DNS        </td><td></td><td>Toto ignorovat, protože jsme nejsou pomocí serveru DNS</td></tr>
<tr><td>Konfigurace VPN typu point-to-site</td><td></td><td>        Můžete tuto zprávu ignorovat</td></tr>
<tr><td>Konfigurace sítě Site-to-Site VPN</td><td></td><td>        Můžete tuto zprávu ignorovat</td></tr>
<tr><td>Adresní prostor    </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Počáteční IP adresu    </td><td>10.2.0.0    </td><td></td></tr>
<tr><td>CIDR    </td><td>/16 (65531)</td><td></td></tr>
</table>

Přidejte následující podsítě:

<table>
<tr><th>Název    </th><th>Počáteční IP adresu    </th><th>CIDR    </th><th>Poznámky</th></tr>
<tr><td>web    </td><td>10.2.1.0    </td><td>/24 (251)    </td><td>Podsíť pro webové farmy</td></tr>
<tr><td>data    </td><td>10.2.2.0    </td><td>/24 (251)    </td><td>Podsíť pro databázové uzly</td></tr>
</table>


### <a name="step-2-create-local-networks"></a>Krok 2: Vytvoření místní sítě
Do místní sítě v Azure virtuální sítě je adresní prostor proxy serveru, který se mapuje na vzdálené lokality, včetně privátního cloudu nebo jiné oblasti Azure. Tento adresní prostor proxy je vázána Brána vzdálené směrování sítě do správné síťové umístění. V tématu [nakonfigurovat virtuální síť připojení mezi virtuálními](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) pokyny k vytvoření připojení VNET-to-VNET.

Vytvořte dvě místní sítě za následující podrobnosti:

| Síťový název | Adresa brány sítě VPN | Adresní prostor | Poznámky |
| --- | --- | --- | --- |
| hk-lnet-map-to-east-us |23.1.1.1 |10.2.0.0/16 |Při vytváření místní síti poskytněte zástupný symbol adresu brány. Po vytvoření brány, naplní adresu skutečné brány. Ujistěte se, že adresní prostor přesně odpovídá příslušné virtuální sítě vzdálené; v takovém případě síť VNET vytvořena v oblasti Východ USA. |
| hk-lnet-map-to-west-us |23.2.2.2 |10.1.0.0/16 |Při vytváření místní síti poskytněte zástupný symbol adresu brány. Po vytvoření brány, naplní adresu skutečné brány. Ujistěte se, že adresní prostor přesně odpovídá příslušné virtuální sítě vzdálené; v takovém případě síť VNET vytvořena v oblasti západní USA. |

### <a name="step-3-map-local-network-to-the-respective-vnets"></a>Krok 3: "Local" sítě mapu příslušných virtuálních sítí
Z portálu Azure vyberte každý virtuální síť, klikněte na tlačítko "Konfigurace", "Připojení k místní síti" a vyberte místní sítě za následující podrobnosti:

| Virtual Network | Místní sítě |
| --- | --- |
| hk-vnet-west-us |hk-lnet-map-to-east-us |
| hk-vnet-east-us |hk-lnet-map-to-west-us |

### <a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Krok 4: Vytvoření brány na VNET1 a VNET2
Na řídicím panelu virtuální sítě klikněte na možnost vytvořit BRÁNU pro aktivaci služby VPN gateway procesu zřizování. Po několika minutách řídicí panel každý virtuální sítě by měl obsahovat adresu skutečného brány.

### <a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Krok 5: Aktualizace "Local" sítě s adresami příslušných "brány"
Upravte místní sítě nahradit IP adresu brány zástupný symbol skutečné IP adresu právě zřízené brány. Použijte následující mapování:

<table>
<tr><th>Místní sítě    </th><th>Brána virtuální sítě</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>Brána (Hong Kong) vnet západní USA</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>Brána (Hong Kong) vnet-– východ nám</td></tr>
</table>

### <a name="step-6-update-the-shared-key"></a>Krok 6: Aktualizace sdílený klíč
Pomocí následujícího skriptu prostředí Powershell se aktualizovat klíč protokolu IPSec každý brány VPN [použití saké klíč pro obě brány]: hk-lnet-map-to-west-us - LocalNetworkSiteName v Set AzureVNetGatewayKey - VNetName (Hong Kong) vnet-– východ us - SharedKey D9E76BKK Set-AzureVNetGatewayKey - VNetName (Hong Kong) vnet západní USA - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

### <a name="step-7-establish-the-vnet-to-vnet-connection"></a>Krok 7: Připojení VNET-to-VNET
Z portálu Azure použijte nabídku "Řídicího PANELU" virtuální sítě k navázání připojení brány brány. Pomocí položky nabídky "Připojit" v dolním panelu nástrojů. Po několika minutách by měl řídicím panelu zobrazovat podrobnosti připojení graficky.

### <a name="step-8-create-the-virtual-machines-in-region-2"></a>Krok 8: Vytvoření virtuálních počítačů v oblasti #2
Vytvoření bitové kopie Ubuntu, jak je popsáno v oblasti #1 nasazení podle stejné kroky nebo kopírování souboru bitové kopie virtuálního pevného disku na účet úložiště Azure umístěný v oblasti #2 a vytvoření bitové kopie. Použijte tuto bitovou kopii a vytvoření následující seznam virtuálních počítačů do novou cloudovou službu hk-c-svc východ nám:

| Název počítače | Podsíť | IP adresa | Skupina dostupnosti | DC/Rack | Počáteční hodnoty? |
| --- | --- | --- | --- | --- | --- |
| hk-c1-east-us |data |10.2.2.4 |hk-c-aset-1 |DC = EASTUS rack = rack1 |Ano |
| (Hong Kong) c2-východ nám |data |10.2.2.5 |hk-c-aset-1 |DC = EASTUS rack = rack1 |Ne |
| (Hong Kong) c3-východ nám |data |10.2.2.6 |hk-c-aset-1 |DC = EASTUS rack = rack2 |Ano |
| (Hong Kong) c5-východ nám |data |10.2.2.8 |hk-c-aset-2 |DC = EASTUS rack = rack3 |Ano |
| (Hong Kong) c6-východ nám |data |10.2.2.9 |hk-c-aset-2 |DC = EASTUS rack = rack3 |Ne |
| (Hong Kong)-s c7 – východ nám |data |10.2.2.10 |hk-c-aset-2 |DC = EASTUS rack = rack4 |Ano |
| (Hong Kong) c8-východ nám |data |10.2.2.11 |hk-c-aset-2 |DC = EASTUS rack = rack4 |Ne |
| (Hong Kong) w1 – východ nám |web |10.2.1.4 |hk-w-aset-1 |neuvedeno |neuvedeno |
| (Hong Kong) w2 – východ nám |web |10.2.1.5 |hk-w-aset-1 |neuvedeno |neuvedeno |

Použijte stejné pokyny jako oblast #1, ale použít 10.2.xxx.xxx adresní prostor.

### <a name="step-9-configure-cassandra-on-each-vm"></a>Krok 9: Konfigurace Cassandra na jednotlivé virtuální počítače
Přihlaste se k virtuálnímu počítači a proveďte následující:

1. Upravit $CASS_HOME/conf/cassandra-rackdc.properties zadat vlastnosti center a rack data ve formátu: dc = EASTUS rack = rack1
2. Upravit cassandra.yaml konfigurace uzly počáteční hodnoty: semen: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

### <a name="step-10-start-cassandra"></a>Krok 10: Spuštění Cassandra
Přihlaste se k každý virtuální počítač a spusťte Cassandra na pozadí spuštěním následujícího příkazu: $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>Testování více oblast clusteru
Nyní Cassandra nasazený na 16 uzlů s 8 uzly v každé oblasti Azure. Tyto uzly jsou ve stejném clusteru na základě běžný název clusteru a počáteční konfigurace uzlu. Použijte následující postup k testování clusteru:

### <a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Krok 1: Získání IP nástroje pro vyrovnávání zatížení pro vnitřní pro oblasti pomocí prostředí PowerShell
* Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-west-us"
* Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-east-us"  
  
    Poznámka: adresy IP (pro – příklad západ - 10.1.2.101, východ - 10.2.2.101) zobrazí.

### <a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Krok 2: Spuštěním následujících v této oblasti po přihlášení na hk-w1 – západní USA
1. Spuštění $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2. Spuštěním následujících příkazů CQL:
   
     Vytvoření KEYSPACE customers_ks REPLIKACE s = {'class': 'NetworkToplogyStrategy', 'WESTUS': 3, 'EASTUS': 3};   POUŽITÍ customers_ks;   Vytvoření tabulky Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Vložit do VALUES(1, 'John', 'Doe') Customers(customer_id, firstname, lastname);   Vložit do Customers(customer_id, firstname, lastname) hodnoty (2, 'Jana', 'Doe');   Vyberte * od zákazníků;

Měli byste vidět zobrazení stejný, jako je nižší než:

| customer_id | FirstName | Příjmení |
| --- | --- | --- |
| 1 |Jan |Doe |
| 2 |Jana |Doe |

### <a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Krok 3: Spusťte následující v oblasti Východ po přihlášení na hk-w1 – východ us:
1. Spuštění $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2. Spuštěním následujících příkazů CQL:
   
     POUŽITÍ customers_ks;   Vytvoření tabulky Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Vložit do VALUES(1, 'John', 'Doe') Customers(customer_id, firstname, lastname);   Vložit do Customers(customer_id, firstname, lastname) hodnoty (2, 'Jana', 'Doe');   Vyberte * od zákazníků;

Jak je vidět pro oblast západní byste měli vidět stejné zobrazení:

| customer_id | FirstName | Příjmení |
| --- | --- | --- |
| 1 |Jan |Doe |
| 2 |Jana |Doe |

Provést několik další vložení a v tématu, aby těch, které se replikovaly západ-nám součástí clusteru.

## <a name="test-cassandra-cluster-from-nodejs"></a>Test Cassandra Cluster z Node.js
Pomocí jednoho z virtuálních počítačů Linux předtím vytvořili ve vrstvě "web", spustit skript jednoduché Node.js čtení dříve vložených dat

**Krok 1: Instalace Node.js a Cassandra klienta**

1. Nainstalovat Node.js a npm
2. Instalace uzlu balíček "cassandra klienta" pomocí npm
3. Spusťte následující skript příkazového řádku prostředí, která zobrazuje řetězec json načtená data:
   
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
Microsoft Azure je flexibilní platforma, která umožňuje spuštění sad Microsoft jak software s otevřeným zdrojem, jak je ukázáno v tomto cvičení. Clustery s vysokou dostupností Cassandra můžete nasadit na jednoho datového centra prostřednictvím šíření uzly clusteru více domén selhání. Také je možné instalovat clustery Cassandra nad několika oblastmi Azure geograficky vzdáleným doklad systémů po havárii. Azure a Cassandra společně umožňuje vytváření vysoce škálovatelnou a vysoce dostupné a po havárii obnovitelné cloudové služby potřeby škálovat podle dnešní internetové služby.  

## <a name="references"></a>Odkazy
* [http://cassandra.apache.org](http://cassandra.apache.org)
* [http://www.datastax.com](http://www.datastax.com)
* [http://www.nodejs.org](http://www.nodejs.org)

