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
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dad088138fea2dd4fadc0cc9eed71245c32a8e0b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162650"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Instalace a konfigurace SAP HANA (velké instance) v Azure

Před čtením tohoto článku, seznamte se s [velkých instancích HANA běžných termínů](hana-know-terms.md) a [SKU velké instance HANA](hana-available-skus.md).

Instalace SAP HANA je vaší odpovědností, a začnete aktivity po předání nové SAP Hana v Azure (velké instance) serveru. A poté, co máte navázat spojení mezi vaší virtuální sítě Azure a jednotek velká Instance HANA. 

> [!Note]
> Podle zásad SAP musí provést instalace SAP HANA osobou certifikaci pro provedení instalace SAP HANA. Osoba, která uplyne zkoušku Certified přidružit technologie SAP, SAP HANA instalace certifikační zkoušku, nebo certifikací SAP systémový integrátor (SI).

Zkontrolovat znovu, zejména při plánování instalace HANA 2.0 [SAP Support Poznámka #2235581 – SAP HANA: podporované operační systémy](https://launchpad.support.sap.com/#/notes/2235581/E) Pokud chcete mít jistotu, že operační systém podporuje jste se rozhodli nainstalovat verzi SAP HANA. Dobré si uvědomit, že má omezenější než pro HANA 1.0 nepodporuje operační systém podporovaný operační systém pro HANA 2.0. 

> [!IMPORTANT] 
> Typ II jednotek pouze SLES 12 SP2 operačního systému verze se v tuto chvíli nepodporuje. 

Před zahájením instalace HANA, musíte ověřit následující:
- [Ověření HLI jednotek](#validate-the-hana-large-instance-units)
- [Konfigurace operačního systému](#operating-system)
- [Konfigurace sítě](#networking)
- [Konfigurace úložiště](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Ověření jednotek velká Instance HANA

Po obdržení velká Instance HANA jednotek od Microsoftu ověřit následující nastavení a upravit podle potřeby.

**První krok** po obdržení velká Instance HANA a mít zavedený přístup a připojení k instancím, je k registraci instance operačního systému u svého poskytovatele operačního systému. Tento krok zahrnuje registrace vašeho operačního systému SUSE Linux do instance SUSE SMT, které potřebujete k nasazení na virtuálním počítači v Azure. Velká Instance HANA jednotku můžete připojit k této instanci SMT (viz [jak server SMT instalační program operačního systému SUSE Linux](hana-setup-smt.md)). Nebo musí být zaregistrované pomocí Red Hat předplatné správce budete potřebovat pro připojení k váš operační systém Red Hat. Viz také poznámky v tomto [dokumentu](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tento krok je taky potřeba mít možnost opravy operačního systému. Úloha, která je v rámci odpovědnosti zákazníka. Pro SUSE, vyhledejte si dokumentaci k instalaci a konfiguraci SMT [tady](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

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

### <a name="time-synchronization"></a>Čas synchronizace

Aplikace SAP postavená na architektuře SAP NetWeaver jsou citlivé na časové rozdíly pro různé součásti, které tvoří systém SAP. Výpisy stavu systému SAP ABAP krátký s názvem chyba ZDATE\_velké\_čas\_DIFF jsou pravděpodobně známý, jak tyto krátké výpisy zobrazí, když je příliš daleko od sebe plovoucí systémového času jiné servery nebo virtuální počítače.

Pro SAP HANA v Azure (velké instance), čas synchronizace v Azure kódu&#39;t platí pro výpočetních jednotek v velká Instance razítka. Tato synchronizace se nedá použít pro provozování aplikací SAP na nativních virtuálních počítačích Azure, jak Azure zajišťuje systému&#39;s správně synchronizované. V důsledku toho samostatné dobu, kterou server musí být nastaven, která mohou být využívána SAP servery aplikace běžící na virtuálních počítačích Azure a SAP HANA databáze na velkých instancích HANA spuštěných instancí. Infrastrukturu úložiště ve velké Instance razítka je časově synchronizované s servery NTP.


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

Přečtěte si dokument a vypadat jednotka velká Instance HANA, zjistíte, že jednotky součástí spíše velkorysá diskovém svazku pro data systému HANA/a vidíme, že svazek HANA/log/zálohování. Důvod, proč jsme HANA/dat velmi velké velikosti je, snímků úložiště, které nabízíme pro vás jako odběratel používáte stejný svazek disku. To znamená větší úložiště snímků provedete, tím více místa je využívána snímky v svazky úložiště přiřazené. Svazek HANA/log/záloha není považované za umístění zálohy databáze do svazku. Je velikosti má být použit jako záložní svazek pro zálohování protokolů transakcí HANA. Podrobnosti najdete v [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

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


**Další kroky**

- Přečtěte si [instalace HANA na HLI](hana-example-installation.md)










































 







 




