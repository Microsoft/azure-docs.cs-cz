---
title: Šablona řešení ethereum během testování pracovní consortium
description: Pomocí šablony řešení Consortium Etherereum testování pracovní nasazení a konfiguraci sítě konsorcia několika člen ethereum během
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 823bea9bac8ff270d5b5c02e3b76a2f7236c9c99
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242264"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Šablona řešení ethereum během testování pracovní consortium

Šablona řešení ethereum během testování pracovní Consortium je navržena pro snadnější a rychlejší nasazení a konfiguraci sítě konsorcia několika člen ethereum během s minimálními znalostmi Azure a Etherea.

Pomocí několika vstupů uživatele a nasazení jedním kliknutím na webu Azure portal můžete každý člen zřídit své nároky na síť, pomocí Microsoft Azure Compute, sítě a služby úložiště po celém světě. Nároky na síť každého člena se skládá ze sady uzlů s vyrovnáváním zatížení transakce s které aplikace nebo uživatele mohou spolupracovat při odeslání transakce, sada uzlů dolování se záznamu transakcemi a bránu VPN. Krok následné připojení připojí brány k vytvoření plně nakonfigurovaného blockchain více členy sítě.

## <a name="about-blockchain"></a>Informace o blockchainech

Pro ty z vás, kteří začínají s komunitou blockchain vydání tohoto řešení je skvělé příležitosti, další informace o technologii snadné a konfigurovat způsobem v Azure. Abyste mohli začít, ale doporučujeme nasadit jednodušší topologie sítě Etherea samostatné se tento návod, před sestavením navýšení kapacity sítě konsorcia více členy.

### <a name="mining-node-details"></a>Podrobnosti o uzlu dolování

Uzly, které dolovat transakce jsou odděleny od uzly, které přijímají transakce zajistit, že se dvě akce soutěží o ceny stejné prostředky.

Člen consortium můžete zřídit až pět oblastí obsahující jeden nebo více uzlů dolování se opírá o spravovaného disku. Jeden nebo více uzlů v oblasti jsou nakonfigurované jako spouštěcí uzel pro podporu dynamické zjistitelnost uzly v síti. Uzly dolování komunikovat s ostatními dolování uzly převede do caiq týkající se stavu základní distribuované účetní knihy. Není nutné pro vaše aplikace mějte nebo komunikaci s těchto uzlů. Díky zaměření na soukromých sítích, jsou izolované od přidání sekundární vrstvy ochrany příchozího internetového provozu veřejné uzly dolování. Odchozí provoz je povolený, ale ne k portu ethereum během zjišťování.

Všechny uzly mají stabilní verzi klienta přejít Etherea (Geth) a jsou nakonfigurované jako uzly dolování. Pokud jste nezadali vlastní genesis blok, všechny uzly používat stejnou adresu Ethereem a pár klíč, který je chráněn heslem účtu Etherea. Etherea přístupové heslo, které jste zadali slouží ke generování výchozího účtu (coinbase) pro každý uzel dolování. Jako uzly dolování dolovat, shromažďují poplatky, které jsou přidány do tohoto účtu.

Počet uzlů dolování na člen consortium závisí na celkovou velikost požadovaného sítě a hash výkonu vyhrazený pro každého člena. Čím větší sítě, další uzly, které je třeba u něho ohrožena bezpečnost, abyste získali nespravedlivou výhodu. Šablony podporují až 15 uzlů dolování v jedné oblasti zřízené s využitím škálovací sady virtuálních počítačů.

### <a name="transaction-node-details"></a>Podrobnosti o uzlu transakce

Člen consortium má také sada uzlů transakce s vyrovnáváním zatížení. Tyto uzly jsou dostupné z mimo virtuální síť, takže aplikace mohou využívat tyto uzly k odeslání transakce nebo provést v rámci sítě blockchain chytrých kontraktů. Všechny uzly mají stabilní verzi klienta přejít Etherea (Geth) a umožňují udržovat úplnou kopii distribuované účetní knihy. Pokud není k dispozici vlastní genesis blok, tyto uzly použít stejný účet Etherea, chráněné heslem účtu Etherea.

Transakce uzly jsou s vyrovnáváním zatížení ve skupině dostupnosti udržet vysokou dostupnost. Šablony podporují až pět uzlů transakce zřízené s využitím škálovací sady virtuálních počítačů.

