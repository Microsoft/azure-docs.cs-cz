---
title: Matice podpory pro zálohování virtuálních počítačů Azure
description: Poskytuje souhrn nastavení podpory a omezení při zálohování virtuálních počítačů Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 8ac2896b0b3b8c87ff993eddcebb063a66add1fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659399"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matice podpory pro zálohování virtuálních počítačů Azure

[Službu Azure Backup](backup-overview.md) můžete použít k zálohování místních počítačů a úloh a virtuálních počítačů Azure. Tento článek shrnuje nastavení a omezení podpory při zálohování virtuálních počítačů Azure pomocí Azure Backup.

Další matrice podpory:

- [Základní tabulka podpory](backup-support-matrix.md) pro Azure Backup
- [Matice podpory](backup-support-matrix-mabs-dpm.md) pro zálohování Azure Backup serveru/nástroje System Center Data Protection Manager (DPM)
- [Matice podpory](backup-support-matrix-mars-agent.md) pro zálohování s agentem Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Podporované scénáře

Tady je postup, jak můžete zálohovat a obnovovat virtuální počítače Azure pomocí služby Azure Backup.

**Scénář** | **Backup** | **Agent** |**Obnovení**
--- | --- | --- | ---
Přímé zálohování virtuálních počítačů Azure  | Zálohujte celý virtuální počítač.  | Na virtuálním počítači Azure není potřeba žádný další agent. Azure Backup nainstaluje a použije rozšíření pro [agenta virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) , který běží na virtuálním počítači. | Obnovte následujícím způsobem:<br/><br/> - **Vytvořte základní virtuální počítač**. To je užitečné v případě, že virtuální počítač nemá žádnou speciální konfiguraci, třeba více IP adres.<br/><br/> - **Obnovte disk virtuálního počítače**. Obnovte disk. Pak ho připojte k existujícímu virtuálnímu počítači nebo vytvořte nový virtuální počítač z disku pomocí PowerShellu.<br/><br/> - **Nahraďte disk virtuálního počítače**. Pokud virtuální počítač existuje a používá spravované disky (nešifrované), můžete disk obnovit a použít ho k nahrazení stávajícího disku na virtuálním počítači.<br/><br/> - **Obnovte konkrétní soubory/složky**. Můžete obnovit soubory nebo složky z virtuálního počítače místo z celého virtuálního počítače.
Přímá záloha virtuálních počítačů Azure (jenom Windows)  | Zálohujte konkrétní soubory/složky/svazky. | Nainstalujte [agenta Azure Recovery Services](backup-azure-file-folder-backup-faq.md).<br/><br/> Agenta MARS můžete spustit vedle záložního rozšíření pro agenta virtuálního počítače Azure, aby se virtuální počítač mohl zálohovat na úrovni souboru nebo složky. | Obnovte konkrétní složky nebo soubory.
Zálohování virtuálního počítače Azure do záložního serveru  | Zálohování souborů, složek a svazků; stav systému/úplné soubory; data aplikace do aplikace System Center DPM nebo do serveru Microsoft Azure Backup (MABS).<br/><br/> DPM/MABS se pak zálohuje do trezoru záloh. | Nainstalujte na virtuální počítač agenta ochrany DPM/MABS. Agent MARS je nainstalován v DPM/MABS.| Obnovit soubory/složky/svazky; stav systému/úplné soubory; data aplikací.

