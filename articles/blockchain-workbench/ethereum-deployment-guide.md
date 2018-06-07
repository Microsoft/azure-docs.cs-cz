---
title: Šablona řešení ověření pracovní consortium Ethereum
description: Pomocí této šablony řešení Etherereum ověření pracovní Consortium nasaďte a nakonfigurujte síť Ethereum consortium více členy
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 0ea9bd2c7d23aa227e62858983e7170b771751da
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654818"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Šablona řešení ověření pracovní consortium Ethereum

Šablona řešení Ethereum ověření pracovní Consortium umožňuje snadnější a rychlejší nasazení a konfiguraci sítě Ethereum consortium více členy s minimálními znalostmi Azure a Ethereum.

Někteří z nich vstupy uživatele a jedním kliknutím nasazení pomocí portálu Azure každý člen můžou zřizovat jejich nároky na síť, pomocí Microsoft Azure Compute, sítě a služby úložiště po celém světě. Každý člen sítě nároků obsahuje sadu uzlů s vyrovnáváním zatížení transakce s které aplikace nebo uživatele mohou komunikovat se odeslat transakce, sada uzlů dolování k záznamu transakce a brány VPN. Krok následné připojení připojí brány vytvořit síť kompletně nakonfigurovaný blockchain více členy.

## <a name="about-blockchain"></a>O blockchain

Pro ty z vás, kdo jsou nové komunitě blockchain verzi toto řešení je skvělé možnost Další informace o technologii způsobem snadno a konfigurovat v Azure. Abyste mohli začít, ale doporučujeme nasazení jednodušší topologie sítě Ethereum samostatné v tomto průvodci s asistencí, před vytváření sítí consortium více členy.

### <a name="mining-node-details"></a>Podrobnosti o dolování uzlu

Uzly, které Moje transakce jsou oddělené z uzlů, které přijímají transakce zajistit, že dvě akce nejsou neslučitelných pro stejné prostředky.

Člen consortium můžete zřídit až pět oblastí obsahující jeden nebo více uzlů dolování zajišťoval spravované disk. Jeden nebo více uzlů v oblasti jsou nakonfigurovány jako spouštěcí uzel pro podporu dynamické zjistitelnost uzly v síti. Uzly dolování komunikovat s ostatními dolování uzly připojí k konsenzu týkající se stavu základní distribuované hlavní knihy. Není nutné, aby aplikace zajímat, nebo komunikovat s tyto uzly. Pokud se zaměříte na soukromých sítích, jsou izolované od příchozí veřejné internetové přenosy pro přidání sekundární vrstvy ochrany uzly dolování. Odchozí provoz je povolený, ale ne na port Ethereum zjišťování.

Všechny uzly mají stabilní verzi klienta přejděte Ethereum (Geth) a jsou nakonfigurované jako uzly dolování. Pokud není poskytnut blok vlastní genesis, všechny uzly používat stejnou adresu Ethereum a páru klíčů, který je chráněn Ethereum heslo účtu. Ethereum přístupové heslo, které jste zadali slouží ke generování výchozí účet (coinbase) pro každý uzel dolování. Jako uzly dolování, odstraňování, které shromažďují poplatků, které jsou k tomuto účtu.

Počet uzlů dolování na každého člena consortium závisí na celkovou velikost požadovaného sítě a objem hash napájení vyhrazeného pro každého člena. Čím větší síti, další uzly, které potřebují k narušení k získání nekalé výhody. Šablona podporuje až 15 dolování uzlů na oblast zřídit pomocí sady škálování virtuálního počítače.

### <a name="transaction-node-details"></a>Podrobnosti transakce uzlu

Člen consortium také obsahuje sadu uzlů s vyrovnáváním zatížení transakce. Tyto uzly jsou dostupné z mimo virtuální síť, takže aplikace můžete použít tyto uzly odeslání transakce nebo provést inteligentní kontrakty v rámci sítě blockchain. Všechny uzly mají stabilní verzi klienta přejděte Ethereum (Geth) a jsou nakonfigurovány k udržování úplnou kopii distribuované hlavní knihy. Pokud není k dispozici vlastní genesis blok, tyto uzly použít stejný účet Ethereum, chráněné heslem účtu Ethereum.

Transakce uzly jsou vyrovnávání zatížení v rámci kvůli udržení vysoké dostupnosti sadu dostupnosti. Šablona podporuje až pět uzlů transakce zřídit pomocí sady škálování virtuálního počítače.

