---
title: Instalace SAP HANA na systému SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Postup instalace SAP HANA na systému SAP HANA v Azure (velká Instance).
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
ms.date: 08/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d335e135551b7b6faed8ee566acb14b46fd6c81
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43107507"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Instalace a konfigurace SAP HANA (velké instance) v Azure

Toto jsou některé důležité definice potřebujete vědět, než se pustíte do čtení této příručky. V [architektura v Azure a SAP HANA (velké instance) přehled](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) zavedli dvě různé třídy velká Instance HANA jednotek:

- S72 S72m, S144, S144m, S192, S192m a S192xm, které označujeme jako "Typu můžu třídy" skladových položek.
- S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm a S960m, které označujeme jako typ II třída skladových položek.

Specifikátor třídy se bude používat v rámci dokumentace velká Instance HANA nakonec odkazovat na různé možnosti a požadavky založené na SKU velké Instance HANA.

Další definice, které používáme často jsou:
- **Velké Instance razítka:** zásobníku hardwarové infrastruktury, která je SAP HANA TDI certifikace a vyhrazeném prostředí ke spuštění instance systému SAP HANA v Azure.
- **SAP HANA v Azure (velké instance):** oficiální název nabídky v Azure ke spuštění HANA instance na hardware, který je nasazený v velká Instance razítka v různých oblastech Azure s certifikací TDI HANA SAP. Související výraz **velká Instance HANA** je zkratka pro SAP HANA v Azure (velké instance) a je široce používá tato příručka technického nasazení.


Instalace SAP HANA je vaší odpovědností, a začnete aktivity po předání nové SAP Hana v Azure (velké instance) serveru. A poté, co máte navázat spojení mezi vaší virtuální sítě Azure a jednotek velká Instance HANA. 

> [!Note]
> Podle zásad SAP musí provést instalace SAP HANA osobou certifikaci pro provedení instalace SAP HANA. Osoba, která uplyne zkoušku Certified přidružit technologie SAP, SAP HANA instalace certifikační zkoušku, nebo certifikací SAP systémový integrátor (SI).

