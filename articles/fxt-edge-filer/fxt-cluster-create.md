---
title: 'Kurz: Vytvoření clusteru Azure FXT Edge souborového cache'
description: Naučte se, jak vytvořit hybridní cluster mezipaměti úložiště pomocí Azure FXT Edge souborového a jak se přihlásit k ovládacímu panelu clusteru a nakonfigurovat nastavení clusteru.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: d019f5df4bba6d223076c8ce35151510afedf2e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220801"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Kurz: Vytvoření clusteru Azure FXT Edge souborového

Po instalaci a inicializaci hardwarových uzlů Azure FXT Edge souborového pro vaši mezipaměť použijte software clusteru FXT k vytvoření clusteru mezipaměti.

Tento kurz vás provede jednotlivými kroky konfigurace hardwarových uzlů jako clusteru.

V tomto kurzu se dozvíte:

> [!div class="checklist"]
>
> * Jaké informace jsou potřeba ještě před zahájením vytváření clusteru
> * Rozdíl mezi sítí pro správu clusteru, sítí s clustery a sítí orientovanými na klienta
> * Jak se připojit k uzlu clusteru
> * Postup vytvoření počátečního clusteru pomocí jednoho uzlu Azure FXT Edge souborového
> * Jak se přihlásit k ovládacímu panelu clusteru a nakonfigurovat nastavení clusteru

Tento postup trvá 15 až 45 minut v závislosti na tom, kolik zdrojů potřebujete k identifikaci IP adres a síťových prostředků.

## <a name="prerequisites"></a>Předpoklady

Před zahájením tohoto kurzu dokončete tyto požadavky:

* Instalace hardwarových systémů Azure FXT Edge souborového do datového centra

  K vytvoření clusteru potřebujete jenom jeden uzel, ale před tím, než budete moct nakonfigurovat cluster a připravit ho k použití, je potřeba [přidat aspoň dva další uzly](fxt-add-nodes.md) .

* Připojit příslušné napájecí a síťové kabely k systému  
* Zapněte aspoň jeden uzel Azure FXT Edge souborového a [nastavte jeho kořenové heslo](fxt-node-password.md) .

## <a name="gather-information-for-the-cluster"></a>Shromáždění informací pro cluster

K vytvoření clusteru Azure FXT Edge souborového potřebujete následující informace:

* Název clusteru

* Heslo správce pro nastavení clusteru

