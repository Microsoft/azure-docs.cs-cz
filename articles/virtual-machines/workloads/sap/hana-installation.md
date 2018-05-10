---
title: Nainstalujte SAP HANA SAP HANA v Azure (velké instance) | Microsoft Docs
description: Postup instalace SAP HANA v SAP HANA v Azure (velké Instance).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 768d9c31cdf019bf73a9d3b3a239c537c72725f6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Postup instalace a konfigurace SAP HANA (velké instance) na Azure

Toto jsou některé důležité definice potřebujete vědět, než se pustíte do čtení této příručky. V [přehled SAP HANA (velké instance) a architektura v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) zavedli jsme dvěma různými třídami velké Instance HANA jednotek s:

- S72, S72m, S144, S144m, S192 a S192m, které označujeme jako typu Type I třídy, z jednotky SKU.
- S384, S384m, S384xm, S576m, S768m a S960m, které označujeme jako typ II třída z jednotky SKU.

Specifikátor třídy se bude používat v dokumentaci velké Instance HANA nakonec odkazovat na různé možnosti a požadavky založené na HANA velké Instance SKU.

Další definice, které jsme používané jsou:
- **Velké Instance razítka:** zásobníku infrastruktury hardwaru, který je SAP HANA TDI certifikaci a vyhrazené pro spuštění instance SAP HANA v rámci Azure.
- **SAP HANA v Azure (velké instance):** oficiální název nabídky v Azure ke spuštění HANA instancí v na SAP HANA TDI certifikaci hardwaru, která je nasazena v velké Instance razítka v různých oblastech Azure. Související termín **HANA velké Instance** je zkratka pro SAP HANA v Azure (velké instance) a je široce používá tato příručka technického nasazení.


Instalace SAP HANA je vaší povinností a aktivity můžete spustit po předání nové SAP HANA na serveru Azure (velké instance). A po tu navázání připojení mezi vaší VNet(s) Azure a jednotka HANA velké Instance. 

> [!Note]
> Podle zásad SAP musí provést instalaci SAP HANA osoba certifikované k provedení instalace SAP HANA. Osoba, která byla úspěšná zkoušku certifikované přidružit technologie SAP, zkouška SAP HANA instalace, nebo certifikaci SAP systémový integrátor (SI).

