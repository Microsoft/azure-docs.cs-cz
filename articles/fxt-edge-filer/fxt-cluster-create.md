---
title: 'Kurz: Vytvoření clusteru mezipamětí Azure FXT Edge Filer'
description: Jak vytvořit cluster mezipaměti hybridního úložiště pomocí Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: bfe1d1aeeac55039acf0c7eb295001277be9cd2e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239207"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Kurz: Vytvoření clusteru Azure FXT Edge Filer

Po instalaci a inicializaci hardwarových uzlů Azure FXT Edge Filer pro vaši mezipaměť vytvořte cluster clusteru FXT. 

Tento kurz vás provede kroky konfigurace hardwarových uzlů jako clusteru. 

V tomto kurzu se dozvíte: 

> [!div class="checklist"]
> * Jaké informace jsou potřeba před zahájením vytváření clusteru
> * Rozdíl mezi sítí pro správu clusteru, sítí clusteru a sítí orientopanskou pro klienty
> * Jak se připojit k uzlu clusteru 
> * Jak vytvořit počáteční cluster pomocí jednoho uzlu Azure FXT Edge Filer
> * Jak se přihlásit k ovládacímu panelu clusteru a nakonfigurovat nastavení clusteru

Tento postup trvá 15 až 45 minut v závislosti na tom, kolik výzkumu je třeba provést k identifikaci IP adres a síťových prostředků.

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu dokončete tyto požadavky:

* Instalace hardwarových systémů Azure FXT Edge Filer v datovém centru 

  K vytvoření clusteru potřebujete pouze jeden uzel, ale před konfigurací clusteru a jeho přípravou k použití je třeba [přidat alespoň dva další uzly.](fxt-add-nodes.md) 