* IP adresy:

  * Jedna IP adresa pro správu clusteru, síťová maska a směrovač, které se mají použít pro síť pro správu
  * První a poslední IP adresy v souvislém rozsahu IP adres pro komunikaci mezi uzly a uzlem clusteru. Podrobnosti najdete v části [distribuce IP adres](#ip-address-distribution)níže.
  * (IP adresy pro klienta jsou nastaveny po vytvoření clusteru.)

* Informace o síťové infrastruktuře:

  * IP adresa serveru DNS pro cluster
  * Název domény DNS pro cluster
  * Název nebo IP adresa pro servery NTP clusteru (jeden nebo tři servery)
  * Zda chcete povolit agregaci propojení IEEE 802.1 AX-2008 na rozhraních clusteru
  * Pokud povolíte agregaci propojení bez ohledu na to, jestli se má používat dynamická agregace IEEE 802.3 AD (LACP)

Po vytvoření clusteru můžete tyto položky síťové infrastruktury nakonfigurovat, ale je lepší je v době vytváření.

### <a name="ip-address-distribution"></a>Distribuce IP adres

Cluster hybridních mezipamětí úložiště Azure FXT Edge souborového používá IP adresy ve třech kategoriích:

* IP adresa pro správu: jedna IP adresa pro správu clusteru

  Tato adresa slouží jako vstupní bod pro přístup k nástrojům pro konfiguraci clusteru (webový ovládací panel nebo rozhraní API pro RPC v jazyce XML). Tato adresa se automaticky přiřadí primárnímu uzlu v clusteru a automaticky se přesune, pokud se změní primární uzel.

  Další IP adresy lze použít pro přístup k ovládacím panelům, ale IP adresa pro správu je navržena tak, aby poskytovala přístup i v případě, že dojde k převzetí jednotlivých uzlů.

* Síť s clustery: rozsah IP adres pro komunikaci clusteru

  Síť s clustery se používá ke komunikaci mezi uzly clusteru a k načítání souborů z back-endu úložiště (Core filers).

  **Osvědčené postupy:** Přidělte jednu IP adresu každému fyzickému portu, který se používá pro komunikaci clusteru, na každém uzlu Azure FXT Edge souborového. Cluster automaticky přiřadí adresy v zadaném rozsahu jednotlivým uzlům.

* Síť směřující na klienta: rozsah IP adres, které klienti používají k vyžádání a zápisu souborů

  Adresy klientské sítě používají klienti pro přístup k základním datům souborového prostřednictvím clusteru. Klient systému souborů NFS může například připojit jednu z těchto adres.

  **Osvědčené postupy:** Přidělte jednu IP adresu na fyzický port, který se používá pro komunikaci klienta na každém uzlu FXT Series.

  Cluster distribuuje klientské IP adresy napříč svými uzly prvků co nejblíže k sobě.

  Pro zjednodušení mnoho správců konfiguruje jeden název DNS s konfigurací DNS (RRDNS) s kruhovým dotazováním, aby bylo snazší distribuovat požadavky klientů napříč rozsahem adres. Tato instalace také umožňuje všem klientům používat stejný příkaz Mount pro přístup ke clusteru. Další informace najdete v tématu [Konfigurace DNS](fxt-configure-network.md#configure-dns-for-load-balancing) .

Aby bylo možné vytvořit nový cluster, musí být zadána IP adresa pro správu a rozsah adres sítě s clustery. Adresy směřující na klienta jsou zadány po vytvoření clusteru.

## <a name="connect-to-the-first-node"></a>Připojit k prvnímu uzlu

Můžete se připojit k libovolnému z nainstalovaných uzlů FXT a použít software operačního systému k nastavení clusteru.

Pokud jste to ještě neudělali, zapněte aspoň jeden z uzlů FXT pro váš cluster a ujistěte se, že má připojení k síti a IP adresu. Chcete-li aktivovat uzel, je nutné nastavit nové kořenové heslo, a postupovat podle kroků v části [Nastavení hesel pro hardware](fxt-node-password.md) (Pokud jste to ještě neudělali).

Chcete-li zkontrolovat připojení k síti, zkontrolujte, zda jsou indikátory LED síťového propojení uzlu osvětleny (a v případě potřeby indikátory síťového přepínače, ke kterému je připojena). Indikátory LED jsou popsané v tématu [monitorování stavu hardwaru Azure FXT Edge souborového](fxt-monitor.md).

Když se uzel spustí, požádá o IP adresu. Pokud je připojený k serveru DHCP, přijme IP adresu poskytovanou protokolem DHCP. (Tato IP adresa je dočasná. Při vytváření clusteru se změní.)

Pokud není připojen k serveru DHCP nebo neobdrží odpověď, uzel použije Bonjour software k nastavení IP adresy přiřazené svým držitelem ve formě 169,254.. \* \* . Na jedné ze síťových karet uzlu byste ale měli nastavit dočasnou statickou IP adresu, než ji použijete k vytvoření clusteru. Pokyny jsou součástí tohoto staršího dokumentu. aktualizované informace získáte od služby a podpory společnosti Microsoft: [Příloha a: nastavení statické IP adresy v uzlu FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html)

### <a name="find-the-ip-address"></a>Najít IP adresu

Připojte se k uzlu Azure FXT Edge souborového, kde najdete jeho IP adresu. Můžete použít sériový kabel, přímé připojení k portům USB a VGA nebo se připojit prostřednictvím přepínače KVM. (Podrobnosti o připojení portu najdete v tématu [nastavení počátečních hesel](fxt-node-password.md).)

Po připojení se přihlaste pomocí uživatelského jména `root` a hesla, které jste nastavili při prvním spuštění uzlu.  

Po přihlášení je potřeba určit IP adresu uzlu.

Pomocí příkazu `ifconfig` Zobrazte adresy přiřazené tomuto systému.

Příkaz například `ifconfig | grep -B5 inet` vyhledá porty pomocí internetových adres a pro zobrazení identifikátoru portu poskytne pět řádků kontextu.

Zapište všechny IP adresy zobrazené v sestavě ifconfig. Adresy uvedené s názvy portů jako e0a nebo e0b jsou dobré možnosti. Nepoužívejte žádné IP adresy uvedené v názvech E7 *, protože tyto názvy se používají jenom pro porty služby iDRAC/IPMI.

## <a name="load-the-cluster-configuration-wizard"></a>Načíst Průvodce konfigurací clusteru

Vytvořte cluster pomocí nástroje Konfigurace clusteru založeného na prohlížeči.

Zadejte IP adresu uzlu ve webovém prohlížeči. Pokud se v prohlížeči zobrazí zpráva o nedůvěryhodné lokalitě, přejděte k lokalitě i přesto. (Jednotlivé uzly Azure FXT Edge souborového nemají certifikáty zabezpečení poskytnuté certifikační autoritou.)

![Přihlašovací stránka ovládacího panelu v okně prohlížeče](media/fxt-cluster-create/unconfigured-node-gui.png)

Pole **uživatelské jméno** a **heslo** ponechte prázdné. Kliknutím na **Přihlásit** načtěte stránku pro vytvoření clusteru.

![Úvodní obrazovka instalačního programu pro nekonfigurovaný uzel v ovládacím panelu GUI na základě prohlížeče. Zobrazuje možnosti aktualizace softwaru, konfiguraci clusteru ručně nebo konfiguraci clusteru z instalačního souboru.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Vytvoření clusteru

Nástroj pro konfiguraci clusteru vás provede sadou obrazovek k vytvoření clusteru Azure FXT Edge souborového. Než začnete, ujistěte se, že jsou [požadované informace](#gather-information-for-the-cluster) připravené.

### <a name="creation-options"></a>Možnosti vytváření

První obrazovka nabízí tři možnosti. Možnost Ruční konfigurace použijte, pokud nemáte zvláštní pokyny od pracovníků podpory.

Klikněte na tlačítko **Chci nakonfigurovat cluster ručně** , aby se načetla obrazovka možnosti konfigurace nového clusteru.

Ostatní možnosti jsou používány zřídka:

* "Aktualizace systémové image" vás vyzve k instalaci nového softwaru operačního systému před vytvořením clusteru. (Aktuálně nainstalovaná verze softwaru je uvedená v horní části obrazovky.) Musíte zadat soubor balíčku softwaru – buď adresu URL, uživatelské jméno/heslo, nebo nahráním souboru z počítače.

* Možnost soubor instalace clusteru se někdy používá v rámci zákaznických služeb a podpory Microsoftu.

## <a name="cluster-options"></a>Možnosti clusteru

Na další obrazovce se zobrazí výzva, abyste nakonfigurovali možnosti pro nový cluster.

Stránka je rozdělena do dvou hlavních částí, **základní konfigurace** a **Konfigurace sítě**. Oddíl konfigurace sítě má také pododdíly: jeden pro síť pro **správu** a jeden pro síť s **clustery** .

### <a name="basic-configuration"></a>Základní konfigurace

V horní části vyplňte základní informace pro nový cluster.

![Podrobnosti o části "základní konfigurace" na stránce GUI prohlížeče. Zobrazuje tři pole (název clusteru, heslo správce, potvrzení hesla).](media/fxt-cluster-create/basic-configuration.png)

* **Název clusteru** – zadejte jedinečný název clusteru.

  Název clusteru musí splňovat tato kritéria:
  
  * Délka 1 až 16 znaků
  * Může obsahovat písmena, číslice a znaky spojovníku (-) a podtržítka (_).
  * Nesmí obsahovat další interpunkční znaménka ani speciální znaky.
  
  Tento název můžete později změnit na   >  stránce konfigurace **obecného nastavení** clusteru. (Další informace o nastaveních clusteru najdete v tématu [Průvodce konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), který není součástí této sady dokumentace.)

  > [!NOTE]
  > Název clusteru se používá k identifikaci systémových informací nahraných pro podporu monitorování nebo řešení potíží, takže je vhodné zahrnout název vaší společnosti.

* **Heslo správce** – nastavte heslo pro výchozího uživatele s právy pro správu, `admin` .
  
  Pro každou osobu, která spravuje cluster, byste měli nastavit jednotlivé uživatelské účty, ale uživatele nemůžete odebrat `admin` . Přihlaste se jako v `admin` případě, že potřebujete vytvořit další uživatele.

  Heslo můžete změnit na `admin` stránce nastavení uživatelů pro **správu**  >   v ovládacím panelu clusteru. Podrobnosti najdete v dokumentaci pro **uživatele** v [Průvodci konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Konfigurace sítě

Část **síť** vás vyzve k zadání síťové infrastruktury, kterou bude cluster používat.

Existují dvě samostatné sítě ke konfiguraci:

* *Síť pro správu* poskytuje ke clusteru přístup správce ke konfiguraci a monitorování. Zadaná IP adresa se používá při připojení k ovládacímu panelu nebo pro přístup přes SSH.

  Většina clusterů používá jenom jednu IP adresu pro správu, ale pokud chcete přidat rozhraní, můžete to udělat až po vytvoření clusteru.

* *Síť s clustery* se používá pro komunikaci mezi uzly clusteru a mezi uzly clusteru a back-endové úložiště (základní filers).

Síť směřující na klienta je nakonfigurována později po vytvoření clusteru.

V této části najdete taky konfiguraci serverů DNS a NTP, které používají obě sítě.

### <a name="configure-the-management-network"></a>Konfigurace sítě pro správu

Nastavení v části **Správa** jsou pro síť, která poskytuje přístup správcům ke clusteru.

![Podrobnosti o části "Správa" s poli pro 5 možností a zaškrtávací políčko pro síť pro správu s 1 GB](media/fxt-cluster-create/management-network-filled.png)

* **IP adresa pro správu** – zadejte IP adresu, kterou budete používat pro přístup k ovládacímu panelu clusteru. Tato adresa bude uplatněna v primárním uzlu clusteru, ale automaticky se přesune na uzel v pořádku, pokud původní primární uzel nebude k dispozici.

  Většina clusterů používá jenom jednu IP adresu pro správu. Pokud potřebujete více než jedno, můžete je přidat po vytvoření clusteru pomocí   >  stránky nastavení **sítě pro správu** clusteru. Další informace najdete v [Průvodci konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* Síťová **Maska** – zadejte síťovou masku pro síť pro správu.

* **Směrovač** – zadejte adresu výchozí brány, kterou používá síť pro správu.

* **Značka VLAN (volitelné)** – Pokud váš cluster používá značky VLAN, zadejte značku pro síť pro správu.

  Další nastavení sítě VLAN jsou nakonfigurovaná na  >  stránce nastavení **sítě VLAN** pro clustery. Další informace najdete v tématu [práce s](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) virtuálními sítěmi a [sítí VLAN >](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) v Průvodci konfigurací clusteru.

* **MTU** – v případě potřeby upravte maximální přenosovou jednotku (MTU) pro síť pro správu vašeho clusteru.

* **Použít síť** pro správu s 1 GB – zaškrtněte toto políčko, pokud chcete přiřadit dva 10 GbE LOM síťové porty na uzlech FXT jenom k síti pro správu. (K dispozici jsou porty 25GbE/10GbE pro všechny ostatní přenosy.) Pokud toto políčko nezaškrtnete, síť pro správu použije nejvyšší dostupný port.

### <a name="configure-the-cluster-network"></a>Konfigurace sítě s clustery

Nastavení sítě clusteru se vztahuje na přenosy mezi uzly clusteru a mezi uzly clusteru a základní filers.

![Podrobnosti o části "cluster" s poli pro zadání šesti hodnot](media/fxt-cluster-create/cluster-network-filled.png)

* **První IP** adresa a **Poslední IP** adresa – zadejte IP adresy definující rozsah, který se má použít pro interní komunikaci s clustery. Zde používané IP adresy musí být souvislé a nepřiřazované protokolem DHCP.

  Po vytvoření clusteru můžete přidat další IP adresy. Použijte stránku nastavení sítě **clusterového**  >  **clusteru** ([dokumentace Průvodce konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  Hodnota v poli **počet IP adres v rozsahu** se vypočítá a automaticky se zobrazí.

* **Síťová maska bez správy (volitelné)** – zadejte síťovou masku pro síť s clustery.

  Systém automaticky navrhne hodnotu síťové masky, kterou jste zadali pro síť pro správu. v případě potřeby ho změňte.

* **Směrovač clusteru (volitelné)** – zadejte adresu výchozí brány, kterou používá síť s clustery.

  Systém automaticky navrhne stejnou adresu brány, kterou jste zadali pro síť pro správu.

* **Značka VLAN clusteru (volitelné)** – Pokud váš cluster používá značky VLAN, zadejte značku pro síť s clustery.

* **Jednotka MTU bez správy (volitelné)** – v případě potřeby upravte maximální přenosovou jednotku (MTU) pro vaši síť s clustery.

### <a name="configure-cluster-dns-and-ntp"></a>Konfigurace DNS clusteru a NTP

Pod oddílem **cluster** jsou pole pro zadání serverů DNS a NTP a pro povolení agregace propojení. Tato nastavení platí pro všechny sítě, které cluster používá.

![Podrobnosti o konfiguraci DNS/NTP, se třemi poli pro servery DNS, o polích pro servery DNS a hledání DNS, o třech polích pro servery NTP a rozevírací nabídce pro možnosti agregace odkazů](media/fxt-cluster-create/dns-ntp-filled.png)

* Servery **DNS** – zadejte IP adresu jednoho nebo více serverů DNS (Domain Name System).

  Služba DNS se doporučuje pro všechny clustery a vyžaduje se, pokud chcete používat protokol SMB, AD nebo Kerberos.
  
  Pro zajištění optimálního výkonu nakonfigurujte server DNS clusteru pro vyrovnávání zatížení kruhového dotazování, jak je popsáno v tématu [Konfigurace DNS pro cluster Azure FXT Edge souborového](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Doména DNS** – zadejte název síťové domény, který bude cluster používat.

* **Hledání DNS** – Volitelně můžete zadat další názvy domén, které by měl systém vyhledat, aby VYŘEŠIL dotazy DNS. Můžete přidat až šest názvů domén oddělených mezerami.

* Servery **NTP** – v zadaných polích zadejte jeden nebo tři servery protokolu NTP (Network Time Protocol). Můžete použít název hostitele nebo IP adresu.

* **Propojení agregace** – agregace propojení vám umožní přizpůsobit způsob, jakým porty sítě Ethernet v uzlech FXT clusteru zpracovávají různé typy provozu. Další informace najdete v tématu Přehled konfigurace clusteru v tématu věnovaném konfiguraci [agregace odkazů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) .

### <a name="click-the-create-button"></a>Klikněte na tlačítko vytvořit.

Po poskytnutí všech požadovaných nastavení ve formuláři klikněte na tlačítko **vytvořit cluster** .

![dolní část dokončeného formuláře pomocí kurzoru nad tlačítkem vytvořit v pravém dolním rohu](media/fxt-cluster-create/create-cluster.png)

Systém zobrazí zprávu při vytváření clusteru.

![stavová zpráva konfigurace clusteru v prohlížeči: "uzel FXT nyní vytváří cluster. To bude trvat několik minut. Po vytvoření clusteru navštivte tento odkaz a dokončete konfiguraci. " s hypertextovým odkazem na webu "Přejít na tento odkaz"](media/fxt-cluster-create/creating-message.png)

Po chvíli můžete kliknutím na odkaz ve zprávě přejít na ovládací panel clusteru. (Tento odkaz vás přesměruje na IP adresu, kterou jste zadali v části **IP adresa pro správu**.) Po kliknutí na tlačítko vytvořit bude odkaz aktivní po dobu 15 sekund až 1 minuty. Pokud se webové rozhraní nenačte, počkejte několik sekund a pak znovu klikněte na odkaz.

Vytvoření clusteru trvá několik minut, ale během procesu se můžete přihlásit k ovládacímu panelu. Stránka Řídicí panel na ovládacím panelu je normální, aby zobrazovala upozornění, dokud proces vytváření clusteru neskončí.

## <a name="open-the-settings-pages"></a>Otevřete stránku nastavení.

Po vytvoření clusteru budete muset přizpůsobit jeho konfiguraci pro vaši síť a pracovní postup.

K nastavení nového clusteru použijte webové rozhraní ovládacích panelů. Použijte odkaz z obrazovky stavu vytvoření clusteru nebo přejděte na IP adresu pro správu, kterou jste v clusteru nastavili.

Přihlaste se k webovému rozhraní pomocí uživatelského jména `admin` a hesla, které jste nastavili při vytváření clusteru.

![webový prohlížeč zobrazující pole přihlášení ovládacích panelů](media/fxt-cluster-create/admin-login.png)

Otevře se ovládací panel a zobrazí se stránka s **řídicím panelem** . Po dokončení vytváření clusteru by se měla zobrazovat jakákoli varovná zpráva.

Klikněte na kartu **Nastavení** a nakonfigurujte cluster.

Na kartě **Nastavení** se na levém bočním panelu zobrazí nabídka konfiguračních stránek. Stránky jsou uspořádány podle kategorií. Kliknutím na + nebo-Control v horní části názvu kategorie rozbalíte nebo skryjete jednotlivé stránky.

![Karta nastavení v Ovládacích panelech (v prohlížeči) s načtenou stránkou pro cluster > Obecné nastavení](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Kroky instalace clusteru

V tomto okamžiku procesu existuje cluster, ale má pouze jeden uzel, žádné IP adresy klientské sítě a žádné back-endové úložiště. Pro přechod z nově vytvořeného clusteru do systému cache, který je připraven k obsluze pracovního postupu, je nutné provést další kroky instalace.

### <a name="required-configuration"></a>Požadovaná konfigurace

Tyto kroky jsou potřeba pro většinu nebo všechny clustery.

* Přidání uzlů do clusteru

  Tři uzly jsou standardní, ale mnoho produkčních clusterů má více než 24 uzlů.

  Přečtěte si téma [Přidání uzlů clusteru](fxt-add-nodes.md) , kde se dozvíte, jak přidat do svého clusteru další jednotky souborového služby Azure FXT Edge a povolit vysokou dostupnost.

* Určení back-endu úložiště

  Přidejte *základní definice souborového* pro každý back-end systém úložiště, který bude cluster používat. Další informace najdete v tématu [přidání back-endu úložiště a konfigurace virtuálního oboru názvů](fxt-add-storage.md#about-back-end-storage) .

* Nastavení přístupu klienta a virtuálního oboru názvů

  Vytvořte aspoň jeden virtuální server (VServer) a přiřaďte mu rozsah IP adres pro klientské počítače, které chcete použít. Je také nutné nakonfigurovat obor názvů clusteru (někdy označovaný jako globální obor názvů nebo GNS), což je funkce Virtual FileSystem, která umožňuje mapovat back-endové exporty úložiště na virtuální cesty. Obor názvů clusteru poskytuje klientům jednotnou a přístupnou strukturu systému souborů i v případě, že přepnete back-end úložná média. Obor názvů také může poskytovat uživatelsky přívětivou hierarchii virtuálních úložišť pro kontejnery objektů blob Azure nebo jiné podporované úložiště cloudových objektů.

  Přečtěte si téma [Konfigurace oboru názvů](fxt-add-storage.md#configure-the-namespace) pro podrobnosti. Tento krok zahrnuje:
  * Vytváření vservers
  * Nastavení spojení mezi zobrazením klienta sítě a back-end úložištěm
  * Definování, které IP adresy klienta obsluhují jednotlivé VServer

  > [!Note]
  > Než začnete s nastavením GNS clusteru, doporučuje se významné plánování. Nápovědu najdete v částech [použití globálního oboru názvů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) a [Vytvoření a práce s VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) oddíly v Průvodci konfigurací clusteru.

* [Upravit nastavení sítě](fxt-configure-network.md)

  Existuje několik nastavení souvisejících se sítí, která by se měla ověřit nebo přizpůsobit pro nový cluster. Podrobnosti o těchto položkách najdete v tématu [Úprava nastavení sítě](fxt-configure-network.md) :

  * Ověřují se konfigurace DNS a NTP.
  * Konfigurace adresářových služeb (v případě potřeby)
  * Nastavení sítí VLAN
  * Konfigurace proxy serverů
  * Přidání IP adres do sítě s clustery
  * Ukládání šifrovacích certifikátů

* [Nastavení monitorování podpory](#enable-support)

  Je nutné, abyste přijali zásady ochrany osobních údajů pro konfigurační nástroj a měli byste nakonfigurovat nastavení nahrávání podpory ve stejnou dobu.

  Cluster může automaticky odeslat data pro odstraňování potíží s clusterem, včetně statistik a ladění souborů. Tato nahrávání umožňují službám a podpoře Microsoftu poskytovat nejlepší možnou službu. Můžete přizpůsobit, co se monitoruje, a volitelně povolit službu proaktivní podpora a řešení potíží se vzdáleným řešením.  

### <a name="optional-configuration"></a>Volitelná konfigurace

U všech clusterů se tyto kroky nevyžadují. Jsou nutné pro některé typy pracovních postupů nebo pro určité styly správy clusteru.

* Přizpůsobení nastavení uzlu

  Můžete nastavit názvy uzlů a nakonfigurovat porty IPMI uzlů na úrovni všech clusterů nebo jednotlivě. Pokud tato nastavení nakonfigurujete před přidáním uzlů do clusteru, můžou nové uzly při připojení automaticky vybrat nastavení. Tyto možnosti jsou popsány v dokumentu vytvoření starší verze clusteru [přizpůsobení nastavení uzlu](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Některá dokumentace k tomuto produktu ještě není k dispozici na webu dokumentace Microsoft Azure. Odkazy na [Průvodce konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) a starší verzi [Průvodce vytvořením clusteru](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) vás převedou na samostatný web hostovaný v GitHubu.

* Konfigurace protokolu SMB

  Pokud chcete povolit přístup SMB ke svému clusteru i k systému souborů NFS, musíte nakonfigurovat protokol SMB a zapnout ho. Protokol SMB (někdy označovaný jako CIFS) se obvykle používá k podpoře klientů Microsoft Windows.

  Plánování a konfigurace protokolu SMB vyžaduje více než kliknutí na několik tlačítek v Ovládacích panelech. V závislosti na požadavcích vašeho systému může SMB ovlivnit způsob, jakým definujete základní filers, kolik vservers vytvoříte, jak nakonfigurujete spojení a obor názvů, přístupová oprávnění a další nastavení.

  Další informace najdete v tématu Průvodce konfigurací clusteru [Konfigurace přístupu SMB](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) .

* Nainstalovat další licence

  Pokud chcete použít cloudové úložiště jiné než Azure Blob, musíte nainstalovat další licenci na funkci. Podrobnosti o nákupu licence FlashCloud<sup>TM</sup> vám poskytne zástupce Microsoftu. Podrobnosti jsou vysvětleny v tématu [přidání back-endu úložiště a konfigurace virtuálního oboru názvů](fxt-add-storage.md#about-back-end-storage).

### <a name="enable-support"></a>Povolit podporu

Cluster Azure FXT Edge souborového může automaticky nahrávat údaje o podpoře pro váš cluster. Tato nahrávání umožňují zaměstnancům poskytovat nejlepší možné služby zákazníkům.

Chcete-li nastavit odesílání podpory, postupujte podle těchto kroků.

1. Přejděte na   >  stránku nastavení **podpory** clusteru. Přijměte zásady ochrany osobních údajů.

   ![Snímek obrazovky s tlačítkem pro ovládací panely a automaticky otevírané okno s tlačítkem potvrdit pro přijetí zásad ochrany osobních údajů](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Kliknutím na trojúhelník nalevo od **informací o zákaznících** rozbalte oddíl.
1. Klikněte na tlačítko znovu **ověřit informace o nahrání** .
1. V **jedinečném názvu clusteru** nastavte název podpory clusteru – Ujistěte se, že jednoznačně identifikuje váš cluster pro podporu pracovníků.
1. Zaškrtněte políčka pro **monitorování statistiky**, **odesílání obecných informací** a **nahrávání informací o chybách**.
1. Klikněte na **Odeslat**.  

   ![Snímek obrazovky s oddílem informace o dokončených zákaznících stránky nastavení podpory](media/fxt-cluster-create/fxt-support-info.png)

1. Kliknutím na trojúhelník nalevo od **zabezpečené proaktivní podpory (SPS)** rozbalte oddíl.
1. Zaškrtněte políčko **Povolit odkaz na SPS**.
1. Klikněte na **Odeslat**.

   ![Snímek obrazovky s dokončenou částí zabezpečená proaktivní podpora na stránce nastavení podpory](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Další kroky

Po vytvoření základního clusteru a přijetí zásad ochrany osobních údajů přidejte zbývající uzly clusteru.

> [!div class="nextstepaction"]
> [Přidání uzlů clusteru](fxt-add-nodes.md)
