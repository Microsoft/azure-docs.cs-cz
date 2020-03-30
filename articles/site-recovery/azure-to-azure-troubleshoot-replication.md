---
title: Poradce při potížích s replikací virtuálních virtuálních stránek Azure pomocí Azure Site Recovery
description: Poradce při potížích s replikací v zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: 67b68cc8a1db4a058675dc51fb3805093c455908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276661"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Poradce při potížích s replikací v zotavení po havárii virtuálního počítače Azure

Tento článek popisuje běžné problémy v Azure Site Recovery při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti. Vysvětluje také, jak řešit běžné problémy. Další informace o podporovaných konfiguracích najdete v [matici podpory pro replikaci virtuálních počítačů Azure](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery konzistentně replikuje data ze zdrojové oblasti do oblasti zotavení po havárii. Také vytvoří bod obnovení konzistentní selhání každých 5 minut. Pokud site recovery nemůže vytvořit body obnovení po dobu 60 minut, upozorní vás s těmito informacemi:

Chybová zpráva: "V posledních 60 minutách není pro virtuální virtuální mě k dispozici žádný bod obnovení konzistentní s selháním."</br>
ID chyby: 153007

Následující části popisují příčiny a řešení.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a><a name="high-data-change-rate-on-the-source-virtal-machine"></a>Vysoká četnost změn dat na zdrojovém virtuálním počítači

Azure Site Recovery vytvoří událost, pokud je rychlost změny dat na zdrojovém virtuálním počítači vyšší než podporované limity. Chcete-li zjistit, zda je problém z důvodu vysoké změny, přejděte na **replikované položky** > **události virtuálního soudu** > **- posledních 72 hodin**.
Měla by se zobrazit událost "Rychlost změny dat nad rámec podporovaných limitů":

![Stránka Obnovení webu Azure, která zobrazuje vysokou příliš vysokou míru změny dat](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Pokud vyberete událost, měli byste vidět přesné informace o disku:

![Stránka, která zobrazuje podrobnosti události rychlosti změny dat](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Omezení Azure Site Recovery

Následující tabulka obsahuje omezení služby Azure Site Recovery. Tyto limity jsou založeny na našich testech, ale nemohou pokrýt všechny možné kombinace vstupu a výstupu aplikace (I/O). Skutečné výsledky se můžou lišit v závislosti na kombinaci vstupně-výstupních operací vaší aplikace.

Existují dvě omezení, která je třeba zvážit: změny dat na disk a změny dat na virtuální počítač. Podívejme se na disk Premium P20 v následující tabulce. Pro jeden virtuální počítače site recovery může zpracovat 5 MB/s změn na disk s maximálně pěti takovými disky. Obnovení webu má limit 25 MB/s celkových změn na virtuální virtuální< žurnál.

**Cíl úložiště replikace** | **Průměrná velikost vstupně-no pro zdrojový disk** |**Průměrná konve dat pro zdrojový disk** | **Celkový počet změn dat za den pro zdrojový datový disk**
---|---|---|---
Storage úrovně Standard | 8 kB    | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB    | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |    336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |20 MB/s | 1684 GB na disk

### <a name="solution"></a>Řešení

Azure Site Recovery má limity na sazby změny dat, v závislosti na typu disku. Chcete-li zjistit, zda je tento problém opakovaný nebo dočasný, vyhledejte rychlost změny dat ovlivněného virtuálního počítače. Přejděte na zdrojový virtuální počítač, vyhledejte metriky v části **Monitorování**a přidejte metriky, jak je znázorněno na tomto snímku obrazovky:

![Stránka, která zobrazuje třístupňový proces hledání rychlosti změny dat](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Vyberte **Přidat metriku**a přidejte **bajty zápisu disku operačního systému/s** a **bajty zápisu datového disku/s**.
1. Sledujte špičku, jak je znázorněno na snímku obrazovky.
1. Zobrazení celkových operací zápisu, které probíhají na discích operačního systému a na všech datových discích dohromady. Tyto metriky nemusí poskytnout informace na úrovni na disk, ale označují celkový vzor změn dat.

Prudký nárůst rychlosti změny dat může pocházet z občasného shluků dat. Pokud je rychlost změny dat větší než 10 MB/s (pro Premium) nebo 2 MB/s (pro standard) a spadne, replikace dožene. Pokud je konzumní konzumní hodnota výrazně nad podporovaný limit, zvažte jednu z těchto možností:

- Vylučte disk, který způsobuje vysokou rychlost změny dat: Nejprve zakažte replikaci. Disk pak můžete vyloučit pomocí [prostředí PowerShell](./azure-to-azure-exclude-disks.md).
- Změna úrovně disku úložiště pro zotavení po havárii: Tato možnost je možná pouze v případě, že konve dat disku je menší než 20 MB/s. Řekněme, že virtuální počítače s diskem P10 má konve větší než 8 MB/s, ale méně než 10 MB/s. Pokud zákazník může použít disk P30 pro cílové úložiště během ochrany, problém lze vyřešit. Toto řešení je možné pouze pro počítače, které používají disky s praženými službou Premium. Postupujte následovně:

    1. Přejděte na **disky** ohroženého replikovaného počítače a zkopírujte název disku repliky.
    1. Přejděte na tuto repliku spravovaného disku.
    1. V **přehledu** se může zobrazit nápis, který říká, že byla vygenerována adresa URL SAS. Vyberte tento nápis a export zrušte. Tento krok ignorujte, pokud nevidíte banner.
    1. Jakmile je adresa URL SAS odvolána, přejděte na **možnost Konfigurace** spravovaného disku. Zvětšete velikost tak, aby site recovery podporuje pozorovanou rychlost změn na zdrojovém disku.

## <a name="network-connectivity-problems"></a><a name="Network-connectivity-problem"></a>Problémy s připojením k síti

### <a name="network-latency-to-a-cache-storage-account"></a>Latence sítě u účtu úložiště mezipaměti

Site Recovery odesílá replikovaná data do účtu úložiště mezipaměti. Latence sítě může dojít, pokud nahrávání dat z virtuálního počítače do účtu úložiště mezipaměti je pomalejší než 4 MB za 3 sekundy.

Chcete-li zkontrolovat problém související s latencí, použijte [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy). Pomocí tohoto nástroje příkazového řádku můžete nahrát data z virtuálního počítače do účtu úložiště mezipaměti. Pokud je latence vysoká, zkontrolujte, jestli používáte síťové virtuální zařízení (NVA) k řízení odchozího síťového provozu z virtuálních počítačů. Zařízení může získat omezení, pokud všechny replikační provoz prochází síťového virtuálního zařízení.

Doporučujeme vytvořit koncový bod síťové služby ve virtuální síti pro "úložiště", aby provoz replikace nepřešel na síťové virtuální zařízení. Další informace naleznete v [tématu Konfigurace síťového virtuálního zařízení](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Připojení k síti

Aby replikace obnovení lokality fungovala, potřebuje virtuální ho virtuální ho disponizátor k zajištění odchozího připojení ke konkrétním adresám URL nebo rozsahům IP adres. Můžete mít virtuální počítač za bránou firewall nebo použít pravidla skupiny zabezpečení sítě (NSG) k řízení odchozího připojení. Pokud ano, může dojít k problémům. Informace o tom, zda jsou všechny adresy URL připojeny, naleznete [v tématu Odchozí připojení adres URL site recovery pro adresy URL obnovení lokality](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>Chyba ID 153006 – v posledních minutách "X" není pro virtuální virtuální server k dispozici žádný bod obnovení konzistentní s aplikací.

Níže jsou uvedeny některé z nejčastějších problémů.

#### <a name="known-issue-in-sql-server-20082008-r2"></a>Známý problém v SQL Server 2008/2008 R2

**Jak opravit**: Je známý problém s SQL server 2008/2008 R2. Naleznete v článku [Agent obnovení webu Azure nebo jiné nesoučásti Záloha VSS selže pro server hostující SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Úlohy obnovení webu Azure selhávají na serverech hostujících libovolnou verzi instancí SQL Serveru s AUTO_CLOSE dbs

**Jak opravit**: Podívejte se na článek [Nekomponentní zálohy VSS, jako jsou úlohy Obnovení webu Azure, selhávají na serverech hostujících instance serveru SQL Server s AUTO_CLOSE dbs](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>Známý problém v SQL Serveru 2016 a 2017

**Jak opravit**: Podívejte se na článek [K chybě dochází při zálohování virtuálního počítače s nesoučásti založené zálohování v SQL Server 2016 a 2017](https://support.microsoft.com/en-us/help/4508218/cumulative-update-16-for-sql-server-2017).

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Používáte azure storage spaces přímá konfigurace

**Jak opravit**: Azure Site Recovery nemůže vytvořit bod obnovení konzistentní aplikace pro přímé konfigurace prostory úložiště. [Konfigurace zásad replikace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms).

### <a name="more-causes-because-of-vss-related-issues"></a>Další příčiny z důvodu problémů souvisejících s VSS:

Chcete-li dále řešit potíže, zkontrolujte soubory ve zdrojovém počítači, abyste získali přesný kód chyby pro selhání:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Chcete-li najít chyby v souboru, vyhledejte řetězec "vacpError" otevřením souboru vacp.log v editoru.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

V předchozím příkladu **2147754994** je kód chyby, který informuje o selhání po této větě.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Zapisovač VSS není nainstalován - chyba 2147221164

**Jak opravit**: Ke generování značky konzistence aplikace azure site recovery používá službu Stínová kopie svazku (VSS). Site Recovery nainstaluje zprostředkovatele VSS pro jeho provoz, aby pořizovat snímky konzistence aplikace. Azure Site Recovery nainstaluje tohoto zprostředkovatele VSS jako službu. Pokud zprostředkovatel VSS není nainstalován, vytvoření snímku konzistence aplikace se nezdaří. Zobrazuje chybu ID 0x80040154 "Třída není registrována." Informace o [řešení potíží s instalací zapisovače služby VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)naleznete v článku .

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Zapisovač VSS je zakázán - chyba 2147943458

**Jak opravit**: Ke generování značky konzistence aplikace azure site recovery používá VSS. Site Recovery nainstaluje zprostředkovatele VSS pro jeho provoz, aby pořizovat snímky konzistence aplikace. Tento zprostředkovatel VSS je nainstalován jako služba. Pokud nemáte povolenou službu Zprostředkovatel VSS, vytvoření snímku konzistence aplikace se nezdaří. Zobrazuje chybu "Zadaná služba je zakázána a nelze ji spustit (0x80070422)."

Pokud je VSS zakázán:

- Ověřte, zda je typ spuštění služby Zprostředkovatel VSS nastaven na **možnost Automaticky**.
- Restartujte následující služby:
   - Služba VSS
   - Obnovení webu Azure vss zprostředkovatele
   - Služba VDS

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - chyba 2147754756

**Jak opravit**: Ke generování značky konzistence aplikace azure site recovery používá VSS. Zkontrolujte, jestli je nainstalovaná služba Zprostředkovatel webu Azure.

K přeinstalaci zprostředkovatele Služby VSS použijte následující příkazy:
1. Odinstalace stávajícího zprostředkovatele: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
1. Přeinstalace zprostředkovatele VSS: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Ověřte, zda je typ spuštění služby Zprostředkovatel VSS nastaven na **možnost Automaticky**.

Restartujte následující služby:
- Služba VSS
- Obnovení webu Azure vss zprostředkovatele
- Služba VDS
