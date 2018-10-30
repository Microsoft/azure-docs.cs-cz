---
title: Instalace SAP HANA na systému SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Postup instalace SAP HANA na systému SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 160cc4fb3ccdabfd76e228c447ad179b3616d195
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231096"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Instalace a konfigurace SAP HANA (velké instance) v Azure

Před čtením tohoto článku, seznamte se s [velkých instancích HANA běžných termínů](hana-know-terms.md) a [SKU velké instance HANA](hana-available-skus.md).

Instalace SAP HANA je vaší povinností. Instalace nové SAP HANA na Azure (velké instance) serveru po navázání připojení mezi vaší virtuální sítě Azure a jednotek velká Instance HANA, můžete začít. 

> [!Note]
> Podle zásad SAP musí provést instalace SAP HANA osobou, který prošel zkoušku Certified přidružit technologie SAP, SAP HANA instalace certifikační zkoušky nebo která je certifikovaná systémový integrátor (SI).

Pokud plánujete nainstalovat HANA 2.0, naleznete v tématu [Poznámka SAP support #2235581 – SAP HANA: podporované operační systémy](https://launchpad.support.sap.com/#/notes/2235581/E) abyste měli jistotu, že operační systém podporuje verzi SAP HANA, který instalujete. Podporovaný operační systém pro HANA 2.0 je více omezující než podporovaný operační systém pro HANA 1.0. 

> [!IMPORTANT] 
> Pro typ II jednotky, aktuálně je podporována pouze verze operačního systému SLES 12 SP2. 

Před zahájením instalace HANA, musíte ověřit následující:
- [HLI jednotek](#validate-the-hana-large-instance-units)
- [Konfigurace operačního systému](#operating-system)
- [Konfigurace sítě](#networking)
- [Konfigurace úložiště](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Ověření jednotek velká Instance HANA

Po obdržení velká Instance HANA jednotek od Microsoftu, ověřte následující nastavení a upravit podle potřeby.

**První krok** po zobrazí velká Instance HANA a navázání přístupu a připojení k instancím, je k registraci instance operačního systému u svého poskytovatele operačního systému. Tento krok zahrnuje registrace vašeho operačního systému SUSE Linux do instance SUSE SMT, který je nasazen ve virtuálním počítači v Azure. 

Velká Instance HANA jednotku můžete připojit k této instanci SMT. (Další informace najdete v tématu [jak nastavit SMT server operačním systémem SUSE Linux](hana-setup-smt.md)). Alternativně musí být zaregistrované pomocí Red Hat předplatné správce, které potřebujete pro připojení k váš operační systém Red Hat. Další informace najdete v části poznámky v [co je SAP HANA v Azure (velké instance)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Tento krok je také je nutné pro použití dílčích oprav operačního systému, který zodpovídá za zákazníka. SUSE, najdete v dokumentaci k instalaci a konfiguraci SMT na této stránce o [SMT instalace](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Druhý krok** se kontroluje nové opravy a oprav konkrétní verze nebo verze operačního systému. Ověřte, že úroveň opravy velké instance HANA ve nejnovější stav. Můžou nastat případy, ve kterém nejsou zahrnuté nejnovějších oprav. Po déle než jednu jednotku velká Instance HANA, je nutné zkontrolovat, jestli je potřeba použít opravy.

**Třetí krok** je zkontrolovat důležité poznámky SAP pro instalaci a konfiguraci SAP HANA na konkrétní verzi/verze operačního systému. Z důvodu mění, doporučení nebo změny SAP poznámky nebo konfigurace, které jsou závislé na scénáře jednotlivé instalace, Microsoft nebude vždy moct nakonfigurovat jednotka velká Instance HANA něco nedobrého. 

Proto je povinné pro vás přečíst poznámky SAP souvisejících se zákazníky k SAP HANA pro svou přesnou verzi systému Linux. Také zkontrolujte konfigurace vydání nebo verze operačního systému a nastavení konfigurace, pokud jste tak již neučinili.

Konkrétně zkontrolujte následující parametry a nakonec upravují:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- NET.Core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Počínaje SLES12 SP1 a RHEL 7.2, musí být nastavena v konfiguračním souboru v adresáři /etc/sysctl.d tyto parametry. Například konfigurační soubor s názvem 91 – NetApp-HANA.conf musí vytvořit. Pro starší verze SLES a RHEL musí být tyto parametry in/etc/sysctl.conf sady.

Pro všechny verze RHEL počínaje SLES12 mějte na paměti následující: 
- Sunrpc.tcp_slot_table_entries = 128 parametr musí být nastaven in/etc/modprobe.d/sunrpc-local.conf. Pokud soubor neexistuje, musíte nejprve vytvořit tak, že přidáte následující položky: 
    - Možnosti sunrpc tcp_max_slot_table_entries = 128

**Čtvrtý krok** je kontrola systémového času testování částí velká Instance HANA. Instancí nasazených se časové pásmo systému. Toto časové pásmo představuje umístění oblast Azure, ve kterém se nachází razítko velká Instance HANA. Můžete změnit systémového času nebo časového pásma z instancí, které vlastníte. 

Pokud si objednat další instance do vašeho tenanta, budete muset upravit časové pásmo nově doručené instancí. Společnost Microsoft nemá žádné informace o časové pásmo systému, že nastavíte s instancemi po předání. Tedy nemusí ve stejném časovém pásmu jako ta, kterou jste změnili na nastavit nově nasazené instance. Má je na vás jako zákazník umožní reagovat na časové pásmo instancí, který byl předán, v případě potřeby. 

**Pátý krok** je kontrola etc/hosts. Jak získat předán okna, mají různé IP adresy, které jsou přiřazené pro různé účely. Zkontrolujte soubor etc/hosts. Když jednotky jsou přidány do existujícího tenanta, Neočekáváme, že máte etc/hostitele nově nasazené systémy udržuje správně s IP adresami systémů, které byly dodány dříve. Je vaší povinností jako zákazník zajistí, že nově nasazené instance můžete pracovat a překlad názvů jednotky, které jste nasadili dříve ve vašem tenantovi. 

## <a name="operating-system"></a>Operační systém

> [!IMPORTANT] 
> Pro typ II jednotky aktuálně podporuje pouze verzi operačního systému SLES 12 SP2. 

Odkládacího souboru rámci smluv bitové kopie operačního systému nastavená na 2 GB podle [Poznámka podpory SAP #1999997 – nejčastější dotazy: paměti SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Naším zákazníkem pokud chcete jiné nastavení, ho musíte nastavit sami.

[SUSE Linux Enterprise Server 12 SP1 pro aplikace SAP](https://www.suse.com/products/sles-for-sap/hana) je distribuce Linuxu, která je nainstalovaná pro SAP HANA v Azure (velké instance). Tento konkrétní poskytuje funkce specifické pro SAP "mimo pole" (včetně přednastaveným parametry pro spuštění SAP na SLES efektivně).

Naleznete v tématu [prostředků knihovny/specifikacích](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) na webu SUSE a [SAP v SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na SAP komunity sítě (oznámení změny stavu) pro několik užitečné zdroje informací související s nasazováním SAP HANA na SLES (včetně nastavení Vysoká dostupnost, posílení zabezpečení, která je specifická pro operace SAP a další).

Tady je další a užitečné SAP na související s operačním systémem SUSE odkazy:

- [SAP HANA na webu operačním systémem SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Osvědčené postupy pro SAP: replikaci zařadit do fronty, SAP NetWeaver na SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – ochrana proti virům SLES pro SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (včetně SLES 12 pro aplikace SAP)

Následují poznámky SAP podpory, které se dají použít k implementaci SAP HANA na SLES 12:

- [Poznámka: podpora #1944799 – SAP HANA pokyny pro instalaci operačního systému SLES pro SAP](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [Doporučené nastavení operačního systému pro SLES 12 pro aplikace SAP Poznámka SAP support #2205917 – databáze SAP HANA](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Poznámka SAP support #1984787 – operačním systémem SUSE Linux Enterprise Server 12: poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787)
- [Poznámka SAP support #171356 – softwaru SAP v Linuxu: Obecné informace](https://launchpad.support.sap.com/#/notes/1984787)
- [Poznámka: podpora 1391070 # – Linux UUID řešení SAP](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) je jinou nabídku pro spuštění SAP HANA ve velkých instancích HANA. Verze RHEL 6.7 a 7.2 jsou k dispozici. Všimněte si, že na rozdíl od nativních virtuálních počítačích Azure kde se podporují jenom RHEL 7.2 a novější verze, velkých instancích HANA podporují RHEL 6.7 také. Doporučujeme však používat o verzi 7.x RHEL.

Tady jsou další užitečné SAP na Red Hat souvisejících odkazů:
- [SAP HANA na webu Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

SAP podporuje poznámky, které se dají použít k implementaci SAP HANA v systému Red Hat jsou následující:

- [Poznámka: podpora #2009879 – pokyny pro SAP HANA pro operační systém Red Hat Enterprise Linux (RHEL) SAP](https://launchpad.support.sap.com/#/notes/2009879/E)
- [Poznámka SAP support #2292690 – databáze SAP HANA: OS doporučené nastavení pro RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Poznámka: podpora SAP #2247020 – databáze SAP HANA: Doporučené nastavení operačního systému pro RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020)
- [Poznámka: podpora 1391070 # – Linux UUID řešení SAP](https://launchpad.support.sap.com/#/notes/1391070)
- [Poznámka SAP support 2228351 # – Linux: revize SAP HANA Database aktualizace Service PACKU 11 110 (nebo vyšší) v systému RHEL 6 nebo SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Poznámka podpory SAP #2397039 – nejčastější dotazy: SAP v RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [Poznámka SAP support #1496410 – Red Hat Enterprise Linux 6.x: instalace a upgrade](https://launchpad.support.sap.com/#/notes/1496410)
- [Poznámka SAP support #2002167 – Red Hat Enterprise Linux 7.x: instalace a upgrade](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Čas synchronizace

Aplikace SAP, které jsou postavené na systému SAP NetWeaver architektury jsou citlivé na časové rozdíly pro různé součásti, které tvoří systém SAP. Výpisy stavu systému SAP ABAP krátký s názvem chyba ZDATE\_velké\_čas\_DIFF pravděpodobně obeznámeni. Důvodem je, tyto krátké výpisy zobrazí, když je příliš daleko od sebe plovoucí systémového času jiné servery nebo virtuální počítače.

Pro SAP HANA v Azure (velké instance) synchronizaci času, který se má provést v Azure neplatí pro výpočetních jednotek v velká Instance razítka. Tato synchronizace není použitelný pro provozování aplikací SAP na virtuálních počítačích Azure nativní, protože Azure tak zajišťuje jistotu, že se správně synchronizují systémového času. 

V důsledku toho musíte vytvořit samostatný čas serveru, který lze použít tak, že aplikační servery SAP, na kterých běží na virtuálních počítačích Azure a instance databáze SAP HANA, které jsou spuštěny na velkých instancích HANA. Infrastrukturu úložiště ve velké Instance razítka je čas synchronizovaný s servery NTP.


## <a name="networking"></a>Sítě
Předpokládáme, že jste postupovali podle doporučení v návrhu virtuálním sítím Azure a připojení těchto virtuálních sítí na velkých instancích HANA, jak je popsáno v následujících dokumentech:

- [SAP HANA (velká Instance) přehled a architektura v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Existují některé podrobnosti stojí za zmínku o možnostech sítě jedné jednotky. Každá jednotka velká Instance HANA obsahuje dvě nebo tři IP adresy, které jsou přiřazeny dvě nebo tři portů síťových karet. Tři IP adresy se používají v HANA horizontální navýšení kapacity konfigurací a scénář replikace systému HANA. Jednou z IP adresy, které jsou přiřazeny k síťovému rozhraní jednotky je mimo server fond IP adres, který je popsaný v [architektura v Azure a SAP HANA (velké instance) přehled](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Další informace o Ethernet podrobnosti o architektuře, najdete v článku [HLI Podporované scénáře](hana-supported-scenario.md).

## <a name="storage"></a>Úložiště

Rozložení úložiště pro SAP HANA v Azure (velké instance) je nakonfigurována ve SAP HANA na Azure service management prostřednictvím SAP Doporučené pokyny. Tyto pokyny jsou dokumentovány v článku [požadavky na úložiště SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) dokument white paper. 

Přibližnou velikostí různé svazky s jinou SKU velké instance HANA je popsána v [architektura v Azure a SAP HANA (velké instance) přehled](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zásady vytváření názvů svazků úložiště jsou uvedeny v následující tabulce:

| Využití úložiště | Název připojení | Název svazku | 
| --- | --- | ---|
| Data systému HANA | /Hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA protokolu | /Hana/log/SID/mnt0000<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| Záloha protokolu HANA | /Hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA sdílené | /Hana/Shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| USR/sap | /USR/SAP/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*Identifikátor SID* je instance HANA ID systému. 

*Tenant* je interní výčet operací při nasazování klienta.

Sdílené složky usr/sap HANA stejném svazku. Klasifikace přípojné body zahrnuje ID systému instance HANA a také počet připojení. V nasazeních vertikálně navýšit kapacitu je pouze jeden přípojný, jako je například mnt00001. V nasazeních horizontální navýšení kapacity na druhé straně se zobrazí tolik připojí jako uzly pracovního procesu a hlavním serverem. 

Horizontální navýšení kapacity prostředí, dat, protokolu a protokolu jsou záložní svazky sdílené a připojené k jednotlivým uzlům v konfiguraci horizontální navýšení kapacity. U konfigurací, které jsou více instancí SAP jinou sadu svazky vytvoří a připojí k jednotce velká Instance HANA. Podrobnosti rozložení úložiště pro váš scénář najdete v tématu [HLI Podporované scénáře](hana-supported-scenario.md).

Při pohledu na jednotku velká Instance HANA, zjistíte, že jednotky součástí velkorysá diskovém svazku pro data systému HANA/a že je svazek/log/zálohování HANA. Důvod, proč jsme provedli HANA/dat velmi velké je, že snímků úložiště nabízíme, že jako odběratel používáte stejný svazek disku. Další snímky úložiště provedete, tím více místa je využívána snímky v svazky úložiště přiřazené. 

HANA/log/zálohování svazku by neměl být svazkem pro zálohování databáze. Je velikosti má být použit jako záložní svazek pro zálohování protokolů transakcí HANA. Další informace najdete v tématu [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Kromě úložiště, která je k dispozici můžete zakoupit kapacitu další úložiště v přírůstcích po 1 TB. Toto dodatečné úložiště lze přidat jako nové svazky na velká Instance HANA.

Během připojování se SAP HANA v Azure service management zákazníkovi Určuje ID uživatele (UID) a skupina ID (ID skupiny) pro skupiny uživatelů a sapsys sidadm (například: 1000,500) během instalace systému SAP HANA, musíte použít tyto stejné hodnoty. Vzhledem k tomu, že budete chtít nasadit víc instancí HANA na jednotce, získáte víc kopií svazků (jedna sada u každé instance). V důsledku toho v době nasazení je potřeba definovat následující:

- Identifikátor SID různé instance HANA (sidadm je odvozen z něj).
- Velikostí paměti různých instancích HANA. Velikost paměti na instanci definuje velikost svazků v každé sadě jednotlivých svazků.

Podle doporučení pro zprostředkovatele úložiště, následující možnosti připojení jsou nakonfigurované pro všechny připojené svazky (nezahrnuje spouštěcí logickou jednotku):

- systém souborů NFS rw vers = 4, pevný, timeo = 600, parametru rsize = 1048576 wsize = 1048576, intr noatime, uzamknout 0 0

Tyto přípojné body konfigurované v /etc/fstab, jak je znázorněno na následujících obrázcích:

![fstab připojených svazků v jednotce velká Instance HANA](./media/hana-installation/image1_fstab.PNG)

Výstup příkaz df -h na jednotce velká Instance HANA S72m vypadá jako:

![fstab připojených svazků v jednotce velká Instance HANA](./media/hana-installation/image2_df_output.PNG)


Servery NTP, které jsou synchronizovány řadič úložiště a uzly velká Instance razítka. Při synchronizaci SAP HANA v Azure (velké instance) jednotky a virtuální počítače Azure proti serveru NTP, měla by existovat žádné odchylek spoustu času mezi infrastruktury a výpočetních jednotek v Azure nebo velká Instance razítka.

Pokud chcete optimalizovat SAP HANA na úložiště používá pod, nastavte následující parametry konfigurace SAP HANA:

- max_parallel_io_requests 128
- async_read_submit na
- async_write_submit_active on
- async_write_submit_blocks all
 
Verze SAP HANA 1.0 až SPS12 tyto parametry můžete udělat během instalace databáze SAP HANA, jak je popsáno v [Poznámka SAP #2267798 – konfiguraci serveru SAP HANA database](https://launchpad.support.sap.com/#/notes/2267798).

Můžete také nakonfigurovat parametry po instalaci databáze SAP HANA pomocí rozhraní hdbparam. 

V SAP HANA 2.0 hdbparam framework je zastaralá. Parametry v důsledku toho musíte nastavit pomocí příkazů jazyka SQL. Další informace najdete v tématu [Poznámka SAP #2399079: odstranění hdbparam v HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Odkazovat na [HLI Podporované scénáře](hana-supported-scenario.md) získat další informace o rozložení úložiště pro architektury.


**Další kroky**

- Odkazovat na [instalace HANA na HLI](hana-example-installation.md)










































 







 