* Připojení vhodných napájecích a síťových kabelů k systému  
* Zapnutí alespoň jednoho uzlu Azure FXT Edge Filer a [nastavení jeho kořenového hesla](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Shromažďování informací pro cluster 

K vytvoření clusteru Azure FXT Edge Filer potřebujete následující informace:

* Název clusteru

* Heslo pro správu pro nastavení clusteru

* IP adresy:

  * Jedna adresa IP pro správu clusteru a maska sítě a směrovač pro síť pro správu
  * První a poslední adresa IP v souvislém rozsahu adres IP pro komunikaci clusteru (uzel s uzlem). Podrobnosti viz [distribuce IP adres](#ip-address-distribution)níže. 
  * (Ip adresy orientované na klienta jsou nastaveny po vytvoření clusteru.)

* Informace o infrastruktuře sítě:

  * Adresa IP serveru DNS pro cluster
  * Název domény DNS pro cluster
  * Název nebo adresa IP serverů NTP clusteru (jeden server nebo tři nebo více) 
  * Chcete-li povolit agregaci propojení IEEE 802.1AX-2008 na rozhraních clusteru
  * Pokud povolíte agregaci propojení, zda chcete použít dynamickou agregaci IEEE 802.3ad (LACP)

Tyto položky síťové infrastruktury můžete nakonfigurovat po vytvoření clusteru, ale je lepší to udělat v době vytvoření. 

### <a name="ip-address-distribution"></a>Distribuce IP adres

Cluster mezipaměti hybridního úložiště Azure FXT Edge Filer používá IP adresy ve třech kategoriích:

* Ip adresa pro správu: Jedna adresa IP pro správu clusteru

  Tato adresa slouží jako vstupní bod pro přístup k konfiguračním nástrojům clusteru (webový ovládací panel nebo rozhraní XML-RPC API). Tato adresa je automaticky přiřazena primárnímu uzlu v clusteru a automaticky se přesune, pokud se změní primární uzel.

  Pro přístup k ovládacímu panelu lze použít jiné adresy IP, ale adresa IP pro správu je navržena tak, aby poskytovala přístup i v případě, že jednotlivé uzly převzaly selhání.

* Síť clusteru: Rozsah adres IP pro komunikaci clusteru

  Síť clusteru se používá pro komunikaci mezi uzly clusteru a pro načtení souborů z back-endového úložiště (základní filers).

  **Osvědčené postupy:** Přidělte jednu IP adresu na fyzický port používaný pro komunikaci clusteru v každém uzlu Azure FXT Edge Filer. Cluster automaticky přiřadí adresy v zadaném rozsahu jednotlivým uzlům.

* Klientská síť: Rozsah adres IP, které klienti používají k vyžádání a zápisu souborů

  Klientské síťové adresy používají klienti pro přístup k základním datům fileru prostřednictvím clusteru. Například klient nfs může připojit jednu z těchto adres.

  **Osvědčené postupy:** Přidělte jednu IP adresu na fyzický port používaný pro komunikaci klienta na každém uzlu FXT Series.

  Cluster distribuuje adresy IP orientované na klienta mezi své základní uzly co nejrovnoměrněji.

  Pro jednoduchost mnoho správců nakonfiguruje jeden název DNS s konfigurací RRDNS (ROUND-robin DNS), aby usnadnili distribuci požadavků klientů v rozsahu adres. Toto nastavení také umožňuje všem klientům používat stejný příkaz připojení pro přístup ke clusteru. Další informace [načláneknete na článek Konfigurace služby DNS.](fxt-configure-network.md#configure-dns-for-load-balancing)

Chcete-li vytvořit nový cluster, musí být zadána adresa IP pro správu a rozsah síťových adres clusteru. Adresy určené pro klienty jsou určeny po vytvoření clusteru.

## <a name="connect-to-the-first-node"></a>Připojení k prvnímu uzlu

Můžete se připojit k libovolnému z nainstalovaných uzlů FXT a použít jeho operační software k nastavení clusteru.

Pokud jste tak ještě neučinili, zapnite alespoň jeden z uzlů FXT pro váš cluster a ujistěte se, že má síťové připojení a IP adresu. Pro aktivaci uzlu je nutné nastavit nové kořenové heslo, postupujte podle pokynů v části [Nastavení hardwarových hesel,](fxt-node-password.md) pokud jste tak ještě neučinili.

Chcete-li zkontrolovat síťové připojení, ujistěte se, že jsou osvětleny LED diody síťového spojení uzlu (a v případě potřeby indikátory na síťovém přepínači, ke kterému je připojen). Indikátorledy LED jsou popsány v [monitoru Azure FXT Edge Filer stavu hardwaru](fxt-monitor.md).

Když se uzel spustí, bude požadovat IP adresu. Pokud je připojen k serveru DHCP, přijímá adresu IP poskytovanou službou DHCP. (Tato adresa IP je dočasná. Při vytváření clusteru se změní.)

Pokud není připojen k serveru DHCP nebo neobdrží odpověď, uzel použije software Bonjour k nastavení vlastní ip adresy ve formuláři 169.254. \*. \*. Před vytvořením clusteru byste však měli nastavit dočasnou statickou adresu IP na jedné ze síťových karet uzlu. Pokyny jsou zahrnuty v tomto starším dokumentu; Obraťte se na službu a podporu společnosti Microsoft a požádejte o aktualizované informace: [Dodatek A: Nastavení statické adresy IP na uzlu FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Najít IP adresu

Připojte se k uzlu Azure FXT Edge Filer a najděte jeho IP adresu. Můžete použít sériový kabel, přímé připojení k portům USB a VGA nebo se připojit pomocí přepínače KVM. (Podrobnosti o připojení k portu viz [Nastavení počátečních hesel](fxt-node-password.md).)

Po připojení se přihlaste `root` pomocí uživatelského jména a hesla, které jste nastavili při prvním spuštění uzlu.  

Po přihlášení je třeba určit IP adresu uzlu.

Pomocí příkazu `ifconfig` zobrazíte adresy přiřazené tomuto systému.

Příkaz `ifconfig | grep -B5 inet` například vyhledá porty s internetovými adresami a poskytne pět řádků kontextu pro zobrazení identifikátoru portu.

Poznamenejte si libovolnou IP adresu uvedenou v sestavě ifconfig. Adresy uvedené s názvy portů, jako je e0a nebo e0b, jsou dobré možnosti. Nepoužívejte žádné IP adresy uvedené s názvy e7*, protože tyto názvy se používají pouze pro porty služby iDRAC/IPMI.  

## <a name="load-the-cluster-configuration-wizard"></a>Načtení průvodce konfigurací clusteru

K vytvoření clusteru použijte konfigurační nástroj clusteru založený na prohlížeči. 

Zadejte IP adresu uzlu ve webovém prohlížeči. Pokud prohlížeč zobrazí zprávu o nedůvěryhodném webu, přejděte k webu tak jako tak. (Jednotlivé uzly Azure FXT Edge Filer nemají certifikáty zabezpečení poskytované certifikační autoritou.)

![Přihlašovací stránka ovládacího panelu v okně prohlížeče](media/fxt-cluster-create/unconfigured-node-gui.png)

Pole **Uživatelské jméno** a **Heslo** ponechejte prázdná. Kliknutím na **Přihlásit** načtěte stránku pro vytvoření clusteru.

![Úvodní obrazovka nastavení nenakonfigurovaného uzlu v ovládacím panelu GRAFICKÉho uživatelského rozhraní založeného na prohlížeči. Zobrazuje možnosti aktualizace softwaru, ruční konfigurace clusteru nebo konfigurace clusteru z instalačního souboru.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Vytvoření clusteru

Konfigurační nástroj clusteru vás provede sadou obrazovek a vytvoří cluster Azure FXT Edge Filer. Před zahájením se ujistěte, že máte připravené [požadované informace.](#gather-information-for-the-cluster) 

### <a name="creation-options"></a>Možnosti vytvoření

První obrazovka nabízí tři možnosti. Pokud nemáte zvláštní pokyny od pracovníků podpory, použijte možnost ruční konfigurace.

Klepněte na **tlačítko Nakonfigurovat cluster ručně** pro načtení nové obrazovky možností konfigurace clusteru. 

Ostatní možnosti se používají jen zřídka:

* "Aktualizovat bitovou kopii systému" vyzve k instalaci nového softwaru operačního systému před vytvořením clusteru. (Aktuálně nainstalovaná verze softwaru je uvedena v horní části obrazovky.) Musíte zadat soubor softwarového balíčku - buď ADRESU URL a uživatelské jméno / heslo, nebo nahráním souboru z počítače. 

* Možnost souboru nastavení clusteru je někdy používána službou Microsoft Customer Service and Support. 

## <a name="cluster-options"></a>Možnosti clusteru

Na další obrazovce se zobrazí výzva ke konfiguraci možností pro nový cluster.

Stránka je rozdělena do dvou hlavních částí, **Základní konfigurace** a **Konfigurace sítě**. Část konfigurace sítě obsahuje také podsekce: jednu pro síť **pro správu** a jednu pro síť **clusteru.**

### <a name="basic-configuration"></a>Základní konfigurace

V horní části vyplňte základní informace pro nový cluster.

![Detail sekce "Základní konfigurace" na stránce GUI prohlížeče. Zobrazuje tři pole (název clusteru, heslo správce, potvrzení hesla)](media/fxt-cluster-create/basic-configuration.png) 

* **Název clusteru** - Zadejte jedinečný název clusteru.

  Název clusteru musí splňovat tato kritéria:
  
  * Délka 1 až 16 znaků
  * Může obsahovat písmena, číslice a pomlčka (-) a podtržítko (_) znaky 
  * Nesmí obsahovat jiné interpunkční znaménko nebo speciální znaky.
  
  Tento název můžete později změnit na konfigurační stránce**Konfigurace obecného nastavení** **clusteru.** >  (Další informace o nastavení clusteru naleznete v [Průvodci konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), který není součástí této sady dokumentace.)

  > [!NOTE] 
  > Název clusteru se používá k identifikaci systémových informací nahraných pro podporu monitorování nebo řešení potíží, takže je užitečné zahrnout název vaší společnosti.

* **Heslo správce** – Nastavte heslo pro `admin`výchozího správce, .
  
  Měli byste nastavit jednotlivé uživatelské účty pro každou osobu, která spravuje `admin`cluster, ale nelze odebrat uživatele . Přihlaste `admin` se, jako byste potřebovali vytvořit další uživatele.
 
  Heslo pro `admin` položku můžete změnit na stránce Nastavení**uživatelů** **správy** > v Ovládacích panelech clusteru. Podrobnosti naleznete v dokumentaci **k uživatelům** v [Průvodci konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Konfigurace sítě

V části **Síť** se zobrazí výzva k zadání síťové infrastruktury, kterou bude cluster používat. 

Konfigurace existují dvě samostatné sítě:

* *Síť pro správu* poskytuje přístup správce ke clusteru pro konfiguraci a monitorování. Zde zadaná IP adresa se používá při připojování k ovládacímu panelu nebo pro přístup SSH. 

  Většina clusterů používá pouze jednu adresu IP pro správu, ale pokud chcete přidat rozhraní, můžete tak učinit po vytvoření clusteru.

* *Síť clusteru* se používá pro komunikaci mezi uzly clusteru a mezi uzly clusteru a back-endovým úložištěm (core filers).

Síť orientovaná na klienta je nakonfigurována později po vytvoření clusteru.

Tato část také obsahuje konfiguraci serverů DNS a NTP, které jsou používány oběma sítěmi.

### <a name="configure-the-management-network"></a>Konfigurace sítě pro správu

Nastavení v části **Správa** jsou určeny pro síť, která poskytuje přístup správce ke clusteru.

![detail sekce "Správa", s poli pro 5 možností a zaškrtávací políčko pro 1Gb správu sítě](media/fxt-cluster-create/management-network-filled.png)

* **Ip pro správu** – Zadejte adresu IP, kterou použijete pro přístup k ovládacímu panelu clusteru. Tato adresa bude deklarace primárníuzel clusteru, ale automaticky přesune do uzlu v pořádku, pokud původní primární uzel přestane být k dispozici.

  Většina clusterů používá pouze jednu adresu IP pro správu. Pokud chcete více než jeden, můžete je přidat po vytvoření clusteru pomocí stránky Nastavení**sítě pro správu** **clusteru.** >  Další informace naleznete v [Průvodci konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Maska sítě** - Zadejte masku sítě pro síť pro správu.

* **Směrovač** – Zadejte výchozí adresu brány používanou sítí pro správu.

* **Značka VLAN (volitelně)** – Pokud váš cluster používá značky VLAN, zadejte značku pro síť pro správu.

  Další nastavení sítě VLAN jsou konfigurována na stránce nastavení sítě**VLAN** **clusteru.** >  Další informace najdete v [části Práce s sítěmi VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) a [> clusteru v síti VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) v průvodci konfigurací clusteru.

* **MTU** – V případě potřeby upravte maximální přenosovou jednotku (MTU) pro síť pro správu clusteru.

* **Použijte síť 1Gb mgmt** - Toto políčko zaškrtněte, pokud chcete přiřadit dva síťové porty 1GbE na vašich fxt uzlech pouze do sítě pro správu. (Pro všechny ostatní přenosy musíte mít k dispozici porty 25GbE/10GbE.) Pokud toto políčko nezaškrtnete, síť pro správu použije port nejvyšší rychlosti, který je k dispozici. 

### <a name="configure-the-cluster-network"></a>Konfigurace sítě clusteru 

Nastavení sítě clusteru platí pro přenosy mezi uzly clusteru a mezi uzly clusteru a základními filery.

![detail sekce "Cluster" s poli pro zadání šesti hodnot](media/fxt-cluster-create/cluster-network-filled.png)

* **První IP** a **Poslední IP adresa** - Zadejte IP adresy, které definují rozsah, který se má použít pro interní komunikaci clusteru. Zde použité adresy IP musí být souvislé a nesmí být přiřazeny službou DHCP.

  Po vytvoření clusteru můžete přidat další adresy IP. Použijte stránku Nastavení > **sítí** **clusteru**[(dokumentace konfiguračního průvodce clusterem).](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)

  Hodnota v **počtu IP v rozsahu** se vypočítá a zobrazí automaticky.

* **Síťová maska bez mgmt (volitelně)** - Zadejte masku sítě pro síť clusteru. 

  Systém automaticky navrhne hodnotu masky sítě, kterou jste zadali pro síť pro správu. v případě potřeby jej změnit.

* **Směrovač clusteru (volitelný)** – Zadejte výchozí adresu brány používanou sítí clusteru. 

  Systém automaticky navrhne stejnou adresu brány, kterou jste zadali pro síť pro správu.

* **Značka VLAN clusteru (volitelná)** – Pokud váš cluster používá značky VLAN, zadejte značku pro síť clusteru.

* **MTU bez mgmt (volitelné)** – v případě potřeby upravte maximální přenosovou jednotku (MTU) pro síť clusteru.

### <a name="configure-cluster-dns-and-ntp"></a>Konfigurace služby DNS clusteru a protokolu NTP 

Pod částí **Cluster** jsou pole pro určení serverů DNS a NTP a pro povolení agregace propojení. Tato nastavení platí pro všechny sítě, které cluster používá.

![Detail oddílu pro konfiguraci DNS/NTP se třemi poli pro servery DNS, poli pro doménu DNS a vyhledávání DNS, třemi poli pro servery NTP a rozevírací nabídkou pro možnosti agregace odkazů](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS servery -** Zadejte IP adresu jednoho nebo více serverů DNS (Domain Name System).

  Služba DNS se doporučuje pro všechny clustery a vyžaduje se, pokud chcete používat protokol YMB, AD nebo Kerberos. 
  
  Pro dosažení optimálního výkonu nakonfigurujte server DNS clusteru pro vyrovnávání zatížení kruhového dotazování, jak je popsáno v [části Konfigurace DNS pro cluster Azure FXT Edge Filer](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Doména DNS** – Zadejte název síťové domény, který bude cluster používat.

* **Vyhledávání DNS** – Volitelně zadejte další názvy domén, které by měl systém hledat, aby vyřešil dotazy DNS. Můžete přidat až šest doménových jmen oddělených mezerami.

* **Servery NTP –** V daných polích zadejte jeden nebo tři servery protokolu NTP (NETWORK TIME). Můžete použít názvy hostitelů nebo ADRESY IP.

* **Agregace odkazů** – agregace odkazů umožňuje přizpůsobit způsob, jakým ethernetové porty v uzlech FXT clusteru zpracovávají různé typy provozu. Další informace najdete v článku [Agregace odkazů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) v Průvodci konfigurací clusteru.

### <a name="click-the-create-button"></a>Klikněte na tlačítko Vytvořit.

Po zadání všech požadovaných nastavení ve formuláři klepněte na tlačítko **Vytvořit cluster.**

![spodní část vyplněného formuláře s kurzorem nad tlačítkem vytvořit vpravo dole](media/fxt-cluster-create/create-cluster.png)

Systém zobrazí zprávu při vytváření clusteru.

![Zpráva o stavu konfigurace clusteru v prohlížeči: "Uzel FXT nyní vytváří cluster. To bude trvat několik minut. Po vytvoření clusteru navštivte tento odkaz a dokončete konfiguraci." s hypertextovým odkazem na "navštivte tento odkaz"](media/fxt-cluster-create/creating-message.png)

Po chvíli můžete klepnutím na odkaz ve zprávě přejít do ovládacího panelu clusteru. (Tento odkaz vás přenese na ADRESU IP, kterou jste zadali v **protokolu Management IP**.) Po klepnutí na tlačítko Vytvořit trvá 15 sekund až jedna minuta, než se odkaz aktivuje. Pokud se webové rozhraní nenačte, počkejte několik sekund a potom znovu klikněte na odkaz. 

Vytvoření clusteru trvá minutu nebo více, ale během procesu se můžete přihlásit k Ovládacím panelům. Je normální, že stránka řídicího panelu ovládacího panelu zobrazuje upozornění, dokud proces vytváření clusteru neskončí. 

## <a name="open-the-settings-pages"></a>Otevření stránek Nastavení 

Po vytvoření clusteru je třeba přizpůsobit jeho konfiguraci pro vaši síť a pracovní postup. 

K nastavení nového clusteru použijte webové rozhraní Ovládacích panelů. Postupujte podle odkazu z obrazovky stavu vytvoření clusteru nebo přejděte na adresu IP pro správu, kterou jste nastavili v clusteru.

Přihlaste se k webovému `admin` rozhraní pomocí uživatelského jména a hesla, které nastavíte při vytváření clusteru.

![webový prohlížeč zobrazující přihlašovací pole ovládacího panelu](media/fxt-cluster-create/admin-login.png)

Otevře se ovládací panel a zobrazí se stránka **Řídicí panel.** Po dokončení vytváření clusteru by se měly všechny varovné zprávy vymazat ze zobrazení.

Chcete-li cluster nakonfigurovat, klepněte na kartu **Nastavení.**

Na kartě **Nastavení** se na levém postranním panelu zobrazí nabídka konfiguračních stránek. Stránky jsou uspořádány podle kategorií. Kliknutím na ovládací prvek + nebo - v horní části názvu kategorie rozbalte nebo skryjte jednotlivé stránky.

![Karta Nastavení ovládacího panelu (v prohlížeči) s načtenou stránkou Obecného nastavení clusteru >](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Kroky nastavení clusteru

V tomto okamžiku procesu existuje cluster, ale má pouze jeden uzel, žádné adresy IP orientované na klienta a žádné úložiště back-end. Další kroky nastavení jsou potřebné pro přejdeme z nově vytvořeného clusteru do systému mezipaměti, který je připraven ke zpracování pracovního postupu.

### <a name="required-configuration"></a>Požadovaná konfigurace

Tyto kroky jsou potřebné pro většinu nebo všechny clustery. 

* Přidání uzlů do clusteru 

  Tři uzly jsou standardní, ale mnoho produkčních clusterů má více - až 24 uzlů.

  Přečtěte si [článek Přidání uzlů clusteru,](fxt-add-nodes.md) kde se dozvíte, jak do clusteru přidat další jednotky Azure FXT Edge Filer a povolit vysokou dostupnost.

* Určení back-endového úložiště

  Přidejte základní definice *filerů* pro každý back-endový úložný systém, který bude cluster používat. Přečtěte si [článek Přidání back-endového úložiště a konfigurace virtuálního oboru názvů,](fxt-add-storage.md#about-back-end-storage) abyste se dozvěděli další informace.

* Nastavení přístupu klienta a virtuálního oboru názvů 

  Vytvořte alespoň jeden virtuální server (vserver) a přiřaďte mu rozsah IP adres pro klientské počítače. Je také nutné nakonfigurovat obor názvů clusteru (někdy nazývaný globální obor názvů nebo GNS), což je funkce virtuálního souborového systému, která umožňuje mapovat exporty back-endového úložiště na virtuální cesty. Obor názvů clusteru poskytuje klientům konzistentní a přístupnou strukturu souborového systému i v případě, že přepnete paměťová média back-end. Obor názvů také může poskytnout uživatelsky přívětivou hierarchii virtuálního úložiště pro kontejnery objektů Blob Azure nebo jiné podporované úložiště cloudových objektů.

  Podrobnosti [načláneknete na článek Konfigurace oboru názvů.](fxt-add-storage.md#configure-the-namespace) Tento krok zahrnuje:
  * Vytváření vserverů
  * Nastavení propojení mezi zobrazením sítě klienta a úložištěm back-end 
  * Definování adres IP klientů obsluhovaných jednotlivými servery vserver

  > [!Note] 
  > Před zahájením nastavování GNS clusteru se doporučuje významné plánování. Přečtěte si [nápovědu v](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) části Použití globálního oboru názvů [a Vytváření a práce s vservers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) v Průvodci konfigurací clusteru.

* [Úprava nastavení sítě](fxt-configure-network.md)

  Existuje několik nastavení souvisejících se sítí, která by měla být ověřena nebo přizpůsobena pro nový cluster. Podrobnosti o těchto položkách naleznete v části [Úprava nastavení sítě:](fxt-configure-network.md)

  * Ověření konfigurace DNS a NTP 
  * Konfigurace adresářových služeb, v případě potřeby 
  * Nastavení vyčárážců VLAN
  * Konfigurace proxy serverů
  * Přidání adres IP do sítě clusteru
  * Ukládání šifrovacích certifikátů

* [Nastavení monitorování podpory](#enable-support)

  Zásady ochrany osobních údajů pro konfigurační nástroj musíte přijmout a současně byste měli nakonfigurovat nastavení nahrávání podpory.

  Cluster může automaticky odesílat data o řešení potíží o vašem clusteru, včetně statistik a ladicích souborů. Tato nahrávání umožňují oddělení služeb zákazníkům a podpoře společnosti Microsoft poskytovat nejlepší možné služby. Můžete přizpůsobit, co je monitorováno, a volitelně povolit proaktivní podporu a vzdálenou službu řešení potíží.  

### <a name="optional-configuration"></a>Volitelná konfigurace

Tyto kroky nejsou vyžadovány pro všechny clustery. Jsou potřebné pro některé typy pracovních postupů nebo pro určité styly správy clusteru. 

* Přizpůsobení nastavení uzlu

  Můžete nastavit názvy uzlů a nakonfigurovat porty IPMI uzlů na úrovni celého clusteru nebo jednotlivě. Pokud tato nastavení nakonfigurujete před přidáním uzlů do clusteru, mohou nové uzly automaticky vyzvednout nastavení, když se připojí. Možnosti jsou popsány v dokumentu pro vytvoření starší verze clusteru [Přizpůsobení nastavení uzlu](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Některé dokumenty pro tento produkt ještě nejsou k dispozici na webu dokumentace Microsoft Azure. Odkazy na [Průvodce konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) a starší verzi [Průvodce vytvořením clusteru](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) vás přenese na samostatný web hostovaný githubem. 

* Konfigurace protokolu SMB

  Pokud chcete povolit přístup SMB ke clusteru i systému připojení k systému připojení k systému připojení k systému zabezpečení, je nutné nakonfigurovat protokol SMB a zapnout jej. SMB (někdy nazývaný CIFS) se obvykle používá k podpoře klientů systému Microsoft Windows.

  Plánování a konfigurace protokolu SMB zahrnuje více než klepnutí na několik tlačítek v Ovládacích panelech. V závislosti na požadavcích vašeho systému může SMB ovlivnit způsob definování základních filerů, počet vserverů, které vytvoříte, jak nakonfigurujete spojení a obor názvů, přístupová oprávnění a další nastavení.

  Další informace naleznete v části Průvodce konfigurací clusteru [Konfigurace přístupu SMB.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html)

* Instalace dalších licencí

  Pokud chcete používat cloudové úložiště jiné než Azure Blob, musíte nainstalovat další licenci na funkce. Podrobnosti o zakoupení licence<sup>FlashCloud TM</sup> vám poskytne zástupce společnosti Microsoft. Podrobnosti jsou vysvětleny v [části Přidat back-end úložiště a nakonfigurovat virtuální obor názvů](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Povolit podporu

Cluster Azure FXT Edge Filer může automaticky nahrávat data podpory o vašem clusteru. Tato nahrávání umožňují zaměstnancům poskytovat nejlepší možné služby zákazníkům.

Podle těchto kroků nastavte nahrávání podpory.

1. Přejděte na stránku**Nastavení podpory** **clusteru.** >  Přijměte zásady ochrany osobních údajů. 

   ![Snímek obrazovky s Ovládacími panely a automaticky otevíraným oknem s tlačítkem Potvrdit pro přijetí zásad ochrany osobních údajů](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Kliknutím na trojúhelník vlevo od informace o **zákazníkovi** rozbalte oddíl.
1. Klikněte na tlačítko **Znovu ověřit informace o nahrání.**
1. Nastavte název podpory clusteru v **jedinečném názvu clusteru** – ujistěte se, že jednoznačně identifikuje váš cluster pro podporu zaměstnanců.
1. Zaškrtněte **políčka Pro sledování statistiky**, **Nahrání obecných informací**a **Informace o selhání**.
1. Klepněte na **tlačítko Odeslat**.  

   ![Snímek obrazovky obsahující dokončenou část informace o zákaznících na stránce nastavení podpory](media/fxt-cluster-create/fxt-support-info.png)

1. Kliknutím na trojúhelník vlevo od **Secure Proactive Support (SPS)** rozbalte oddíl.
1. Zaškrtněte políčko **Povolit odkaz SPS**.
1. Klepněte na **tlačítko Odeslat**.

   ![Snímek obrazovky obsahující dokončený oddíl Zabezpečené proaktivní podpory na stránce nastavení podpory](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Další kroky

Po vytvoření základního clusteru a přijetí zásad ochrany osobních údajů přidejte zbývající uzly clusteru. 

> [!div class="nextstepaction"]
> [Přidání uzlů clusteru](fxt-add-nodes.md)
