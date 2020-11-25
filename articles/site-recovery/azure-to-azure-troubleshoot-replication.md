---
title: Řešení potíží s replikací virtuálních počítačů Azure pomocí Azure Site Recovery
description: Řešení potíží s replikací v zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: dc14334668b76ee8cbb81e48abfe1eecf17fa138
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007354"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Řešení potíží s replikací v zotavení po havárii virtuálního počítače Azure

Tento článek popisuje běžné problémy v Azure Site Recovery při replikaci a obnovování virtuálních počítačů Azure z jedné oblasti do jiné oblasti. Vysvětluje také, jak řešit běžné problémy. Další informace o podporovaných konfiguracích najdete v [matrici podpory pro replikaci virtuálních počítačů Azure](./azure-to-azure-support-matrix.md).

Azure Site Recovery konzistentně replikuje data ze zdrojové oblasti do oblasti zotavení po havárii. Zároveň vytvoří bod obnovení konzistentní vzhledem k selháním každých 5 minut. Pokud Site Recovery nemůžou vytvářet body obnovení po dobu 60 minut, upozorní vás na tyto informace:

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

Následující části popisují příčiny a řešení.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>Vysoká četnost změn dat na zdrojovém virtuálním počítači

Azure Site Recovery vytvoří událost, pokud je frekvence změny dat na zdrojovém virtuálním počítači vyšší než podporované limity. Pokud se chcete podívat, jestli se jedná o problémy s vysokou četností, přejděte na **replikované položky**  >  události **virtuálního počítače**  >  **– posledních 72 hodin**.
Měla by se zobrazit **rychlost změny dat události nad rámec podporovaných omezení**:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="Azure Site Recovery stránka, která zobrazuje vysokou rychlost změny dat, která je příliš vysoká.":::

Pokud vyberete událost, měli byste vidět přesné informace o disku:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="Na stránce se zobrazí podrobnosti o události četnosti změny dat.":::

### <a name="azure-site-recovery-limits"></a>Omezení Azure Site Recovery

Následující tabulka obsahuje omezení služby Azure Site Recovery. Tato omezení vycházejí z našich testů, ale nemůžou pokrýt všechny možné kombinace vstupně-výstupních operací aplikace (v/v). Skutečné výsledky se můžou lišit v závislosti na kombinaci vstupně-výstupních operací vaší aplikace.

Je potřeba vzít v úvahu dvě omezení: četnost změn dat na disk a data na virtuální počítač. Pojďme se podívat na disk Premium P20 v následující tabulce. Pro jeden virtuální počítač může Site Recovery zpracovávat 5 MB/s změn na disk s maximálně pěti takovými disky. Site Recovery má limit 54 MB/s z celkové změny na virtuální počítač.

**Cíl úložiště replikace** | **Průměrná velikost vstupně-výstupních operací pro zdrojový disk** |**Průměrná četnost změn dat na zdrojovém disku** | **Celková četnost změn dat za den pro zdrojový datový disk**
---|---|---|---
Storage úrovně Standard | 8 kB    | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB    | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |    336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |20 MB/s | 1684 GB na disk

### <a name="solution"></a>Řešení

Azure Site Recovery má omezení rychlosti změny dat v závislosti na typu disku. Pokud chcete zjistit, jestli je tento problém opakovaný nebo dočasný, najděte rychlost změny dat ovlivněného virtuálního počítače. Přejít na zdrojový virtuální počítač, najít metriky v části **monitorování** a přidat metriky, jak je znázorněno na tomto snímku obrazovky:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="Stránka, která zobrazuje proces tří kroků pro hledání frekvence změny dat.":::

1. Vyberte **Přidat metriku** a přidejte **bajty zapsané na disk s operačním systémem/s** a **zapsáním bajtů datového disku/s**.
1. Sledujte špičku, jak je znázorněno na snímku obrazovky.
1. Zobrazuje celkový počet operací zápisu prováděných na discích s operačním systémem a všechny datové disky v kombinaci. Tyto metriky vám nemusí poskytnout informace na úrovni jednotlivých disků, ale označují celkový vzor četnosti změn dat.

