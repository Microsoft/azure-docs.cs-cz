---
title: Instalace SAP HANA na SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Jak nainstalovat SAP HANA na SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca59305b22fcf1e81ef518612910731cb6edea5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617099"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Jak nainstalovat a nakonfigurovat SAP HANA (velké instance) v Azure

Před přečtením tohoto článku se seznamte s [běžnými termíny HANA Large Instances](hana-know-terms.md) a [slokami velkých instancí HANA](hana-available-skus.md).

Instalace SAP HANA je vaší odpovědností. Po navázání připojení mezi virtuálními sítěmi Azure a jednotkami velké instance HANA můžete začít instalovat nový server SAP HANA v Azure (velké instance). 

> [!Note]
> Podle zásad SAP musí instalaci SAP HANA provést osoba, která složila zkoušku Certified SAP Technology Associate, certifikační zkoušku SAP HANA Installation nebo která je systémovým integrátorem certifikovaným SAP (SI).

Když plánujete nainstalovat HANA 2.0, najdete [v tématu poznámka podpory SAP #2235581 - SAP HANA: Podporované operační systémy](https://launchpad.support.sap.com/#/notes/2235581/E) a ujistěte se, že operační systém je podporován s SAP HANA verze, kterou instalujete. Podporovaný operační systém pro HANA 2.0 je více omezující než podporovaný operační systém pro HANA 1.0. Musíte také zkontrolovat, zda je verze operačního systému, která vás zajímá, uvedena jako podporovaná pro konkrétní jednotku HLI v tomto publikovaném [seznamu](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Klikněte na jednotku pro získání celých detailů s podporovaným seznamem operačního systému této jednotky. 

Před zahájením instalace hana ověřte následující:
- [Jednotka (jednotky) HLI](#validate-the-hana-large-instance-units)
- [Konfigurace operačního systému](#operating-system)
- [Konfigurace sítě](#networking)
- [Konfigurace úložiště](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Ověření jednotky velké instance HANA

Po obdržení jednotky velké instance HANA od společnosti Microsoft ověřte následující nastavení a podle potřeby upravte.

**Prvním krokem** po přijetí velké instance HANA a navázání přístupu a připojení k instancí je zkontrolovat na portálu Azure, zda se instance zobrazují se správnými skum a os. Přečtěte si [řízení velkých instancí Azure HANA prostřednictvím portálu Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal) kde najdou kroky potřebné k provedení kontrol.

**Druhým krokem** po přijetí velké instance HANA a navázání přístupu a připojení k instancí je registrace operačního systému instance u vašeho poskytovatele operačního systému. Tento krok zahrnuje registraci operačního systému SUSE Linux v instanci SUSE SMT, která se nasadí ve virtuálním počítači v Azure. 

Jednotka velké instance HANA se může připojit k této instanci SMT. (Další informace naleznete v tématu [Jak nastavit SMT server pro SUSE Linux](hana-setup-smt.md)). Případně musí být váš operační zdroj Red Hat zaregistrován u Správce předplatného Red Hat, ke kterému se musíte připojit. Další informace naleznete v poznámkách v tématu [Co je SAP HANA v Azure (velké instance)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Tento krok je nezbytný pro opravu operačního e-s, který je odpovědností zákazníka. V části SUSE naleznete dokumentaci k instalaci a konfiguraci nástroje SMT na této stránce o [instalaci SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Třetím krokem** je kontrola nových oprav a oprav konkrétní verze operačního systému. Ověřte, zda je úroveň opravy velké instance HANA v nejnovějším stavu. Mohou nastat případy, kdy nejsou zahrnuty nejnovější opravy. Po převzetí jednotky velké instance HANA je povinné zkontrolovat, zda je třeba použít opravy.

**Čtvrtým krokem** je podívat se na příslušné poznámky SAP pro instalaci a konfiguraci SAP HANA na konkrétní verzi operačního systému. Kvůli změně doporučení nebo změn poznámky SAP nebo konfigurace, které jsou závislé na jednotlivých scénářích instalace, Microsoft nebude vždy moci nakonfigurovat hana velké instance jednotky dokonale. 

Proto je povinné, abyste si jako zákazník přečetli poznámky SAP týkající se SAP HANA pro přesné vydání Linuxu. Zkontrolujte také konfigurace verze/verze operačního systému a použijte nastavení konfigurace, pokud jste tak již neučinili.

Konkrétně zkontrolujte následující parametry a případně upravte:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Počínaje sles12 SP1 a RHEL 7.2, tyto parametry musí být nastaveny v konfiguračním souboru v adresáři /etc/sysctl.d. Například musí být vytvořen konfigurační soubor s názvem 91-NetApp-HANA.conf. U starších verzí SLES a RHEL musí být tyto parametry nastaveny v/etc/sysctl.conf.

U všech verzí RHEL počínaje rhel 6.3 mějte na paměti: 
- Parametr sunrpc.tcp_slot_table_entries = 128 musí být nastaven v/etc/modprobe.d/sunrpc-local.conf. Pokud soubor neexistuje, je třeba jej nejprve vytvořit přidáním položky: 
    - možnosti sunrpc tcp_max_slot_table_entries=128

**Pátým krokem** je kontrola systémového času jednotky velké instance HANA. Instance jsou nasazeny se systémovým časovým pásmem. Toto časové pásmo představuje umístění oblasti Azure, ve kterém je umístěno razítko velké instance HANA. Můžete změnit systémový čas nebo časové pásmo instancí, které vlastníte. 

Pokud si objednáte více instancí do vašeho tenanta, musíte upravit časové pásmo nově dodaných instancí. Společnost Microsoft nemá žádný přehled o systémovém časovém pásmu, které jste nastavili s instancemi po předání. Nově nasazené instance tedy nemusí být nastaveny ve stejném časovém pásmu jako ty, které jste změnili. Je vaší odpovědností jako zákazníka přizpůsobit časové pásmo instancí, které byly předány, pokud je to nutné. 

**Šestým krokem** je kontrola atd/ hostitelů. Jak se čepele předávají, mají různé IP adresy, které jsou přiřazeny pro různé účely. Zkontrolujte soubor etc/hosts. Když jsou jednotky přidány do existujícího klienta, neočekávejte, že budou mít etc/hosts nově nasazených systémů správně udržovány s IP adresami systémů, které byly dodány dříve. Je vaší odpovědností jako zákazníka zajistit, že nově nasazená instance může komunikovat a přeložit názvy jednotek, které jste nasadili dříve ve vašem tenantovi. 


## <a name="operating-system"></a>Operační systém

Odkládací prostor dodaného bitového kopie operačního systému je nastaven na 2 GB podle [poznámky podpory SAP #1999997 - FAQ: SAP HANA memory](https://launchpad.support.sap.com/#/notes/1999997/E). Pokud jako zákazník chcete jiné nastavení, musíte ho nastavit sami.

[SUSE Linux Enterprise Server 12 SP1 pro aplikace SAP](https://www.suse.com/products/sles-for-sap/download/) je distribuce Linuxu, který je nainstalovaný pro SAP HANA v Azure (velké instance). Tato konkrétní distribuce poskytuje funkce specifické pro SAP "out of the box" (včetně přednastavených parametrů pro efektivní spuštění SAP na SLES).

Viz [knihovna zdrojů/ dokumenty white paper](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) na webu SUSE a SAP na [SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) v síti SAP Community Network (SCN) pro několik užitečných prostředků souvisejících s nasazením SAP HANA na SLES (včetně nastavení vysoké dostupnosti, posílení zabezpečení, které je specifické pro operace SAP a další).

Následuje další a užitečné SAP na Odkazy související s SUSE:

- [SAP HANA na webu SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Doporučené postupy pro SAP: Replikace fronty – SAP NetWeaver na SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP - SLES virus ochrana pro SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (včetně SLES 12 pro aplikace SAP)

Následují poznámky podpory SAP, které jsou použitelné pro implementaci SAP HANA na SLES 12:

- [Poznámka podpory SAP #1944799 – pokyny SAP HANA pro instalaci operačního systému SLES](http://service.sap.com/sap/support/notes/1944799)
- [Poznámka podpory SAP #2205917 – SAP HANA DB doporučená nastavení operačního systému pro SLES 12 pro aplikace SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Poznámka podpory SAP #1984787 – SUSE Linux Enterprise Server 12: poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787)
- [Poznámka podpory SAP #171356 - SOFTWARE SAP na Linuxu: Obecné informace](https://launchpad.support.sap.com/#/notes/1984787)
- [Poznámka podpory SAP #1391070 – Řešení UUID pro Linux](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux pro SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) je další nabídka pro spuštění SAP HANA na HANA Large Instances. Verze RHEL 7.2 a 7.3 jsou k dispozici a podporovány. 

Níže jsou uvedeny další užitečné SAP na Red Hat související odkazy:
- [SAP HANA na red hat linuxovém webu](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Následují poznámky podpory SAP, které jsou použitelné pro implementaci SAP HANA na Red Hat:

- [Poznámka podpory SAP #2009879 - pokyny SAP HANA pro operační systém Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [Poznámka podpory SAP #2292690 - SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Poznámka podpory SAP #1391070 – Řešení UUID pro Linux](https://launchpad.support.sap.com/#/notes/1391070)
- [Poznámka podpory SAP #2228351 - Linux: SAP HANA Database SPS 11 revize 110 (nebo vyšší) na RHEL 6 nebo SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Poznámka podpory SAP #2397039 - nejčastější dotazy: SAP na RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [Poznámka podpory SAP #2002167 – Red Hat Enterprise Linux 7.x: Instalace a upgrade](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Čas synchronizace

Aplikace SAP, které jsou postaveny na architektuře SAP NetWeaver, jsou citlivé na časové rozdíly pro různé součásti, které tvoří systém SAP. SAP ABAP krátké výpisy s názvem\_\_chyby ZDATE VELKÉ ČASOVÉ\_DIFF jsou pravděpodobně známé. Je to proto, že tyto krátké výpisy se zobrazí, když se systémový čas různých serverů nebo virtuálních zařízení příliš vzdaluje.

Pro SAP HANA v Azure (velké instance) se synchronizace času, která se provádí v Azure, nevztahuje na výpočetní jednotky v razítkách velkých instancí. Tato synchronizace se nevztahuje na spouštění aplikací SAP v nativních virtuálních počítačích Azure, protože Azure zajišťuje, že je správně synchronizovaný čas systému. 

V důsledku toho je nutné nastavit samostatný časový server, který mohou používat aplikační servery SAP, které jsou spuštěny na virtuálních počítačích Azure a instance databáze SAP HANA, které jsou spuštěny na velkých instancích HANA. Infrastruktura úložiště v razítkách velkých instancí je časově synchronizována se servery NTP.


## <a name="networking"></a>Síťové služby
Předpokládáme, že jste postupovali podle doporučení při navrhování virtuálních sítí Azure a při připojování těchto virtuálních sítí k velkým instancím HANA, jak je popsáno v následujících dokumentech:

- [Přehled SAP HANA (velká instance) a architektura v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastruktura SAP HANA (velké instance) a připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Tam jsou některé detaily stojí za zmínku o vytváření sítí jednotlivých jednotek. Každá jednotka velké instance HANA je dodávána se dvěma nebo třemi adresami IP, které jsou přiřazeny ke dvěma nebo třem portům nic. V konfiguracích s horizontálním navýšením kapacity HANA a ve scénáři replikace systému HANA se používají tři adresy IP. Jedna z IP adres přiřazených k nic jednotky je mimo fond IP serveru, který je popsán v [přehledu SAP HANA (Velké instance) a architektuře](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)v Azure .

Další informace o podrobnostech sítě Ethernet pro vaši architekturu naleznete v [tématu scénáře podporované hli](hana-supported-scenario.md).

## <a name="storage"></a>Úložiště

Rozložení úložiště pro SAP HANA v Azure (velké instance) je `service management` nakonfigurované SAP HANA v Azure pomocí sap doporučené pokyny. Tyto pokyny jsou popsány v dokumentu white paper [požadavků na úložiště SAP HANA.](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 

Hrubé velikosti různých svazků s různými objekty sloka velké instance HANA je dokumentován v [SAP HANA (velké instance) přehled a architektura v Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Konvence pojmenování svazků úložiště jsou uvedeny v následující tabulce:

| Využití úložiště | Název připojení | Název svazku | 
| --- | --- | ---|
| Data HANA | /hana/data/SID/mnt0000\<m> | IP adres úložiště:/hana_data_SID_mnt00001_tenant_vol |
| Protokol HANA | /hana/log/SID/mnt0000\<m> | IP adres úložiště:/hana_log_SID_mnt00001_tenant_vol |
| Záloha protokolu HANA | /hana/log/backups | IP adresa úložiště:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA sdílel(a) | /hana/shared/SID | IP adres úložiště:/hana_shared_SID_mnt00001_tenant_vol/sdílené |
| usr/míza | /usr/sap/SID | IP adres úložiště:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* je ID systému instance HANA. 

*Tenant* je interní výčet operací při nasazování klienta.

HANA usr/sap sdílejí stejný svazek. Názvosloví přípojných bodů zahrnuje ID systému instancí HANA a číslo připojení. V nasazení škálovat na kapacitu existuje pouze jedno připojení, například mnt00001. V nasazení horizontálnínavýšení kapacity, na druhé straně vidíte tolik připojení, jak máte pracovní ho a hlavní uzly. 

Pro horizontální navýšení kapacity prostředí, data, protokol a protokol zálohovací svazky jsou sdíleny a připojeny ke každému uzlu v konfiguraci horizontálnínavýšení kapacity. Pro konfigurace, které jsou více instancí SAP, je vytvořena a připojena k jednotce velké instance HANA jiná sada svazků. Podrobnosti o rozložení úložiště pro váš scénář najdete v [tématu hli podporované scénáře](hana-supported-scenario.md).

Když se podíváte na jednotku velké instance HANA, uvědomíte si, že jednotky jsou dodávány s velkorysým svazkem disku pro HANA/data a že existuje svazek HANA/log/backup. Důvod, proč jsme hana/data tak velký je, že snímky úložiště nabízíme vám jako zákazník používají stejný svazek disku. Čím více snímků úložiště provedete, tím více místa spotřebovávají snímky v přiřazených svazcích úložiště. 

Svazek HANA/log/backup nemá být svazkem pro zálohování databáze. Je dimenzován pro použití jako záložní svazek pro zálohy protokolu transakcí HANA. Další informace najdete v [tématu SAP HANA (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Kromě úložiště, které je k dispozici, můžete zakoupit další úložnou kapacitu v přírůstcích po 1 TB. Toto další úložiště lze přidat jako nové svazky hana velké instance.

Během registrace s SAP HANA v Azure `service management`zákazník zadá ID uživatele (UID) a ID skupiny (GID) pro uživatele sidadm a skupinu sapsys (například: 1000 500). Během instalace systému SAP HANA je nutné použít stejné hodnoty. Vzhledem k tomu, že chcete nasadit více instancí HANA na jednotce, získáte více sad svazků (jednu sadu pro každou instanci). V důsledku toho v době nasazení je třeba definovat:

- SID různých instancí HANA (sidadm je odvozen z něj).
- Velikosti paměti různých instancí HANA. Velikost paměti na instanci definuje velikost svazků v každé jednotlivé sadě svazků.

Na základě doporučení zprostředkovatele úložiště jsou pro všechny připojené svazky nakonfigurovány následující možnosti připojení (s výjimkou spouštěcí logické jednotky):

- nfs rw, vers=4, tvrdý, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Tyto přípojné body jsou konfigurovány v /etc/fstab, jak je znázorněno na následující obrázce:

![fstab namontovaných svazků v jednotce Velké instance HANA](./media/hana-installation/image1_fstab.PNG)

Výstup příkazu df -h na jednotce Velké instance S72m HANA vypadá takto:

![fstab namontovaných svazků v jednotce Velké instance HANA](./media/hana-installation/image2_df_output.PNG)


Řadič úložiště a uzly v razítkách velké instance jsou synchronizovány se servery NTP. Při synchronizaci jednotky SAP HANA v Azure (velké instance) a virtuální počítače Azure proti serveru NTP, by měla být žádná významná časová posun mezi infrastrukturou a výpočetní jednotky v Razítka Azure nebo velké instance.

Chcete-li optimalizovat SAP HANA pro úložiště používané pod ním, nastavte následující parametry konfigurace SAP HANA:

- max_parallel_io_requests 128.
- async_read_submit dne
- async_write_submit_active dne
- async_write_submit_blocks všechny
 
Pro verze SAP HANA 1.0 až Do SPS12 lze tyto parametry nastavit během instalace databáze SAP HANA, jak je popsáno v [poznámce SAP #2267798 - Konfigurace databáze SAP HANA](https://launchpad.support.sap.com/#/notes/2267798).

Můžete také nakonfigurovat parametry po instalaci databáze SAP HANA pomocí rozhraní hdbparam. 

Úložiště používané v velkých instancích HANA má omezení velikosti souboru. [Omezení velikosti je 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) na soubor. Na rozdíl od omezení velikosti souborů v systémech souborů EXT3 hana není implicitně vědoma omezení úložiště vynuceného úložištěm velkých instancí HANA. V důsledku toho HANA automaticky nevytvoří nový datový soubor po dosažení limitu velikosti souboru 16 TB. Jako HANA pokusí zvětšit soubor nad 16 TB, HANA bude hlásit chyby a indexový server se zhroutí na konci.

> [!IMPORTANT]
> Chcete-li zabránit hana pokusu o zvýšení datové soubory nad 16 TB limit velikosti souboru HANA velké instance úložiště, je třeba nastavit následující parametry v sap hana global.ini konfigurační soubor
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Viz také [SAP](https://launchpad.support.sap.com/#/notes/2400005) poznámka #2400005
> - Uvědomte si poznámku SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


S SAP HANA 2.0 rozhraní hdbparam byl zastaralá. V důsledku toho musí být parametry nastaveny pomocí příkazů SQL. Další informace naleznete v [tématu SAP poznámka #2399079: Eliminace hdbparam v HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Další informace o rozložení úložiště pro vaši architekturu najdete v [části HlI podporované scénáře.](hana-supported-scenario.md)


**Další kroky**

- Viz [HANA Instalace na HLI](hana-example-installation.md)










































 







 




