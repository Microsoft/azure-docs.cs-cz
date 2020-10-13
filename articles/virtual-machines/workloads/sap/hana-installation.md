---
title: Instalace SAP HANA na SAP HANA v Azure (velké instance) | Microsoft Docs
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
ms.openlocfilehash: 60d889b232857ae69372df8ebabbd0edd01a2f17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91529826"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Jak nainstalovat a nakonfigurovat SAP HANA (velké instance) v Azure

Než si přečtete tento článek, Seznamte se s [běžnými podmínkami pro velké instance Hana](hana-know-terms.md) a [SKU velkých instancí Hana](hana-available-skus.md).

Instalace SAP HANA je vaše zodpovědnost. Po navázání připojení mezi virtuálními sítěmi Azure a jednotkami velkých instancí HANA můžete na serveru Azure (velké instance) začít instalovat nový SAP HANA. 

> [!Note]
> V případě zásad SAP musí být instalace SAP HANA prováděna osobou, která prošla certifikací s certifikací SAP pro technologie SAP, certifikátem pro instalaci SAP HANA instalace nebo nejedná se o integrátor systému certifikovaný systémem SAP (SI).

Pokud plánujete instalaci HANA 2,0, přečtěte si článek o [podpoře SAP #2235581-SAP HANA: podporované operační systémy](https://launchpad.support.sap.com/#/notes/2235581/E) se ujistěte, že je operační systém podporován s verzí SAP HANA, kterou instalujete. Podporovaný operační systém pro HANA 2,0 je více omezující než podporovaný operační systém pro HANA 1,0. Musíte také ověřit, jestli je verze operačního systému, na kterou vás zajímá, uvedená jako podporovaná pro konkrétní HLI jednotku v tomto publikovaném [seznamu](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Kliknutím na jednotku získáte úplné informace s podporovaným seznamem operačních systémů této jednotky. 

Než začnete s instalací HANA, ověřte následující:
- [Počet jednotek: HLI](#validate-the-hana-large-instance-units)
- [Konfigurace operačního systému](#operating-system)
- [Konfigurace sítě](#networking)
- [Konfigurace úložiště](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Ověřit jednotky velkých instancí HANA

Po přijetí jednotky velkých instancí HANA od Microsoftu ověřte následující nastavení a podle potřeby ho upravte.

**První krok** po obdržení velké instance Hana a vytvoření přístupu a připojení k instancím je třeba vrátit se změnami Azure Portal, jestli se instance zobrazují se správnými SKU a operačním systémem. Pomocí Azure Portal si můžete přečíst [rozsáhlé instance Azure Hana](./hana-li-portal.md) , které vás provedou kroky potřebnými k provedení kontrol.

**Druhý krok** po obdržení velké instance Hana a zavedení přístupu a připojení k instancím je registrace operačního systému instance s vaším poskytovatelem operačního systému. Tento krok zahrnuje registraci operačního systému SUSE Linux v instanci SUSE SMT, která je nasazená na virtuálním počítači v Azure. 

Jednotka velká instance HANA se může připojit k této instanci SMT. (Další informace najdete v tématu [jak nastavit server SMT pro systém SUSE Linux](hana-setup-smt.md)). Případně musí být váš operační systém Red Hat zaregistrován ve Správci předplatných Red Hat, ke kterému se potřebujete připojit. Další informace najdete v komentářích v tématu [co je SAP HANA v Azure (velké instance)?](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json). 

Tento krok je nezbytný pro opravy operačního systému, který je zodpovědný za zákazníka. Informace o SUSE najdete v dokumentaci k instalaci a konfiguraci SMT na této stránce o [instalaci SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Třetí krok** je vyhledat nové opravy a opravy konkrétní verze a verze operačního systému. Ověřte, zda je úroveň opravy vysoké instance HANA v nejnovějším stavu. Můžou nastat případy, kdy nejsou zahrnuté nejnovější opravy. Po převzetí jednotky velkých instancí HANA je nutné ověřit, jestli je potřeba použít opravy.

**Čtvrtým krokem** je Projděte si příslušné poznámky ke SAP pro instalaci a konfiguraci SAP HANA v konkrétní verzi nebo verzi operačního systému. V důsledku změny doporučení nebo změn v poznámkách SAP nebo konfiguracích, které jsou závislé na jednotlivých scénářích instalace, společnost Microsoft nebude moct vždy konfigurovat velké jednotky instance HANA. 

Proto je jako zákazník povinný, abyste si přečetli poznámky SAP týkající se SAP HANA pro vaši konkrétní verzi pro Linux. Taky Ověřte konfiguraci verze nebo verze operačního systému a použijte nastavení konfigurace, pokud jste to ještě neudělali.

Konkrétně proveďte kontrolu následujících parametrů a nakonec proveďte úpravu na:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Počínaje SLES12 SP1 a RHEL 7,2 musí být tyto parametry nastaveny v konfiguračním souboru v adresáři/etc/sysctl.d. Například je třeba vytvořit konfigurační soubor s názvem 91 – NetApp-HANA. conf. Pro starší verze SLES a RHEL musí být tyto parametry nastaveny v/v/sysctl. conf.

Pro všechny RHEL verze počínaje verzí RHEL 6,3 Pamatujte na toto: 
- Parametr sunrpc.tcp_slot_table_entries = 128 musí být nastaven na hodnotu/etc/modprobe. d/sunRPC-Local. conf. Pokud soubor neexistuje, je nutné jej nejprve vytvořit přidáním položky: 
    - možnosti sunRPC tcp_max_slot_table_entries = 128

**Pátý krok** je ověřit systémový čas jednotky velkých instancí Hana. Instance jsou nasazeny se systémovým časovým pásmem. Toto časové pásmo představuje umístění oblasti Azure, ve které se nachází razítko velké instance HANA. Můžete změnit systémový čas nebo časové pásmo instancí, které vlastníte. 

Pokud do svého tenanta přiřadíte více instancí, budete muset upravit časové pásmo nově dodaných instancí. Společnost Microsoft nemá žádné informace o časovém pásmu, které jste nastavili s instancemi po předají. Nově nasazené instance tedy nemusí být nastaveny ve stejném časovém pásmu jako ten, který jste změnili na. Jedná se o zodpovědnost jako zákazník, který přizpůsobuje časové pásmo instancí, které byly v případě potřeby předány. 

**Šestým krokem** je kontrolovat hostitele atd. Při obdržení oken mají různé IP adresy, které jsou přiřazeny k různým účelům. Prohlédněte si soubor etc/hosts. Když se jednotky přidají do existujícího tenanta, neočekává se, že se nehostitelé (a) nově nasazené systémy správně udržují s IP adresami systémů, které se předaly dříve. Vaše zodpovědnost jako zákazník zajistí, že nově nasazená instance může interagovat a řešit názvy jednotek, které jste dříve nasadili ve vašem tenantovi. 


## <a name="operating-system"></a>Operační systém

Místo odkládacího souboru doručené image operačního systému se nastaví na 2 GB podle [poznámky o podpoře SAP #1999997-Nejčastější dotazy: SAP HANA paměti](https://launchpad.support.sap.com/#/notes/1999997/E). Pokud chcete mít jiné nastavení, musíte ho jako zákazník nastavit sami.

[SUSE Linux Enterprise Server 12 SP1 pro aplikace SAP](https://www.suse.com/products/sles-for-sap/download/) je distribuce systému Linux, která je nainstalovaná pro SAP HANA v Azure (velké instance). Tato konkrétní distribuce poskytuje funkce specifické pro SAP, které jsou uvedené dále (včetně předem nastavených parametrů pro efektivní spouštění SAP na SLES).

V tématu [Knihovna prostředků/dokumenty White Paper](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) na webu SUSE a v článku [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) v síti SAP Community Network (SCN) najdete několik užitečných prostředků souvisejících s nasazením SAP HANA v SLES (včetně nastavení vysoké dostupnosti, posílení zabezpečení, které je specifické pro operace SAP a další).

Níže jsou uvedené další a užitečné SAP na odkazech souvisejících s SUSE:

- [SAP HANA na webu SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Osvědčené postupy pro SAP: replikace zařazení do fronty – SAP NetWeaver v systému SUSE Linux Enterprise 12](https://www.suse.com/media/guide/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12_color_en.pdf)
- [ClamSAP – SLES antivirová ochrana pro SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (včetně SLES 12 pro aplikace SAP)

Níže jsou uvedené poznámky k podpoře SAP, které se vztahují k implementaci SAP HANA v SLES 12:

- [Poznámka k podpoře SAP #1944799 – pokyny pro SAP HANA instalace operačního systému SLES](http://service.sap.com/sap/support/notes/1944799)
- [SAP – Poznámka k podpoře #2205917 – doporučená nastavení operačního systému SAP HANA DB pro aplikace SAP pro SLES 12](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Poznámka k podpoře SAP #1984787 – SUSE Linux Enterprise Server 12: poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787)
- [Poznámka k podpoře SAP #171356 – software SAP na platformě Linux: Obecné informace](https://launchpad.support.sap.com/#/notes/1984787)
- [Podpora SAP Poznámka #1391070 – řešení UUID pro Linux](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux pro SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) je další nabídka pro spuštění SAP HANA velkých instancí Hana. Jsou dostupné a podporované verze RHEL 7,2 a 7,3. 

Níže najdete další užitečné SAP v odkazech na Red Hat:
- [SAP HANA na webu Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Níže jsou uvedené poznámky k podpoře SAP, které se vztahují k implementaci SAP HANA v Red Hat:

- [Poznámka k podpoře SAP #2009879 – pokyny pro SAP HANA operačního systému Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [Poznámka k podpoře SAP #2292690 – SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Podpora SAP Poznámka #1391070 – řešení UUID pro Linux](https://launchpad.support.sap.com/#/notes/1391070)
- [Podpora SAP Poznámka #2228351-Linux: SAP HANA databáze SPS 11 – Revize 110 (nebo vyšší) na RHEL 6 nebo SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Poznámka k podpoře SAP #2397039 – Nejčastější dotazy: SAP v RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [Poznámka k podpoře SAP #2002167-Red Hat Enterprise Linux 7. x: instalace a upgrade](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Čas synchronizace

Aplikace SAP, které jsou postavené na architektuře SAP NetWeaver, jsou citlivé na časové rozdíly mezi různými součástmi, které tvoří systém SAP. Krátké výpisy SAP ABAP s názvem chyby ZDATE ve \_ velkém \_ časovém \_ rozdílu jsou pravděpodobně známé. Důvodem je, že tyto krátké výpisy se zobrazí, když je systémový čas různých serverů nebo virtuálních počítačů příliš daleko od sebe.

V případě SAP HANA v Azure (velké instance) se synchronizace času prováděná v Azure nevztahuje na výpočetní jednotky ve velkých objemech instancí. Tuto synchronizaci nejde použít ke spouštění aplikací SAP v nativních virtuálních počítačích Azure, protože Azure zajišťuje správnou synchronizaci času systému. 

V důsledku toho musíte nastavit samostatný časový server, který mohou používat aplikační servery SAP běžící na virtuálních počítačích Azure a instance SAP HANAch databází, které jsou spuštěny ve velkých instancích služby HANA. Infrastruktura úložiště v označeních velkých instancí je časově synchronizovaná na serverech NTP.


## <a name="networking"></a>Sítě
Předpokládáme, že jste postupovali podle doporučení v tématu navrhování virtuálních sítí Azure a v tématu propojení těchto virtuálních sítí s velkými instancemi HANA, jak je popsáno v následujících dokumentech:

- [Přehled a architektura SAP HANA (Velká instance) v Azure](./hana-overview-architecture.md)
- [SAP HANA (velké instance) infrastruktury a připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

V některých podrobnostech se dozvíte o sítích jednotlivých jednotek. Každá jednotka velkých instancí HANA má dvě nebo tři IP adresy, které jsou přiřazené dvěma nebo třem portům síťových adaptérů. V konfiguracích se škálováním na více systémů a ve scénáři replikace systému HANA se používají tři IP adresy. Jedna z IP adres přiřazená síťovému rozhraní jednotky je mimo fond IP adres, který je popsaný v tématu [SAP Hana (velké instance) přehled a architektura v Azure](./hana-overview-architecture.md).

Další informace o možnostech sítě Ethernet pro vaši architekturu najdete v tématu [podporované scénáře HLI](hana-supported-scenario.md).

## <a name="storage"></a>Storage

Rozložení úložiště pro SAP HANA v Azure (velké instance) se konfiguruje SAP HANA v Azure `service management` prostřednictvím doporučených pokynů SAP. Tyto pokyny jsou popsány v dokumentu White Paper [požadavky na úložiště SAP HANA](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) . 

Hrubou velikost různých svazků s různými jednotkami SKU velkých instancí HANA je popsána v článku [SAP Hana (velké instance) přehled a architektura v Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zásady vytváření názvů svazků úložiště jsou uvedené v následující tabulce:

| Využití úložiště | Název připojení | Název svazku | 
| --- | --- | ---|
| Data HANA | /hana/data/SID/mnt0000\<m> | IP adresa úložiště:/hana_data_SID_mnt00001_tenant_vol |
| Protokol HANA | /hana/log/SID/mnt0000\<m> | IP adresa úložiště:/hana_log_SID_mnt00001_tenant_vol |
| Zálohování protokolu HANA | /hana/log/backups | IP adresa úložiště:/hana_log_backups_SID_mnt00001_tenant_vol |
| Sdílená HANA | /hana/shared/SID | IP adresa úložiště:/hana_shared_SID_mnt00001_tenant_vol/Shared |
| usr/SAP | /usr/sap/SID | IP adresa úložiště:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*Identifikátor SID* je ID systému instance Hana. 

*Tenant* je interní výčet operací při nasazení tenanta.

HANA usr/SAP sdílí stejný svazek. Klasifikace mountpoints zahrnuje ID systému instancí HANA a číslo připojení. V nasazení se škálováním na více míst existuje jenom jedno připojení, například mnt00001. V nasazení se škálováním na více instancí vidíte na druhé straně tolik připojení, kolik máte pracovní a řídicí uzly. 

Pro prostředí se škálováním na více instancí se svazky, protokoly a zálohy protokolů sdílejí a připojují k jednotlivým uzlům v konfiguraci s možností horizontálního rozšíření kapacity. U konfigurací, které jsou několika instancemi SAP, se vytvoří jiná sada svazků a připojí se k jednotce velké instance služby HANA. Podrobnosti o rozložení úložiště pro váš scénář najdete v tématu [podporované scénáře HLI](hana-supported-scenario.md).

Když se podíváte na jednotku velkých instancí HANA, zjistíte, že jednotky přicházejí do velkorysáho disku pro HANA a data a že je k dispozici svazek HANA/log/Backup. Důvodem pro velké objemy HANA/dat je to, že snímky úložiště, které nabízíme jako zákazník, používají stejný diskový svazek. Víc snímků úložiště, které provedete, je více místa spotřebované snímky v přiřazených svazcích úložiště. 

Svazek HANA/log/Backup by neměl být svazkem pro zálohy databáze. Má velikost, která se má použít jako záložní svazek pro zálohy protokolu transakcí HANA. Další informace najdete v tématu [SAP Hana (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Kromě úložiště, které je k dispozici, můžete zakoupit další kapacitu úložiště v přírůstcích po 1 TB. Toto dodatečné úložiště je možné přidat jako nové svazky do velké instance HANA.

Během připojování pomocí SAP HANA v Azure `service management` určí zákazník ID uživatele (UID) a ID skupiny (GID) pro uživatele sidadm a skupinu sapsys (například: 1 000 500). Během instalace SAP HANA systému je nutné použít stejné hodnoty. Vzhledem k tomu, že chcete nasadit více instancí HANA na jednotku, získáte více sad svazků (jedna sada pro každou instanci). V důsledku toho je potřeba v době nasazení definovat:

- Identifikátor SID různých instancí HANA (sidadm je z něj odvozený).
- Velikosti paměti různých instancí HANA. Velikost paměti na instanci definuje velikost svazků v jednotlivých svazcích sady.

Na základě doporučení pro poskytovatele úložiště jsou pro všechny připojené svazky nakonfigurované následující možnosti připojení (nezahrnuje spouštěcí logickou jednotku):

- NFS – RW, západ = 4, tvrdý, Timeo = 600, rsize = 1048576, wsize = 1048576, intr, noatime, Lock 0 0

Tyto přípojné body jsou nakonfigurovány v/etc/fstab, jak je znázorněno na následujícím obrázku:

![fstab připojených svazků v jednotce velkých instancí HANA](./media/hana-installation/image1_fstab.PNG)

Výstup příkazu df-h na velké jednotce instance S72m HANA vypadá nějak takto:

![Snímek obrazovky zobrazuje výstup příkazu pro jednotku velkých instancí HANA.](./media/hana-installation/image2_df_output.PNG)


Řadič úložiště a uzly v dlouhých razítkech instance se synchronizují se servery NTP. Když synchronizujete SAP HANA jednotky Azure (velké instance) a virtuální počítače Azure se serverem NTP, neměli byste nijak významně přetěžovat mezi infrastrukturou a výpočetními jednotkami v Azure nebo v označeních velkých instancí.

Pokud chcete optimalizovat SAP HANA v níže používaném úložišti, nastavte následující parametry konfigurace SAP HANA:

- max_parallel_io_requests 128
- async_read_submit na
- async_write_submit_active na
- async_write_submit_blocks vše
 
Pro verze SAP HANA 1,0 až do SPS12 lze tyto parametry nastavit během instalace databáze SAP HANA, jak je popsáno v tématu [SAP note #2267798-Configuration of SAP HANA Database](https://launchpad.support.sap.com/#/notes/2267798).

Parametry můžete nakonfigurovat i po instalaci SAP HANA databáze pomocí architektury hdbparam. 

Úložiště používané ve velkých instancích HANA má omezení velikosti souboru. [Omezení velikosti je 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) na jeden soubor. Na rozdíl od omezení velikosti souborů v systémech souborů EXT3 úložiště HANA neví implicitně omezení úložiště vynutilé úložištěm velkých instancí HANA. V důsledku toho nebude při dosažení limitu velikosti souboru 16TB automaticky vytvářet nové datové soubory. Vzhledem k tomu, že HANA se pokusí zvětšit soubor nad rámec 16 TB, HANA odešle zprávy o chybách a indexový server selže na konci.

> [!IMPORTANT]
> Aby se zabránilo tomu, že se HANA snaží rozšířit datové soubory nad rámec velikosti souborů o velikosti 16 TB úložiště velkých instancí HANA, musíte nastavit následující parametry v konfiguračním souboru global.ini SAP HANA.
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - Viz také SAP – Poznámka [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Pamatujte na poznámku ke SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


S SAP HANA 2,0 se rozhraní hdbparam už nepoužívá. V důsledku toho musí být parametry nastaveny pomocí příkazů SQL. Další informace najdete v tématu [SAP note #2399079: eliminace hdbparam v Hana 2](https://launchpad.support.sap.com/#/notes/2399079).

Další informace o rozložení úložiště pro vaši architekturu najdete v tématu [podporované scénáře HLI](hana-supported-scenario.md) .


**Další kroky**

- Přečtěte si [instalaci Hana v HLI](hana-example-installation.md) .










































 







 
