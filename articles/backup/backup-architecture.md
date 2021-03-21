---
title: Přehled architektury
description: Poskytuje přehled architektury, komponent a procesů, které používá služba Azure Backup.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 1e5a61bd4e3287c1100ff1f54fda797c1add438b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466407"
---
# <a name="azure-backup-architecture-and-components"></a>Architektura Azure Backup a součásti

[Službu Azure Backup](backup-overview.md) můžete použít k zálohování dat na cloudovou platformu Microsoft Azure. Tento článek shrnuje Azure Backup architekturu, komponent a procesů.

## <a name="what-does-azure-backup-do"></a>Co Azure Backup udělat?

Azure Backup zálohují data, stav počítače a úlohy spuštěné na místních počítačích a instancích virtuálních počítačů Azure. Existuje několik scénářů Azure Backup.

## <a name="how-does-azure-backup-work"></a>Jak Azure Backup funguje?

Počítače a data můžete zálohovat pomocí několika metod:

- **Zálohování místních počítačů**:
  - Místní počítače s Windows můžete zálohovat přímo do Azure pomocí agenta Azure Backup Microsoft Azure Recovery Services (MARS). Počítače se systémem Linux nejsou podporovány.
  - Místní počítače můžete zálohovat na záložní server – buď System Center Data Protection Manager (DPM) nebo server Microsoft Azure Backup (MABS). Záložní server pak můžete zálohovat do trezoru Recovery Services v Azure.

- **Zálohování virtuálních počítačů Azure**:
  - Virtuální počítače Azure můžete zálohovat přímo. Azure Backup nainstaluje záložní rozšíření agenta virtuálního počítače Azure, který běží na virtuálním počítači. Toto rozšíření zálohuje celý virtuální počítač.
  - Konkrétní soubory a složky můžete na virtuálním počítači Azure zálohovat spuštěním agenta MARS.
  - Virtuální počítače Azure můžete zálohovat na MABS, která běží v Azure, a pak můžete MABS zálohovat do trezoru Recovery Services.