### <a name="log-analytics-details"></a>Podrobnosti o log analytics

Každé nasazení také vytvoří novou instanci Log Analytics nebo se může připojit k existující instanci. To umožňuje monitorování různých metrik výkonu každého virtuálního počítače, který vytvoří síť nasazená.

## <a name="deployment-architecture"></a>Architektura nasazení

### <a name="description"></a>Popis

Tato šablona řešení můžete nasadit jednu nebo více oblastí na základě více člen Etherea sítě konsorcia. Všechny oblasti virtuální sítě je připojen k další oblasti v řetězu topologii s použitím brány virtuální sítě a prostředky připojení. Také zřídí registrátora, který obsahuje požadované informace pro všemi Miner a transakce uzly nasazený v jednotlivých oblastech.

### <a name="standalone-and-consortium-leader-overview"></a>Samostatné a consortium vedoucí instancí – přehled

![Přehled Consortium vedoucí instancí a samostatné](./media/ethereum-deployment/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Přehled členů consortium připojení

![Přehled členů Consortium připojení](./media/ethereum-deployment/member-overview.png)

## <a name="getting-started"></a>Začínáme

Tento proces vyžaduje předplatné Azure, které podporují nasazení několika škálovacími sadami virtuálních počítačů a spravované disky. V případě potřeby vytvořte si bezplatný účet Azure začít.

Po přihlášení k odběru je zabezpečená, přejděte na web Azure portal. Vyberte **+ vytvořit prostředek**, Marketplace (Zobrazit vše) a vyhledejte **ethereum během testování pracovní Consortium**.

Šablona nasazení vás provede procesem konfigurace první člen nároky na místo v síti. Tok nasazení se dělí na pět kroků: základy, Operations Management Suite, oblastí nasazení, velikost síťových a výkonu, ethereum během nastavení.

### <a name="basics"></a>Základy

V části **Základy**, zadejte hodnoty pro standardní parametry pro každé nasazení, jako je například předplatné, skupinu prostředků a vlastnosti základního virtuálního počítače.

![Základy](./media/ethereum-deployment/sample-deployment.png)

Název parametru|Popis| Povolené hodnoty|Výchozí hodnoty
---|---|---|---
Vytvořit novou síť nebo připojit k existující síti?|Vytvořit novou síť nebo se připojit ke stávajícím sítě konsorcia|Vytvořit nové připojení k existující|Vytvořit nový
Nasazení sítě, které budou součástí konsorcium?|Sítě konsorcia umožňuje budoucí nasazení této síti připojit (viditelné při *vytvořit nový* je vybraná výše)|Samostatné W3c|Standalone
Předpona prostředků |Řetězec použitý jako základ pro vytváření názvů prostředků (2 až 4 alfanumerické znaky). Jedinečnou hodnotu hash je přidáno jako předpona řetězce pro některé prostředky, zatímco je připojen informací specifických pro prostředky.|Alfanumerické znaky o délce 2 až 4|Není k dispozici
Uživatelské jméno virtuálního počítače| Uživatelské jméno správce každé nasazení virtuálního počítače (jenom alfanumerické znaky)|1 – 64 znaků. |gethadmin
Typ ověřování|Metodu k ověření k virtuálnímu počítači. |Heslo nebo SSH veřejný klíč|Heslo
Heslo (typ ověřování = heslo)|Heslo pro účet správce pro jednotlivé virtuální počítače nasazené. Heslo musí obsahovat 3 z těchto požadavků: 1 velké písmeno, 1 malé písmeno, 1 číslici a 1 speciální znak. <br />Všechny virtuální počítače mají zpočátku stejné heslo, můžete změnit heslo po zřízení.|12 – 72 znaků|Není k dispozici
Klíč SSH (typ ověření veřejného klíče =)|Klíč zabezpečeného prostředí použitý ke vzdálenému přihlášení.|| Není k dispozici
Předplatné| Předplatné, pro které má být nasazení sítě konsorcia||Není k dispozici
Skupina prostředků| Skupina prostředků, do které chcete nasadit sítě konsorcia.||Není k dispozici
Umístění| Oblast Azure pro skupinu prostředků. ||Není k dispozici



### <a name="operations-management-suite"></a>Operations Management Suite

V okně Operations Management Suite (OMS) umožňuje nakonfigurovat prostředek OMS pro vaši síť. OMS bude shromažďovat a poskytuje schopnost rychle zkontrolovat stav sítě nebo ladění problémů surface užitečné metriky a protokoly z vaší sítě. Bezplatné nabídky OMS se po dosažení kapacity řádně selhat.

![Vytváří se nový OMS](./media/ethereum-deployment/new-oms.png)

Název parametru|Popis| Povolené hodnoty|Výchozí hodnoty
---|---|---|---
Připojení k existující OMS|Vytvořit novou instanci Log Analytics nebo připojit k existující instanci|Vytvořit nové připojení k existující|Vytvořit nové umístění Log Analytics|Oblast, kam se nasadí nové Log Analytics (viditelné pokud *vytvořit nový* zaškrtnuto)
Stávající Id pracovního prostoru OMS|ID pracovního prostoru existující instance (viditelné pokud *připojte se k existující* je vybraná) úroveň služby OMS|Zvolte cenovou úroveň pro nové instance. Další informace najdete na https://azure.microsoft.com/pricing/details/log-analytics/ (viditelné pokud *připojte se k existující* zaškrtnuto)|Bezplatné samostatné za uzel|Free
Existujícímu primárnímu klíči OMS|Primární klíč pro připojení k existující instanci OMS (viditelné pokud *připojte se k existující* zaškrtnuto)

### <a name="deployment-regions"></a>Nasazení oblastí

Části **oblastí nasazení**, zadejte vstupy pro **počet položky nakoupené** k nasazení sítě konsorcia a výběr oblastí Azure na základě počtu oblastí, které jsou uvedeny. Uživatele můžete nasadit v maximálně pět oblastí.

![Nasazení oblastí](./media/ethereum-deployment/deployment-regions.png)

Název parametru| Popis| Povolené hodnoty |Výchozí hodnoty
---|---|---|---
Číslo oblasti| Počet oblastí k nasazení sítě konsorcia|1, 2, 3, 4, 5| 2
První oblasti| První oblasti k nasazení sítě konsorcia|Všechny povolené oblastí Azure| USA – západ
Druhé oblasti |Druhé oblasti k nasazení sítě konsorcia (viditelné pouze v případě počtu oblastí je vybrán jako 2)|Všechny povolené oblastí Azure| USA – východ
Třetí oblasti| Třetí oblast pro nasazení sítě konsorcia (viditelné pouze v případě počtu oblastí je vybrán jako 3)|Všechny povolené oblastí Azure| USA – střed
Čtvrtý oblasti| Čtvrtý oblast pro nasazení sítě konsorcia (viditelné pouze v případě počtu oblastí je vybrán jako 4)|Všechny povolené oblastí Azure| Východní USA 2
Pátý oblasti| Pátý oblast pro nasazení sítě konsorcia (viditelné pouze v případě počtu oblastí je vybrán jako 5)|Všechny povolené oblastí Azure| USA – západ 2

### <a name="network-size-and-performance"></a>Velikost síťových a výkonu

V části Další **velikost a výkon sítě** zadejte vstupy pro velikost sítě konsorcia, jako je počet a velikost dolování uzly a uzly transakce.

![Velikost síťových a výkonu](./media/ethereum-deployment/network-size-performance.png)

Název parametru |Popis |Povolené hodnoty| Výchozí hodnoty
---|---|---|---
Počet uzlů dolování|Počet uzlů dolování, které jsou nasazené v jedné oblasti|2 - 15| 2
Výkon úložiště uzel dolování|Typ spravovaný disk, na kterém každý z nasazených dolování uzlů.|Úrovně Standard nebo Premium|Standard
Velikost virtuálního počítače uzlu dolování|Velikost virtuálního počítače pro uzly dolování.|Standardní A <br />Standardní D <br />Standardní D-v2 <br />Standardní řada F <br />Standard DS <br />a Standard služby FS|Standardní D1v2
Počet uzlů transakce s vyrovnáváním zatížení|Počet uzlů transakci ke zřízení jako části sítě.|1 - 5| 2
Výkon transakcí uzlu úložiště|Typ spravovaný disk, na kterém každý z uzlů nasazené transakce.|Úrovně Standard nebo Premium|Standard
Velikost virtuálního počítače uzlu transakce|Velikost virtuálního počítače pro uzly transakce.|Standardní A <br />Standardní D <br />Standardní D-v2 <br />Standardní řada F <br />Standard DS <br />a Standard služby FS|Standardní D1v2

### <a name="ethereum-settings"></a>Ethereum během nastavení

V části Další **ethereum během nastavení**, zadejte nastavení konfigurace související s Ethereem, například síťového ID a Etherea účtu heslo nebo genesis bloku.

![Ethereum během nastavení](./media/ethereum-deployment/standalone-leader-deployment.png)

Název parametru |Popis |Povolené hodnoty|Výchozí hodnoty
---|---|---|---
ConsortiumMember ID|ID přidružené k kolizí, aby každý člen účastnících se sítě konsorcia slouží ke konfiguraci adresní prostory IP adres. <br /><br />ID člena musí být jedinečné v různých organizacích ve stejné síti. Unique – člen ID je potřeba i v případě, že stejné organizace nasadí do víc oblastí.<br /><br />Poznamenejte si hodnotu tohoto parametru vzhledem k tomu je potřeba sdílet s ostatními spojovacího členy.|0 - 255
ID sítě ethereum během|ID sítě pro síť consortium ethereum během nasazení. Každá síť Etherea má svůj vlastní ID sítě, kdy 1 je ID pro veřejnou síť. I když dolování uzly omezen přístup k síti, přesto doporučujeme využívat velké množství, abyste zabránili kolizím.|5 - 999 999 999| 10101010
Vlastní genesis bloku|Možnost automaticky vygenerovat genesis bloku nebo zadat vlastní.|Ano/Ne| Ne
Heslo účtu Etherea (vlastní genesis bloku = No)|Heslo správce používá k zabezpečení účtu ethereum během importu do každého uzlu. Heslo musí obsahovat následující: 1 velké písmeno, 1 malé písmeno a 1 číslici.|12 znaků|Není k dispozici
Heslo privátního klíče Etherea (vlastní genesis bloku = No)|Heslo použité ke generování privátního klíče ECC spojené s výchozím Etherea účet, který je generován. Není potřeba explicitně předávat v předem vygenerovaný soukromý klíč.<br /><br />Vezměte v úvahu přístupové heslo s dostatečnou náhodnost silným privátním klíčem a překrytí s ostatními členy consortium. Heslo musí obsahovat minimálně následující: 1 velké písmeno, 1 malé písmeno a 1 číslici.<br /><br />Mějte na paměti, pokud dva členy používat stejné heslo v účtech generovaných budou stejné. Stejné heslo je užitečné, pokud jedna organizace se pokouší o nasazení v oblastech a chce sdílet jeden účet (základní mince) napříč všemi uzly.|12 znaků|Není k dispozici
Blok Genesis (vlastní genesis bloku = Ano)|Řetězec JSON představující vlastní genesis bloku. Další podrobnosti o formátu bloku genesis tady, najdete v části vlastní sítě.<br /><br />Při zadávání blok vlastní genesis stále vytvoření účtu Etherea. Zvažte zadání prefunded Etherea účtu v bloku genesis není čekat dolování.|Platný kód JSON |Není k dispozici
Sdílený klíč pro připojení|Sdílený klíč pro připojení bran virtuálních sítí.| 12 znaků|Není k dispozici
Adresa URL Consortium dat|Adresa URL odkazující na konfigurační data relevantní consortium poskytované jiný člen nasazení. <br /><br />Tato informace jsou poskytovány již připojené člena, který má nasazení. Pokud jste nasadili zbytku sítě, adresa URL je výstup nasazení šablony, s názvem CONSORTIUM DATA.||Není k dispozici
Brána virtuální sítě pro připojení k|Cesta prostředku brány virtuální sítě pro připojení.<br />Tato informace jsou poskytovány již připojené člena, který má nasazení. Pokud jste nasadili zbytku sítě, adresa URL je ve výstupu šablony nasazení s názvem CONSORTIUM_MEMBER_GATEWAY_ID. Poznámka: Stejného člena consortium data adresy URL a bránu virtuální sítě resource musí použít.||Není k dispozici
Koncový bod sdílené informace registrátora|Sdílená informace o koncový bod nasazení jiného člena|Platný koncový bod prvního člena v W3C|Není k dispozici
Doménový Registrátor informace sdílené klíče|Sdílená informace o primární klíč poskytuje jiný člen nasazení|Platný primární klíč prvního člena v consortium|Není k dispozici

### <a name="summary"></a>Souhrn

Proklikejte se okno s přehledem si zadané vstupy a spustit základní ověření před nasazením.

![Souhrn](./media/ethereum-deployment/summary.png)

Přečíst si právní informace a ochrana osobních údajů podmínky a klikněte na tlačítko **nákupní** k nasazení. Pokud nasazení obsahuje více než jedné oblasti, nebo je pro sítě konsorcia, tato šablona předem nasadí nezbytné brány sítě VPN pro podporu připojení k síti s ostatními členy. Nasazení brány může trvat až 45 minut 50.

## <a name="post-deployment-sanity-checks"></a>Kontrola správnosti nasazení příspěvku

### <a name="administrator-page"></a>Stránce správce.

Po úspěšném dokončení nasazení a zajištěny všechny prostředky, můžete přejít na stránku správce a získejte přehled o vaší síti blockchain a vhodnosti zkontrolujte stav nasazení. Adresa URL stránky pro správu je název DNS nástroje pro vyrovnávání zatížení; je k dispozici v části výstupů šablony nasazení s názvem jako ADMIN_SITE.

Pokud chcete ji najít, vyberte skupinu prostředků, který byl nasazen. Vyberte **přehled**a klikněte na odkaz přímo pod **nasazení** , která zobrazuje číslo, které bylo úspěšné.

![Přehled skupiny prostředků](./media/ethereum-deployment/resource-overview.png)

Nová obrazovka se zobrazuje historie nasazení. Vyberte první prostředek nasazení (například microsoft-azure-blockchain.azure-blockchain – aktualizace servi...) a vyhledejte **výstupy** části v dolní polovině stránky. Zobrazí se vám adresa URL stránky pro správu uvedená v parametru šablony nasazení výstup jako ADMIN_SITE.

![Nasazení prostředků](./media/ethereum-deployment/resource-deployments.png)

![Výstup nasazení](./media/ethereum-deployment/deployment-output.png)

Chcete-li získat na stránku pro správu, zkopírujte **web pro správu** výstupní a otevřete ho na jiné kartě.

Na stránce správce můžete získat základní přehled o topologii, kterou jste nasadili kontrolou části stav uzlu Etherea. Zahrnuje všechny názvy hostitelů uzlu, počtu jejich partnera a nejnovější bloku vidět. Počet partnera pro každý uzel je mezi minimálně jednu menší než *celkový počet uzlů* a počet nakonfigurované maximální druhé strany. Všimněte si, že počet peer neomezuje počet uzlů, které je možné nasadit v rámci sítě.
V některých případech uvidíte počet peer kolísat a být menší než (celkový počet uzlů – 1). Rozdíl v počtu není vždy znaménko, že uzly nejsou v pořádku, protože větve v knize může způsobit menší změny v počtu partnera. A konečně můžete si prohlédnout nejnovější bloku vidět každý uzel v síti k určení větve nebo zjevně demonstruje výskyt pomalé v systému.

![Stav uzlu](./media/ethereum-deployment/node-status.png)

Stav uzlu se aktualizují každých 10 sekund. Aktualizujte stránku v prohlížeči nebo **Reload** tlačítko Aktualizovat zobrazení.

### <a name="oms-portal"></a>Portál OMS

Na portálu OMS můžete najít pomocí následujícího odkazu ve výstupu nasazení (OMSPORTALURL) nebo tak, že vyberete prostředků OMS ve vaší skupině nasazených prostředků.

![ADRESA URL OMS](./media/ethereum-deployment/oms-url.png)

![Prostředek OMS](./media/ethereum-deployment/oms-resource.png)

Na portálu se nejprve zobrazí statistika základní sítě jako základní informace o.

![Přehled OMS](./media/ethereum-deployment/oms-overview.png)

Kliknutím na přehled vás nasměrujeme na portálu zobrazit podle uzlu statistiky.

![Statistika za uzel](./media/ethereum-deployment/per-node-stats.png)

### <a name="accessing-nodes"></a>Přístup k uzly

Můžete se vzdáleně připojit k virtuálním počítačům pro uzly transakce přes protokol SSH klíč správce zadané uživatelské jméno a heslo nebo SSH. Vzhledem k tomu, že virtuální počítače uzlů transakce nemají vlastní veřejné IP adresy, je potřeba projít nástroje pro vyrovnávání zatížení a zadejte číslo portu. Příkaz SSH ke spuštění pro přístup k první uzel transakce je uveden v parametr výstupu šablony nasazení jako **SSH_TO_FIRST_TX_NODE** (pro ukázkové nasazení: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). Pokud chcete vrátit do transakce další uzly, zvyšovat číslo portu jednou (první uzel transakce je třeba na portu 4000).

Protože virtuální počítače, na kterých uzlech dolování spuštění nejsou zvenku přístupný, budete muset přejít do jednoho z uzlů transakce. Až budete mít relaci SSH k uzlu transakce, transakce uzlu nainstalovat váš privátní klíč nebo spusťte některý z uzlů dolování relace SSH pomocí hesla.

**Poznámka**

Názvy hostitelů je možné získat z webu správce nebo na webu Azure portal. Na webu Azure portal, názvy hostitelů uzly, které jsou k dispozici v prostředku virtuálního počítače škálovací sady (VMSS) uveden v části **instance**, která se liší od skutečné názvy hostitelů. Název hostitele na webu Azure portal může například vypadat **mn. asdfmv reg1_0** ale skutečný název hostitele by například **mn. asdfmv reg**.

Příklad:

Název hostitele Azure portal| Skutečný název hostitele
---|---
Mn. ethwvu reg1_0| Mn. ethwvu reg1000000
Mn. ethwvu reg1_1 |Mn. ethwvu reg1000001
Mn. ethwvu reg1_2 |Mn. ethwvu reg1000002

## <a name="adding-a-new-consortium-member"></a>Přidání nového člena W3C

### <a name="sharing-data"></a>Sdílení dat

Jako první člen (nebo připojených člen) konsorcia potřebujete poskytovat ostatním členům několik údaje, aby se můžete připojit a jejich připojení. Zejména:

1. **Sdílená Data konfigurace Consortium**: existuje sada dat, která se používá k orchestraci ethereum během připojení mezi dvěma členy. Potřebné informace, včetně genesis bloku, consortium ID sítě a spouštěcí uzly, se zapisují do souboru na uzlech transakce se vedoucí instancí nebo jiné nasazené člena. Umístění tohoto souboru je uveden v parametr výstupu šablony nasazení s názvem **CONSORTIUM DATA**.
2. **Vytvoření partnerského vztahu koncového bodu údaje**: The sdílené informace o registrátora koncový bod se získat informace o všech uzlů již připojen k síti Etherea od vedoucích nebo jiný člen nasazení. Úložiště databáze sadu informace týkající se každý uzel připojení v síti, informace, jako je název hostitele uzlu, privátní IP adresu atd. Toto je parametr výstupu šablony nasazení s názvem **PEER_INFO_ENDPOINT**.
3. **Vytvoření partnerského vztahu primární klíč**: The sdílené informace registrátora primární klíč slouží k získání přístupu se vedoucí instancí nebo jiný člen sdílené informace o primárním klíči. Toto je parametr výstupu šablony nasazení s názvem **PEER_INFO_PRIMARY_KEY**.


4. **Brána virtuální sítě**: každý člen naváže připojení k síti celý blockchainu pomocí existujícího člena. Pro připojení virtuální sítě, budete potřebovat cestu prostředku k bráně virtuální sítě členu, ke kterému se připojujete. Toto je parametr výstupu šablony nasazení s názvem **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Sdílený klíč**: A předem vytvořit tajný mezi dva členy sítě konsorcia, které jsou navazování připojení. Toto je alfanumerický řetězec (mezi 1 až 128 znaků), který byl dohodnutých mimo kontext nasazení. (Například **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Přijetí nového člena

Tento krok byste měli provést po spojovacího člen úspěšně nasadil své síti. Člen můžete připojit k síti a sledovat provoz transakce, existujícího člena nutné provést konečnou konfiguraci na jejich VPN Gateway tak, aby přijímal připojení. To znamená, že uzly Etherea spojovacího člena se nespustí, dokud se naváže spojení. Tato konfigurace, můžete to udělat pomocí Powershellu nebo rozhraní příkazového řádku xPlat. Skript prostředí PowerShell module a xPlat CLI jsou také uloženy na uzlu transakce, vedle dat consortium. Umístění skriptu je výstupní parametry nasazení s názvem **PÁR brány PS modulu** a **PÁR-GATEWAY-AZURE-CLISCRIPT**v uvedeném pořadí.

**Instalace Powershellu nebo rozhraní příkazového řádku**

Další informace o tom, jak začít pracovat s rutinami Azure Powershellu a Azure xPlat CLI najdete v dokumentaci k Azure.

Budete potřebovat nejnovější verzi rutin Azure místně nainstalovaný a relace otevřít. Ujistěte se, že pro přihlášení do relace pomocí svých přihlašovacích údajů předplatného Azure.

**Powershellu: Připojení**

Stáhnout modul prostředí PowerShell a uložte ho místně. Zadané umístění modulu prostředí PowerShell jako **PÁR brány PS modulu** nasazení šablony výstupní parametr.

Pokud ještě není povolené, použijte **Set-ExecutionPolicy** rutiny pro místní relaci povolit modul bez znaménka.

**Set-ExecutionPolicy Unrestricted CurrentUser**

Další, přihlášení k předplatnému Azure, ve které jste nasadili, vedoucí nasazení pomocí

**Login-AzureRmAccount**

Dále naimportujte modul:

**Import-Module <filepath to downloaded file>**

Nakonec spusťte funkci pomocí odpovídající vstup:

- **MyGatewayResourceId** : cestu prostředku brány. Toto je parametr výstupu šablony nasazení s názvem **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : cestu prostředku brány připojující člena. To poskytuje spojovacího člena a má také parametr výstupu šablony nasazení z název **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : název k identifikaci tohoto připojení brány.
- **Sdílený klíč** : předem zavedené tajný kód mezi dvěma členy sítě konsorcia, které jsou navazování připojení.

**CreateConnection** -MyGatewayResourceId <resource path of your Gateway> - OtherGatewayResourceId < cesta prostředku brány připojující člena > - ConnectionName Mojepripojeni - SharedKey "MySharedKeyAbc123"

**xPlat CLI: vytvoření připojení**

Stáhněte si skript rozhraní příkazového řádku Azure a uložte ho místně. Umístění skriptu rozhraní příkazového řádku Azure je zadaný v parametru šablony nasazení s názvem **PÁR-GATEWAY-AZURE-skript rozhraní příkazového řádku**.

Spusťte skript s odpovídající vstup:

- **MyGatewayResourceId** : cestu prostředku brány. Toto je parametr výstupu šablony nasazení s názvem **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : cestu prostředku brány připojující člena. To, kterou pomocí spojovacího členu, je parametr šablony nasazení jejich nasazení také s názvem **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : název k identifikaci tohoto připojení brány.
- **Sdílený klíč** : předem zavedené tajný kód mezi dvěma členy sítě konsorcia, které jsou navazování připojení.
- **Umístění** : oblast Azure, které se nasadí váš prostředek brány.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

Tato architektura se po úspěšném nakonfigurování připojení mezi měl vypadat takto **vedoucí** a **člen** nasazení.

![Architektura vedoucí instancí a člena](./media/ethereum-deployment/leader-member.png)

## <a name="fund-new-member-account"></a>Nový členský účet fondu

### <a name="generated-genesis-block"></a>Vygenerovaný genesis bloku

Jako první člen účtu Etherea je financování s jedním bilion ether Pokud nasazení generuje bloku genesis (vlastní Genesis bloku = ne). Účet, který není předem financování a musíte počkat s nashromáždí Ether během jejich uzlů dolování začít dolovat bloky budou mít další členové. Abyste nemuseli kterou čekat Ether nové členy, je potřeba explicitně fondu spojovacího členy Etherea účty.

Uděláte to tak, spojování členy musí poskytují Etherea účet, který se zobrazí na svém webu správce. Potom můžete svůj web správce k přenosu Ether z vašeho účtu do svého účtu tak, že zadáte účet, který jste.

### <a name="custom-genesis-block"></a>Vlastní genesis bloku

Pokud vlastní genesis blok s určeným účtem Etherea můžete použít MetaMask nebo jiný nástroj pro přenos ether od zadané účtu předem generovaného Etherea účet viditelné na webu správce. Pokyny k používání MetaMask najdete v tématu [účet ethereum během vytváření](#create-ethereum-account).

Pokud vlastní genesis bloku je k dispozici bez účtu nebo nemáte přístup k žádné předběžně přidělit účty, musíte počkat, dokud uzly dolování začít dolovat ke generování Ether ke svému účtu (základní mince). Jak rychle jsou generovány prostředků závisí na úrovni potíže, které zadáte v bloku vlastní genesis.

## <a name="create-ethereum-account"></a>Vytvoření účtu ethereum během

Chcete-li vytvořit další účet, můžete použít širokou škálu řešení. Jedním z těchto řešení je MetaMask, Chrome příponu, která poskytuje identitu trezor a připojení k síti s Ethereem, veřejná, testovací nebo vlastní. MetaMask výrobky zpracovává transakce k registraci účtu v síti.

Tato transakce, stejně jako jakékoli jiné transakce, přejdete na jednom z uzlů transakce a nakonec dolována do bloku, jak je znázorněno níže.

![Uzel transakce](./media/ethereum-deployment/transaction-node.png)

K instalaci rozšíření v prohlížeči Chrome, přejděte k přizpůsobení a řízení Google Chrome (tlačítku přetečení) > více nástroje > Rozšíření > Získejte více rozšíření a vyhledejte MetaMask.

![MetaMask rozšíření](./media/ethereum-deployment/metamask-extension.png)

Po instalaci otevřete MetaMask a vytvořte nový trezor. Ve výchozím nastavení připojí k testovací síti Morden trezoru. Je potřeba změnit pro připojení k síti nasazené privátní consortium, konkrétně pro vyrovnávání zatížení před uzly transakce. Z výstupu šablony načíst vystavené Etherea RPC koncový bod na port 8545, s názvem jako `ETHEREUM-RPC-ENDPOINT`a zadejte ho na vlastní RPC, jak je znázorněno níže.

![Nastavení MetaMask](./media/ethereum-deployment/metamask-settings.png)

Tím vytvoříte trezor, vytvoříte peněženky obsahující účet. Chcete-li vytvořit další účty, vyberte **přepínač účty** a pak **+** tlačítko.

![MetaMask vytvořit účet](./media/ethereum-deployment/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Spustit počáteční Ether přidělení

Prostřednictvím stránky Správce formulujete transakce přenést do jiného účtu Etherea Ether z předběžně přidělit účtu. Tento převod Ether je transakce, která se odesílají do uzlu transakce a dolována do bloku, jak je znázorněno níže.

![Uzel transakce](./media/ethereum-deployment/transaction-node-mined.png)

Pomocí ikony schránky v MetaMask peněženky zkopírujte adresu Etherea účet, ke kterému chcete přenést ether a přejít zpět na stránku pro správce. Vložte zkopírovaný účtu do vstupního pole pro přenos 1000 ether z předběžně přidělit účtu Etherea k nově vytvořenému účtu. Klikněte na tlačítko **odeslat** a počkejte transakce dolována do bloku.

![Stav uzlu](./media/ethereum-deployment/node-status2.png)

Jakmile transakce se potvrdí do bloku vydolovaná, zůstatku v MetaMask pro váš účet bude odrážet přenos 1000 Ether.

![MetaMask přenosu](./media/ethereum-deployment/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Přenos Ether mezi účty

V tuto chvíli jste připraveni k provedení transakcí ve vaší síti privátní consortium. Nejjednodušší transakce je k přenosu Ether z jednoho účtu do druhého. Formulovat tyto transakce, můžete použít MetaMask zase přenosu peníze z první účet jako výše můžete druhého účtu.

Z **peněženky 1** v MetaMask, klikněte na Odeslat. Zkopírujte adresu druhý peněženky vytvořené do **adresu příjemce** vstupní pole a množství Ether příchozí přenos **částka** vstupní pole. Klikněte na tlačítko **odeslat** a přijměte transakce.

![MetaMask odeslání transakce](./media/ethereum-deployment/metamask-send.png)

Ještě jednou když transakce je dolována a potvrdit do bloku, zůstatky na účtu se projeví odpovídajícím způsobem. Všimněte si, **peněženky 1**společnosti odečte vyrovnávat víc než 15 Ether, protože jste měli poplatek dolování ke zpracování transakce.

![Peněženky 1](./media/ethereum-deployment/wallet1.png)

![Peněženky 2](./media/ethereum-deployment/wallet2.png)

## <a name="next-steps"></a>Další postup

Nyní jste připraveni soustředit na vývoj inteligentní smlouvy proti vaší privátní consortium blockchain sítě a aplikace.