Zkontrolujte znovu, zejména při plánování instalace HANA 2.0 [SAP podporu Poznámka #2235581 - SAP HANA: podporované operační systémy](https://launchpad.support.sap.com/#/notes/2235581/E) Pokud chcete mít jistotu, že operačního systému je podporovaná s jste se rozhodli nainstalovat verzi SAP HANA. Zjistíte, že je podporovaný operační systém pro HANA 2.0 omezenější než operačního systému pro HANA 1.0 podporována. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>První kroky po přijetí Instance jednotka velké HANA

**První krok** po obdržení velké Instance HANA a zavedených přístupu a připojení k instancí, je k registraci instance operačního systému u svého poskytovatele operačního systému. Tento krok bude zahrnovat registrace operačním systému SUSE Linux v instanci SMT SUSE, že je potřeba mít nasazené ve virtuálním počítači v Azure. Jednotka HANA velké Instance může připojit k této instanci SMT (viz dále v této dokumentaci). Nebo operačním systému Red Hat je nutné zaregistrovat pomocí Red Hat předplatné správce je třeba se připojit k. Viz také remarks v tomto [dokumentu](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tento krok je také nutné mít opravy operačního systému. Úloha, která je v rámci odpovědnosti zákazníka. U SUSE, vyhledejte v dokumentaci k instalaci a konfiguraci SMT [zde](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Druhé krok** je kontroluje nové aktualizace a opravy konkrétní verzi nebo verzi operačního systému. Zkontrolujte, zda úroveň oprav velké instance HANA je na nejnovější stav. Podle časování aktualizací operačního systému a změny na bitovou kopii, kterou můžete nasadit Microsoft, mohou existovat případech, kdy nejnovějších oprav nemusí být zahrnuty. Proto je povinný krok po převzetí HANA Instance velké jednotky, a zkontrolujte, zda opravy, které jsou relevantní pro zabezpečení, funkce, dostupnosti a výkonu uvolnila mezitím konkrétní dodavatele Linux se mají použít.

**Třetí krok** je rezervovat relevantní SAP poznámky pro instalaci a konfiguraci SAP HANA na konkrétní verzi nebo verzi operačního systému. Z důvodu změna doporučení nebo změny SAP poznámky nebo konfigurace, které jsou závislé na jednotlivé instalace scénáře, společnost Microsoft vždycky nebude mohou mít velký Instance HANA jednotku perfektně nakonfigurované. Proto je povinné pro vás jako zákazník, přečtěte si poznámky k SAP související s SAP HANA na vaše přesnou verzi systému Linux. Také zkontrolujte konfiguraci operačního systému nebo verzi potřebné a použít nastavení konfigurace kde neudělali.

V konkrétní, zkontrolujte následující parametry a nakonec upravený tak, aby:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- NET.Core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Od verze SLES12 SP1 a RHEL 7.2, musí tyto parametry nastavit v konfiguračním souboru v adresáři /etc/sysctl.d. Například musíte vytvořit konfigurační soubor s názvem 91-NetApp-HANA.conf. Pro starší verze SLES a RHEL musí být tyto parametry in/etc/sysctl.conf sady.

Pro všechny RHEL uvolní a počínaje SLES12, 
- sunrpc.tcp_slot_table_entries = 128

Parametr musí být nastaven in/etc/modprobe.d/sunrpc-local.conf. Pokud soubor neexistuje, musíte nejprve vytvořit přidáním následující položku: 
- Možnosti sunrpc tcp_max_slot_table_entries = 128

**Čtvrtý krok** je kontrola systémového času HANA velké jednotky Instance. Instance se nasadí s systémovým časovým pásmem, které představují umístění, které Instance razítka velké HANA se nachází v oblasti Azure. Jste volné Změna systémového času a časového pásma z instancí, které vlastníte. Díky tomu a řazení více instancí do vašeho klienta, připravené, je potřeba přizpůsobit časové pásmo nově doručené instancí. Microsoft operations mít žádné přehledy systémovým časovým pásmem, že nastavíte s instancí po předání. Proto není ve stejném časovém pásmu jako ten, který jste změnili na nastaveno nově nasazené instancí. V důsledku toho je vaší povinností jako zákazník zkontrolujte a v případě potřeby přizpůsobit na časové pásmo instance předá. 

**První krok** je kontrola etc/hosts. Jak získat předá okna, mají různé IP adresy přiřazené pro různé účely (viz další část). Zkontrolujte soubor etc/hosts. V případech, kde jsou jednotky přidané do existujícího klienta nebudete mít atd nově nasazené systémy správně udržovaná IP adresy dříve doručené systémy hostitele. Proto je na vás jako zákazník zkontrolujte správné nastavení tak, že nově nasazená instance můžete interakci a vyřešit názvy dříve nasazené jednotek ve vašem klientovi. 

## <a name="networking"></a>Sítě
Předpokládáme, že jste postupovali podle doporučení v návrhu sítě Azure Vnet a připojení k velké instancí HANA tyto virtuální sítě, jak je popsáno v těchto dokumentů:

- [Přehled SAP HANA (velké Instance) a architektura v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastruktura SAP HANA (velké instance) a připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Abychom zmínili o možnostech sítě jedné jednotky existují některé podrobnosti vhodné. Každá jednotka HANA velké Instance obsahuje dvě nebo tři IP adresy, které jsou přiřazeny k seskupování porty dvě nebo tři jednotky. Tři IP adresy se používají v HANA konfigurace Škálováním na více systémů a scénář replikaci HANA systému. Jednou z IP adresy přiřazené k seskupování jednotky je z fondu IP adresy serveru, který je popsáno v [přehled SAP HANA (velké Instance) a architektura v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Distribuce pro jednotky s přiřazeny dvě IP adresy by měl vypadat podobně jako:

- eth0.xx by měl mít IP adresu přiřadit, která je mimo rozsah adres fondu IP serveru, který jste odeslali do společnosti Microsoft. Tato IP adresa se použije pro zachování v/etc/hosts operačního systému.
- eth1.xx by měl mít IP adresu přiřazen, který se používá pro komunikaci systému souborů NFS. Proto se tyto adresy **není** třeba udržet v etc/hosts, aby bylo možné povolit provoz instance pro instance v rámci klienta.

Pro nasazení případech replikaci systému HANA nebo HANA škálování není vhodný okno Konfigurace s přiřazeny dvě IP adresy. Pokud má dvě IP adresy, které jsou přiřazeny pouze a chtějí nasadit taková konfigurace, obraťte se na SAP HANA na Azure Service Management získání třetí IP adresu ve třetí přiřadit síť VLAN. Pro velké Instance HANA jednotky s tři IP adresy přiřazené na tři porty NIC platí následující pravidla využití:

- eth0.xx by měl mít IP adresu přiřadit, která je mimo rozsah adres fondu IP serveru, který jste odeslali do společnosti Microsoft. Proto tuto IP adresu se nepoužijí pro zachování v/etc/hosts operačního systému.
- eth1.xx by měl mít IP adresu přiřadit, který se používá pro komunikaci úložiště systému souborů NFS. Tento typ adresy proto by nemělo být udržovány v etc/hosts.
- eth2.xx by měl být zachován v atd nebo hostitele pro komunikaci mezi různými instancemi výhradně použije. Tyto adresy by také IP adresy, které je potřeba udržovat v konfiguracích HANA škálování jako IP adresy, kterou používá HANA pro konfigurace mezi uzly.



## <a name="storage"></a>Úložiště

Rozložení úložiště pro SAP HANA v Azure (velké instance) se nakonfiguruje prostřednictvím SAP HANA na Azure Service Management prostřednictvím SAP doporučená Průvodce řádky, jak je uvedeno v [požadavky na úložiště SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) dokumentu white paper. Hrubý velikosti jiné svazky s jinou HANA velké instance SKU tu popsané v [přehled SAP HANA (velké Instance) a architektura v Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zásady vytváření názvů svazky úložiště jsou uvedeny v následující tabulce:

| Využití úložiště | Název připojení | Název svazku | 
| --- | --- | ---|
| HANA data | /Hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA protokolu | /Hana/log/SID/mnt0000<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| Záloha protokolu HANA | /Hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| Sdílené HANA | /Hana/Shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| USR/sap | /USR/SAP/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Kde SID = instance HANA ID systému 

A klient = interní výčet operací při nasazování klienta.

Jak vidíte, HANA sdílené a usr/sap sdílejí stejný svazek. Klasifikace přípojné body nezahrnuje systému ID instance HANA, jakož i počet připojení. V nasazení škálování jenom je jeden přípojný, jako je mnt00001. Zatímco v nasazení s více instancemi zobrazí se jako v mnoha připojení zařízení, jako, máte uzly pracovního procesu a hlavní server. Pro prostředí Škálováním na více systémů, dat, protokolu jsou záložní svazky protokolu sdílené a připojené k každý uzel v konfiguraci škálovaného na více systémů. Pro konfigurace spuštěním několika instancí SAP jinou sadu svazků je vytvořen a připojena k jednotce HANU velké Instance.

Číst papíru a vypadat Instance HANA velké jednotky, zjistíte, že jednotky dodávány spolu s místo štědrém diskový svazek pro HANA nebo data a že obsahuje svazek HANA/log/zálohování. Důvod, proč jsme velikost HANA nebo data tak velký, je, nabízíme vám jako zákazník snímky úložiště používáte stejný svazek disku. Znamená to, další úložiště snímků provedete, snímky v svazků přiřazené úložiště využívá více místa. HANA/log/zálohování svazku není představit jako svazek se umístí zálohy databáze. Je podle použít jako záložní svazek pro zálohování HANA transakčního protokolu. V budoucích verzích úložiště snímku vlastní službu, že jsme se zaměří na konkrétním svazku mít častější snímky. A s třídou další časté replikace na web pro zotavení po havárii pro možnost v pro obnovení po havárii funkce poskytované službou infrastruktury velké Instance HANA vyžadujete-li. Zobrazit podrobnosti v [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Kromě úložiště poskytuje si můžete zakoupit dodatečné úložiště kapacity v přírůstcích po 1 TB. Tato další úložiště lze přidat jako nové svazky na velké instance HANA.

Během registrace s SAP HANA na Azure Service Management zákazník Určuje ID uživatele (UID) a ID skupiny (GID) pro skupinu uživatelů a sapsys sidadm (např: 1000,500) je nutné během instalace systému SAP HANA, používají tyto stejné hodnoty. Jak chcete nasadit více instancí HANA na jednotce, získáte více sad svazky (jednu sadu pro každou instanci). V důsledku toho v době nasazení je třeba definovat:

- Identifikátor SID různými instancemi HANA (sidadm je odvozený z jeho).
- Velikosti paměti různých instancí HANA. Vzhledem k tomu, že velikost paměti na instancí definuje velikost svazků v každé sadě jednotlivých svazku.

Na základě doporučení zprostředkovatele úložiště následující možnosti připojení jsou nakonfigurované pro všechny připojené svazky (nezahrnuje spouštěcí logickou jednotku):

- systém souborů NFS rw vladače = 4, pevné, timeo = 600, parametru rsize = 1048576 wsize = 1048576, intr, noatime, uzamčení 0 0

Tyto přípojné body jsou konfigurovány v fstab/etc/jako je vidět na následujících obrázcích:

![fstab připojených svazků v jednotce velké Instance HANA](./media/hana-installation/image1_fstab.PNG)

Výstup příkaz df -h na jednotce, S72m HANA velké Instance bude vypadat:

![fstab připojených svazků v jednotce velké Instance HANA](./media/hana-installation/image2_df_output.PNG)


Řadič úložiště a uzly ve velkých Instance razítka, která jsou synchronizovány do NTP servery. S vámi synchronizace SAP HANA na jednotkách Azure (velké instance) a virtuální počítače Azure proti serveru NTP měla by existovat žádné významné čas odlišily situaci mezi infrastruktury a výpočetní jednotky v Azure nebo velké Instance razítka.

Chcete-li optimalizovat SAP HANA do úložiště používá pod, je třeba nastavit také následující konfigurační parametry SAP HANA:

- max_parallel_io_requests 128
- async_read_submit na
- async_write_submit_active on
- async_write_submit_blocks all
 
Verze SAP HANA 1.0 až SPS12, tyto parametry lze nastavit při instalaci databázi SAP HANA, jak je popsáno v [SAP Poznámka #2267798 - konfigurace databázi SAP HANA](https://launchpad.support.sap.com/#/notes/2267798)

Také můžete nakonfigurovat parametry po instalaci databáze SAP HANA pomocí rozhraní hdbparam. 

S SAP HANA 2.0 se již nepoužívá rozhraní hdbparam. V důsledku parametry musí být nastavena pomocí příkazů SQL. Podrobnosti najdete v tématu [2399079 # Poznámka SAP: odstranění hdbparam v HANA 2](https://launchpad.support.sap.com/#/notes/2399079).


## <a name="operating-system"></a>Operační systém

Velikosti odkládacího souboru doručené bitové kopie operačního systému je nastavena na 2 GB podle [SAP podporu Poznámka #1999997 – nejčastější dotazy: paměti SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Všechny nichž různých nastavení požadované musí být nastavená vy jako zákazník.

[SUSE Linux Enterprise Server 12 SP1 pro aplikace SAP](https://www.suse.com/products/sles-for-sap/hana) je distribuci systému Linux nainstalován pro SAP HANA v Azure (velké instance). Tuto konkrétní distribuci poskytuje funkce specifické pro SAP &quot;ihned&quot; (včetně předem nastavené parametry pro SAP systémem SLES efektivně).

V tématu [dokumenty prostředků knihovny nebo White Paper](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) na webu SUSE a [SAP na SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na SAP komunity sítě (oznámení změny stavu) pro několik užitečné zdroje související s nasazováním SAP HANA na SLES (včetně nastavení vysoké dostupnosti, posílení zabezpečení specifická pro operace SAP a další).

Další a užitečné SAP SUSE související odkazy:

- [SAP HANA v lokalitě SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Nejvhodnější pro SAP: replikace zařazování – SAP NetWeaver na operačního systému SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP – SLES antivirovou ochranu pro SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (včetně SLES 12 pro aplikace SAP).

SAP podporu poznámky platí pro implementace SAP HANA na SLES 12:

- [Podpora Poznámka SAP #1944799 – SAP HANA pokyny pro instalaci operačního systému SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [Podpora Poznámka SAP #2205917 – SAP HANA DB doporučené nastavení operačního systému pro SLES 12 pro aplikace SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
- [Podpory Poznámka SAP #1984787 – SUSE Linux Enterprise Server 12: Poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787).
- [Podpora Poznámka SAP #171356 – SAP softwaru v systému Linux: Obecné informace](https://launchpad.support.sap.com/#/notes/1984787).
- [Podpora Poznámka SAP #1391070 – řešení Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux pro SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) je jinou nabídku pro spouštění na HANA velké instance SAP HANA. Verze RHEL 6.7 a 7.2 jsou k dispozici. Poznámka: v opačném nativní virtuálních počítačích Azure kde je podporováno pouze RHEL 7.2 a novější verzí, velké instancí HANA prosím podporovat RHEL 6.7 také. Doporučujeme ale používat verze 7.x RHEL.

Další a užitečné SAP Red Hat související odkazy:
- [SAP HANA na Red Hat Linux lokality](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

SAP podporu poznámky platí pro implementace SAP HANA na Red Hat:

- [Podpora Poznámka SAP #2009879 - SAP HANA pokyny pro operační systém Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E).
- [Podporu Poznámka SAP #2292690 - SAP HANA DB: OS doporučená nastavení pro RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [Podporu Poznámka SAP #2247020 - SAP HANA DB: OS doporučená nastavení pro RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [Podpora Poznámka SAP #1391070 – řešení Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).
- [Podpora Poznámka SAP #2228351 - Linux: SAP HANA databáze aktualizace Service Pack 11 revize 110 (nebo vyšší) v systému RHEL 6 nebo SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [Podpora Poznámka SAP #2397039 – nejčastější dotazy: SAP na RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [Podpora Poznámka SAP #1496410 - Red Hat Enterprise Linux 6.x: instalace a Upgrade](https://launchpad.support.sap.com/#/notes/1496410).
- [Podpora Poznámka SAP #2002167 - Red Hat Enterprise Linux 7.x: instalace a Upgrade](https://launchpad.support.sap.com/#/notes/2002167).

## <a name="time-synchronization"></a>Synchronizaci času

SAP aplikace založené na architektuře SAP NetWeaver jsou citlivé na čas rozdíly pro různé součásti, které tvoří systém SAP. Výpisy paměti SAP ABAP krátké s název chyby ZDATE\_velké\_čas\_Rozdílové jsou pravděpodobně známý, jako když je příliš daleko od sebe plovoucí systémového času jiné servery nebo virtuální počítače, zobrazí se tyto krátké výpisy.

Pro SAP HANA v Azure (velké instance), čas synchronizace provádí v Azure nemá&#39;t platí pro výpočetní jednotky v velké Instance razítka. Tato synchronizace se nedá použít pro spouštění aplikací SAP v nativní virtuálních počítačích Azure, jak Azure zajistí systému&#39;s čas je správně synchronizovaný. V důsledku toho samostatné čas, který server musí být nastaven, mohou být využívána SAP aplikačních serverů se systémem na virtuálních počítačích Azure a SAP HANA databáze instancí spuštěných na velké instance HANA. Infrastruktura úložiště v velké Instance razítka je časově synchronizované s NTP servery.

## <a name="setting-up-smt-server-for-suse-linux"></a>Nastavení serveru SMT SUSE Linux
Velké instance SAP HANA nemají přímé připojení k Internetu. Proto není jednoduchý proces registrace taková jednotka s poskytovatelem operačního systému a ke stažení a instalaci opravy. V případě SUSE Linux může být jedno řešení nastavit serveru SMT ve virtuálním počítači Azure. Zatímco virtuálního počítače Azure musí být hostované ve virtuální síti Azure, který je připojen k instanci velké HANA. Pomocí těchto SMT serveru může jednotka velké Instance HANA zaregistrovat a stažení oprav. 

SUSE poskytuje většího Průvodce na jejich [nástroj pro správu předplatného pro SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Jako předběžnou podmínku pro instalaci serveru SMT splňující úlohu pro velké instanci HANA potřebovali byste:

- Azure VNet, která je připojena k okruhu HANA velké ER Instance.
- SUSE účet, který je přidružen organizaci. Vzhledem k tomu třeba, aby měl některé platné předplatné SUSE organizace.

### <a name="installation-of-smt-server-on-azure-vm"></a>Instalace serveru SMT na virtuální počítač Azure

V tomto kroku instalujete server SMT virtuální počítač Azure. První měr se pro přihlášení do [SUSE zákazníka Center](https://scc.suse.com/)

Jak jste se přihlásili, přejděte na organizaci--> přihlašovacích údajů organizace. V této části byste měli najít přihlašovací údaje, které jsou potřebné k nastavení serveru SMT.

Je třetí krok instalace virtuálního počítače s Linuxem SUSE ve virtuální síti Azure. Nasadit virtuální počítač, trvat bitovou kopii SLES 12 SP2 Galerie Azure. V procesu nasazení nemusíte definovat název DNS a nepoužívejte statické IP adresy, jak je vidět v tento snímek obrazovky

![nasazení virtuálního počítače pro SMT server](./media/hana-installation/image3_vm_deployment.png)

Nasazený virtuální počítač byl menší virtuálního počítače a obdržel interní IP adresu ve virtuální síti Azure z 10.34.1.4. Název virtuálního počítače byl smtserver. Po instalaci byla zaškrtnutá připojení k HANA velké jednotky instance. Závisí na uspořádání překlad může musíte nakonfigurovat rozlišení velkých Instance HANA jednotky v atd na hostitele virtuálního počítače Azure. Přidejte další disk k virtuálnímu počítači, který chcete použít k uložení opravy. Samotný spouštěcí disk může být příliš malá. V případě ukázán tu je disk připojený k /srv/www/htdocs, jak je znázorněno na následujícím snímku obrazovky. Měla by stačit 100 GB místa na disku.

![nasazení virtuálního počítače pro SMT server](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Přihlaste se k jednotka HANA velké Instance, Udržovat/etc/hosts a zkontrolujte, zda se lze připojit virtuální počítač Azure, který má být spuštěn SMT serveru přes síť.

Jakmile tato kontrola probíhá úspěšně, budete muset přihlásit k Azure virtuální počítač, který se má spustit SMT server. Pokud používáte putty k přihlášení k virtuálnímu počítači, je třeba provést toto pořadí příkazů v okně bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Po provedení těchto příkazů, restartujte vaší bash aktivovat nastavení. Spusťte YAST.

V YAST přejděte do údržba softwaru a vyhledejte smt. Vyberte smt, který automaticky přepne do yast2 smt, jak je uvedeno níže

![SMT v yast](./media/hana-installation/image5_smt_in_yast.PNG)


Výběr pro instalaci smtserver přijměte. Po instalaci, přejděte na konfigurace serveru SMT a zadejte přihlašovací údaje organizace z centra zákazníka SUSE, který jste získali dříve. Jako adresu URL serveru SMT také zadejte název hostitele vašeho virtuálního počítače Azure. V této ukázce bylo https://smtserver jak je uvedeno v další grafiky.

![Konfigurace serveru SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Jako další krok musíte otestovat, zda funguje připojení k centru SUSE zákazníka. Jak je vidět na následujících obrázcích, v případě ukázkový fungovat.

![Test připojení k SUSE zákazníka Center](./media/hana-installation/image7_test_connect.png)

Jednou spustí instalační program SMT, musíte zadat heslo k databázi. Vzhledem k tomu, že jde o novou instalaci, musíte definovat toto heslo, jak je uvedeno v další grafiky.

![Zadejte heslo pro databázi](./media/hana-installation/image8_define_db_passwd.PNG)

Další interakce, měli byste je, když se vytvoří certifikát. Přejděte pomocí dialogu, jak ukazuje následující a má pokračovat v kroku.

![Vytvoření certifikátu pro SMT server](./media/hana-installation/image9_certificate_creation.PNG)

Může být několik minut stráví v kroku "Spustit synchronizaci kontrolou" na konci konfigurace. Po instalaci a konfiguraci serveru SMT, byste měli najít adresář úložiště v rámci přípojného bodu /srv/www/htdocs/plus některé dílčí adresáře v úložišti. 

Restartujte SMT server a její související služby s těmito příkazy.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Stahování balíčky do serveru SMT

Po restartování všechny služby jsou v SMT správu pomocí Yast vyberte odpovídající balíčky. Výběr balíčku závisí na bitovou kopii operačního systému serveru HANA velké instancí a ne na SLES verzi nebo verzi serverem SMT virtuálního počítače. Níže je uveden příklad obrazovky výběru.

![Vyberte balíčky](./media/hana-installation/image10_select_packages.PNG)

Jakmile budete hotovi s výběrem balíčku, budete muset spustit počáteční kopii vyberte balíčky, do kterých SMT serveru, který nastavíte. Tato kopie se spustí v prostředí pomocí příkazu smt zrcadlení jak je uvedeno níže


![Stáhnout balíčky SMT server](./media/hana-installation/image11_download_packages.PNG)

Jak vidíte výše, by měl získat balíčky zkopírován do adresáře vytvořil v rámci přípojného bodu /srv/www/htdocs. Tento proces může chvíli trvat. Závisí na tom, kolik balíčky, které jste vybrali, to může trvat až jednu hodinu nebo déle.
Když tento proces dokončí, potřebujete přesunout v instalačním programu SMT klienta. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Nastavení klienta SMT na velké Instance HANA jednotky

Mailového klienta v tomto případě jsou velké Instance HANA jednotky. Instalační program serveru SMT zkopírují clientSetup4SMT.sh skriptu do virtuálního počítače Azure. Kopírování, která skript přes pro velké Instance HANA jednotky můžete se chcete připojit k serveru SMT. Spusťte skript s -h a pojmenujte ho jako parametr názvu SMT serveru. V této smtserver příklad.

![Konfigurace klienta SMT](./media/hana-installation/image12_configure_client.PNG)

Mohou existovat scénáře, kde se úspěšně zatížení certifikát ze serveru klientem, ale registrace se nezdařilo, jak je uvedeno níže.

![Neúspěšné registrace klienta](./media/hana-installation/image13_registration_failed.PNG)

Pokud se registrace nezdařila, přečtěte si to [SUSE podporu dokumentu](https://www.suse.com/de-de/support/kb/doc/?id=7006024) a provedení uvedeného postupu existuje.

> [!IMPORTANT] 
> Jako název serveru, budete muset zadat název virtuálního počítače, v této případu smtserver bez s plně kvalifikovaným názvem domény. Právě název funguje virtuálních počítačů. 

Po provedení těchto kroků budete muset spusťte následující příkaz na jednotce velké Instance HANA

```
SUSEConnect –cleanup
```

> [!Note] 
> V našich testech jsme vždy museli Počkejte několik minut po provedení tohoto kroku. ClientSetup4SMT.sh okamžité spuštění, po nápravná opatření popsané v článku SUSE skončila, certifikát nebude platný ještě zprávy. Čeká se obvykle 5 až 10 minut a provádění clientSetup4SMT.sh skončila v konfiguraci klienta úspěšné.

Pokud jste spustili na problém, který je nutná k vyřešení podle kroků v článku SUSE, musíte restartovat clientSetup4SMT.sh na jednotce velké Instance HANA znovu. Nyní je třeba dokončit úspěšně, jak je uvedeno níže.

![Registrace klienta proběhla úspěšně.](./media/hana-installation/image14_finish_client_config.PNG)

Pro tento krok nakonfigurovat klienta SMT jednotky velké Instance HANA připojit proti SMT serveru, který jste nainstalovali ve virtuálním počítači Azure. Pro instalaci oprav operačního systému na velké instance HANA nebo nainstalovat další balíčky můžete využít nyní 'zypper nahoru' nebo 'zypper v'. Předpokládá se, že pouze můžete získat opravy, které jste stáhli před na SMT serveru.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Příklad instalace SAP HANA na velké instancí HANA
Tato část ukazuje postup instalace SAP HANA na jednotce, velké Instance HANA. Počáteční stav jsme vypadat:

- Microsoft poskytuje všechna data nasazení můžete velké Instance SAP HANA.
- Velké Instance SAP HANA jste dostali od společnosti Microsoft.
- Vytvořili jste virtuální síť Azure, která je připojena k místní síti.
- Jste se přihlásili ExpressRotue okruhu pro velké instance HANA pro stejnou virtuální síť Azure.
- Nainstalovali jste virtuální počítač Azure používat jako pole přechod pro velké instance HANA.
- Je ověřeno, zda se můžete připojit z pole přechod do Instance HANA velké jednotky a naopak.
- Můžete zkontrolovat, zda jsou nainstalovány všechny potřebné balíčky a opravy.
- Přečíst poznámky k SAP a dokumentace týkající se instalace HANA na operačního systému používáte a zkontrolujte, že HANA verzi volba je podporována v operačního systému verze.

Informace zobrazené v další pořadí je ke stažení instalačních balíčků HANA do pole přechod virtuálních počítačů, v takovém případě systémem operačního systému Windows, kopii balíčky, do Instance HANA velké jednotky a pořadí instalačního programu.

### <a name="download-of-the-sap-hana-installation-bits"></a>Stahování bits instalace SAP HANA
Vzhledem k tomu, že Instance HANA velké jednotky nemají přímé připojení k Internetu, nelze stáhnout přímo instalační balíčky ze SAP do virtuálních počítačů velké Instance HANA. K překonání chybějící přímé připojení k Internetu, musíte pole přechod. Balíčky stáhnout do pole přechod virtuálních počítačů.

Chcete-li stáhnout instalační balíčky HANA, potřebujete SAP uživatelem S nebo jiný uživatel, který umožňuje přístup k SAP Marketplace. Projděte této posloupnosti obrazovky po přihlášení:

Přejděte na [Marketplace služby SAP](https://support.sap.com/en/index.html) > klikněte na tlačítko Stáhnout Software > Instalace a Upgrade > podle abecední Index > v části H – SAP HANA platformy edice > SAP HANA platformy verze 2.0 > instalace > stažení následujících souborů

![Stažení a instalace HANA](./media/hana-installation/image16_download_hana.PNG)

V případě ukázce jsme stáhli instalační balíčky SAP HANA 2.0. V poli Azure přechod virtuálních počítačů rozbalte samorozbalující archivy do adresáře jak je uvedeno níže.

![Extrahování HANA instalace](./media/hana-installation/image17_extract_hana.PNG)

Při extrahování archivu zkopírujte adresář vytvořený extrakce v případě, že nad 51052030, do HANA velké jednotky instance do svazku /hana/shared do adresáře, kterou jste vytvořili.

> [!Important]
> Nekopírujte instalační balíčky do kořenového adresáře nebo spouštěcí logické jednotky, protože místo je omezený a musí použít také jinými procesy.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Nainstalujte na jednotce HANA velké Instance SAP HANA
Abyste mohli nainstalovat SAP HANA, musíte být přihlášení jako kořenové uživatele. Pouze kořenové má dostatečná oprávnění k instalaci SAP HANA.
Je první věcí, kterou je potřeba nastavit oprávnění pro adresáře, který jste zkopírovali přes do/hana/sdílené. Oprávnění je nutné nastavit jako

```
chmod –R 744 <Installation bits folder>
```

Pokud chcete nainstalovat SAP HANA používá instalace s grafickým rozhraním, musí být nainstalovaný na velké instancí HANA gtk2 balíčku. Zkontrolujte, zda je nainstalována pomocí příkazu

```
rpm –qa | grep gtk2
```

V části Další kroky jsme jsou demonstraci SAP HANA instalace s grafickým uživatelským rozhraním. Jako další krok přejděte do instalačního adresáře a přejděte do adresáře dílčí HDB_LCM_LINUX_X86_64. Start

```
./hdblcmgui 
```
z tohoto adresáře. Nyní získávání projdete posloupnost obrazovky potřebujete-li poskytnout data pro instalaci. V případě ukázán jsme instalaci databázového serveru SAP HANA a klientské komponenty SAP HANA. Proto naše výběr je 'Databázi SAP HANA', jak je uvedeno níže

![Vyberte HANA v instalaci](./media/hana-installation/image18_hana_selection.PNG)

Na další obrazovce zvolte možnost nainstalovat nový systém

![Vyberte nové instalace HANA](./media/hana-installation/image19_select_new.PNG)

Po provedení tohoto kroku je nutné vybrat mezi několik dalších součástí, které mohou být nainstalovány kromě k serveru databáze SAP HANA.

![Vyberte další součásti HANA](./media/hana-installation/image20_select_components.PNG)

Pro účely této dokumentace jsme zvolili SAP HANA klienta a nástroje Studio SAP HANA. Také jsme nainstalovali instanci škálování. proto na další obrazovce, je třeba vybrat "systém jednoho hostitele. 

![Vyberte možnost instalace škálování](./media/hana-installation/image21_single_host.PNG)

Na další obrazovce budete muset zadat některá data

![Zadejte identifikátor SID SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako HANA systému ID (SID), musíte zadat stejný identifikátor SID, jako jste zadali Microsoft při řazení velké Instance HANA nasazení. Výběr jiný identifikátor SID umožňuje instalaci nezdaří z důvodu problémů oprávnění přístupu v různých svazcích.

Instalace použijete/hana/directory sdílený adresář. V dalším kroku budete muset zadat umístění pro HANA datové soubory a soubory protokolu HANA


![Zadejte umístění protokolu HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Musí definovat jako data a soubory svazků, které již byly dodány s přípojné body, které obsahují SID jste zvolili v obrazovce výběr před Tato obrazovka protokolů. Pokud SID došlo k neshodě s kódem, který jste zadali v, na obrazovce před, přejděte zpět a upravit SID na hodnotu, které máte na přípojné body.

V dalším kroku zkontrolujte název hostitele a nakonec opravte ji. 

![Zkontrolujte název hostitele](./media/hana-installation/image24_review_host_name.PNG)

V dalším kroku musíte také načíst data, která jste zadali do společnosti Microsoft při řazení velké Instance HANA nasazení. 

![Zadejte uživatele systému UID a GID](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Potřebujete poskytovat stejné systému ID uživatele a skupiny uživatelů ID jako automatického Microsoft jako pořadí nasazení jednotky. Pokud nedodržíte poskytují velmi stejná ID, se instalace SAP HANA na jednotce HANA velké Instance se nezdaří.

V následujících dvou obrazovky, které nezobrazují se všechna v této dokumentaci, budete muset zadat heslo pro uživatele systému databázi SAP HANA a heslo pro uživatele sapadm, který se používá pro SAP Agent hostitele, který získá nainstalované v rámci instance databáze SAP HANA.

Po definování heslo, se zobrazuje obrazovka s potvrzením, protože. Zkontrolujte všechna data uvedena v seznamu a pokračovat v instalaci. Probíhá na obrazovce, která dokumenty průběh instalace, jako je níže

![Zkontrolovat průběh instalace](./media/hana-installation/image27_show_progress.PNG)

Jak se instalace dokončí, měli byste jako následujícím obrázku

![Po dokončení instalace](./media/hana-installation/image28_install_finished.PNG)

V tomto okamžiku instance SAP HANA by to být až a spuštěná a připravená na použití. Nyní byste měli mít k němu připojit z SAP HANA Studio. Také zajistěte, aby kontrolovat nejnovějších oprav SAP HANA a používat tyto opravy.
























































 







 