Špička v rychlosti změny dat může pocházet z příležitostného nárůstu dat. Pokud je frekvence změny dat větší než 10 MB/s (v případě úrovně Premium) nebo 2 MB/s (u standardu) a nastane nižší, replikace se zachytí. Pokud je četnost změn stále větší než podporovaný limit, vezměte v úvahu jednu z těchto možností:

- Vylučte disk, který způsobuje vysokou rychlost změny dat: nejdřív zakažte replikaci. Pak můžete disk vyloučit pomocí [prostředí PowerShell](azure-to-azure-exclude-disks.md).
- Změna úrovně úložného disku pro zotavení po havárii: Tato možnost je dostupná jenom v případě, že je četnost změn dat na disku menší než 20 MB/s. Například virtuální počítač s diskem P10 má změny dat větší než 8 MB, ale menší než 10 MB/s. Pokud zákazník může během ochrany použít disk P30 pro cílové úložiště, můžete problém vyřešit. Toto řešení je možné jenom u počítačů, které používají Premium-Managed disky. Postupujte takto:

  1. Přejít na **disky** ovlivněného replikovaného počítače a zkopírovat název disku repliky.
  1. Přejít na tuto repliku spravovaného disku.
  1. V **přehledu** se může zobrazit informační zpráva s informací o tom, že se vygenerovala adresa URL SAS. Vyberte tuto hlavičku a zrušte export. Pokud se banner nezobrazuje, tento krok ignorujte.
  1. Jakmile se adresa URL SAS odvolá, pokračujte na **konfiguraci** spravovaného disku. Zvětšete velikost tak, aby Site Recovery podporovala pozorovanou četnost změn na zdrojovém disku.

## <a name="network-connectivity-problems"></a>Problémy s připojením k síti

### <a name="network-latency-to-a-cache-storage-account"></a>Latence sítě pro účet úložiště mezipaměti

Site Recovery odesílá replikovaná data do účtu úložiště mezipaměti. Může dojít k latenci sítě, pokud je nahrávání dat z virtuálního počítače do účtu úložiště mezipaměti pomalejší než 4 MB během 3 sekund.

Pokud chcete zjistit problém týkající se latence, použijte [AzCopy](../storage/common/storage-use-azcopy-v10.md). Tento nástroj příkazového řádku můžete použít k nahrání dat z virtuálního počítače do účtu úložiště mezipaměti. Pokud je latence vysoká, ověřte, jestli k řízení odchozího síťového provozu z virtuálních počítačů používáte síťové virtuální zařízení (síťové virtuální zařízení). Zařízení může být omezené, pokud se veškerý provoz replikace projde přes síťové virtuální zařízení.

