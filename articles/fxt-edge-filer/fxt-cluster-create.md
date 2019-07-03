---
title: Microsoft Azure FXT hrany Filer vytváření clusteru
description: Vytvoření hybridního clusteru úložiště mezipaměti s Filer Azure FXT hrany
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 94ec2b088940f4f1f683a4f88ae312879d909bc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543550"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Kurz: Vytvoření clusteru Azure FXT hrany vyfiltrovat

Po instalaci a inicializaci hardwarových uzlů Azure FXT hrany Filer ke svojí mezipaměti, použijte k vytvoření clusteru mezipaměti FXT software clusteru. 

Tento kurz vás provede kroky konfigurace váš hardware do uzlů jako cluster. 

V tomto kurzu se dozvíte: 

> [!div class="checklist"]
> * Jaké informace je třeba před zahájení tvorby clusteru
> * Rozdíl mezi síť pro správu clusteru, síť s clustery a klienta sítě
> * Jak se připojit k uzlu clusteru 
> * Jak vytvořit cluster počáteční pomocí jednoho uzlu Azure FXT hrany vyfiltrovat
> * Jak se přihlásit k ovládacím panelům clusteru nakonfigurovat nastavení clusteru

Tento postup přijímá mezi 15 a 45 minut v závislosti na tom, kolik výzkumu, budete muset identifikovat IP adres a síťové prostředky.

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu zajistěte splnění těchto požadavků:

* Instalace systémů hardware Azure FXT hrany Filer ve vašem datovém centru 

  Potřebujete jenom jeden uzel k vytvoření clusteru, ale budete muset [přidejte aspoň dva další uzly](fxt-add-nodes.md) předtím, než můžete nakonfigurovat cluster a získat připravené k použití. 