Přečtěte si další informace o [tom, co můžete zálohovat](backup-overview.md) , a o [podporovaných scénářích zálohování](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Kde se data zálohují?

Azure Backup ukládá zálohovaná data v trezorech – Recovery Services trezory a trezory služby Backup. Trezor je online entita v Azure, která se používá k ukládání dat, jako jsou záložní kopie, body obnovení a zásady zálohování.

Trezory mají následující funkce:

- Trezory usnadňují uspořádání zálohovaných dat a současně minimalizují nároky na správu.
- Zálohované položky můžete monitorovat v trezoru, včetně virtuálních počítačů Azure a místních počítačů.
- Přístup k trezoru můžete spravovat pomocí [řízení přístupu na základě role Azure (RBAC)](../role-based-access-control/role-assignments-portal.md).
- Určíte, jak se data v trezoru replikují pro redundanci:
  - **Místně redundantní úložiště (LRS)**: Pokud chcete chránit před selháním v datacentru, můžete použít LRS. LRS replikuje data do jednotky škálování úložiště. [Další informace](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **Geograficky redundantní úložiště (GRS)**: Pokud chcete chránit před výpadky v rámci oblastí, můžete použít GRS. GRS replikuje vaše data do sekundární oblasti. [Další informace](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - **Zóna – redundantní úložiště (ZRS)**: replikuje vaše data do [zón dostupnosti](../availability-zones/az-overview.md#availability-zones)a zaručuje jejich započet a odolnost dat ve stejné oblasti. [Další informace](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Ve výchozím nastavení používají trezory Recovery Services GRS.

Recovery Services trezory mají následující další funkce:

- V každém předplatném Azure můžete vytvořit až 500 trezorů.

## <a name="backup-agents"></a>Agenti zálohování

Azure Backup poskytuje různé agenty zálohování v závislosti na tom, jaký typ počítače se zálohuje:

**Agenta** | **Podrobnosti**
--- | ---
**Agent MARS** | <ul><li>Spouští se na jednotlivých místních počítačích Windows serveru pro zálohování souborů, složek a stavu systému.</li> <li>Spouští se na virtuálních počítačích Azure pro zálohování souborů, složek a stavu systému.</li> <li>Spouští na serverech DPM nebo MABS k zálohování místního úložiště aplikace DPM/MABS do Azure.</li></ul>
**Rozšíření virtuálního počítače Azure** | Spustí se na virtuálních počítačích Azure, které je zálohují do trezoru.

## <a name="backup-types"></a>Typy zálohování

Následující tabulka popisuje různé typy zálohování a jejich použití:

**Typ zálohování** | **Podrobnosti** | **Použití**
--- | --- | ---
**Do bloku** | Úplná záloha obsahuje celý zdroj dat. Trvá větší šířku pásma sítě než rozdílové nebo přírůstkové zálohy. | Slouží k prvotnímu zálohování.
**Diferenciál** |  Rozdílové zálohování ukládá bloky, které se od počátečního úplného zálohování změnily. Používá menší množství sítě a úložiště a neuchovává redundantní kopie nezměněných dat.<br/><br/> Neefektivní vzhledem k tomu, že se přenesou a ukládají datové bloky nezměněné mezi novějšími zálohami. | Nepoužívá se Azure Backup.
**Přírůstkový** | Přírůstkové zálohování ukládá pouze bloky dat, které se od předchozí zálohy změnily. Vysoká efektivita úložiště a sítě. <br/><br/> Pomocí přírůstkového zálohování není nutné doplňovat s úplnými zálohami. | Používá se aplikací DPM/MABS pro zálohování disku a používá se ve všech zálohách do Azure. Nepoužívá se pro zálohování SQL Server.

## <a name="sql-server-backup-types"></a>SQL Server typy zálohování

Následující tabulka popisuje různé typy záloh používaných pro SQL Server databáze a četnost jejich používání:

**Typ zálohování** | **Podrobnosti** | **Použití**
--- | --- | ---
**Úplné zálohování** | Úplná záloha databáze zálohuje celou databázi. Obsahuje všechna data v konkrétní databázi nebo v sadě skupin souborů nebo souborů. Úplné zálohování také obsahuje dostatek protokolů pro obnovení těchto dat. | Maximálně můžete aktivovat jednu úplnou zálohu denně.<br/><br/> Můžete si zvolit, že chcete vytvořit úplnou zálohu na denní nebo týdenní interval.
**Rozdílové zálohování** | Rozdílová záloha vychází z poslední předchozí zálohy na základě úplného zálohování dat.<br/><br/> Zachycuje jenom data, která se od úplného zálohování změnila. |  Maximálně můžete aktivovat jedno rozdílové zálohování za den.<br/><br/> V jednom dni nemůžete nakonfigurovat úplnou zálohu a rozdílovou zálohu.
**Zálohování protokolu transakcí** | Zálohování protokolu umožňuje obnovení k určitému bodu v čase až na určitou sekundu. | V tuto chvíli můžete nakonfigurovat zálohování transakčních protokolů každých 15 minut.

### <a name="comparison-of-backup-types"></a>Porovnání typů zálohování

Spotřeba úložiště, plánovaná doba obnovení (RTO) a spotřeba sítě se u každého typu zálohování liší. Následující obrázek znázorňuje porovnání typů zálohování:

- Zdroj dat A se skládá z 10 bloků úložiště a1 – A10, které se zálohují měsíčně.
- Bloky A2, A3, A4 a A9 se mění první měsíc a blok A5 se mění následující měsíc.
- U rozdílových záloh se v druhém měsíci zálohují bloky a2, a3, A4 a buňce 3. Třetí měsíc se znovu zálohují tyto stejné bloky, společně se změněným blokem A5. Změněné bloky se budou zálohovat až do doby, kdy dojde k dalšímu úplnému zálohování.
- V případě přírůstkových záloh jsou v druhý měsíc bloky a2, a3, A4 a buňce označeny jako změněné a přenesené. Třetí měsíc se označí a přenese pouze změněný blok A5.

![Obrázek znázorňující porovnání metod zálohování](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funkce zálohování

Následující tabulka shrnuje podporované funkce pro různé typy zálohování:

**Funkce** | **Přímé zálohování souborů a složek (pomocí agenta MARS)** | **Zálohování virtuálních počítačů Azure** | **Počítače nebo aplikace s DPM/MABS**
--- | --- | --- | ---
Zálohování do trezoru | ![Yes][green] | ![Yes][green] | ![Yes][green]
Zálohování na disk DPM/MABS, potom do Azure | | | ![Yes][green]
Komprimovat data odesílaná k zálohování | ![Yes][green] | Při přenosu dat se nepoužívá žádná komprese. Úložiště je mírně nepatrné, ale obnovení je rychlejší.  | ![Yes][green]
Spustit přírůstkové zálohování |![Yes][green] |![Yes][green] |![Yes][green]
Zálohování disků s odstraněnými duplicitními daty | | | ![Částečně][yellow]<br/><br/> Jenom pro servery DPM/MABS nasazené místně.

![Klíč tabulky](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Základy zásad zálohování

- Zásady zálohování se vytvoří pro každý trezor.
- Zásady zálohování se dají vytvořit pro zálohování následujících úloh: virtuální počítače Azure, SQL ve virtuálních počítačích Azure, SAP HANA ve virtuálních počítačích Azure a sdílených složkách Azure. Zásada pro zálohování souborů a složek pomocí agenta MARS je uvedena v konzole MARS.
  - Sdílená složka Azure
- Zásady je možné přiřadit k mnoha prostředkům. Zásady zálohování virtuálních počítačů Azure je možné použít k ochraně mnoha virtuálních počítačů Azure.
- Zásada se skládá ze dvou součástí
  - Plán: kdy se má provést zálohování
  - Uchovávání informací: pro dobu, po kterou by se měly uchovávat zálohy.
- Plán lze definovat jako "denní" nebo "týdně" s konkrétním časovým bodem.
- Uchovávání informací lze definovat pro "denní", "týdenní", "měsíční", "roční" body zálohování.
  - "týdně" odkazuje na zálohu v určitý den v týdnu
  - "měsíční" odkazuje na zálohu v určitý den v měsíci
  - "roční" odkazuje na zálohu v určitý den v roce
- Doba uchovávání "měsíčně", "ročních" bodů zálohy se označuje jako dlouhodobá doba uchování (LTR).
- Při vytvoření trezoru se vytvoří také "DefaultPolicy" a můžete ho použít k zálohování prostředků.
- Všechny změny provedené v době uchování zásady zálohování se použijí zpět na všechny starší body obnovení z nových.

### <a name="impact-of-policy-change-on-recovery-points"></a>Dopad změny zásad na body obnovení

- **Doba uchování se zvýšila nebo snížila:** Při změně doby uchování se nová doba uchování použije i na existující body obnovení. V důsledku toho se vyčistí některé body obnovení. Pokud se prodlouží doba uchovávání, budou mít existující body obnovení také zvýšené uchovávání.
- **Změněno z denní na týdně:** Když se naplánované zálohy změní z každodenního na týdně, vyčistí se stávající denní body obnovení.
- **Změněno z týdně na denní:** Stávající týdenní zálohy se uchovávají na základě počtu dní zbývajících v závislosti na aktuálních zásadách uchovávání informací.

### <a name="additional-reference"></a>Další referenční informace

- Počítač Azure VM: jak [vytvářet](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) a [upravovat](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) zásady.
- SQL Server databáze na virtuálním počítači Azure: jak [vytvářet](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) a [upravovat](./manage-monitor-sql-database-backup.md#modify-policy) zásady.
- Sdílená složka Azure: jak [vytvářet](./backup-afs.md) a [upravovat](./manage-afs-backup.md#modify-policy) zásady.
- SAP HANA: jak [vytvářet](./backup-azure-sap-hana-database.md#create-a-backup-policy) a [upravovat](./sap-hana-db-manage.md#change-policy) zásady.
- MARS: jak [vytvářet](./backup-windows-with-mars-agent.md#create-a-backup-policy) a [upravovat](./backup-azure-manage-mars.md#modify-a-backup-policy) zásady.
- [Existují nějaká omezení pro plánování zálohování na základě typu úlohy?](./backup-azure-backup-faq.md#are-there-limits-on-backup-scheduling)
- [Co se stane s existujícími body obnovení, když změním zásady uchovávání informací?](./backup-azure-backup-faq.md#what-happens-when-i-change-my-backup-policy)

## <a name="architecture-built-in-azure-vm-backup"></a>Architektura: Integrovaná záloha virtuálního počítače Azure

1. Když povolíte zálohování pro virtuální počítač Azure, zálohování se spustí podle plánu, který zadáte.
1. Při prvním zálohování se na virtuálním počítači nainstaluje rozšíření zálohování, pokud je virtuální počítač spuštěný.
    - U virtuálních počítačů s Windows se nainstaluje rozšíření VMSnapshot.
    - Pro virtuální počítače se systémem Linux se nainstaluje rozšíření VMSnapshot Linux.
1. Rozšíření používá snímek na úrovni úložiště.
    - Pro virtuální počítače s Windows, na kterých běží, se zaregistrují služby Windows služba Stínová kopie svazku (VSS), aby vybraly snímek konzistentní vzhledem k aplikacím virtuálního počítače. Ve výchozím nastavení provádí zálohování úplné zálohy VSS. Pokud zálohování nedokáže vytvořit snímek konzistentní vzhledem k aplikacím, převezme snímek konzistentní se souborem.
    - Pro virtuální počítače se systémem Linux aplikace Backup provede snímek konzistentní se souborem. U snímků konzistentních vzhledem k aplikacím je nutné ručně přizpůsobit skripty před/po.
    - Zálohování je optimalizované zálohováním jednotlivých disků virtuálních počítačů paralelně. U každého zálohovaného disku Azure Backup načte bloky na disku a uloží jenom změněná data.
1. Po pořízení snímku se data přenesou do trezoru.
    - Zkopírovány jsou pouze bloky dat, které se od posledního zálohování změnily.
    - Data nejsou šifrovaná. Azure Backup můžou zálohovat virtuální počítače Azure, které se šifrují pomocí Azure Disk Encryption.
    - Data snímku se nemusí do trezoru zkopírovat okamžitě. V časech špičky může zálohování trvat několik hodin. Celková doba zálohování virtuálního počítače bude u zásad denního zálohování menší než 24 hodin.
1. Po odeslání dat do trezoru se vytvoří bod obnovení. Ve výchozím nastavení se snímky uchovávají po dobu dvou dnů, než se odstraní. Tato funkce umožňuje operaci obnovení z těchto snímků, takže vystřihuje časy obnovení. Zkracuje dobu potřebnou k transformaci a zkopírování dat zpět z trezoru. Informace najdete v tématu [Azure Backup možnosti okamžitého obnovení](./backup-instant-restore-capability.md).

Nemusíte explicitně povolit připojení k Internetu pro zálohování virtuálních počítačů Azure.

![Zálohování virtuálních počítačů Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektura: přímé zálohování místních počítačů s Windows serverem nebo souborů nebo složek virtuálních počítačů Azure

1. Pokud chcete nastavit scénář, Stáhněte a nainstalujte na počítači agenta MARS. Pak můžete vybrat, co se má zálohovat, kdy se zálohování spustí a jak dlouho se budou uchovávat v Azure.
1. Počáteční zálohování se spouští podle nastavení zálohování.
1. Agent MARS používá službu Stínová kopie svazku k provedení snímků vybraných pro zálohování v čase.
    - Agent MARS používá pouze operaci zápisu systému Windows k zachycení snímku.
    - Vzhledem k tomu, že agent nepoužívá žádné aplikace zapisovače služby VSS, nezachycuje snímky konzistentní vzhledem k aplikacím.
1. Po pořízení snímku pomocí VSS vytvoří agent MARS virtuální pevný disk (VHD) ve složce mezipaměti, kterou jste zadali při konfiguraci zálohování. Agent také ukládá kontrolní součty pro každý blok dat. Tyto změny se použijí později ke zjištění změněných bloků pro následné přírůstkové zálohování.
1. Přírůstkové zálohování se spouští podle plánu, který zadáte, Pokud nespustíte zálohování na vyžádání.
1. V přírůstkových zálohách se identifikují změněné soubory a vytvoří se nový virtuální pevný disk. Virtuální pevný disk je komprimovaný a zašifrovaný a pak se pošle do trezoru.
1. Po dokončení přírůstkového zálohování se nový virtuální pevný disk sloučí s virtuálním pevným diskem vytvořeným po počáteční replikaci. Tento sloučený virtuální pevný disk poskytuje nejnovější stav, který se má použít pro porovnání probíhajícího zálohování.

![Zálohování místních počítačů s Windows serverem s agentem MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektura: zálohování do DPM/MABS

1. Agenta ochrany DPM nebo MABS nainstalujete na počítače, které chcete chránit. Pak přidáte počítače do skupiny ochrany aplikace DPM.
    - K ochraně místních počítačů se musí server DPM nebo MABS nacházet místně.
    - Pro ochranu virtuálních počítačů Azure musí být server MABS umístěný v Azure, který běží jako virtuální počítač Azure.
    - Pomocí aplikace DPM/MABS můžete chránit záložní svazky, sdílené složky, soubory a složky. Můžete také chránit stav systému počítače (holý počítač) a můžete chránit konkrétní aplikace pomocí nastavení zálohování s podporou aplikací.
1. Když nastavíte ochranu pro počítač nebo aplikaci v DPM/MABS, vyberete možnost zálohovat na místní disk MABS/DPM pro krátkodobé ukládání a do Azure pro online ochranu. Také určíte, kdy se má spustit zálohování do místního úložiště DPM/MABS a kdy se má spustit online zálohování do Azure.
1. Disk chráněné úlohy se zálohuje na místní disky MABS/DPM podle plánu, který jste zadali.
1. DPM/MABS disky se zálohují do trezoru agentem MARS, který běží na serveru DPM nebo MABS.

![Zálohování počítačů a úloh chráněných aplikací DPM nebo MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Úložiště virtuálních počítačů Azure

Virtuální počítače Azure využívají disky k ukládání svých operačních systémů, aplikací a dat. Každý virtuální počítač Azure má aspoň dva disky: disk pro operační systém a dočasný disk. Virtuální počítače Azure mohou mít také datové disky pro data aplikací. Disky se ukládají jako VHD.

- Virtuální pevné disky se ukládají jako objekty blob stránky v účtech úložiště Standard nebo Premium v Azure:
  - **Úložiště úrovně Standard:** Spolehlivá podpora disků s nízkými náklady pro virtuální počítače, na kterých běží úlohy, které nejsou citlivé na latenci. Služba Storage úrovně Standard může používat standardní disky SSD (Solid-State Drive) nebo standardní disky pevného disku (HDD).
  - **Premium Storage:** Vysoce výkonná podpora disků. Využívá disky SSD úrovně Premium.
- Pro disky jsou k dispozici různé úrovně výkonu:
  - **HDD úrovně Standard disk:** Zajištěno HDD a slouží k nákladově efektivnímu úložišti.
  - **SSD úrovně Standard disk:** Kombinuje prvky disků SSD úrovně Premium a standardní disky HDD. Nabízí spolehlivější výkon a spolehlivost než HDD, ale stále nákladově efektivní.
  - **SSD úrovně Premium disk:** Je zajištěná SSD a poskytuje vysoce výkonná a nízká latenci pro virtuální počítače, na kterých běží úlohy náročné na vstupně-výstupní operace.
- Disky je možné spravovat nebo nespravované:
  - **Nespravované disky:** Tradiční typ disků využívaných virtuálními počítači. Pro tyto disky vytvoříte vlastní účet úložiště a určíte ho při vytváření disku. Pak budete muset zjistit, jak maximalizovat prostředky úložiště pro vaše virtuální počítače.
  - **Spravované disky:** Azure vytvoří a spravuje účty úložiště za vás. Zadáte velikost disku a úroveň výkonu a Azure pro vás vytvoří spravované disky. Při přidávání disků a škálování virtuálních počítačů Azure zpracovává účty úložiště.

Další informace o diskovém úložišti a dostupných typech disků pro virtuální počítače najdete v těchto článcích:

- [Azure Managed disks pro virtuální počítače se systémem Linux](../virtual-machines/managed-disks-overview.md)
- [Dostupné typy disků pro virtuální počítače](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Zálohování a obnovení virtuálních počítačů Azure pomocí služby Premium Storage

Virtuální počítače Azure můžete zálohovat pomocí služby Premium Storage s Azure Backup:

- Během zálohování virtuálních počítačů pomocí služby Premium Storage vytvoří služba Backup v účtu úložiště dočasné pracovní umístění s názvem *AzureBackup-*. Velikost pracovního umístění se rovná velikosti snímku bodu obnovení.
- Ujistěte se, že účet Premium Storage má dostatek volného místa pro dočasné pracovní umístění. Další informace najdete v tématu [cíle škálovatelnosti pro účty úložiště objektů blob stránky úrovně Premium](../storage/blobs/scalability-targets-premium-page-blobs.md). Neupravujte pracovní umístění.
- Po dokončení úlohy zálohování se pracovní umístění odstraní.
- Cena za úložiště využitá pro pracovní umístění je konzistentní s [cenami služby Premium Storage](../virtual-machines/disks-types.md#billing).

Při obnovení virtuálních počítačů Azure pomocí služby Premium Storage je můžete obnovit do úložiště úrovně Premium nebo Standard. Obvykle byste je měli obnovit do Premium Storage. Pokud ale potřebujete jenom podmnožinu souborů z virtuálního počítače, může být cenově výhodnější obnovit úložiště do úrovně Standard.

### <a name="back-up-and-restore-managed-disks"></a>Zálohování a obnovení spravovaných disků

Virtuální počítače Azure můžete zálohovat pomocí spravovaných disků:

- Virtuální počítače se spravovanými disky zálohujete stejným způsobem jako ostatní virtuální počítače Azure. VIRTUÁLNÍ počítač můžete zálohovat přímo z nastavení virtuálního počítače nebo můžete povolit zálohování virtuálních počítačů v trezoru Recovery Services.
- Virtuální počítače na spravovaných discích můžete zálohovat prostřednictvím kolekcí RestorePoint postavených na spravovaných discích.
- Azure Backup podporuje taky zálohování virtuálních počítačů se spravovanými disky, které se šifrují pomocí Azure Disk Encryption.

Po obnovení virtuálních počítačů se spravovanými disky můžete provést obnovení do kompletního virtuálního počítače se spravovanými disky nebo s účtem úložiště:

- Během procesu obnovení Azure zpracovává spravované disky. Pokud používáte možnost účet úložiště, můžete spravovat účet úložiště, který se vytvořil během procesu obnovení.
- Pokud obnovíte spravovaný virtuální počítač, který je zašifrovaný, ujistěte se, že klíče a tajné klíče virtuálního počítače v trezoru klíčů existují a teprve potom spusťte proces obnovení.

## <a name="next-steps"></a>Další kroky

- [Další informace o podporovaných funkcích a omezeních pro scénáře zálohování](backup-support-matrix.md)najdete v matici podpory.
- Nastavte zálohu pro jeden z těchto scénářů:
  - [Zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md).
  - [Zálohování počítačů s Windows přímo](tutorial-backup-windows-server-to-azure.md)bez záložního serveru.
  - [Nastavte MABS](backup-azure-microsoft-azure-backup.md) pro zálohování do Azure a pak zálohujte úlohy na MABS.
  - [Nastavte DPM](backup-azure-dpm-introduction.md) pro zálohování do Azure a pak zálohujte úlohy do DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