Přečtěte si další informace o zálohování [pomocí záložního serveru](backup-architecture.md#architecture-back-up-to-dpmmabs) a o [požadavcích na podporu](backup-support-matrix-mabs-dpm.md).

>[!NOTE]
> **Azure Backup teď podporuje zálohování a obnovení selektivního disku pomocí řešení zálohování virtuálních počítačů Azure.**
>
>V současné době Azure Backup podporuje zálohování všech disků (operačního systému a dat) na virtuálním počítači společně s využitím řešení zálohování virtuálních počítačů. Díky funkci vyloučení disku získáte možnost zálohovat jeden nebo několik datových disků ve virtuálním počítači. To poskytuje efektivní a nákladově efektivní řešení pro potřeby zálohování a obnovení. Každý bod obnovení obsahuje data disků zahrnutých v operaci zálohování, která dále umožňuje mít v průběhu operace obnovení podmnožinu disků obnovených z daného bodu obnovení. To platí pro obnovení ze snímku i z trezoru.
>
>**Pokud si chcete zaregistrovat verzi Preview, napište nám naAskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Podporované akce zálohování

**Akce** | **Podpora**
--- | ---
Zálohování virtuálního počítače, který je vypnutý nebo offline. | Podporuje se.<br/><br/> Snímek je jenom v konzistentním stavu, ne v konzistentním vzhledem k aplikacím.
Zálohování disků po migraci na spravované disky | Podporuje se.<br/><br/> Zálohování bude fungovat i nadále. Nevyžaduje se žádná akce.
Zálohování spravovaných disků po povolení zámku skupiny prostředků | Není podporováno.<br/><br/> Azure Backup nemůže odstranit starší body obnovení, a pokud dojde k dosažení maximálního limitu bodů obnovení, zálohování se spustí neúspěšné.
Úprava zásad zálohování pro virtuální počítač | Podporuje se.<br/><br/> Virtuální počítač se zálohuje pomocí nastavení plánu a uchovávání v nové zásadě. Pokud jsou nastavení uchovávání rozšířená, existující body obnovení jsou označeny a udržovány. Pokud se sníží, existující body obnovení se vyřadí do další úlohy čištění a nakonec se odstraní.
Zrušení úlohy zálohování| Podporováno během procesu snímku.<br/><br/> Nepodporováno, pokud se snímek přenáší do trezoru.
Zálohování virtuálního počítače do jiné oblasti nebo předplatného |Není podporováno.
Zálohy za den (přes rozšíření virtuálního počítače Azure) | Jedno naplánované zálohování za den.<br/><br/>Služba Azure Backup podporuje až devět záloh na vyžádání za den, ale společnost Microsoft nedoporučuje více než čtyři každodenní zálohy na vyžádání, aby bylo zajištěno nejlepší výkon.
Zálohy za den (přes agenta MARS) | Tři plánované zálohy za den.
Zálohy za den (přes DPM/MABS) | Dvě naplánované zálohy za den.
Měsíční nebo roční záloha| Při zálohování s rozšířením virtuálního počítače Azure se nepodporuje. Podporuje se jenom každodenní a týdenní.<br/><br/> Můžete nastavit zásady pro uchovávání každodenních a týdenních záloh pro měsíční a roční dobu uchování.
Automatické úpravy hodin | Není podporováno.<br/><br/> Azure Backup se při zálohování virtuálního počítače automaticky nezmění na letní čas při ukládání.<br/><br/>  Zásadu podle potřeby upravte ručně.
[Funkce zabezpečení pro hybridní zálohování](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Zákaz funkcí zabezpečení není podporován.
Zálohování virtuálního počítače, u kterého se změnil čas počítače | Není podporováno.<br/><br/> Pokud se čas počítače změní na budoucí datum a čas, po povolení zálohování pro tento virtuální počítač; Nicméně i v případě, že se změna času vrátí zpět, není zaručeno úspěšné zálohování.  

## <a name="operating-system-support-windows"></a>Podpora operačního systému (Windows)

Následující tabulka shrnuje podporované operační systémy při zálohování virtuálních počítačů s Windows Azure.

**Scénář** | **Podpora operačního systému**
--- | ---
Zálohování pomocí rozšíření agenta virtuálního počítače Azure | – Klient Windows 10 (jenom bit 64) <br/><br/>– Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> – Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> – Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> – Windows Server 2008 R2 (RTM a SP1 Standard)  <br/><br/> – Windows Server 2008 (pouze bit 64)
Zálohování pomocí agenta MARS | [Podporované](backup-support-matrix-mars-agent.md#supported-operating-systems) operační systémy.
Zálohování pomocí DPM/MABS | Podporované operační systémy pro zálohování pomocí [MABS](backup-mabs-protection-matrix.md) a [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

Azure Backup nepodporuje 32bitové operační systémy.

## <a name="support-for-linux-backup"></a>Podpora zálohování pro Linux

Co je se podporuje, pokud chcete zálohovat počítače se systémem Linux.

**Akce** | **Podpora**
--- | ---
Zálohování virtuálních počítačů Azure se systémem Linux pomocí agenta virtuálního počítače Azure pro Linux | Zálohování konzistentní se soubory.<br/><br/> Zálohování konzistentní s aplikací pomocí [vlastních skriptů](backup-azure-linux-app-consistent.md).<br/><br/> Během obnovení můžete vytvořit nový virtuální počítač, obnovit disk a použít ho k vytvoření virtuálního počítače nebo obnovení disku a použít ho k nahrazení disku na existujícím virtuálním počítači. Můžete také obnovit jednotlivé soubory a složky.
Zálohování virtuálních počítačů Linux Azure pomocí agenta MARS | Není podporováno.<br/><br/> Agenta MARS lze nainstalovat pouze do počítačů se systémem Windows.
Zálohování virtuálních počítačů se systémem Linux Azure pomocí DPM/MABS | Není podporováno.

## <a name="operating-system-support-linux"></a>Podpora operačního systému (Linux)

Pro zálohování virtuálních počítačů Azure Azure Backup podporuje seznam distribucí systému Linux, které jsou [schváleny v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Je třeba počítat s následujícím:

- Azure Backup nepodporuje základní operační systém Linux.
- Azure Backup nepodporuje 32bitové operační systémy.
- Další vlastní distribuce systému Linux mohou fungovat, pokud je ve virtuálním počítači k dispozici [Agent virtuálního počítače Azure pro Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) , a pokud je podpora jazyka Python podporovaná.
- Azure Backup nepodporuje virtuální počítač se systémem Linux nakonfigurovaný na proxy serveru, pokud nemá nainstalovanou Python verze 2,7.

## <a name="backup-frequency-and-retention"></a>Četnost a uchovávání záloh

**Nastavení** | **Omezení**
--- | ---
Maximální počet bodů obnovení na chráněnou instanci (počítač/zatížení) | 9999.
Maximální doba vypršení platnosti bodu obnovení | Bez omezení.
Maximální četnost zálohování do trezoru (rozšíření virtuálního počítače Azure) | Jednou denně.
Maximální četnost záloh do trezoru (agent MARS) | Tři zálohy za den.
Maximální četnost zálohování pro DPM/MABS | Každých 15 minut SQL Server.<br/><br/> Jednou za hodinu pro jiné úlohy.
Uchování bodu obnovení | Denně, týdně, měsíčně a ročně.
Maximální doba uchovávání | Závisí na četnosti zálohování.
Body obnovení na disku DPM/MABS | 64 pro souborové servery a 448 pro aplikační servery.<br/><br/> Body obnovení pásky jsou neomezené pro místní DPM.

## <a name="supported-restore-methods"></a>Podporované metody obnovení

**Možnost obnovení** | **Podrobnosti**
--- | ---
**Vytvořte nový virtuální počítač.** | Rychle vytvoří a načte základní virtuální počítač v bodu obnovení a spustí ho.<br/><br/> Můžete zadat název virtuálního počítače, vybrat skupinu prostředků a virtuální síť (VNet), do které se bude umístit, a zadat účet úložiště pro obnovený virtuální počítač. Nový virtuální počítač musí být vytvořený ve stejné oblasti jako zdrojový virtuální počítač.
**Obnovit disk** | Obnoví disk virtuálního počítače, který se pak dá použít k vytvoření nového virtuálního počítače.<br/><br/> Azure Backup poskytuje šablonu, která vám umožní přizpůsobit a vytvořit virtuální počítač. <br/><br> Úloha obnovení vygeneruje šablonu, kterou si můžete stáhnout a použít k určení vlastního nastavení virtuálního počítače, a vytvořit virtuální počítač.<br/><br/> Disky se zkopírují do skupiny prostředků, kterou zadáte.<br/><br/> Případně můžete disk připojit k existujícímu virtuálnímu počítači nebo vytvořit nový virtuální počítač pomocí prostředí PowerShell.<br/><br/> Tato možnost je užitečná, pokud chcete virtuální počítač přizpůsobit, přidat nastavení konfigurace, která se v době zálohování nevyskytla, nebo přidat nastavení, která se musí nakonfigurovat pomocí šablony nebo PowerShellu.
**Nahradit existujícího** | Disk můžete obnovit a použít ho k nahrazení disku na stávajícím virtuálním počítači.<br/><br/> Aktuální virtuální počítač musí existovat. Pokud je tato možnost odstraněna, nelze ji použít.<br/><br/> Před nahrazením disku Azure Backup pořizuje stávající virtuální počítač a uloží ho do pracovního umístění, které zadáte. Existující disky připojené k virtuálnímu počítači se nahradily vybraným bodem obnovení.<br/><br/> Snímek se zkopíruje do trezoru a zachová se podle zásad uchovávání informací. <br/><br/> Po operaci nahradit disk se původní disk zachová ve skupině prostředků. Pokud nepotřebujete, můžete původní disky odstranit ručně. <br/><br/>Nahradit existující se podporuje pro nešifrované spravované virtuální počítače. U nespravovaných disků, [zobecněných virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)nebo u virtuálních počítačů [vytvořených pomocí vlastních imagí](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)se nepodporuje.<br/><br/> Pokud má bod obnovení více nebo méně disků než aktuální virtuální počítač, bude počet disků v bodu obnovení odpovídat pouze konfiguraci virtuálního počítače.<br><br> Náhrada stávajících není podporovaná pro virtuální počítače s propojenými prostředky (jako je [uživatelem přiřazená identita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nebo [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)), protože klient zálohování nemá při provádění obnovení oprávnění k těmto prostředkům.
**Mezi oblastmi (sekundární oblast)** | Obnovení mezi oblastmi se dá použít k obnovení virtuálních počítačů Azure v sekundární oblasti, která je [spárované v oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).<br><br> Pokud se zálohování provádí v sekundární oblasti, můžete obnovit všechny virtuální počítače Azure pro vybraný bod obnovení.<br><br> Tato funkce je k dispozici pro následující možnosti:<br> * [Vytvoření virtuálního počítače](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Obnovit disky](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Momentálně nepodporujeme možnost [nahradit existující disky](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) .<br><br> Oprávnění<br> Operaci obnovení v sekundární oblasti můžou provádět správci zálohování a správci aplikací.

## <a name="support-for-file-level-restore"></a>Podpora pro obnovení na úrovni souborů

**Obnovení** | **Doložen**
--- | ---
Obnovení souborů mezi operačními systémy | Můžete obnovit soubory na jakémkoli počítači, který má stejný (nebo kompatibilní) operační systém jako zálohovaný virtuální počítač. Podívejte se na [tabulku kompatibilní operační systém](backup-azure-restore-files-from-vm.md#system-requirements).
Obnovování souborů z šifrovaných virtuálních počítačů | Není podporováno.
Obnovování souborů z účtů úložiště s omezením sítě | Není podporováno.
Obnovení souborů na virtuálních počítačích pomocí prostorů úložiště Windows | Obnovení není podporováno na stejném virtuálním počítači.<br/><br/> Místo toho obnovte soubory na kompatibilním virtuálním počítači.
Obnovení souborů na virtuálním počítači se systémem Linux pomocí polí LVM/RAID | Obnovení není podporováno na stejném virtuálním počítači.<br/><br/> Obnovení na kompatibilním virtuálním počítači.
Obnovení souborů se speciálním nastavením sítě | Obnovení není podporováno na stejném virtuálním počítači. <br/><br/> Obnovení na kompatibilním virtuálním počítači.

## <a name="support-for-vm-management"></a>Podpora správy virtuálních počítačů

Následující tabulka shrnuje podporu zálohování během úloh správy virtuálních počítačů, jako je například přidání nebo nahrazení disků virtuálního počítače.

**Obnovení** | **Doložen**
--- | ---
Obnova v rámci předplatného/oblasti nebo zóny. | Není podporováno.
Obnovit na existující virtuální počítač | Použijte možnost nahradit disk.
Obnovení disku s povoleným účtem úložiště pro šifrování služby Azure Storage (SSE) | Není podporováno.<br/><br/> Obnovit na účet bez povoleného SSE.
Obnovení do smíšených účtů úložiště |Není podporováno.<br/><br/> Na základě typu účtu úložiště budou všechny obnovené disky buď Premium, nebo Standard, a nejsou smíšené.
Obnovení virtuálního počítače přímo do skupiny dostupnosti | U spravovaných disků můžete obnovit disk a použít v šabloně možnost Skupina dostupnosti.<br/><br/> Nepodporuje se pro nespravované disky. U nespravovaných disků obnovte disk a pak vytvořte virtuální počítač ve skupině dostupnosti.
Obnovení zálohy nespravovaných virtuálních počítačů po upgradu na spravovaný virtuální počítač| Podporuje se.<br/><br/> Můžete obnovit disky a pak vytvořit spravovaný virtuální počítač.
Obnovení virtuálního počítače do bodu obnovení před migrací virtuálního počítače na Managed disks | Podporuje se.<br/><br/> Obnovíte na nespravované disky (výchozí), převedete obnovené disky na spravovaný disk a vytvoříte virtuální počítač se spravovanými disky.
Obnovte virtuální počítač, který byl odstraněn. | Podporuje se.<br/><br/> Virtuální počítač můžete obnovit z bodu obnovení.
Obnovení virtuálního počítače řadiče domény, který je součástí konfigurace s více řadiči domény prostřednictvím portálu | Podporováno, pokud obnovíte disk a vytvoříte virtuální počítač pomocí prostředí PowerShell.
Obnovení virtuálního počítače v jiné virtuální síti |Podporuje se.<br/><br/> Virtuální síť musí být ve stejném předplatném a oblasti.

## <a name="vm-compute-support"></a>Výpočetní podpora virtuálních počítačů

**Compute** | **Podpora**
--- | ---
Velikost virtuálního počítače |Všechny velikosti virtuálních počítačů Azure s minimálně 2 jádry procesoru a 1 GB paměti RAM.<br/><br/> [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Zálohování virtuálních počítačů ve [skupinách dostupnosti](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Podporuje se.<br/><br/> Virtuální počítač nejde obnovit v dostupné sadě pomocí možnosti pro rychlé vytvoření virtuálního počítače. Místo toho můžete při obnovení virtuálního počítače obnovit disk a použít ho k nasazení virtuálního počítače nebo obnovení disku a použít ho k nahrazení stávajícího disku.
Zálohování virtuálních počítačů nasazených s [využitím programu zvýhodněné hybridní využití (centrum)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Podporuje se.
Zálohování virtuálních počítačů, které jsou nasazené v [sadě škálování](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Není podporováno.
Zálohování virtuálních počítačů, které se nasazují z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Vydavatel: Microsoft, třetí strana) |Podporuje se.<br/><br/> Na virtuálním počítači musí běžet podporovaný operační systém.<br/><br/> Při obnovování souborů na virtuálním počítači se můžete zotavit jenom na kompatibilní operační systém (ne na starší verzi operačního systému nebo novější). Azure Marketplace virtuálních počítačů, které se zálohují jako virtuální počítače, neobnovíme, protože tyto počítače potřebují informace o nákupu, ale jenom jako disky.
Zálohování virtuálních počítačů, které se nasazují z vlastní image (třetí strana) |Podporuje se.<br/><br/> Na virtuálním počítači musí běžet podporovaný operační systém.<br/><br/> Při obnovování souborů na virtuálním počítači se můžete zotavit jenom na kompatibilní operační systém (ne na starší verzi operačního systému nebo novější).
Zálohování virtuálních počítačů, které se migrují do Azure| Podporuje se.<br/><br/> Pokud chcete zálohovat virtuální počítač, musí být na migrovaném počítači nainstalovaný agent virtuálního počítače.
Zálohování konzistence s více virtuálními počítači | Azure Backup neposkytuje konzistenci dat a aplikací napříč více virtuálními počítači.
Zálohování s [nastavením diagnostiky](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Neplatné. <br/><br/> Pokud je obnovení virtuálního počítače Azure s diagnostickým nastavením aktivované pomocí možnosti [vytvořit novou](backup-azure-arm-restore-vms.md#create-a-vm) , obnovení se nepovede.
Obnovení virtuálních počítačů připojených k zóně | Podporováno (pro virtuální počítač, který je zálohovaný po lednu 2019 a kde je dostupná [zóna dostupnosti](https://azure.microsoft.com/global-infrastructure/availability-zones/) ).<br/><br/>V současné době podporujeme obnovení do stejné zóny, která je připnuté na virtuální počítače. Pokud však zóna není k dispozici, obnovení se nezdařilo.
Virtuální počítače s Gen2 | Podporuje se <br> Azure Backup podporuje zálohování a obnovení [virtuálních počítačů s Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Když se tyto virtuální počítače obnoví z bodu obnovení, obnoví se jako [virtuální počítače Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Podpora úložiště virtuálních počítačů

**Komponenta** | **Podpora**
--- | ---
Datové disky virtuálních počítačů Azure | Podpora pro zálohování virtuálních počítačů Azure s až 32 disky je ve verzi Public Preview v [těchto oblastech](#backup-of-azure-virtual-machines-with-up-to-32-disks).<br><br> Podpora zálohování virtuálních počítačů Azure s nespravovanými disky nebo klasickými virtuálními počítači je jenom na 16 disků.
Velikost datového disku | Velikost jednotlivých disků může být až 32 TB a pro všechny disky ve virtuálním počítači je v kombinaci maximálně 256 TB.
Typ úložiště | HDD úrovně Standard, SSD úrovně Standard SSD úrovně Premium.
Spravované disky | Podporuje se.
Šifrované disky | Podporuje se.<br/><br/> Virtuální počítače Azure s povoleným Azure Disk Encryption můžou být zálohované (s aplikací Azure AD nebo bez ní).<br/><br/> Šifrované virtuální počítače nelze obnovit na úrovni souboru nebo složky. Musíte obnovit celý virtuální počítač.<br/><br/> Můžete povolit šifrování u virtuálních počítačů, které už jsou chráněné pomocí Azure Backup.
Disky s povoleným Akcelerátor zápisu | Není podporováno.<br/><br/> Azure Backup automaticky vyloučí disky s povoleným Akcelerátor zápisu (WA) při zálohování. Vzhledem k tomu, že nejsou zálohovány, nebudete moci obnovit tyto disky z bodů obnovení virtuálního počítače. <br><br> **Důležité upozornění**: virtuální počítače s disky v WA potřebují pro úspěšnou zálohu připojení k Internetu (i když se tyto disky ze zálohy vylučují.)
Zálohování & obnovení virtuálních počítačů nebo disků s odstraněnými duplicitními daty | Azure Backup nepodporuje odstranění duplicit. Další informace najdete v tomto [článku](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) . <br/> <br/>  – Azure Backup neprovádí odstranění duplicit mezi virtuálními počítači v trezoru Recovery Services. <br/> <br/>  – Pokud během obnovování dojde k virtuálním počítačům ve stavu odstranění duplicitních dat, soubory nejde obnovit, protože trezor nerozumí formátu. Kompletní obnovení virtuálního počítače ale budete moct úspěšně provést.
Přidat disk k chráněnému virtuálnímu počítači | Podporuje se.
Změna velikosti disku na chráněném virtuálním počítači | Podporuje se.
Sdílené úložiště| Zálohování virtuálních počítačů pomocí sdílený svazek clusteru (CSV) nebo Souborový server se škálováním na více systémů se nepodporuje. Při zálohování pravděpodobně dojde k chybě zapisovačů sdílených svazků clusteru. Při obnovení se nemusí nacházet disky obsahující svazky sdíleného svazku clusteru.
[Sdílené disky](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | Není podporováno.

### <a name="backup-of-azure-virtual-machines-with-up-to-32-disks"></a>Zálohování virtuálních počítačů Azure s až 32 disky

Azure Backup teď podporuje zálohování virtuálních počítačů Azure s až 32 připojenými disky.  Tato funkce je ve verzi Public Preview v Středozápadní USA.  Pokud vás zajímá Tato funkce v jiných oblastech, zaregistrujte se do verze s omezeným náhledem zapsáním do nás na adrese AskAzureBackupTeam@microsoft.com .  

>[!NOTE]
>Azure Backup podporuje až 16 disků pro virtuální počítače Azure s nespravovanými disky nebo klasickými virtuálními počítači.

## <a name="vm-network-support"></a>Podpora sítě virtuálních počítačů

**Komponenta** | **Podpora**
--- | ---
Počet síťových rozhraní (nic) | Maximální počet síťových adaptérů podporovaných pro konkrétní velikost virtuálního počítače Azure.<br/><br/> Síťové karty se vytvoří při vytvoření virtuálního počítače během procesu obnovení.<br/><br/> Počet síťových adaptérů na obnoveném virtuálním počítači zrcadlí počet síťových adaptérů na VIRTUÁLNÍm počítači, když jste povolili ochranu. Odebrání síťových karet po povolení ochrany nebude mít vliv na počet.
Externí/interní nástroj pro vyrovnávání zatížení |Podporuje se. <br/><br/> [Přečtěte si další informace](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o obnovení virtuálních počítačů se speciálním nastavením sítě.
Několik rezervovaných IP adres |Podporuje se. <br/><br/> [Přečtěte si další informace](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o obnovení virtuálních počítačů se speciálním nastavením sítě.
Virtuální počítače s několika síťovými adaptéry| Podporuje se. <br/><br/> [Přečtěte si další informace](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o obnovení virtuálních počítačů se speciálním nastavením sítě.
Virtuální počítače s veřejnými IP adresami| Podporuje se.<br/><br/> Přidružte existující veřejnou IP adresu k síťovému rozhraní nebo vytvořte adresu a přidružte ji k síťovému rozhraní po dokončení obnovení.
Skupina zabezpečení sítě (NSG) na síťové kartě nebo podsíti. |Podporuje se.
Statická IP adresa | Není podporováno.<br/><br/> K novému virtuálnímu počítači vytvořenému z bodu obnovení je přiřazena dynamická IP adresa.<br/><br/> U klasických virtuálních počítačů nemůžete zálohovat virtuální počítač pomocí rezervované IP adresy a žádného definovaného koncového bodu.
Dynamická IP adresa |Podporuje se.<br/><br/> Pokud síťová karta na zdrojovém virtuálním počítači používá dynamické přidělování IP adres, použije se ve výchozím nastavení síťové rozhraní na obnoveném virtuálním počítači.
Azure Traffic Manager| Podporuje se.<br/><br/>Pokud je zálohovaný virtuální počítač v Traffic Manager, ručně přidejte obnovený virtuální počítač do stejné instance Traffic Manager.
Azure DNS |Podporuje se.
Vlastní DNS |Podporuje se.
Odchozí připojení prostřednictvím proxy serveru HTTP | Podporuje se.<br/><br/> Ověřený proxy server není podporován.
Koncové body služby pro virtuální síť| Podporuje se.<br/><br/> Nastavení účtu brány firewall a virtuálního síťového úložiště by měla umožňovat přístup ze všech sítí.

## <a name="vm-security-and-encryption-support"></a>Podpora šifrování a zabezpečení virtuálních počítačů

Azure Backup podporuje šifrování pro data v přenosu a na REST:

Síťový provoz do Azure:

- Provoz zálohování ze serverů do trezoru Recovery Services je zašifrovaný pomocí standard AES (Advanced Encryption Standard) 256.
- Zálohovaná data se odesílají přes zabezpečené připojení HTTPS.
- Zálohovaná data jsou uložená v trezoru Recovery Services v šifrované podobě.
- K odemknutí těchto dat máte jenom přístupové heslo. Společnost Microsoft nemůže data záloh kdykoli dešifrovat.

  > [!WARNING]
  > Po nastavení trezoru máte k šifrovacímu klíči přístup jenom vy. Společnost Microsoft nikdy neudržuje kopii a nemá přístup k tomuto klíči. Pokud je klíč nesprávně umístěn, Microsoft nemůže obnovit zálohovaná data.

Zabezpečení dat:

- Při zálohování virtuálních počítačů Azure je potřeba nastavit šifrování *v rámci* virtuálního počítače.
- Azure Backup podporuje Azure Disk Encryption, která používá BitLocker na virtuálních počítačích s Windows a US **dm-crypt** na virtuálních počítačích se systémem Linux.
- Na straně back-endu Azure Backup používá [šifrování služby Azure Storage](../storage/common/storage-service-encryption.md), které chrání neaktivní uložená data.

**Počítač** | **Při přenosu** | **V klidovém umístění**
--- | --- | ---
Místní počítače s Windows bez DPM/MABS | ![Ano][green] | ![Ano][green]
Virtuální počítače Azure | ![Ano][green] | ![Ano][green]
Místní nebo virtuální počítače Azure s DPM | ![Ano][green] | ![Ano][green]
Místní nebo virtuální počítače Azure s MABS | ![Ano][green] | ![Ano][green]

## <a name="vm-compression-support"></a>Podpora komprese virtuálních počítačů

Zálohování podporuje komprimaci provozu zálohování, jak je shrnuto v následující tabulce. Je třeba počítat s následujícím:

- Pro virtuální počítače Azure načte rozšíření virtuálního počítače data přímo z účtu úložiště Azure přes síť úložiště. Tento provoz není nutné komprimovat.
- Pokud používáte DPM nebo MABS, můžete ušetřit šířku pásma komprimací dat před jejich zálohováním do DPM/MABS.

**Počítač** | **Komprimovat do MABS/DPM (TCP)** | **Komprimovat do trezoru (HTTPS)**
--- | --- | ---
Místní počítače s Windows bez DPM/MABS | NA | ![Ano][green]
Virtuální počítače Azure | NA | NA
Místní nebo virtuální počítače Azure s DPM | ![Ano][green] | ![Ano][green]
Místní nebo virtuální počítače Azure s MABS | ![Ano][green] | ![Ano][green]

## <a name="next-steps"></a>Další kroky

- [Zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md).
- [Zálohování počítačů s Windows přímo](tutorial-backup-windows-server-to-azure.md)bez záložního serveru.
- [Nastavte MABS](backup-azure-microsoft-azure-backup.md) pro zálohování do Azure a pak zálohujte úlohy na MABS.
- [Nastavte DPM](backup-azure-dpm-introduction.md) pro zálohování do Azure a pak zálohujte úlohy do DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