* Připojte kabely odpovídající napájení a sítě v systému  
* Zapnutí nejméně jeden uzel Azure FXT hrany vyfiltrovat a [nastavit jeho kořenové heslo](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Shromažďování informací o clusteru 

Budete potřebovat následující informace k vytvoření clusteru Azure FXT hrany filtr:

* Název clusteru

* Heslo správce nastavit pro cluster

* IP addresses:

  * Jedna IP adresa pro správu clusteru a síťová maska a směrovač pro síť pro správu
  * První a poslední IP adresy v souvislý rozsah IP adres pro komunikaci v rámci clusteru (na uzel). V tématu [distribuci IP adres](#ip-address-distribution)níže, pro podrobnosti. 
  * (Určených pro klientské IP adresy jsou nastaveny po vytvoření clusteru.)

* Informace o síťové infrastruktury:

  * IP adresa serveru DNS pro cluster
  * Název domény DNS pro cluster
  * Název nebo IP adresu pro clusterové servery NTP (buď jeden nebo tři nebo více serverů) 
  * Určuje, zda chcete povolit IEEE 802.1ax-2008 propojit agregace rozhraní clusteru
  * Pokud povolíte odkaz agregace, jestli se mají použít IEEE 802.3ad (Protokol LACP) dynamické agregace

Po vytvoření clusteru, ale je vhodnější k tomu v okamžiku vytvoření můžete nakonfigurovat tyto položky infrastruktury sítě. 

### <a name="ip-address-distribution"></a>Distribuci IP adres

Cluster Azure FXT hrany Filer hybridní úložiště mezipaměti používá IP adresy do tří kategorií:

* Správa IP: Jedna IP adresa pro správu clusteru

  Tato adresa slouží jako vstupní bod pro přístup k konfigurační nástroje clusteru (panelu doprovázenou webovým ovládáním nebo rozhraní API XML-RPC). Tato adresa se automaticky přiřadí k primárnímu uzlu v clusteru a přesunu automaticky pokud primárního uzlu se změní.

  Další IP adresy lze použít pro přístup k panelu ovládacího prvku, ale IP adresa pro správu je navržené pro poskytování přístupu i v případě, že jednotlivé uzly převzetí služeb při selhání.

* Síť s clustery: Rozsah IP adres pro komunikaci s clustery

  Síť s clustery se používá pro komunikaci mezi uzly clusteru a k načtení souborů z back-endového úložiště (křížového core).

  **Osvědčený postup:** Přidělte jednu IP adresu na fyzický port používaný ke komunikace clusteru v každém uzlu Azure FXT hrany vyfiltrovat. Cluster automaticky přiřadí adresy v zadaném rozsahu k jednotlivým uzlům.

* Síť směřující na klienta: Rozsah IP adres, které používají klienti k požadavku a zápis souborů

  Síťové adresy klienta používají klienti pro přístup k datům filer core prostřednictvím clusteru. Například může klient NFS připojte některou z těchto adres.

  **Osvědčený postup:** Přidělte jednu IP adresu na fyzický port používaný pro komunikaci s klienty na každém uzlu FXT řady.

  Clusteru jako rovnoměrně distribuuje IP adresy klienta mezi jeho základní uzly.

  Pro jednoduchost mnoho správců konfigurovat jeden název DNS s konfigurací kruhové dotazování DNS (RRDNS) aby bylo snazší k distribuci požadavků klientů napříč rozsah adres. Toto nastavení také umožňuje používat stejný příkaz připojení pro přístup ke clusteru. Čtení [konfigurace služby DNS](fxt-configure-network.md#configure-dns-for-load-balancing) Další informace.

IP adresa pro správu a rozsah adres sítě clusteru je nutné zadat k vytvoření nového clusteru. Po vytvoření clusteru jsou zadány adresy klienta.

## <a name="connect-to-the-first-node"></a>Připojte se k první uzel

Můžete připojit k libovolné nainstalované FXT uzlů a použijte svůj software s operačním systémem k nastavení clusteru.

Pokud jste tak již neučinili, napájení na alespoň jednom uzlu FXT pro váš cluster a ujistěte se, že má síťové připojení a IP adresu. Musíte nastavit nové kořenové heslo, chcete-li aktivovat uzel, proto postupujte podle kroků v [nastavit hesla hardwaru](fxt-node-password.md) Pokud jste tak již neučinili.

Pokud chcete zkontrolovat připojení k síti, ujistěte se, že jsou osvětlená uzlu síťového propojení LED (a v případě potřeby přepnout indikátory v síti pro který je připojen). Indikátorů LED jsou popsány v [stav hardwaru monitorování Azure FXT hrany Filer](fxt-monitor.md).

Při spuštění uzlu, bude vyžadovat IP adresy. Pokud je připojený k serveru DHCP, přijímá IP adres přidělovaných DHCP SERVEREM. (Tato IP adresa je dočasný. To se změní při vytváření clusteru.)

Pokud není připojený k serveru DHCP nebo neobdrží odpověď, uzlu pomocí softwaru Bonjour nastavit samoobslužné přiřazenou IP adresu ve tvaru 169.254. \*. \*. Nicméně byste měli nastavit dočasnou statickou IP adresu na jednom uzlu síťových karet než je použijete k vytvoření clusteru. Pokyny jsou uvedené v této starší verze dokumentu. Obraťte se na Microsoft Service a podpora pro aktualizované informace: [Příloha A: Nastavení statické IP adresy do uzlu FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Zjistit IP adresu

Připojení k Azure FXT hrany Filer uzlu najít jeho IP adresu. Můžete použít sériový kabel přímé připojení k portům USB a VGA nebo připojit prostřednictvím KVM přepínače. (Port připojení. Podrobnosti najdete v tématu [nastavení počátečního hesla](fxt-node-password.md).)

Až se připojíte, přihlaste se pomocí uživatelského jména `root` a heslo, které jste nastavili při spuštění uzlu poprvé.  

Po přihlášení, je nutné určit IP adresu uzlu.

Použijte příkaz `ifconfig` zobrazíte adresy přiřazené k tomuto systému.

Například příkaz `ifconfig | grep -B5 inet` vyhledá portů pomocí adres v síti internet a obsahuje pět řádků kontextu zobrazíte identifikátoru portu.

Poznamenejte si všechny IP adresou uvedenou v sestavě ifconfig. Adresy, které uvedete s názvy portu, jako jsou e0a nebo e0b jsou vhodné možnosti. Nepoužívejte žádné IP adresy uvedené s e7 * názvy, protože tyto názvy se používají pouze pro služby iDRAC/IPMI porty.  

## <a name="load-the-cluster-configuration-wizard"></a>Načíst Průvodce konfigurací clusteru

Použijte nástroj pro konfiguraci založené na prohlížeči clusteru k vytvoření clusteru. 

Zadejte IP adresu pro uzel ve webovém prohlížeči. Pokud v prohlížeči zprávu o webu se nedůvěryhodné, k webu přesto pokračujte. (Uzly jednotlivé Azure FXT hrany Filer certifikáty zabezpečení poskytované certifikační Autority nemají.)

![Ovládací prvek panel přihlašovací stránky v okně prohlížeče](media/fxt-cluster-create/unconfigured-node-gui.png)

Nechte **uživatelské jméno** a **heslo** pole prázdné. Klikněte na tlačítko **přihlášení** načtení stránky vytváření clusteru.

![Počáteční nastavení obrazovky pro nenakonfigurovaný uzel v Ovládacích panelech grafické uživatelské rozhraní založené na prohlížeči. Zobrazí možnosti pro aktualizace softwaru, ručně konfigurovat cluster nebo konfigurace clusteru ze souboru nastavení.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Vytvoření clusteru

Nástroj pro konfiguraci clusteru provede sadu obrazovek pro vytvoření clusteru Azure FXT hrany vyfiltrovat. Ujistěte se, že máte [požadované informace](#gather-information-for-the-cluster) připravené před spuštěním. 

### <a name="creation-options"></a>Možnosti vytvoření

Na první obrazovce poskytuje tři možnosti. Pokud nemáte speciální pokyny od pracovníků podpory, použijte možnost Ruční konfigurace.

Klikněte na tlačítko **nakonfiguruji clusteru ručně** načíst nové obrazovky možností konfigurace clusteru. 

Další možnosti jsou zřídka se používá:

* "Aktualizovat bitovou kopii systému" zobrazí výzvu k instalaci nového operačního systému softwaru před vytvořením clusteru. (Aktuálně nainstalovaná verze softwaru je uvedený v horní části obrazovky.) Musíte zadat soubor balíčku softwaru – buď adresu URL a uživatelského jména a hesla, nebo tak že nahrajete soubor z vašeho počítače. 

* Někdy se používá možnost soubor nastavení clusteru podle Microsoft zákaznický servis a podporu. 

## <a name="cluster-options"></a>Možnosti clusteru

Na další obrazovce vás vyzve, abyste nakonfigurovat možnosti pro nový cluster.

Na stránce je rozdělené na dvě hlavní části, **základní konfigurace** a **konfigurace sítě**. Síťové konfigurace část má rovněž pododdíly: jeden pro **správu** síť a jednu pro **clusteru** sítě.

### <a name="basic-configuration"></a>Základní konfigurace

V horní části zadejte základní informace pro nový cluster.

![Podrobné informace o oddílu "Základní konfigurace" v prohlížeči grafického uživatelského rozhraní stránky. Zobrazuje tři pole (název clusteru, heslo správce, potvrďte heslo)](media/fxt-cluster-create/basic-configuration.png) 

* **Název clusteru** – zadejte jedinečný název pro cluster.

  Název clusteru musí splňovat tato kritéria:
  
  * 1 až 16 znaků
  * Může obsahovat písmena, číslice a pomlčky (-) a podtržítka (_) 
  * Nesmí obsahovat jiné interpunkční znaménka nebo speciální znaky
  
  Tento název se dá změnit později **clusteru** > **obecné nastavení** stránka konfigurace. (Další informace o nastavení clusteru, najdete v článku [Průvodce konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), který není součástí této sady dokumentace ke službě.)

  > [!NOTE] 
  > Název clusteru se používá k identifikaci systémové informace odeslány na podporu pro monitorování a řešení potíží, takže je vhodné zahrnout název vaší společnosti.

* **Heslo správce** – nastavení pro výchozí správce a hesla `admin`.
  
  Individuální uživatelské účty by měl nastavený pro každou osobu, která spravuje cluster, ale nemůžete odebrat uživatele `admin`. Přihlaste se jako `admin` Pokud potřebujete vytvořit další uživatele.
 
  Můžete změnit heslo pro `admin` v **správu** > **uživatelé** stránky nastavení v clusteru ovládacích panelech. Podrobnosti najdete v článku **uživatelé** dokumentaci [Průvodce konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Konfigurace sítě

**Sítě** části vás vyzve k zadání síťovou infrastrukturu, která bude cluster používat. 

Existují dvě samostatné sítě ke konfiguraci:

* *Síť pro správu* poskytuje přístup správce ke clusteru pro konfiguraci a monitorování. IP adresy uvedené tady se používá při připojení v Ovládacích panelech nebo pro přístup přes SSH. 

  Většina clusterů použít pouze jedné IP adresa pro správu, ale pokud chcete přidat rozhraní můžete tak učinit po vytvoření clusteru.

* *Síť s clustery* se používá pro komunikaci mezi uzly clusteru a mezi uzly clusteru a back endové úložné (křížového core).

Síť směřující na klienta je nakonfigurován později, po vytvoření clusteru.

Tato část také obsahuje konfiguraci pro servery DNS a NTP, které jsou používány obě sítě.

### <a name="configure-the-management-network"></a>Konfigurace sítě pro správu

V nastavení **správu** oddílu jsou pro síť, která poskytuje přístup správce ke clusteru.

![Podrobné informace o části "Správa" poli 5 možnosti a zaškrtávací políčko pro síť pro správu 1Gb](media/fxt-cluster-create/management-network-filled.png)

* **Správa IP** – zadejte IP adresu, kterou použijete pro přístup ke clusteru ovládacích panelech. Tato adresa se převzatá podle primárního uzlu clusteru, ale bude automaticky přesunut do zdravý uzel Pokud původní primární uzel stane nedostupným.

  Většina clusterů použít jenom jedna IP adresa pro správu. Pokud chcete víc, můžete je přidat po vytvoření clusteru pomocí **clusteru** > **pro správu sítě** stránku nastavení. Další informace najdete v [Průvodce konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Síťová maska** -zadat síťovou masku pro síť pro správu.

* **Směrovač** – zadejte adresu výchozí brány, používat síť pro správu.

* **(Volitelné) příznaku VLAN** – Pokud je váš cluster používá značky sítě VLAN, zadejte značky pro síť pro správu.

  Další nastavení sítě VLAN jsou nakonfigurované v **clusteru** > **VLAN** stránku nastavení. Čtení [práce se sítěmi VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) a [clusteru > síť VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) v Průvodci konfigurace clusteru Další informace.

* **MTU** – v případě potřeby upravte maximální přenosové jednotky (MTU) pro síť pro správu vašeho clusteru.

* **Použití 1Gb mgmt sítě** -toto políčko zaškrtněte, pokud chcete přiřadit dva 1GbE síťové porty na svých uzlech FXT pouze síť pro správu. (Musí mít k dispozici pro všechny ostatní přenosy 25GbE/10GbE porty.) Pokud není toto políčko zaškrtnete, používá síť pro správu nejvyšší rychlosti portů dostupné. 

### <a name="configure-the-cluster-network"></a>Nakonfigurujte síť s clustery 

Nastavení sítě pro cluster použít pro provoz mezi uzly clusteru a mezi uzly clusteru a křížového core.

![Podrobné informace o oddílu "Clusteru" s poli zadejte šest hodnoty](media/fxt-cluster-create/cluster-network-filled.png)

* **První IP adresa** a **poslední IP** – zadejte IP adresy, které definují rozsahu, který chcete použít pro komunikaci v rámci clusteru. IP adresy používané v tomto poli musí být souvislé a server DHCP není přiřazen.

  Po vytvoření clusteru můžete přidat další IP adresy. Použití **clusteru** > **sítě s clustery** nastavení stránky ([dokumentace k Průvodci konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  Hodnota v **číslo IP adresy v rozsahu** prodeje se vypočítá a zobrazí automaticky.

* **(Volitelné) síťovou masku non-mgmt** -zadat síťovou masku pro síť s clustery. 

  Systém automaticky navrhne hodnota masky sítě, kterou jste zadali pro síť pro správu; v případě potřeby změňte.

* **Směrovač clusteru (volitelné)** – zadejte adresu výchozí brány, používat síť s clustery. 

  Systém automaticky navrhne stejnou adresu brány, kterou jste zadali pro síť pro správu.

* **Cluster značky sítě VLAN (volitelné)** – Pokud je váš cluster používá značky sítě VLAN, zadejte značky pro síť s clustery.

* **(Volitelné) hodnota MTU non-mgmt** – v případě potřeby upravte maximální přenosové jednotky (MTU) pro vaši síť s clustery.

### <a name="configure-cluster-dns-and-ntp"></a>Konfigurace clusteru služby DNS a NTP. 

Níže **clusteru** existuje části jsou pole pro zadání serverů DNS a NTP a umožňující použití agregace odkaz. Tato nastavení platí pro všechny sítě, které cluster používá.

![Podrobné informace o oddílu pro konfiguraci DNS/NTP tři pole týkající se servery DNS, pole pro domény DNS a hledání DNS, tři pole týkající se servery NTP a rozevírací nabídka pro možnosti agregace odkazu](media/fxt-cluster-create/dns-ntp-filled.png)

* **Servery DNS** – zadejte jednu IP adresu nebo název domény další systémové servery (DNS).

  DNS se doporučuje pro všechny clustery a vyžaduje, pokud chcete použít protokol SMB, AD, nebo Kerberos. 
  
  Pro zajištění optimálního výkonu, konfiguraci clusteru serveru DNS pro vyrovnávání zatížení s kruhovým, jak je popsáno v [konfigurace služby DNS pro cluster Azure FXT hrany Filer](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Doména DNS** – zadejte název domény sítě bude cluster používat.

* **Vyhledávání DNS** – volitelně zadejte dalších názvů domén, které by měl systém vyhledávání pro přeložení dotazy DNS. Můžete přidat až šest názvy domén, oddělené mezerami.

* **Servery NTP** -zadat jeden nebo tři servery protokol (NTP) čas sítě do příslušných polí. Můžete použít názvy hostitelů nebo IP adresy.

* **Propojit agregace** -odkaz agregace umožňuje přizpůsobit, jak portů sítě ethernet na uzlech clusteru FXT zpracovávat různé typy provozu. Další informace najdete v článku [odkaz agregace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) v Průvodci konfigurace clusteru.

### <a name="click-the-create-button"></a>Klikněte na tlačítko Vytvořit

Po zadání všechna požadovaná nastavení ve formuláři, klikněte na tlačítko **vytvořit Cluster** tlačítko.

![Konec dokončené formuláře se ukazatel myši na tlačítko vytvořit v vpravo dole](media/fxt-cluster-create/create-cluster.png)

Systém zobrazí zprávu při vytváření clusteru.

![Konfigurace clusteru stavová zpráva v prohlížeči: "Uzel FXT je teď vytváření clusteru. To bude trvat několik minut. Když je cluster vytvořen, navštivte tento odkaz a dokončete tak konfiguraci." s hypertextovým odkazem na "navštivte tento odkaz"](media/fxt-cluster-create/creating-message.png)

Po chvíli můžete kliknout na odkaz ve zprávě přejděte do ovládacích panelů clusteru. (Tento odkaz vás nasměruje na IP adresu, která jste zadali v **Management IP**.) Trvá 15 sekund na jednu minutu pro propojení aktivuje po kliknutí na tlačítko vytvořit. Pokud webové rozhraní se nenačte, počkejte několik sekund a pak klikněte na odkaz. 

Vytvoření clusteru trvat minutu nebo informace, ale můžete se přihlásit do ovládacích panelů během procesu se děje. To je normální stránku řídicího panelu ovládacího panelu zobrazit upozornění, až do dokončení procesu vytváření clusteru. 

## <a name="open-the-settings-pages"></a>Otevřete nastavení stránky 

Po vytvoření clusteru budete muset upravit jeho konfiguraci pro síť a pracovního postupu. 

Pomocí ovládacích panelů webové rozhraní nastavit nový cluster. Pomocí následujícího odkazu z obrazovky stav vytváření clusteru, nebo přejděte do správy IP adresu, kterou jste nastavili v clusteru.

Přihlaste se k webovým rozhraním k uživatelskému jménu `admin` a heslo, které jste nastavili při vytváření clusteru.

![webový prohlížeč zobrazující ovládací prvek panel pole přihlášení](media/fxt-cluster-create/admin-login.png)

Ovládací Panel se otevře a zobrazí **řídicí panel** stránky. Při vytváření clusteru dokončení, měli vymazat všechny zprávy upozornění ze zobrazení.

Klikněte na tlačítko **nastavení** kartu Konfigurace clusteru.

Na **nastavení** kartě na levém bočním panelu se zobrazí nabídka stránky konfigurace. Na stránkách jsou uspořádány podle kategorií. Klikněte + nebo - ovládacího prvku v horní části název kategorie zvětšení nebo skrýt na jednotlivých stránkách.

![Karta nastavení ovládacích panelů (v prohlížeči) s clusterem > Obecné nastavení stránky načíst](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Postup instalace clusteru

V tomto okamžiku v procesu, váš cluster existuje, ale má pouze jeden uzel, žádné IP adresy klienta a žádné úložiště back-end. Další kroky jsou potřebné pro nově vytvořený cluster budou směřovat do mezipaměti systému, která jsou připravená pro zpracování pracovního postupu.

### <a name="required-configuration"></a>Požadované konfigurace

Tyto kroky jsou potřeba pro většinu nebo všechny clustery. 

* Přidání uzlů do clusteru 

  Tři uzly je standardní, ale mnoho produkční clustery mají víc – až do maximálního počtu uzlů 24.

  Čtení [přidat uzly clusteru](fxt-add-nodes.md) Další informace o přidání jiné jednotky Azure FXT hrany filtr pro váš cluster a chcete povolit vysokou dostupnost.

* Zadejte back endové úložiště

  Přidat *základní filer* definice pro každý back endové úložné systém, který bude cluster používat. Čtení [přidat back endové úložné a nakonfigurovat virtuální obor názvů](fxt-add-storage.md#about-back-end-storage) Další informace.

* Nastavení přístupu klienta a virtuální obor názvů 

  Vytvořte aspoň jeden virtuální server (vserver) a přiřaďte ho rozsah IP adres pro klientské počítače používat. Je také nutné nakonfigurovat obor názvů clusteru (říká se jim globální Namespace nebo GNS), funkce virtuálního systému souborů, která vám umožní mapovat exporty back endové úložiště pro virtuální cesty. Obor názvů clusteru poskytuje klientům strukturu systému souborů konzistentní a přístupné i v případě, že přejdete na back endové úložné médium. Obor názvů lze také zadat, hierarchii uživatelsky přívětivé virtuálního úložiště pro kontejnery objektů Blob v Azure nebo jiné podporované cloudové úložiště objektů.

  Čtení [konfigurace oboru názvů](fxt-add-storage.md#configure-the-namespace) podrobnosti. Tento krok zahrnuje:
  * Vytváření vservers
  * Nastavení spojení mezi zobrazení klientské sítě a úložiště back-end 
  * Definování klientských IP adres obsluhuje každý vserver

  > [!Note] 
  > Významné plánování se doporučuje před zahájením nastavení GNS clusteru. Čtení [pomocí globální Namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) a [vytváření a práci s VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) části v Průvodci konfigurace clusteru nápovědu.

* [Upravit nastavení sítě](fxt-configure-network.md)

  Existuje několik nastavení související se sítí, které by měly být ověřena nebo přizpůsobené pro nový cluster. Čtení [upravit nastavení sítě](fxt-configure-network.md) podrobné informace o těchto položek:

  * Ověřuje se konfigurace služby DNS a NTP. 
  * Konfigurace adresářové služby, v případě potřeby 
  * Nastavení sítě VLAN
  * Konfigurace proxy serverů
  * Přidání IP adres pro síť s clustery
  * Ukládání šifrovacích certifikátů

* [Nastavte monitorování podpory](#enable-support)

  Je nutné přijmout zásady ochrany osobních údajů pro nástroj pro konfiguraci a byste měli nakonfigurovat nastavení nahrávání podpory ve stejnou dobu.

  Řešení potíží data o clusteru, včetně statistiky a ladění soubory můžete nahrát automaticky clusteru. Tyto nahrávání umožní Microsoftu zákaznický servis a podporu poskytují nejlepší možné služby. Můžete přizpůsobit, co je monitorována a volitelně povolit proaktivní podpora a řešení problémů s vzdálené služby.  

### <a name="optional-configuration"></a>Volitelná konfigurace

Tyto kroky nejsou nutné pro všemi clustery. Jsou potřeba pro některé typy pracovních postupů, nebo pro určité styly správy clusteru. 

* Vlastní nastavení uzlu

  Můžete nastavit názvy wwnn a konfigurace uzlu IPMI portů v celém clusteru úroveň nebo samostatně. Pokud tato nastavení nakonfigurujete před přidáním uzlů do clusteru, nových uzlů můžete vybrat nastavení automaticky při připojí. Tyto možnosti jsou popsány v dokumentu vytváření starší verze clusteru [přizpůsobení nastavení uzlu](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Některé dokumentaci pro tento produkt není zatím k dispozici na webu Dokumentace k Microsoft Azure. Obsahuje odkazy na [Průvodce konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) a starší verze [Průvodce vytvořením clusteru](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) přejdete na web samostatné hostované na Githubu. 

* Konfigurace funkce SMB

  Pokud chcete povolit přístup k protokolu SMB pro váš cluster, jakož i systému souborů NFS, musí konfigurace funkce SMB a zapnout ho. Protokol SMB (říká se jim CIFS) se obvykle používá pro podporu klientů Microsoft Windows.

  Plánování a konfigurace protokolu SMB zahrnuje více než kliknete na pár tlačítek v Ovládacích panelech. V závislosti na požadavcích váš systém SMB může ovlivnit jak definovat křížového core, kolik vservers vytvoříte, jak nakonfigurovat vaše spojení a obor názvů, oprávnění k přístupu a další nastavení.

  Další informace najdete v příručce konfigurace clusteru [konfigurace přístupu k protokolu SMB](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) oddílu.

* Nainstalujte další licence

  Pokud chcete používat cloudové úložiště než objektů Blob v Azure, je nutné nainstalovat licenci další funkce. Podrobnosti o nákupu FlashCloud získáte od zástupce Microsoftu<sup>TM</sup> licence. Podrobnosti jsou vysvětlené v [přidat back endové úložné a nakonfigurovat virtuální obor názvů](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Povolení podpory

Cluster Azure FXT hrany Filer automaticky nahrát data podpory o clusteru. Tyto nahrávání umožní pracovníkům poskytovat nejlepší možné zákaznické služby.

Následujícím postupem nastavit podporu nahrávání.

1. Přejděte **clusteru** > **podporu** stránku nastavení. Přijměte zásady ochrany osobních údajů. 

   ![Snímek obrazovky zobrazující ovládací panely a automaticky otevírané okno s tlačítkem pro potvrzení přijímat zásady ochrany osobních údajů](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Klikněte na trojúhelník nalevo od **informace o zákaznících** rozbalte v části.
1. Klikněte na tlačítko **Revalidate odesílat informace** tlačítko.
1. Nastavení podpory názvu clusteru v **jedinečný název clusteru** – Ujistěte se, že ho jednoznačně identifikuje clusteru pracovníci podpory.
1. Zaškrtněte políčka pro **statistiky monitorování**, **obecné informace o nahrání**, a **havárií nahrát informace**.
1. Klikněte na **Submit** (Odeslat).  

   ![Snímek obrazovky, který obsahuje oddíl informací o dokončené Zákaznická podpora nastavení stránky](media/fxt-cluster-create/fxt-support-info.png)

1. Klikněte na trojúhelník nalevo od **zabezpečení proaktivní podpory (SPS)** rozbalte v části.
1. Zaškrtněte políčko u **odkaz povolit aktualizace Service PACKU**.
1. Klikněte na **Submit** (Odeslat).

   ![Snímek obrazovky obsahující dokončené zabezpečení proaktivní podporují oddíl na stránce nastavení podpory](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Další postup

Po vytvoření základní cluster a přijmout zásady ochrany osobních údajů, přidejte zbylé uzly clusteru. 

> [!div class="nextstepaction"]
> [Přidání uzlů clusteru](fxt-add-nodes.md)