Doporučujeme vytvořit koncový bod síťové služby ve virtuální síti pro úložiště, aby provoz replikace nepřešel do síťové virtuální zařízení. Další informace najdete v tématu [Konfigurace síťového virtuálního zařízení](azure-to-azure-about-networking.md#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Připojení k síti

Aby replikace Site Recovery fungovala, potřebuje virtuální počítač pro poskytování odchozího připojení ke konkrétním adresám URL nebo rozsahům IP adres. Můžete mít virtuální počítač za bránou firewall nebo použít pravidla skupiny zabezpečení sítě (NSG) k řízení odchozího připojení. Pokud ano, může docházet k problémům. Pokud chcete mít jistotu, že jsou všechny adresy URL připojené, přečtěte si téma [odchozí připojení pro adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>ID chyby 153006 – pro virtuální počítač není k dispozici žádný bod obnovení konzistentní vzhledem k aplikacím v posledních "X" minut

Níže jsou uvedené některé z nejběžnějších problémů.

### <a name="known-issue-in-sql-server-20082008-r2"></a>Známý problém v systému SQL Server 2008/2008 R2

**Jak opravit:** Došlo k známému problému s SQL serverem 2008/2008 R2. Přečtěte si článek [Azure Site Recovery agent nebo jiná nekomponentová záloha služby VSS se nezdařila pro server, který je hostitelem SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Azure Site Recovery úlohy selžou na serverech, které hostují jakékoli verze SQL Server instancí AUTO_CLOSE databáze

**Jak opravit:** Přečtěte si článek [zálohování služby VSS, které není součástí komponenty, například Azure Site Recovery úlohy, se nezdaří na serverech, které hostují SQL Server instancí AUTO_CLOSE databáze](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).

### <a name="known-issue-in-sql-server-2016-and-2017"></a>Známý problém v SQL Serveru 2016 a 2017

**Jak opravit**: Přečtěte si článek [kumulativní aktualizace 16 pro SQL Server 2017](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017).

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Používáte Azure Storage konfiguraci s přímými prostory

**Oprava**: Azure Site Recovery nelze vytvořit bod obnovení konzistentní vzhledem k aplikacím pro konfiguraci prostory úložiště s přímým přístupem. [Nakonfigurujte zásady replikace](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="app-consistency-not-enabled-on-linux-servers"></a>Konzistence aplikací není povolena na serverech se systémem Linux

**Oprava** : Azure Site Recovery pro operační systém Linux podporuje vlastní skripty aplikace pro konzistenci aplikací. Vlastní skript s možnostmi před a po odeslání bude použit agentem Azure Site Recovery mobility pro konzistenci aplikací. [Tady](./site-recovery-faq.md#replication) je postup, jak ho povolit.

### <a name="more-causes-because-of-vss-related-issues"></a>Další příčiny v důsledku potíží souvisejících se službou VSS:

Pokud chcete problém vyřešit, Projděte si soubory na zdrojovém počítači, abyste získali přesný kód chyby pro selhání:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

Chcete-li najít chyby, otevřete soubor _vacp. log_ v textovém editoru hledání řetězce **vacpError**.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

V předchozím příkladu je **2147754994** kód chyby, který vám oznamuje selhání po této větě.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Zapisovač VSS není nainstalovaný – chyba 2147221164

**Jak opravit**: Pokud chcete generovat značku konzistence aplikace, Azure Site Recovery používá služba Stínová kopie svazku (VSS). Site Recovery nainstaluje poskytovatele služby Stínová kopie svazku, aby jeho operace mohla provádět snímky konzistence aplikací. Azure Site Recovery nainstaluje tohoto poskytovatele služby VSS jako službu. Pokud není nainstalován poskytovatel služby VSS, vytvoření snímku konzistence aplikace se nezdařilo. Ukazuje, že **není zaregistrovaná třída ID chyby 0x80040154**. [Řešení potíží s instalací zapisovače VSS](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)najdete v článku.

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Zapisovač VSS je zakázaný – chyba 2147943458

**Jak opravit**: Pokud chcete vygenerovat značku konzistence aplikace, Azure Site Recovery používá Stínová kopie svazku (VSS). Site Recovery nainstaluje poskytovatele služby Stínová kopie svazku, aby jeho operace mohla provádět snímky konzistence aplikací. Tento zprostředkovatel služby Stínová kopie svazku je nainstalován jako služba. Pokud nemáte povolenou službu poskytovatele VSS, vytvoření snímku konzistence aplikace se nezdařilo. Zobrazuje se chyba: **Zadaná služba je zakázaná a nedá se spustit (0x80070422)**.

Pokud je VSS zakázaný:

- Ověřte, zda je typ spouštění služby VSS Provider nastaven na hodnotu **automaticky**.
- Restartujte následující služby:
  - Služba VSS.
  - Azure Site Recovery poskytovatele služby Stínová kopie svazku.
  - Služba VDS.

#### <a name="vss-provider-not_registered---error-2147754756"></a>Zprostředkovatel služby Stínová kopie svazku NOT_REGISTERED – chyba 2147754756

**Jak opravit**: Pokud chcete vygenerovat značku konzistence aplikace, Azure Site Recovery používá Stínová kopie svazku (VSS). Ověřte, jestli je nainstalovaná služba poskytovatele VSS Azure Site Recovery.

K opětovné instalaci poskytovatele VSS použijte následující příkazy:

1. Odinstalace stávajícího zprostředkovatele:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. Přeinstalujte zprostředkovatele VSS:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Ověřte, zda je typ spouštění služby VSS Provider nastaven na hodnotu **automaticky**.

Restartujte následující služby:

- Služba VSS.
- Azure Site Recovery poskytovatele služby Stínová kopie svazku.
- Služba VDS.