### <a name="log-analytics-details"></a>Podrobnosti protokolu analýzy

Každé nasazení také vytvoří novou instanci analýzy protokolů nebo se můžete připojit existující instanci. To umožňuje monitorování různých metrik výkonu každého virtuálního počítače, které tvoří nasazené sítě.

## <a name="deployment-architecture"></a>Architektura nasazení služby

### <a name="description"></a>Popis

Tato šablona řešení můžete nasadit jeden nebo více na základě oblast více člen Ethereum consortium sítě. Virtuální síť každá oblast je připojený k jiné oblasti v topologii řetězu pomocí brány virtuální sítě a prostředky připojení. Zřídí také doménového registrátora, který obsahuje požadované informace všech analýzy a transakce uzlů nasazený v jednotlivých oblastech.

### <a name="standalone-and-consortium-leader-overview"></a>Samostatné a consortium vedoucí – přehled

![Samostatné a Consortium vedoucí – přehled](./media/ethereum-deployment-guide/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Přehled členů consortium připojení

![Přehled členů Consortium připojení](./media/ethereum-deployment-guide/member-overview.png)

## <a name="getting-started"></a>Začínáme

Tento proces vyžaduje předplatné Azure, kterou může podporovat nasazení několik sady škálování virtuálních počítačů a spravovat disky. V případě potřeby vytvořte zahájíte bezplatný účet Azure.

Jakmile se předplatné musí být zabezpečená, přejděte na portál Azure. Vyberte **+ vytvořit prostředek**, Marketplace (Zobrazit vše) a vyhledejte **Ethereum ověření pracovní Consortium**.

Nasazení šablony budou vás provede procesem konfigurace první člen nároků v síti. Tok nasazení je rozdělené do pěti krocích: základy, Operations Management Suite, nasazení oblastí, Network velikost a výkon, Ethereum nastavení.

### <a name="basics"></a>Základy

V části **Základy**, zadejte hodnoty pro standardní parametry pro všechna nasazení, jako je například předplatné, skupinu prostředků a vlastnosti základní virtuálního počítače.

![Základy](./media/ethereum-deployment-guide/sample-deployment.png)

Název parametru|Popis| Povolené hodnoty|Výchozí hodnoty
---|---|---|---
Vytvoření nové sítě nebo existující připojení k síti?|Vytvořit novou síť nebo připojení k existující síti consortium|Vytvořit nové připojení k existující|Vytvořit nový
Nasazení sítě, který bude součástí konsorcium?|Síť consortium umožňuje budoucí nasazení ke této sítě (viditelné v případě *vytvořit nový* nevybrali)|Samostatné Consortium|Standalone
Předpona prostředků |Řetězec se používá jako základ pro názvy prostředků (2 až 4 alfanumerické znaky). Jedinečnou hodnotu hash před řetězec pro některé prostředky, když se připojí informace specifické pro prostředek.|Alfanumerické znaky o délce 2 až 4|Není k dispozici
Virtuální počítač uživatelské jméno| Uživatelské jméno správce každé nasazení virtuálních počítačů (jenom alfanumerické znaky)|1 - 64 znaků. |gethadmin
Typ ověřování|Metoda ověřování do virtuálního počítače. |Heslo nebo SSH veřejný klíč|Heslo
Heslo (typ ověřování = heslo)|Heslo pro účet správce pro jednotlivé virtuální počítače nasazené. Heslo musí obsahovat 3 z následujících požadavků: 1 velké písmeno, 1 malé písmeno, 1 číslice a 1 speciální znak. <br />I když všechny virtuální počítače původně má stejné heslo, můžete změnit heslo po zřízení.|12 - 72 znaků|Není k dispozici
Klíč SSH (typ ověřování = veřejný klíč)|Klíč zabezpečeného prostředí použít pro vzdálené přihlášení.|| Není k dispozici
Předplatné| Předplatné, do které chcete nasadit consortium sítě||Není k dispozici
Skupina prostředků| Skupinu prostředků, do které chcete nasadit consortium sítě.||Není k dispozici
Umístění| Oblast Azure pro skupinu prostředků. ||Není k dispozici



### <a name="operations-management-suite"></a>Operations Management Suite

V okně Operations Management Suite (OMS) umožňuje nakonfigurovat prostředek OMS vaší sítě. OMS bude shromažďovat a prostor užitečné metriky a protokoly z vaší sítě, což umožňuje rychle zjistit stav sítě nebo ladění problémy. Možnosti zadarmo nabízeného OMS selže řádně, jakmile je dosažena kapacita.

![Vytvoření nové OMS](./media/ethereum-deployment-guide/new-oms.png)

Název parametru|Popis| Povolené hodnoty|Výchozí hodnoty
---|---|---|---
Připojit k existující OMS|Vytvoření nové instance analýzy protokolů nebo se připojit ke stávající instanci|Vytvořit nové připojení k existující|Vytvořit nové umístění analýzy protokolů|Oblast, kde bude nasazen nové analýzy protokolů (Pokud viditelné *vytvořit nový* je vybraná)
Id pracovního prostoru existující OMS|ID pracovního prostoru existující instance (viditelné v případě *připojení k existující* je vybraná) vrstvy služby OMS|Vyberte cenovou úroveň pro nové instance. Další informace v https://azure.microsoft.com/pricing/details/log-analytics/ (viditelné v případě *připojení k existující* je vybraná)|Bezplatnou samostatnou na uzel|Free
Existující primární klíč OMS|Primární klíč používaný k připojení k existující instanci OMS (viditelné v případě *připojení k existující* je vybraná)

### <a name="deployment-regions"></a>Nasazení oblastí

V části Další **nasazení oblasti**, zadejte vstupy pro **počet oblast(i)** nasazení consortium sítě a výběr oblastí Azure na základě počtu oblasti zadané. Uživatele můžete nasadit v maximálně pět oblastí.

![Nasazení oblastí](./media/ethereum-deployment-guide/deployment-regions.png)

Název parametru| Popis| Povolené hodnoty |Výchozí hodnoty
---|---|---|---
Počet oblast(i)| Počet oblastí nasadíte consortium síť|1, 2, 3, 4, 5| 2
První oblasti| První oblasti nasadíte consortium síť|Všechny povolené oblastí Azure| Západní USA
Druhý oblast |Druhý oblast nasadíte consortium síť (viditelný jenom v případě, že počet oblastí je vybrán jako 2)|Všechny povolené oblastí Azure| Východ USA
Třetí oblast| Třetí oblast nasadíte consortium síť (viditelný jenom v případě, že počet oblastí je vybrán jako 3)|Všechny povolené oblastí Azure| Střed USA
Čtvrtý oblast| Čtvrtý oblast nasadíte consortium síť (viditelný jenom v případě, že počet oblastí je vybrán jako 4)|Všechny povolené oblastí Azure| Východní USA 2
Páté oblast| Páté oblast nasadíte consortium síť (viditelný jenom v případě, že počet oblastí je vybrán jako 5)|Všechny povolené oblastí Azure| Západní USA 2

### <a name="network-size-and-performance"></a>Velikost sítě a výkon

V části Další **sítě velikost a výkon** zadejte vstupy pro velikost consortium sítě, jako je počet a velikost dolování uzly a uzly transakce.

![Velikost sítě a výkon](./media/ethereum-deployment-guide/network-size-performance.png)

Název parametru |Popis |Povolené hodnoty| Výchozí hodnoty
---|---|---|---
Počet uzlů dolování|Počet uzlů dolování nasazení podle oblastí|2 – 15| 2
Výkon úložiště dolování uzlu|Typ spravovaných disků na všech uzlech nasazené dolování zálohování.|Standard nebo Premium|Standard
Velikost virtuálního počítače uzlu dolování|Velikost virtuálního počítače použít pro uzly dolování.|Standardní A <br />Standardní D <br />Standardní D-v2 <br />Standardní řady F, <br />Standardní DS <br />a standardní služby FS|Standardní D1v2
Počet transakcí uzly Vyrovnávání zatížení|Počet uzlů transakce zřídit v rámci sítě.|1 - 5| 2
Výkon úložiště uzlu transakce|Typ spravovaných disků na všech uzlech nasazené transakce zálohování.|Standard nebo Premium|Standard
Velikost virtuálního počítače uzlu transakce|Velikost virtuálního počítače použít pro uzly transakce.|Standardní A <br />Standardní D <br />Standardní D-v2 <br />Standardní řady F, <br />Standardní DS <br />a standardní služby FS|Standardní D1v2

### <a name="ethereum-settings"></a>Nastavení Ethereum

V části Další **Ethereum nastavení**, zadat nastavení konfigurace související se Ethereum, jako jsou sítě ID a Ethereum účet heslo nebo genesis bloku.

![Nastavení Ethereum](./media/ethereum-deployment-guide/standalone-leader-deployment.png)

Název parametru |Popis |Povolené hodnoty|Výchozí hodnoty
---|---|---|---
ConsortiumMember ID|Identifikátor přidružený k zamezení kolize každý člen podílejících se na síti consortium slouží ke konfiguraci adresní prostory IP adres. <br /><br />ID člena by měl být jedinečný v rámci různých organizací ve stejné síti. Člen Jedinečný ID je potřeba i v případě, že stejné organizace nasadí do několika oblastí.<br /><br />Poznamenejte si hodnotu tohoto parametru vzhledem k tomu, že je potřeba sdílet s ostatními spojující členy.|0 - 255
ID sítě Ethereum|ID sítě pro síť Ethereum consortium nasazuje. Každá síť Ethereum má svou vlastní ID sítě, s 1 se ID pro veřejné síti. Při přístupu k síti je určeno pro uzly dolování, doporučujeme využívat velké množství, abyste zabránili kolizím.|5 - 999 999 999| 10101010
Vlastní genesis bloku|Možnost automaticky vygenerovat blok genesis nebo zadejte vlastní.|Ano/Ne| Ne
Heslo účtu Ethereum (vlastní genesis bloku = ne)|Heslo správce používá k zabezpečení účtu Ethereum importovat do každého uzlu. Heslo musí obsahovat následující: 1 velké písmeno, 1 malé písmeno a číslo 1.|12 nebo více znaků|Není k dispozici
Ethereum privátní klíče přístupové heslo (vlastní genesis bloku = ne)|Přístupové heslo používané ke generování přidružený výchozí Ethereum účet, který se vygeneruje privátní klíč ECC. Předem vygenerovaná privátní klíč nemusí být explicitně předán.<br /><br />Zvažte s dostatečnou náhodnost zajistit silným privátním klíčem a bez překrytí s jinými členy consortium přístupové heslo. Heslo musí obsahovat minimálně následující: 1 velké písmeno, 1 malé písmeno a číslo 1.<br /><br />Poznámka: Pokud dva členové používají stejné heslo v účtech generovaných budou stejné. Stejné heslo je užitečné, pokud jedna organizace je pokusu o nasazení v oblastech a chce sdílet jeden účet (mince základní) ve všech uzlech.|12 nebo více znaků|Není k dispozici
Blok Genesis (vlastní genesis bloku = Yes)|Řetězce formátu JSON představující vlastní genesis bloku. Další informace o formátu bloku genesis zde najdete v části vlastní sítě.<br /><br />Účet Ethereum stále vytváření plynoucí z poskytování vlastních genesis bloku. Zvažte zadání účet prefunded Ethereum v bloku genesis není čekat dolování.|Platný kód JSON |Není k dispozici
Sdílený klíč pro připojení|Sdílený klíč pro připojení mezi brány virtuální sítě.| 12 nebo více znaků|Není k dispozici
Adresy URL Consortium dat|Adresa URL odkazující na konfigurační data relevantní consortium poskytované jiného člena nasazení. <br /><br />Tato informace jsou poskytovány již připojené člen, který má nasazení. Pokud jste nasadili zbytku sítě, adresa URL je výstup šablony nasazení, název CONSORTIUM DATA.||Není k dispozici
Brána virtuální sítě pro připojení k|Cesta prostředku brány virtuální sítě, ke kterému chcete připojit.<br />Tato informace jsou poskytovány již připojené člen, který má nasazení. Pokud jste nasadili zbytku sítě, adresa URL je ve výstupu šablony nasazení s názvem CONSORTIUM_MEMBER_GATEWAY_ID. Poznámka: Stejného člena consortium data adresy URL a bránu VNet prostředků musí použít.||Není k dispozici
Koncový bod sdílené informace registrátora|Koncový bod sdílené informace poskytované jiného člena nasazení|Neplatný koncový bod prvního člena v consortium|Není k dispozici
Klíč sdílené informace registrátora|Primární klíč pro sdílené informace poskytované jiného člena nasazení|Platný primární klíč prvního člena v consortium|Není k dispozici

### <a name="summary"></a>Souhrn

Proklikejte se prostřednictvím souhrnu okna pro zkontrolujte vstupní hodnoty zadané a spuštění základní ověření před nasazením.

![Souhrn](./media/ethereum-deployment-guide/summary.png)

Přečtěte si podmínky právní a ochrana osobních údajů a klikněte na tlačítko **nákupu** k nasazení. Pokud nasazení obsahuje více než jedné oblasti nebo je v síti consortium, tato šablona předem nasadí nezbytné brány sítě VPN pro podporu připojení k síti s jinými členy. Nasazení brány může trvat až 45 minut 50.

## <a name="post-deployment-sanity-checks"></a>Po nasazení správností kontroluje

### <a name="administrator-page"></a>Správce stránky

Po nasazení byla úspěšně dokončena a všechny prostředky se zřizují, můžete přejít na stránku správce získat zobrazení blockchain sítě a správností zkontrolujte stav nasazení. Adresu URL stránky pro správu je název DNS nástroje pro vyrovnávání zatížení; nachází se v části nasazení šablony s názvem jako ADMIN_SITE výstup.

Chcete-li ji najít, vyberte skupinu prostředků, který byl nasazen. Pak vyberte **přehled**a klikněte na odkaz bezprostředně pod **nasazení** , se zobrazuje číslo, které bylo úspěšné.

![Přehled skupiny prostředků](./media/ethereum-deployment-guide/resource-overview.png)

Nové obrazovky zobrazuje historii nasazení. Vyberte na první prostředek nasazení (například microsoft-azure-blockchain.azure-blockchain-servi...) a podívejte se **výstupy** části ve spodní polovině stránky. Zobrazí adresu URL pro uvedené v parametru šablony nasazení výstup jako ADMIN_SITE stránky pro správu.

![Nasazení prostředků](./media/ethereum-deployment-guide/resource-deployments.png)

![Výstup nasazení](./media/ethereum-deployment-guide/deployment-output.png)

Chcete-li se na stránku správce, zkopírujte **web pro správu** výstup a otevřete ji na jiné kartě.

Na stránce správce můžete získat souhrnné informace o topologii, které jste nasadili kontrolou části Ethereum stav uzlu. Obsahuje všechny názvy hostitelů uzlu, jejich počet sdílené a nejnovější bloku vidět. Počet sdílené pro každý uzel je mezi alespoň jednu méně než *celkový počet uzlů* a počet nakonfigurované maximální druhé strany. Všimněte si, že počet sdílené neomezuje počet uzlů, které lze nasadit v rámci sítě.
V některých případech uvidíte počet sdílené kolísá a být menší než (celkový počet uzlů - 1). Rozdíl ve počet není vždy znaménkem, že uzly jsou není v pořádku, protože větve do hlavní knihy může způsobit méně závažné změny v počtu partnera. Navíc si můžete prohlédnout nejnovější bloku prohlížet každý uzel v síti k určení větve nebo pomalou v systému.

![Stav uzlu](./media/ethereum-deployment-guide/node-status.png)

Stav uzlu se aktualizují každých 10 sekund. Znovu načíst stránku v prohlížeči nebo **opětovného načtení** tlačítko Aktualizovat zobrazení.

### <a name="oms-portal"></a>Portál OMS

Pomocí následujícího odkazu ve výstupu nasazení (OMSPORTALURL) nebo výběrem OMS prostředků ve vaší skupině prostředků nasazené můžete vyhledat portálu OMS.

![ADRESA URL OMS](./media/ethereum-deployment-guide/oms-url.png)

![OMS prostředků](./media/ethereum-deployment-guide/oms-resource.png)

Na portálu se nejprve zobrazí statistiku vysoké úrovně sítě jako přehled.

![Přehled OMS](./media/ethereum-deployment-guide/oms-overview.png)

Kliknutím na přehledu vás nasměrovat k portálu zobrazení statistik podle uzlu.

![Statistiky podle uzlu](./media/ethereum-deployment-guide/per-node-stats.png)

### <a name="accessing-nodes"></a>Přístup k uzly

Můžete vzdáleně připojit k virtuální počítače pro uzly transakce prostřednictvím SSH. váš klíč pro uživatelské jméno a heslo nebo SSH Zadaný správce. Vzhledem k tomu, že uzel transakce virtuální počítače nemají své vlastní veřejné IP adresy, musíte přejít pomocí nástroje pro vyrovnávání zatížení a zadejte číslo portu. Příkaz SSH ke spuštění pro přístup do prvního uzlu transakce je uvedena v parametru šablony nasazení výstup jako, **SSH_TO_FIRST_TX_NODE** (pro ukázkové nasazení: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). Abyste se dostali na další transakci uzly, zvýšit číslo portu o jedna (například u prvního uzlu transakce je na portu 4000).

Vzhledem k tomu, že virtuální počítače, na které uzly dolování spusťte nejsou externě dostupné, budete muset přejít pomocí jednoho z uzlů transakce. Až budete mít relace SSH do uzlu transakce, nainstalovat privátní klíč na uzlu transakce nebo použít heslo pro spuštění relace SSH do jakéhokoli z těchto dolování uzlů.

**Poznámka**

Názvy hostitelů můžete získat z webu správce nebo na portálu Azure. Na portálu Azure, názvy hostitelů uzlů, které jsou součástí prostředek (VMSS) sady škálování virtuálního počítače je uveden v části **instance**, který se liší od skutečné názvy hostitelů. Název hostitele na portálu Azure může například vypadat **mn. asdfmv reg1_0** , ale skutečný název hostitele by jako **mn. asdfmv reg**.

Příklad:

Název hostitele Azure portálu| Skutečný název hostitele
---|---
Mn. ethwvu reg1_0| Mn. ethwvu reg1000000
Mn. ethwvu reg1_1 |Mn. ethwvu reg1000001
Mn. ethwvu reg1_2 |Mn. ethwvu reg1000002

## <a name="adding-a-new-consortium-member"></a>Přidání nového člena consortium

### <a name="sharing-data"></a>Sdílení dat

Jako první člen (nebo členem připojené) konsorcium potřebujete poskytovat ostatní členové několik řadu informací, můžete připojit a jejich připojení. Zejména:

1. **Sdílené Consortium konfigurační Data**: existuje sada dat, která se používá k orchestraci Ethereum připojení mezi dvěma členy. Potřebné informace, včetně genesis bloku, consortium ID sítě a spouštěcí uzly se zapisují do souboru na uzly transakce vedoucí nebo jiné nasazené člen. Umístění tohoto souboru, je uvedena ve výstupní parametr nasazení šablony s názvem **CONSORTIUM DATA**.
2. **Peer koncový bod informace**: The sdílené informace registrátora koncový bod se získat informace o všech uzlů už připojený k síti Ethereum z vedoucích nebo jiného člena nasazení. Úložiště databáze a sadu informace týkající se každý uzel připojen v síti, informace, jako je název hostitele uzlu, privátní IP adresy atd. Toto je výstupní parametr nasazení šablony s názvem **PEER_INFO_ENDPOINT**.
3. **Informace o primární klíč peer**: primární klíč registrátora The sdílené informace o se použije k získání přístupu k vedoucí nebo jiných člen sdílené informace o primární klíč. Toto je výstupní parametr nasazení šablony s názvem **PEER_INFO_PRIMARY_KEY**.


4. **Brána virtuální sítě**: každý člen naváže připojení k síti celý blockchain prostřednictvím existujícího člena. Chcete-li připojit virtuální síť, musíte cesta prostředku k bráně virtuální sítě člena, ke kterému se připojujete. Toto je výstupní parametr nasazení šablony s názvem **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Sdílený klíč**: A předem vytvořit tajný mezi dva členy consortium sítě, které jsou navázání připojení. Toto je alfanumerický řetězec (mezi 1 až 128 znaků), který byl dohodnutých mimo kontext nasazení. (Například **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Přijetí nového člena

Tento krok je potřeba po spojující člen úspěšně nasadil svoji síť. Než člena můžete připojovat k síti a zobrazit provoz transakce, musí existujícího člena konečnou konfiguraci proveďte v jejich VPN Gateway tak, aby přijímal připojení. To znamená, že uzly Ethereum spojující člena se nespustí, dokud nebude navázáno připojení. Tato konfigurace, můžete to udělat pomocí prostředí PowerShell nebo xPlat rozhraní příkazového řádku. Skript prostředí PowerShell modul a xPlat rozhraní příkazového řádku jsou také uloženy na uzlu transakce spolu s consortium data. Umístění skriptu je výstupní parametry nasazení s názvem **PÁR brány PS modulu** a **PÁR brány AZURE CLISCRIPT**, v uvedeném pořadí.

**Instalace prostředí PowerShell nebo rozhraní příkazového řádku**

Další informace o tom, jak začít pracovat s rutin prostředí Azure PowerShell a Azure xPlat rozhraní příkazového řádku naleznete v dokumentaci k Azure.

Budete potřebovat nejnovější verzi rutin Azure, které jsou nainstalovány místně a relaci otevřené. Zajistěte, aby k protokolování do relace pomocí svých přihlašovacích údajů předplatného Azure.

**Powershellu: Připojení**

Stáhnout modul prostředí PowerShell a uložte ho místně. Umístění modulu prostředí PowerShell je zadán jako **PÁR brány PS modulu** nasazení šablony výstupní parametr.

Pokud ještě není povolené, použijte **Set-ExecutionPolicy** rutiny pro místní relaci povolit spuštění nepodepsaných modulu.

**Set-ExecutionPolicy Unrestricted CurrentUser**

Dále přihlášení k předplatnému Azure, ve které jste nasadili vedoucí nasazení pomocí

**Login-AzureRmAccount**

Dále naimportujte modul:

**Import-Module <filepath to downloaded file>**

Nakonec spusťte funkci s odpovídající vstup:

- **MyGatewayResourceId** : cesta prostředku brány. Toto je výstupní parametr nasazení šablony s názvem **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : cesta prostředku spojující člen brány. To zajišťuje spojující člen a je také parametr šablony nasazení výstup z pojmenované **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : název identifikovat toto připojení brány.
- **Sdílený klíč** : předem zavedené tajný klíč mezi dvěma členy consortium sítě, které jsou navázání připojení.

**CreateConnection** -MyGatewayResourceId <resource path of your Gateway> - OtherGatewayResourceId < cesta prostředku spojující člen brány > - ConnectionName Mojepripojeni - SharedKey "MySharedKeyAbc123"

**xPlat rozhraní příkazového řádku: připojení**

Stáhnout skript příkazového řádku Azure CLI a uloží jej místně. Umístění skriptu rozhraní příkazového řádku Azure je zadaný v parametru šablony nasazení s názvem **PÁR brány-AZURE-rozhraní příkazového řádku-skriptu**.

Spusťte skript s odpovídající vstup:

- **MyGatewayResourceId** : cesta prostředku brány. Toto je výstupní parametr nasazení šablony s názvem **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : cesta prostředku spojující člen brány. Toto jsou poskytovány spojující členem a parametr šablony nasazení jejich nasazení také s názvem **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : název identifikovat toto připojení brány.
- **Sdílený klíč** : předem zavedené tajný klíč mezi dvěma členy consortium sítě, které jsou navázání připojení.
- **Umístění** : oblast Azure, kde je nasazená prostředek brány.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

Architektura bude takto, po úspěšném nakonfigurování připojení mezi **vedoucí** a **člen** nasazení.

![Architektura vedoucí a členů](./media/ethereum-deployment-guide/leader-member.png)

## <a name="fund-new-member-account"></a>Nový členský účet fondu

### <a name="generated-genesis-block"></a>Vygenerovaný genesis bloku

Jako první člen účtu Ethereum je financované s jedním bilion ether Pokud nasazení vygeneruje blok genesis (vlastní Genesis bloku = ne). Jiní členové bude mít účet, který není předem financovány a nutné počkat se nashromáždí Ether během jejich dolování uzly začít Moje bloky. Chcete-li předejít nové členy počkat Ether, musíte explicitně účty Ethereum spojující členy fondu.

Uděláte to tak připojení členy musí poskytnout Ethereum účet, který se zobrazí na své správce webu. Potom můžete správce webu k přenosu Ether z vašeho účtu do svého účtu tak, že zadáte zadaný účet.

### <a name="custom-genesis-block"></a>Vlastní genesis bloku

Není-li s určeným účtem Ethereum vlastní genesis blok, můžete použít MetaMask nebo jiný nástroj pro přenos ether od zadané účtu předem vygenerovaná účet Ethereum viditelné na správce webu. Pokyny týkající se používání MetaMask najdete v tématu [vytváření účtu Ethereum](#create-ethereum-account).

Pokud je zadaný blok vlastní genesis bez účtu nebo nemáte přístup k žádné předběžně přidělených účty, musíte počkat, dokud uzly dolování začít Moje ke generování Ether ke svému účtu (mince základní). Jak rychle se generují fondů závisí na úrovni potíže, které zadáte v bloku vlastní genesis.

## <a name="create-ethereum-account"></a>Vytvoření účtu Ethereum

Chcete-li vytvořit další účet, můžete použít různá řešení. Jedním z těchto řešení je MetaMask, Chrome rozšíření, která poskytuje identitu trezoru a připojení k síti Ethereum, veřejné, testovací nebo vlastní. MetaMask výrobky zpracovává transakce k registraci účtu v síti.

Tato transakce, jako je žádné jiné transakci, přejde na jednom z uzlů transakce a nakonec vytěžen do bloku, jak je uvedeno dále.

![Uzel transakce](./media/ethereum-deployment-guide/transaction-node.png)

Chcete-li nainstalovat rozšíření v prohlížeči Chrome, přejděte do přizpůsobit a řídit Google Chrome (přetečení tlačítko) > více nástrojů > Rozšíření > získat více rozšíření a vyhledejte MetaMask.

![MetaMask rozšíření](./media/ethereum-deployment-guide/metamask-extension.png)

Po instalaci otevřete MetaMask a vytvořte nové úložiště. Ve výchozím úložišti se připojí k testovací síti Morden. Potřebujete změnit tuto pro připojení k síti nasazené privátní consortium, konkrétně pro vyrovnávání zatížení před uzly transakce. Z výstupu šablony načíst zveřejněné Ethereum RPC koncový bod na port 8545, pojmenovány jako `ETHEREUM-RPC-ENDPOINT`a zadejte ho ve vlastní RPC, jak je uvedeno níže.

![Nastavení MetaMask](./media/ethereum-deployment-guide/metamask-settings.png)

Při vytvoření trezoru, vytvoříte Peněženka, který obsahuje účet. Chcete-li vytvořit další účty, vyberte **přepínač účty** a potom **+** tlačítko.

![MetaMask vytvoření účtu](./media/ethereum-deployment-guide/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Spustit počáteční Ether přidělení

Přes stránku Správce můžete formulovali transakce přenést na jiný účet Ethereum Ether z předběžně přidělených účtu. Tento přenos Ether je transakci, která se odesílají do uzlu transakce a vytěžen do bloku, jak je uvedeno dále.

![Uzel transakce](./media/ethereum-deployment-guide/transaction-node-mined.png)

Přes ikonu schránky v Peněženka MetaMask zkopírujte adresu Ethereum účtu, ke které chcete k přenosu ether a přejděte zpět na stránku správce. Vložte zkopírovaný účet do vstupního pole přenos 1000 ether z předběžně přidělených Ethereum účtu do nově vytvořený účet. Klikněte na tlačítko **odeslání** a počkat na transakci být vytěžen do bloku.

![Stav uzlu](./media/ethereum-deployment-guide/node-status2.png)

Jakmile transakce se potvrdí do mined blok, se projeví zůstatek účtu v MetaMask pro váš účet přenos 1000 Ether.

![Přenos MetaMask](./media/ethereum-deployment-guide/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Přenos Ether mezi účty

V tomto okamžiku jste připraveni provést transakce v rámci vaší privátní consortium sítě. Nejjednodušší transakce je přenos Ether z jednoho účtu do jiné. Formulovali tuto transakci, když použijete MetaMask znovu přenosu peníze z první účet výše na druhého účtu.

Z **Peněženka 1** v MetaMask, klikněte na Odeslat. Zkopírujte adresu druhý Peněženka vytvořené do **adresa příjemce** vstupní pole a množství Ether k přenosu v **velikost** vstupní pole. Klikněte na tlačítko **odeslat** a přijměte transakce.

![MetaMask odeslání transakce](./media/ethereum-deployment-guide/metamask-send.png)

Znovu Pokud transakce je vytěžen a zapsána do bloku, zůstatky na účtu se projeví odpovídajícím způsobem. Poznámka: **Peněženka 1**na odečtením vyrovnávat víc než 15 Ether, protože jste měli věnovat dolování poplatek zpracovat transakce.

![Peněženka 1](./media/ethereum-deployment-guide/wallet1.png)

![Peněženka 2](./media/ethereum-deployment-guide/wallet2.png)

## <a name="next-steps"></a>Další postup

Nyní jste připraveni a zaměřit se na aplikace a inteligentní kontrakt vývoj proti vaší privátní consortium blockchain sítě.