Zkontrolovat znovu, zejména při plánování instalace HANA 2.0 [SAP Support Poznámka #2235581 – SAP HANA: podporované operační systémy](https://launchpad.support.sap.com/#/notes/2235581/E) Pokud chcete mít jistotu, že operační systém podporuje jste se rozhodli nainstalovat verzi SAP HANA. Dobré si uvědomit, že má omezenější než pro HANA 1.0 nepodporuje operační systém podporovaný operační systém pro HANA 2.0. 

> [!IMPORTANT] 
> Typ II jednotek pouze SLES 12 SP2 operačního systému verze se v tuto chvíli nepodporuje. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>První kroky po přijetí jednotek aplikace pro velké Instance HANA

**První krok** po obdržení velká Instance HANA a mít zavedený přístup a připojení k instancím, je k registraci instance operačního systému u svého poskytovatele operačního systému. Tento krok zahrnuje registrace vašeho operačního systému SUSE Linux do instance SUSE SMT, které potřebujete k nasazení na virtuálním počítači v Azure. Velká Instance HANA jednotku můžete připojit k této instanci SMT (viz dále v této dokumentaci). Nebo musí být zaregistrované pomocí Red Hat předplatné správce budete potřebovat pro připojení k váš operační systém Red Hat. Viz také poznámky v tomto [dokumentu](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tento krok je taky potřeba mít možnost opravy operačního systému. Úloha, která je v rámci odpovědnosti zákazníka. Pro SUSE, vyhledejte si dokumentaci k instalaci a konfiguraci SMT [tady](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Druhý krok** se kontroluje nové opravy a oprav konkrétní verze nebo verze operačního systému. Zkontrolujte, zda úroveň opravy velké instance HANA na nejnovější stav. Podle časování aktualizací operačního systému a změny do image, kterou můžete nasadit Microsoft, můžou nastat případy, kde nemusí být součástí nejnovější opravy. Proto je povinný krok po déle než jednu jednotku velká Instance HANA, zkontrolujte, zda opravy, které jsou relevantní pro zabezpečení, funkce, dostupnosti a výkonu byly mezitím vydané konkrétní dodavatel Linuxových a je potřeba použít.

**Třetí krok** je zkontrolovat důležité poznámky SAP pro instalaci a konfiguraci SAP HANA na konkrétní verzi/verze operačního systému. Kvůli změně doporučení nebo změny poznámky SAP nebo konfigurace, které jsou závislé na scénáře jednotlivé instalace Microsoft vždy nebudou moct mít procesor velká Instance HANA zcela nakonfigurovaný. Proto je nutné jako zákazník, přečtěte si poznámky SAP vztahující se k SAP HANA na svou přesnou verzi systému Linux. Také zkontrolujte konfigurace OS/verze nezbytné a použijte nastavení konfigurace tam, kde dosud neučinili.

V konkrétní, zkontrolujte následující parametry a nakonec upraveny tak, aby:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- NET.Core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Počínaje SLES12 SP1 a RHEL 7.2, musí být nastavena v konfiguračním souboru v adresáři /etc/sysctl.d tyto parametry. Například konfigurační soubor s názvem 91 – NetApp-HANA.conf musí vytvořit. Pro starší verze SLES a RHEL musí být tyto parametry in/etc/sysctl.conf sady.

Pro všechny RHEL uvolní a počínaje SLES12, 
- sunrpc.tcp_slot_table_entries = 128

Parametr musí být nastaven in/etc/modprobe.d/sunrpc-local.conf. Pokud soubor neexistuje, musí nejprve vytvořit přidáním následující položky: 
- Možnosti sunrpc tcp_max_slot_table_entries = 128

**Čtvrtý krok** je kontrola systémového času jednotky velké Instance HANA. Instancí nasazených se časové pásmo systému, které představují umístění razítko velké Instance HANA je umístěn v oblasti Azure. Můžete libovolně změnit systémového času nebo časového pásma z instancí, které vlastníte. To a řazení více instancí do vašeho tenanta, připravené, že je potřeba upravit časové pásmo nově doručené instancí. Operace Microsoft mít žádné informace o časovém pásmu systému, že nastavíte s instancemi po předání. Nově nasazené instance proto nemusí být nastavení ve stejném časovém pásmu jako ta, kterou jste změnili na. V důsledku toho je vaší odpovědností zákazníky ke kontrole a v případě potřeby upravit časové pásmo instancí předán. 

**Pátý krok** je kontrola etc/hosts. Jak získat předán okna, mají různé IP adresy přiřazené pro různé účely (viz další část). Zkontrolujte soubor etc/hosts. V případech, kdy jsou jednotky přidány do existujícího tenanta Neočekáváme, že mají atd/hostitele nově nasazené systémy udržuje správně s IP adresami dříve doručené systémů. Proto je na vás jako zákazník zkontrolovat správné nastavení tak, že nově nasazené instance spolupracovat a překládat názvy jednotek dříve nasazené ve vašem tenantovi. 

## <a name="networking"></a>Sítě
Předpokládáme, že jste postupovali podle doporučení v návrhu vašich virtuálních sítí Azure a připojení těchto virtuálních sítí na velkých instancích HANA, jak je popsáno v těchto dokumentech:

- [SAP HANA (velká Instance) přehled a architektura v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Existují některé podrobnosti, které stojí za to se zmínit o možnostech sítě jedné jednotky. Každá jednotka velká Instance HANA obsahuje dvě nebo tři IP adresy, které jsou přiřazeny k portům NIC dvě nebo tři jednotky. Tři IP adresy se používají v HANA horizontální navýšení kapacity konfigurací a scénář HANA System Replication. Jednou z IP adresy přiřazené k síťovému rozhraní jednotky z fondu IP adresa serveru, která byla popsána v je [architektura v Azure a SAP HANA (velká Instance) přehled](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Přečtěte si [HLI Podporované scénáře](hana-supported-scenario.md) další ethernet podrobnosti o vaší architektuře.

## <a name="storage"></a>Úložiště

Rozložení úložiště pro SAP HANA v Azure (velké instance) je nakonfigurované v SAP HANA v Azure Service Management prostřednictvím SAP doporučuje vodicí čáry, jak je uvedeno v [požadavky na úložiště pro SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) dokument white paper. Přibližnou velikostí různé svazky s jinou SKU velké instance HANA je teď uvedené v [architektura v Azure a SAP HANA (velká Instance) přehled](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zásady vytváření názvů svazků úložiště jsou uvedeny v následující tabulce:

| Využití úložiště | Název připojení | Název svazku | 
| --- | --- | ---|
| Data systému HANA | /Hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA protokolu | /Hana/log/SID/mnt0000<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| Záloha protokolu HANA | /Hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA sdílené | /Hana/Shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| USR/sap | /USR/SAP/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Kde SID = instance HANA ID systému 

A tenanta = interní výčet operací při nasazování klienta.

Jak je vidět, sdílené HANA a sap s/usr sdílí stejný svazek. Klasifikace přípojné body zahrnuje ID systému instance HANA a také počet připojení. V nasazeních vertikálně navýšit kapacitu pouze neexistuje jednoho připojení, jako je mnt00001. Zatímco v nasazení scale-out uvidíte počet připojení, jako mají pracovní procesy a hlavní uzly. Záložní svazky s protokoly pro horizontální navýšení kapacity prostředí, dat, protokolu, jsou sdílené a připojené k jednotlivým uzlům v konfiguraci horizontální navýšení kapacity. Konfigurace spuštění více instancí SAP jinou sadu svazky vytvoří a připojí k jednotce HAN velkou instanci. Přečtěte si [HLI Podporované scénáře](hana-supported-scenario.md) podrobnosti rozložení úložiště pro váš scénář.

Přečtěte si dokument a vypadat jednotka velká Instance HANA, zjistíte, že jednotky součástí spíše velkorysá diskovém svazku pro data systému HANA/a vidíme, že svazek HANA/log/zálohování. Důvod, proč jsme HANA/dat velmi velké velikosti je, snímků úložiště, které nabízíme pro vás jako odběratel používáte stejný svazek disku. To znamená větší úložiště snímků provedete, tím více místa je využívána snímky v svazky úložiště přiřazené. Svazek HANA/log/záloha není považované za umístění zálohy databáze do svazku. Je velikosti má být použit jako záložní svazek pro zálohování protokolů transakcí HANA. V budoucích verzích úložiště snímků self service, že jsme se zaměří na tento konkrétní svazek mít častější snímky. A s ním další časté replikace na lokalitu pro obnovení po havárii-li se chtít možnost-v pro fungování obnovy po havárii poskytované velká Instance HANA infrastruktury. Podrobnosti najdete v [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Kromě úložiště k dispozici můžete zakoupit další kapacitu v přírůstcích po 1 TB. Toto dodatečné úložiště lze přidat jako nové svazky na velkých instancích HANA.

Během připojování se SAP HANA v Azure Service managementu, zákazník Určuje ID uživatele (UID) a ID skupiny ID (skupiny) pro skupiny uživatelů a sapsys sidadm (ex: 1000,500) je nutné při instalaci systému SAP HANA, použijí tyto stejné hodnoty. Jak budete chtít nasadit víc instancí HANA na jednotce, získáte víc kopií svazků (jedna sada u každé instance). V důsledku toho v době nasazení je potřeba definovat:

- Identifikátor SID různé instance HANA (sidadm je odvozen z ní).
- Velikostí paměti různých instancích HANA. Protože velikost paměti na instance definuje velikost svazků v každé sadě jednotlivých svazků.

Na základě doporučení služby poskytovatele úložiště jsou následující možnosti připojení nakonfigurované pro všechny připojené svazky (nezahrnuje spouštěcí logickou jednotku):

- systém souborů NFS rw vers = 4, pevný, timeo = 600, parametru rsize = 1048576 wsize = 1048576, intr noatime, uzamknout 0 0

Tyto přípojné body konfigurované v/etc/fstab jako je znázorněno na následujících obrázcích:

![fstab připojených svazků v jednotce velká Instance HANA](./media/hana-installation/image1_fstab.PNG)

Výstup příkaz df -h na jednotce velká Instance HANA S72m může vypadat třeba:

![fstab připojených svazků v jednotce velká Instance HANA](./media/hana-installation/image2_df_output.PNG)


Servery NTP, které jsou synchronizovány řadič úložiště a uzly velká Instance razítka. S vámi synchronizace SAP HANA v Azure (velké instance) jednotky a virtuální počítače Azure proti serveru NTP měla by existovat žádné děje odchylek spoustu času mezi infrastruktury a výpočetních jednotek v Azure nebo velká Instance razítka.

K optimalizaci SAP HANA na úložiště používá pod, měli byste také nastavit následující parametry konfigurace SAP HANA:

- max_parallel_io_requests 128
- async_read_submit na
- async_write_submit_active on
- async_write_submit_blocks all
 
Verze SAP HANA 1.0 až SPS12 tyto parametry můžete udělat během instalace databáze SAP HANA, jak je popsáno v [SAP Poznámka #2267798 – konfigurace databáze SAP HANA](https://launchpad.support.sap.com/#/notes/2267798)

Také můžete nakonfigurovat parametry po instalaci databáze SAP HANA s využitím rozhraní hdbparam. 

V SAP HANA 2.0 hdbparam framework je zastaralá. V důsledku parametry musí být nastaven pomocí příkazů SQL. Podrobnosti najdete v tématu [2399079 # SAP Poznámka: odstranění hdbparam v HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Přečtěte si [HLI Podporované scénáře](hana-supported-scenario.md) další rozložení úložiště pro architektury.

## <a name="operating-system"></a>Operační systém

> [!IMPORTANT] 
> Typ II jednotek pouze SLES 12 SP2 operačního systému verze se v tuto chvíli nepodporuje. 

Velikosti odkládacího souboru rámci smluv bitové kopie operačního systému nastavená na 2 GB podle [SAP Support Poznámka #1999997 – nejčastější dotazy: paměti SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Žádné nichž různých nastavení požadované se musí nastavit vy jako zákazník.

[SUSE Linux Enterprise Server 12 SP1 pro aplikace SAP](https://www.suse.com/products/sles-for-sap/hana) je distribuce systému Linux pro SAP HANA v Azure (velké instance) nainstalována. Tento konkrétní poskytuje funkce specifické pro SAP &quot;předpřipravených&quot; (včetně přednastaveným parametry pro spuštění SAP na SLES efektivně).

Naleznete v tématu [prostředků knihovny/specifikacích](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) na webu SUSE a [SAP v SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na SAP komunity sítě (oznámení změny stavu) pro několik užitečné zdroje informací související s nasazováním SAP HANA na SLES (včetně nastavení Vysoká Dostupnost, posílení zabezpečení, které jsou specifické pro operace SAP a další).

Další a užitečné SAP SUSE související odkazy:

- [SAP HANA v SUSE Linuxu lokalitě](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Osvědčený postup pro SAP: replikaci zařadit do fronty, SAP NetWeaver na SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP – ochrana proti virům SLES pro SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (včetně SLES 12 pro aplikace SAP).

Podpora poznámky SAP pro implementaci SAP HANA na SLES 12:

- [Poznámka: podpora SAP #1944799 – SAP HANA pokyny pro instalaci operačního systému SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [Poznámka: podpora SAP #2205917 – databáze SAP HANA doporučené nastavení operačního systému pro SLES 12 pro aplikace SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
- [Podpory Poznámka SAP #1984787 – SUSE Linux Enterprise Server 12: Poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787).
- [Poznámka: podpora SAP #171356 – softwaru SAP v Linuxu: Obecné informace](https://launchpad.support.sap.com/#/notes/1984787).
- [Poznámka: podpora SAP 1391070 # – Linux UUID řešení](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) je jinou nabídku pro spuštění SAP HANA ve velkých instancích HANA. Verze RHEL 6.7 a 7.2 jsou k dispozici. Poznámka: v opačném nativních virtuálních počítačích Azure kde se podporují jenom RHEL 7.2 a novější verze, velkých instancích HANA prosím podporují RHEL 6.7 také. Doporučujeme ale používat verzi 7.x RHEL.

Další a užitečné SAP na Red Hat souvisejících odkazů:
- [SAP HANA v Red Hat Linux lokality](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Podpora poznámky SAP pro implementaci SAP HANA v systému Red Hat:

- [Poznámka: podpora SAP #2009879 – pokyny pro SAP HANA pro operační systém Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E).
- [Poznámka: podpora SAP #2292690 – databáze SAP HANA: Nastavení doporučuje operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [Poznámka: podpora SAP #2247020 – databáze SAP HANA: Nastavení doporučuje operačního systému pro RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [Poznámka: podpora SAP 1391070 # – Linux UUID řešení](https://launchpad.support.sap.com/#/notes/1391070).
- [Poznámka: podpora SAP 2228351 # – Linux: SAP HANA Database aktualizace Service PACKU 11 revize 110 (nebo vyšší) v systému RHEL 6 nebo SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [Poznámka: podpora SAP #2397039 – nejčastější dotazy k: Řešení SAP v RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [Poznámka: podpora SAP #1496410 – Red Hat Enterprise Linux 6.x: instalace a Upgrade](https://launchpad.support.sap.com/#/notes/1496410).
- [Poznámka: podpora SAP #2002167 – Red Hat Enterprise Linux 7.x: instalace a Upgrade](https://launchpad.support.sap.com/#/notes/2002167).

## <a name="time-synchronization"></a>Čas synchronizace

Aplikace SAP postavená na architektuře SAP NetWeaver jsou citlivé na časové rozdíly pro různé součásti, které tvoří systém SAP. Výpisy stavu systému SAP ABAP krátký s názvem chyba ZDATE\_velké\_čas\_DIFF jsou pravděpodobně známý, jak tyto krátké výpisy zobrazí, když je příliš daleko od sebe plovoucí systémového času jiné servery nebo virtuální počítače.

Pro SAP HANA v Azure (velké instance), čas synchronizace v Azure kódu&#39;t platí pro výpočetních jednotek v velká Instance razítka. Tato synchronizace se nedá použít pro provozování aplikací SAP na nativních virtuálních počítačích Azure, jak Azure zajišťuje systému&#39;s správně synchronizované. V důsledku toho samostatné dobu, kterou server musí být nastaven, která mohou být využívána SAP servery aplikace běžící na virtuálních počítačích Azure a SAP HANA databáze na velkých instancích HANA spuštěných instancí. Infrastrukturu úložiště ve velké Instance razítka je časově synchronizované s servery NTP.

## <a name="setting-up-smt-server-for-suse-linux"></a>Nastavení serveru SMT operačním systémem SUSE Linux
Velké instance SAP HANA nemají přímé připojení k Internetu. Proto není jednoduchý proces registrace jako jednotek s operačním systémem zprostředkovatele a ke stažení a použití opravy. V případě operačním systémem SUSE Linux může být jedním z řešení k nastavení serveru SMT ve Virtuálním počítači Azure. Vzhledem k tomu virtuálního počítače Azure musí být hostované ve virtuální síti Azure, která je připojena k velká Instance HANA. Pomocí těchto SMT serveru může velká Instance HANA jednotku zaregistrujte a stažení oprav. 

SUSE poskytuje větší návod na jejich [nástroj pro správu předplatného pro SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Jako předpoklad instalace SMT serveru, který splňuje požadavky úlohy pro velké Instance HANA potřebujete:

- Virtuální síť Azure, který je připojený k okruhu ER velké Instance HANA.
- SUSE účet, který je spojen s organizací. Vzhledem k tomu třeba, aby měl některé platné předplatné SUSE organizace.

### <a name="installation-of-smt-server-on-azure-vm"></a>Instalace SMT serveru na virtuálním počítači Azure

V tomto kroku nainstalujete server SMT ve Virtuálním počítači Azure. První z měr se pro přihlášení k [centrum zákazníků SUSE](https://scc.suse.com/)

Protože jste přihlášeni, přejděte na organizaci--> přihlašovacími údaji organizace. V této části byste měli najít přihlašovací údaje, které jsou potřebné k nastavení SMT serveru.

Třetím krokem je instalace virtuálního počítače s operačním systémem SUSE Linux ve virtuální síti Azure. Pokud chcete nasadit virtuální počítač, proveďte SLES 12 SP2 image z Galerie Azure. V procesu nasazení nebudete definovat název DNS a nepoužívejte statické IP adresy, jak je vidět na tomto snímku obrazovky

![nasazení virtuálního počítače pro SMT server](./media/hana-installation/image3_vm_deployment.png)

Nasazený virtuální počítač byl menších virtuálních počítačů a máte ve virtuální síti Azure z 10.34.1.4 interní IP adresa. Název virtuálního počítače byla smtserver. Po dokončení instalace došlo k zaškrtnutí připojení k HANA velké instance jednotek. Závislé na uspořádání překlad můžete potřebovat konfigurace řešení velká Instance HANA jednotek v etc/hostitele virtuálního počítače Azure. Přidejte další disk k virtuálnímu počítači, který se použije k uložení opravy. Spouštěcí disk může být příliš malá. V tomto případě jsme vám ukázali máte disk připojený k /srv/www/htdocs, jak je znázorněno na následujícím snímku obrazovky. 100 GB disk by měl stačit.

![nasazení virtuálního počítače pro SMT server](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Přihlaste se k jednotek velká Instance HANA, Udržovat/etc/hosts a zkontrolujte, zda virtuální počítač Azure, který se má spustit SMT server přes síť mohou mít přístup.

Poté, co tato kontrola se úspěšně dokončí, budete muset přihlásit k virtuálnímu počítači Azure, který by měl spustit SMT serveru. Pokud používáte klienta putty k přihlášení k virtuálnímu počítači, je nutné provést toto pořadí příkazů v okně bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Po spuštění těchto příkazů, restartujte vaše prostředí bash a aktivovat nastavení. Potom spusťte YAST.

V YAST přejděte na údržbu softwaru a vyhledejte smt. Vyberte smt, automaticky přepnout do yast2 smt, jak je znázorněno níže

![SMT v yast](./media/hana-installation/image5_smt_in_yast.PNG)


Přijměte výběr pro instalaci smtserver. Po instalaci, přejděte na konfigurace serveru SMT a zadejte firemní přihlašovací údaje v Centru pro SUSE zákazníka, který jste získali dříve. Jako adresu URL serveru SMT také zadejte název hostitele vašeho virtuálního počítače Azure. V této ukázce se https://smtserver se zobrazuje další grafiky.

![Konfigurace serveru SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Jako další krok je potřeba otestovat, jestli funguje připojení k centru SUSE zákazníka. Jak vidíte na následujících obrázcích v tomto případě ukázka, fungovalo.

![Test připojení ke službě Center zákazníků operačním systémem SUSE](./media/hana-installation/image7_test_connect.png)

Jednou SMT instalační program spustí, musíte zadat heslo databáze. Protože se jedná o novou instalaci, budete muset definovat toto heslo, jak je znázorněno v další grafiky.

![Definujte heslo pro databázi](./media/hana-installation/image8_define_db_passwd.PNG)

Další interakce, které máte je, když se vytvoří certifikát. Projděte si dialogového okna, jak je ukázáno dále a pokračovat v kroku.

![Vytvoření certifikátu pro SMT server](./media/hana-installation/image9_certificate_creation.PNG)

Může existovat několik minut stráví v kroku "Spustit kontrola synchronizace" na konci konfigurace. Po instalaci a konfiguraci serveru SMT, měli byste najít adresář úložiště v rámci přípojný bod /srv/www/htdocs/plus některé dílčí adresáře v úložišti. 

Restartujte SMT server a souvisejících služeb pomocí následujících příkazů.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Stažení balíčků na serveru SMT

Po restartují se všechny služby, vyberte ve správě SMT pomocí Yast odpovídající balíčky. Výběr balíčku závisí na image operačního systému serveru velká Instance HANA a ne na SLES verze nebo verze serverem SMT virtuálního počítače. Níže je uveden příklad výběr obrazovky.

![Vybrat balíčky](./media/hana-installation/image10_select_packages.PNG)

Jakmile budete hotovi s výběrem balíčku, musíte spustit počáteční kopii výběr balíčků SMT serveru, který nastavíte. Tato kopie se aktivuje v prostředí shell pomocí příkazu smt – zrcadlení jak je znázorněno níže


![Stáhnout balíčky SMT server](./media/hana-installation/image11_download_packages.PNG)

Jak vidíte výše, by se do adresáře vytvořené v rámci přípojný bod /srv/www/htdocs zkopíruje balíčky. Tento proces může trvat nějakou dobu. Závisí na počet balíčků, které vyberete, může trvat až jedné hodiny nebo i déle.
Když tento proces dokončí, budete potřebovat přesunout SMT instalace klienta. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Nastavení klienta SMT u jednotek pro velké Instance HANA

Klientů v tomto případě jsou jednotky velká Instance HANA. Nastavení serveru SMT kopírovat skript clientSetup4SMT.sh připojení k virtuálnímu počítači Azure. Kopii, která skript, který přes jednotkou velká Instance HANA jste se chcete připojit k serveru SMT. Spuštění skriptu s parametrem -h a pojmenujte ji jako parametr SMT serveru. V tomto příkladu smtserver.

![Konfigurace klienta SMT](./media/hana-installation/image12_configure_client.PNG)

Mohou existovat scénáře, kde načíst certifikát ze serveru klientovi bylo úspěšné, ale registrace nebyla úspěšná, protože je uvedeno níže.

![Neúspěšné registrace klienta](./media/hana-installation/image13_registration_failed.PNG)

Pokud registrace se nezdařila, přečtěte si tento [SUSE podporují dokumentu](https://www.suse.com/de-de/support/kb/doc/?id=7006024) a proveďte kroky popsané existuje.

> [!IMPORTANT] 
> Jako název serveru budete muset zadat název virtuálního počítače v tomto případu smtserver bez plně kvalifikovaný název domény. Pouze název funguje virtuálního počítače. 

Po provedení těchto kroků je potřeba spuštěním následujícího příkazu na jednotce velká Instance HANA

```
SUSEConnect –cleanup
```

> [!Note] 
> V našich testech jsme museli vždy počkejte pár minut po provedení tohoto kroku. ClientSetup4SMT.sh hned po nápravných opatření popsané v článku SUSE skončilo s zprávy, že certifikát nebude platný ještě. Čeká se obvykle 5 až 10 minut a provádění clientSetup4SMT.sh skončila v konfiguraci úspěšné klienta.

Pokud jste narazili na problém, který je potřebný k opravě podle kroků v článku SUSE, budete muset restartovat clientSetup4SMT.sh na jednotce velká Instance HANA znovu. Nyní ji by měl úspěšně dokončit jak je znázorněno níže.

![Úspěšná registrace klienta](./media/hana-installation/image14_finish_client_config.PNG)

Pro tento krok nakonfigurovat klienta SMT jednotky velká Instance HANA a připojit se SMT serverem, který jste nainstalovali ve virtuálním počítači Azure. Pro instalaci oprav operačního systému na velkých instancích HANA nebo nainstalovat další balíčky můžete využít nyní "zypperu nahoru" nebo "zypperu v". Předpokládá se, že pouze můžete získat opravy, které jste stáhli dříve na serveru SMT.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Příklad instalace SAP HANA ve velkých instancích HANA
Tato část ukazuje, jak nainstalovat na jednotce velká Instance HANA SAP HANA. Počáteční stav, který máme k dispozici vypadat takto:

- Microsoft poskytuje všechna data nasazení velké Instance SAP HANA.
- Velká Instance SAP HANA jste dostali od Microsoftu.
- Vytvoříte virtuální síť Azure, který je připojený k vaší místní síti.
- Připojení okruhu ExpressRotue pro velké instance HANA ve stejné virtuální síti Azure.
- Nainstalovali jste virtuální počítač Azure použijete jako jump box pro velké instance HANA.
- Provedené jistotu, že se můžete připojit z jump box jednotky velká Instance HANA a naopak.
- Jste vrátili se, zda jsou nainstalovány všechny potřebné balíčky a opravy.
- Čtení poznámky SAP a dokumentace týkající se instalace HANA na operační systém používáte a zkontrolujte, že je podporována verze HANA podle výběru v operačním systému verze.

Jak je zobrazeno v dalším pořadí je ke stažení instalačních balíčků HANA k systému jump box virtuálního počítače, v tomto případě spuštěný v OS Windows, zkopírujte balíčky k jednotce velká Instance HANA a pořadí instalace.

### <a name="download-of-the-sap-hana-installation-bits"></a>Časový limit stažení bity instalace SAP HANA
Protože velká Instance HANA jednotky nemají přímé připojení k Internetu, nelze stáhnout přímo instalační balíčky ze SAP do virtuálních počítačů velké Instance HANA. K překonání chybějící přímé připojení k Internetu, musíte jump box. Stáhnout balíčky k systému jump box virtuálního počítače.

Pokud chcete stáhnout instalační balíčky HANA, musíte SAP S uživatelem nebo jiný uživatel, který umožňuje přístup k SAP web Marketplace. Projděte si toto pořadí obrazovek po přihlášení:

Přejděte na [SAP Service Marketplace](https://support.sap.com/en/index.html) > klikněte na možnost stažení softwaru > Instalace a Upgrade > podle abecední rejstřík > pod H – SAP HANA platformy Edition > SAP HANA platformy verze 2.0 > instalace > stáhnout Následující soubory

![Stažení a instalace HANA](./media/hana-installation/image16_download_hana.PNG)

V případě ukázku stáhli instalační balíčky SAP HANA 2.0. V systému Azure jump box virtuálního počítače rozbalte samorozbalovací archivy do adresáře jak je znázorněno níže.

![Extrahovat HANA instalace](./media/hana-installation/image17_extract_hana.PNG)

Při extrahování archivy zkopírujte adresář, vytvoří pomocí extrakce k HANA velké instance jednotky do svazku /hana/shared do adresáře, který jste vytvořili v případě výše 51052030.

> [!Important]
> Nekopírovat instalační balíčky do kořenového adresáře nebo spouštěcí logickou jednotku, protože místo je omezený a musí být používán jinými procesy také.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalace SAP HANA na jednotce velká Instance HANA
Abyste mohli nainstalovat SAP HANA, musíte se přihlásit jako uživatel root. Pouze root má dostatečná oprávnění k instalaci SAP HANA.
Je první věcí, kterou je potřeba nastavit oprávnění pro adresář, který se zkopíruje do/hana/sdílené. Oprávnění je potřeba nastavit jako

```
chmod –R 744 <Installation bits folder>
```

Pokud chcete nainstalovat SAP HANA pomocí grafický instalační program, musí být nainstalovaný na velkých instancích HANA gtk2 balíčku. Zkontrolujte, zda je nainstalována pomocí příkazu

```
rpm –qa | grep gtk2
```

V dalších krocích jsme se ukázka instalace SAP HANA pomocí grafického uživatelského rozhraní. Jako další krok přejděte do instalačního adresáře a přejděte do podadresáře HDB_LCM_LINUX_X86_64. Start

```
./hdblcmgui 
```
z tohoto adresáře. Nyní získávání projdete pořadí obrazovek, které je potřeba zadat data pro instalaci. V případě jsme vám ukázali jsme instalaci databázového serveru SAP HANA a klientské komponenty SAP HANA. Proto naši výběr je "Databáze SAP HANA" jak je znázorněno níže

![Vyberte HANA v instalaci](./media/hana-installation/image18_hana_selection.PNG)

Na další obrazovce zvolíte možnost instalace nového systému

![Vyberte novou instalaci HANA](./media/hana-installation/image19_select_new.PNG)

Po provedení tohoto kroku je potřeba vybrat mezi několik dalších komponent, které lze nainstalovat navíc k databázovému serveru SAP HANA.

![Vyberte další součásti HANA](./media/hana-installation/image20_select_components.PNG)

Pro účely této dokumentace Rozhodli jsme se pro SAP HANA Client a SAP HANA Studio. Také jsme nainstalovali instanci vertikálně navýšit kapacitu. proto na další obrazovce, musíte zvolit "jednoho hostitele systému. 

![Vyberte možnost instalace vertikálně navýšit kapacitu](./media/hana-installation/image21_single_host.PNG)

Na další obrazovce je potřeba zadat některá data

![Zadejte identifikátor SID SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako HANA System Identifikátor (SID), budete muset zadat stejný identifikátor SID, jako jste zadali Microsoft, když jste si objednali velká Instance HANA nasazení. Zvolíte jiné SID díky selhat z důvodu problémů s přístupem k oprávnění v různých svazcích instalací

Instalace sdílený adresář použijete/hana nebo adresář. V dalším kroku je potřeba zadat umístění pro HANA datové soubory a soubory protokolu HANA


![Zadejte umístění protokolu HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> By měl definovat jako data a svazky, které již dodán jako součást přípojné body, které obsahují SID, kterou jste zvolili v výběr obrazovky před Tato obrazovka soubory protokolu. Pokud identifikátor SID neshoda, který jste zadali, v dialogovém okně před přejděte zpět a upravte identifikátor SID k hodnotě, kterou máte v přípojné body.

V dalším kroku zkontrolujte název hostitele a nakonec ho opravit. 

![Zkontrolujte název hostitele](./media/hana-installation/image24_review_host_name.PNG)

V dalším kroku budete potřebovat k načtení dat, které dáte společnosti Microsoft, když jste si objednali velká Instance HANA nasazení. 

![Zadejte uživatele systému UID a ID skupiny](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Je třeba zadat stejného ID uživatele systému a ID skupiny uživatelů, jako jste zadali jako pořadí nasazení jednotek Microsoft. Pokud chcete poskytnout stejné ID, SAP Hana na jednotce velká Instance HANA se nezdaří.

V další dvě obrazovky, které jsme se nezobrazují v této dokumentaci, budete muset zadat heslo pro uživatele systému databáze SAP HANA a heslo pro uživatele sapadm, který se používá pro hostitele agenta SAP, který se nainstaluje jako součást datab SAP HANA instance služby ase.

Po definování heslo, se zobrazuje obrazovka s potvrzením, protože. Zkontrolujte všechna data uvedená a pokračovat v instalaci. Průběh na obrazovce, dokumentující průběh instalace, jako je ten níže

![Zkontrolujte průběh instalace](./media/hana-installation/image27_show_progress.PNG)

Když instalace dokončí, měli byste jako na následujícím obrázku

![Po dokončení instalace](./media/hana-installation/image28_install_finished.PNG)

Instance SAP HANA v tomto okamžiku by měl být připravené a spuštěné a budete připraveni za využití. Měli byste být schopni k němu připojit z SAP HANA Studio. Také se ujistěte, že zkontrolujte nejnovější opravy SAP HANA a použít tyto opravy.
























































 







 




