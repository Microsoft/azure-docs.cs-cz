---
title: Azure Site Recovery řešení potíží při probíhajících potížích s replikací z Azure do Azure | Microsoft Docs
description: Řešení chyb a problémů při replikaci virtuálních počítačů Azure na zotavení po havárii
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.author: asgang
ms.openlocfilehash: 02f3dff4c9649beeadade942f4b32595f8543c2d
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742552"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Řešení potíží s probíhajícími operacemi v Azure-to-Azure – replikace virtuálních počítačů

Tento článek popisuje běžné problémy v Azure Site Recovery při replikaci a obnovování virtuálních počítačů Azure z jedné oblasti do jiné oblasti. Vysvětluje také, jak je řešit. Další informace o podporovaných konfiguracích najdete v tématu [matice podpory pro replikaci virtuálních počítačů Azure](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery konzistentně replikuje data ze zdrojové oblasti do oblasti zotavení po havárii a vytváří bod obnovení konzistentního vzhledem k selháním každých 5 minut. Pokud Site Recovery nemůžou vytvářet body obnovení po dobu 60 minut, upozorní vás na tyto informace:

Chybová zpráva: "Pro virtuální počítač během posledních 60 minut není k dispozici žádný bod obnovení konzistentní vzhledem k selháním."</br>
ID chyby: 153007 </br>

Následující části popisují příčiny a řešení.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Vysoká frekvence změny dat na zdrojovém virtuálním počítači
Azure Site Recovery aktivuje událost, pokud je frekvence změny dat na zdrojovém virtuálním počítači vyšší než podporované limity. Pokud chcete zjistit, jestli je problém způsobený vysokými změnami, vraťte se na **replikované položky** > události**virtuálního počítače** >  **– posledních 72 hodin**.
Měla by se zobrazit událost "rychlost změny dat nad rámec podporovaných omezení":

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Pokud vyberete událost, měli byste vidět přesné informace o disku:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Omezení Azure Site Recovery
Následující tabulka obsahuje omezení služby Azure Site Recovery. Tato omezení vycházejí z našich testů, ale nemůžou pokrýt všechny možné kombinace v/v aplikace. Skutečné výsledky se můžou lišit v závislosti na kombinaci vstupně-výstupních operací vaší aplikace. 

Existují dvě omezení, která je potřeba vzít v úvahu, četnost změn dat na disku a změny dat na virtuální počítač. Podívejme se například na disk Premium P20 v následující tabulce. Site Recovery může zpracovávat 5 MB/s změn na disk s maximálně pěti takovými disky na jeden virtuální počítač, a to kvůli limitu 25 MB/s celkové četnosti změn na virtuální počítač.

**Cíl ukládání replikace** | **Průměrná velikost vstupně-výstupních operací pro zdrojový disk** |**Průměrná četnost změn dat na zdrojovém disku** | **Celková četnost změn dat za den pro zdrojový datový disk**
---|---|---|---
Storage úrovně Standard | 8 kB | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB  | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |  336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |10 MB/s | 842 GB na disk

### <a name="solution"></a>Řešení
Azure Site Recovery má omezení četnosti změn dat na základě typu disku. Pokud chcete zjistit, jestli je tento problém opakovaný nebo chvilku, najděte rychlost změny dat ovlivněného virtuálního počítače. Přejít na zdrojový virtuální počítač, najít metriky v části **monitorování**a přidat metriky, jak je znázorněno na tomto snímku obrazovky:

![Postup tří kroků pro vyhledání frekvence změny dat](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Vyberte **Přidat metriku**a přidejte **bajty zapsané na disk s operačním systémem/s** a zapsáním **bajtů datového disku/s**.
2. Sledujte špičku, jak je znázorněno na snímku obrazovky.
3. Zobrazuje celkový počet operací zápisu prováděných na discích s operačním systémem a všechny datové disky v kombinaci. Tyto metriky vám nemusí poskytnout informace na úrovni jednotlivých disků, ale označují celkový vzor četnosti změn dat.

Pokud je špička z příležitostného nárůstu dat a frekvence změny dat je větší než 10 MB/s (v případě úrovně Premium) a 2 MB/s (v případě standardu) po nějakou dobu a nastane, replikace se zachytí. Pokud je ale četnost větší než podporovaný limit, zvažte jednu z těchto možností, pokud je to možné:

* **Vylučte disk, který způsobuje vysokou míru změny dat**: Disk můžete vyloučit pomocí [prostředí PowerShell](./azure-to-azure-exclude-disks.md). Pokud chcete vyloučit disk, musíte nejdřív zakázat replikaci. 
* **Změňte úroveň disku úložiště pro zotavení po havárii**: Tato možnost je dostupná jenom v případě, že je četnost změn dat na disku menší než 20 MB/s. Řekněme, že virtuální počítač s diskem P10 je větší než 8 MB/s, ale menší než 10 MB/s. Pokud zákazník může během ochrany použít disk P30 pro cílové úložiště, můžete problém vyřešit. Všimněte si, že toto řešení je možné jenom u počítačů, které používají prémiové Managed Disks. Postupujte podle následujících kroků:
    - Přejděte do okna disky ovlivněného replikovaného počítače a zkopírujte název disku repliky.
    - Přejít na tento spravovaný disk repliky
    - V okně Přehled se může zobrazit informační zpráva s informací o tom, že se vygenerovala adresa URL SAS. Klikněte na tuto hlavičku a zrušte export. Pokud se banner nezobrazuje, tento krok ignorujte.
    - Jakmile se adresa URL SAS odvolá, přejdete do okna konfigurace spravovaného disku a zvýšíte velikost tak, aby ASR podporovala pozorovanou četnost změn na zdrojovém disku.

## <a name="Network-connectivity-problem"></a>Problémy s připojením k síti

### <a name="network-latency-to-a-cache-storage-account"></a>Latence sítě pro účet úložiště mezipaměti
Site Recovery odesílá replikovaná data do účtu úložiště mezipaměti. Může se zobrazit latence sítě, pokud nahrávání dat z virtuálního počítače do účtu úložiště mezipaměti je pomalejší než 4 MB za 3 sekundy. 

Pokud chcete zjistit problém týkající se latence, pomocí [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) nahrajte data z virtuálního počítače do účtu úložiště mezipaměti. Pokud je latence vysoká, ověřte, jestli k řízení odchozího síťového provozu z virtuálních počítačů používáte síťové virtuální zařízení (síťové virtuální zařízení). Zařízení může být omezené, pokud se veškerý provoz replikace projde přes síťové virtuální zařízení. 

Doporučujeme vytvořit koncový bod síťové služby ve virtuální síti pro úložiště, aby provoz replikace nepřešel do síťové virtuální zařízení. Další informace najdete v tématu [Konfigurace síťového virtuálního zařízení](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Připojení k síti
U replikace Site Recovery pro práci, odchozí připojení ke konkrétní adresy URL nebo IP rozsahy se vyžaduje z virtuálního počítače. Pokud je váš virtuální počítač za bránou firewall nebo používá pravidla skupiny zabezpečení sítě (NSG) k řízení odchozího připojení, můžete se setkat s jedním z těchto problémů. Pokud chcete mít jistotu, že jsou všechny adresy URL připojené, přečtěte si téma [odchozí připojení pro adresy url Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID chyby 153006 – pro virtuální počítač není k dispozici žádný bod obnovení konzistentní vzhledem k aplikacím během posledních ' XXX ' minut

Níže jsou uvedené některé z nejběžnějších problémů.

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Příčina 1: Známý problém v systému SQL Server 2008/2008 R2 
**Jak opravit** : Došlo k známému problému s SQL serverem 2008/2008 R2. Přečtěte si prosím tento článek znalostní báze [Azure Site Recovery agent nebo jiná nekomponentová záloha služby VSS u serveru, který hostuje SQL Server 2008 R2, se nezdařil](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2) .

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Příčina 2: Azure Site Recovery úlohy selžou na serverech hostujících jakékoli verze SQL Server instancí pomocí AUTO_CLOSE databáze 
**Jak opravit** : Informace najdete v [článku](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) znalostní báze. 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Příčina 3: Známý problém v SQL Server 2016 a 2017
**Jak opravit** : Informace najdete v [článku](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) znalostní báze. 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>Příčina 4: Používáte konfiguraci prostorů úložiště s přímým přístupem
**Jak opravit** : Azure Site Recovery nemůže vytvořit bod obnovení konzistentní vzhledem k aplikacím pro konfiguraci prostorů úložiště s přímým přístupem. Pro správné [nakonfigurování zásad replikace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms) prosím přečtěte článek.

### <a name="more-causes-due-to-vss-related-issues"></a>Další příčiny v důsledku potíží souvisejících se službou VSS:

Pokud chcete problém vyřešit, Projděte si soubory na zdrojovém počítači, abyste získali přesný kód chyby pro selhání:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Jak najít chyby v souboru?
Vyhledejte řetězec "vacpError" otevřením souboru vacp. log v editoru.
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Ve výše uvedeném příkladu **2147754994** je kód chyby, který vám oznamuje selhání, jak je uvedeno níže.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Zapisovač VSS není nainstalovaný – chyba 2147221164 

*Jak opravit*: K vygenerování značky konzistence aplikace Azure Site Recovery používá službu Stínová kopie svazku (VSS) společnosti Microsoft. Nainstaluje poskytovatele služby VSS, aby jeho operace mohla provádět snímky konzistence aplikací. Tento zprostředkovatel služby Stínová kopie svazku je nainstalován jako služba. V případě, že není nainstalovaná služba poskytovatele VSS, vytvoření snímku konzistence aplikací se nepovede s ID chyby 0x80040154 "třída není zaregistrovaná". </br>
Přečtěte si [článek pro řešení potíží s instalací zapisovače VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Zapisovač VSS je zakázaný – chyba 2147943458

**Jak opravit**: K vygenerování značky konzistence aplikace Azure Site Recovery používá službu Stínová kopie svazku (VSS) společnosti Microsoft. Nainstaluje poskytovatele služby VSS, aby jeho operace mohla provádět snímky konzistence aplikací. Tento zprostředkovatel služby Stínová kopie svazku je nainstalován jako služba. V případě, že je služba poskytovatele VSS zakázaná, vytvoření snímku konzistence aplikací se nepovede s ID chyby. Zadaná služba je zakázaná a nedá se spustit (0x80070422). </br>

- Pokud je VSS zakázaný,
    - Ověřte, zda je typ spouštění služby VSS Provider nastaven na hodnotu **automaticky**.
    - Restartujte následující služby:
        - Služba Stínová kopie svazku
        - Poskytovatel služby Stínová kopie svazku Azure Site Recovery
        - Služba VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>Zprostředkovatel služby Stínová kopie svazku NOT_REGISTERED – chyba 2147754756

**Jak opravit**: K vygenerování značky konzistence aplikace Azure Site Recovery používá službu Stínová kopie svazku (VSS) společnosti Microsoft. Ověřte, jestli je nainstalovaná služba poskytovatele VSS Azure Site Recovery, nebo ne. </br>

- Opakujte instalaci poskytovatele pomocí následujících příkazů:
- Odinstalace stávajícího zprostředkovatele: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Instaluje C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Ověřte, zda je typ spouštění služby VSS Provider nastaven na hodnotu **automaticky**.
    - Restartujte následující služby:
        - Služba Stínová kopie svazku
        - Poskytovatel služby Stínová kopie svazku Azure Site Recovery
        - Služba VDS
