---
title: Matice podpory pro zálohování virtuálních počítačů Azure
description: Obsahuje souhrn nastavení podpory a omezení při zálohování virtuálních počítačích Azure se službou Azure Backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: c30a1d1b30fcc7a12449b44d35704b3b43daa385
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247901"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matice podpory pro zálohování virtuálních počítačů Azure

[Službu Azure Backup](backup-overview.md) můžete použít k zálohování místních počítačů a úloh a virtuálních počítačů (Virtuální počítače) Azure. Tento článek shrnuje nastavení a omezení podpory při zálohování virtuálních počítačích Azure pomocí azure backup.

Další podpůrné matice:

- [Matice obecné podpory](backup-support-matrix.md) pro Azure Backup
- [Matice podpory](backup-support-matrix-mabs-dpm.md) pro zálohování serveru Azure Backup/System Center Data Protection Manager (DPM)
- [Matice podpory](backup-support-matrix-mars-agent.md) pro zálohování s agentem Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Podporované scénáře

Tady je postup, jak můžete zálohovat a obnovovat virtuální počítače Azure pomocí služby Azure Backup.

**Scénář** | **Backup** | **Agent** |**Obnovení**
--- | --- | --- | ---
Přímé zálohování virtuálních počítačích Azure  | Zálohovat celý virtuální ms.  | Na virtuálním počítači Azure není potřeba žádný další agent. Azure Backup nainstaluje a použije rozšíření pro [agenta virtuálního počítače Azure,](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) který běží na virtuálním počítači. | Obnovení následujícím způsobem:<br/><br/> - **Vytvořte základní virtuální ms**. To je užitečné, pokud virtuální počítač nemá žádnou zvláštní konfiguraci, jako je například více IP adres.<br/><br/> - **Obnovte disk virtuálního počítače**. Obnovte disk. Pak jej připojte k existujícímu virtuálnímu počítače nebo vytvořte nový virtuální virtuální počítače z disku pomocí prostředí PowerShell.<br/><br/> - **Nahraďte disk virtuálního počítače**. Pokud virtuální počítač existuje a používá spravované disky (nezašifrované), můžete obnovit disk a použít jej k nahrazení existujícího disku na virtuálním počítači.<br/><br/> - **Obnovení určitých souborů nebo složek**. Soubory nebo složky z virtuálního virtuálního mě můžete obnovit místo z celého virtuálního virtuálního soudu.
Přímé zálohování virtuálních počítačů Azure (jenom windows)  | Zálohování určitých souborů/složek/svazku. | Nainstalujte [agenta služby Azure Recovery Services](backup-azure-file-folder-backup-faq.md).<br/><br/> Můžete spustit agenta MARS vedle rozšíření zálohování pro agenta virtuálního počítače Azure zálohovat virtuální počítač na úrovni souboru nebo složky. | Obnovení určitých složek nebo souborů.
Zálohování virtuálního počítače Azure na záložní server  | Zálohování souborů/složek/svazků; stav systému/holé kovové soubory; data aplikací do system center DPM nebo na Microsoft Azure Backup Server (MABS).<br/><br/> DPM/MABS pak zálohuje do záložního trezoru. | Nainstalujte agenta ochrany DPM/MABS na virtuální počítač. Agent MARS je nainstalován na DPM/MABS.| Obnovení souborů/složek/svazků; stav systému/holé kovové soubory; data aplikace.

Další informace o zálohování [pomocí záložního serveru](backup-architecture.md#architecture-back-up-to-dpmmabs) a [o požadavcích na podporu](backup-support-matrix-mabs-dpm.md).

>[!NOTE]
> **Azure Backup teď podporuje selektivní zálohování a obnovení disku pomocí řešení zálohování virtuálního počítače Azure.**
>
>Azure Backup dnes podporuje zálohování všech disků (operačního systému a dat) ve virtuálním počítači společně pomocí řešení zálohování virtuálního počítače. S funkcí vyloučit disk, získáte možnost zálohovat jeden nebo několik z mnoha datových disků ve virtuálním počítače. To poskytuje efektivní a nákladově efektivní řešení pro potřeby zálohování a obnovení. Každý bod obnovení obsahuje data disků zahrnutých do operace zálohování, což dále umožňuje obnovit podmnožinu disků z daného bodu obnovení během operace obnovení. To platí pro obnovení ze snímku a úložiště.
>
>**Chcete-li se zaregistrovat do náhledu, napište nám naAskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Podporované akce zálohování

**Akce** | **Podpora**
--- | ---
Zálohování virtuálního počítače, který je vypnutí nebo offline virtuální hosti | Podporuje se.<br/><br/> Snímek je konzistentní pouze při selhání, není konzistentní v aplikaci.
Zálohování disků po migraci na spravované disky | Podporuje se.<br/><br/> Zálohování bude i nadále fungovat. Nevyžaduje se žádná akce.
Zálohování spravovaných disků po povolení uzamčení skupiny prostředků | Není podporováno.<br/><br/> Azure Backup nemůže odstranit starší body obnovení a zálohy se spustí po dosažení maximálního limitu bodů obnovení.
Úprava zásad zálohování pro virtuální počítače | Podporuje se.<br/><br/> Virtuální počítače se zálohuje pomocí plánu a nastavení uchovávání informací v nové zásady. Pokud jsou nastavení uchovávání informací prodloužena, jsou existující body obnovení označeny a zachovány. Pokud jsou sníženy, existující body obnovení budou v yomované a nakonec odstraněny.
Zrušení úlohy zálohování| Podporováno během procesu snímku.<br/><br/> Není podporováno při přenosu snímku do úložiště.
Zálohování virtuálního virtuálního virtuálního mísy do jiné oblasti nebo předplatného |Není podporováno.
Zálohy za den (prostřednictvím rozšíření virtuálního počítače Azure) | Jedna naplánovaná záloha za den.<br/><br/>Služba Azure Backup podporuje až devět záloh na vyžádání denně, ale Microsoft doporučuje maximálně čtyři zálohy na vyžádání, aby byl zajištěn nejlepší výkon.
Zálohy za den (prostřednictvím agenta MARS) | Tři naplánované zálohy denně.
Zálohy za den (přes DPM/MABS) | Dvě naplánované zálohy denně.
Měsíční/roční zálohování| Při zálohování s rozšířením virtuálního počítače Azure není podporováno. Podporovány jsou pouze denní a týdenní.<br/><br/> Můžete nastavit zásady pro uchovávání denních/týdenních záloh pro měsíční/roční dobu uchovávání.
Automatické nastavení hodin | Není podporováno.<br/><br/> Azure Backup se při zálohování virtuálního počítače automaticky nepřizpůsobí pro změny letního času.<br/><br/>  Podle potřeby upravte zásadu ručně.
[Funkce zabezpečení pro hybridní zálohování](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Zakázání funkcí zabezpečení není podporováno.
Zálohování virtuálního počítače, jehož čas počítače se změní | Není podporováno.<br/><br/> Pokud se čas počítače změní na budoucí datum po povolení zálohování pro tento virtuální počítač; Však i v případě, že změna času je vrácena, úspěšné zálohování není zaručena.  

## <a name="operating-system-support-windows"></a>Podpora operačního systému (Windows)

Následující tabulka shrnuje podporované operační systémy při zálohování virtuálních počítačů Windows Azure.

**Scénář** | **Podpora pro Operační režim**
--- | ---
Zálohování s rozšířením agenta virtuálního počítače Azure | - Windows 10 Client (pouze 64 bitů) <br/><br/>- Windows Server 2019 (Jádro/standard datového centra) <br/><br/> - Windows Server 2016 (Datové centrum / Datové centrum Core / Standard) <br/><br/> - Windows Server 2012 R2 (Datové centrum/standard) <br/><br/> - Windows Server 2008 R2 (RTM a SP1 Standard)  <br/><br/> - Windows Server 2008 (pouze 64 bitů)
Zálohování s agentem MARS | [Podporované](backup-support-matrix-mars-agent.md#supported-operating-systems) operační systémy.
Zálohování pomocí dpm/mabs | Podporované operační systémy pro zálohování pomocí [MABS](backup-mabs-protection-matrix.md) a [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

Azure Backup nepodporuje 32bitové operační systémy.

## <a name="support-for-linux-backup"></a>Podpora zálohování Linuxu

Zde je to, co je podporováno, pokud chcete zálohovat linuxové počítače.

**Akce** | **Podpora**
--- | ---
Zálohování virtuálních počítačů Linux Azure pomocí agenta virtuálního počítače Azure pro Linux Azure | Zálohování konzistentní se soubory.<br/><br/> Zálohování konzistentní s aplikací pomocí [vlastních skriptů](backup-azure-linux-app-consistent.md).<br/><br/> Během obnovení můžete vytvořit nový virtuální počítač, obnovit disk a použít ho k vytvoření virtuálního počítače nebo obnovit disk a použít ho k nahrazení disku na existujícím virtuálním počítači. Můžete také obnovit jednotlivé soubory a složky.
Zálohování virtuálních počítačů Linux Azure pomocí agenta MARS | Není podporováno.<br/><br/> Agenta MARS lze nainstalovat pouze na počítačích se systémem Windows.
Zálohování virtuálních počítačů Linux Azure pomocí DPM/MABS | Není podporováno.

## <a name="operating-system-support-linux"></a>Podpora operačního systému (Linux)

Pro zálohy Azure VM Linux azure zálohování podporuje seznam [distribucí](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)Linuxu schválila Azure . Je třeba počítat s následujícím:

- Azure Backup nepodporuje Core OS Linux.
- Azure Backup nepodporuje 32bitové operační systémy.
- Ostatní distribuce Linuxu pro vaše vlastní mohou fungovat tak dlouho, dokud je [agent virtuálního počítače Azure pro Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) dostupný na virtuálním počítači a pokud je podporováno Python.
- Azure Backup nepodporuje virtuální počítač s Linuxem nakonfigurovaný proxy serverem, pokud nemá nainstalovaný Python verze 2.7.

## <a name="backup-frequency-and-retention"></a>Frekvence zálohování a uchovávání

**Nastavení** | **Limity**
--- | ---
Maximální počet bodů obnovení na chráněnou instanci (počítač/pracovní vytížení) | 9999.
Maximální doba expirace bodu obnovení | Bez omezení.
Maximální frekvence zálohování do trezoru (rozšíření virtuálního počítače Azure) | Jednou denně.
Maximální frekvence zálohování do trezoru (agent MARS) | Tři zálohy denně.
Maximální frekvence zálohování na DPM/MABS | Každých 15 minut pro SQL Server.<br/><br/> Jednou za hodinu pro jiné úlohy.
Uchování bodu obnovení | Denně, týdně, měsíčně a ročně.
Maximální doba uchovávání | Záleží na záložní frekvenci.
Body obnovení na disku DPM/MABS | 64 pro souborové servery a 448 pro aplikační servery.<br/><br/> Body pro obnovení pásky jsou neomezené pro místní aplikace DPM.

## <a name="supported-restore-methods"></a>Podporované metody obnovení

**Možnost obnovení** | **Podrobnosti**
--- | ---
**Vytvořte nový virtuální počítač.** | Rychle vytvoří a získá základní virtuální hod z provozu z bodu obnovení.<br/><br/> Můžete zadat název virtuálního počítače, vybrat skupinu prostředků a virtuální síť (VNet), do které bude umístěn, a určit účet úložiště pro obnovený virtuální počítač. Nový virtuální virtuální město musí být vytvořen ve stejné oblasti jako zdrojový virtuální hod.
**Obnovit disk** | Obnoví disk virtuálního počítače, který pak můžete použít k vytvoření nového virtuálního počítače.<br/><br/> Azure Backup poskytuje šablonu, která vám pomůže přizpůsobit a vytvořit virtuální počítač. <br/><br> Úloha obnovení generuje šablonu, kterou můžete stáhnout a použít k určení vlastního nastavení virtuálního počítače a k vytvoření virtuálního počítače.<br/><br/> Disky se zkopírují do zadané skupiny prostředků.<br/><br/> Případně můžete připojit disk k existujícímu virtuálnímu počítače nebo vytvořit nový virtuální počítače pomocí Prostředí PowerShell.<br/><br/> Tato možnost je užitečná, pokud chcete přizpůsobit virtuální hod, přidat nastavení konfigurace, které nebyly v době zálohování, nebo přidat nastavení, která musí být nakonfigurována pomocí šablony nebo prostředí PowerShell.
**Nahradit existujícího** | Disk můžete obnovit a použít ho k nahrazení disku na existujícím virtuálním počítači.<br/><br/> Aktuální virtuální virtuální byl konto. Pokud byla odstraněna, nelze tuto možnost použít.<br/><br/> Azure Backup pořídí snímek existujícího virtuálního počítače před nahrazením disku a uloží ho do určeného pracovního umístění. Existující disky připojené k virtuálnímu počítače se nahradí vybraným bodem obnovení.<br/><br/> Snímek je zkopírován do trezoru a zachován v souladu se zásadami uchovávání informací. <br/><br/> Po operaci nahradit disk je původní disk zachován ve skupině prostředků. Pokud nejsou potřebné, můžete je odstranit ručně. <br/><br/>Nahradit stávající je podporované pro nešifrované spravované virtuální chody. Není podporována pro nespravované disky, [generalizované virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)nebo pro virtuální počítače [vytvořené pomocí vlastních irek](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Pokud bod obnovení má více nebo méně disků než aktuální virtuální počítače, pak počet disků v bodě obnovení bude odrážet pouze konfiguraci virtuálního počítače.<br><br> Nahradit existující není podporována pro virtuální počítače s propojenými prostředky (jako [je uživatelem přiřazená spravovaná identita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nebo [trezor klíčů),](https://docs.microsoft.com/azure/key-vault/key-vault-overview)protože zálohovací klientská aplikace nemá oprávnění k těmto prostředkům při provádění obnovení.
**Křížová oblast (sekundární oblast)** | Obnovení mezi oblastmi se dá použít k obnovení virtuálních počítačů Azure v sekundární oblasti, což je [spárovaná oblast Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).<br><br> Můžete obnovit všechny virtuální počítače Azure pro vybraný bod obnovení, pokud se zálohování provádí v sekundární oblasti.<br><br> Tato funkce je k dispozici pro následující možnosti:<br> * [Vytvoření virtuálního virtuálního mísa](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Obnovit disky](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> V současné době nepodporujeme možnost [Nahradit existující disky.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Oprávnění<br> Operaci obnovení v sekundární oblasti mohou provádět správci zálohování a správci aplikací.

## <a name="support-for-file-level-restore"></a>Podpora obnovení na úrovni souborů

**Obnovení** | **Podporovány**
--- | ---
Obnovení souborů v operačních systémech | Soubory můžete obnovit v libovolném počítači, který má stejný (nebo kompatibilní) operační systém jako zálohovaný virtuální počítač. Viz [tabulka Kompatibilní operační systém](backup-azure-restore-files-from-vm.md#system-requirements).
Obnovení souborů na klasických virtuálních počítačích | Není podporováno.
Obnovení souborů ze šifrovaných virtuálních mích | Není podporováno.
Obnovení souborů z účtů úložiště s omezeným přístupem k síti | Není podporováno.
Obnovení souborů na virtuálních počítačích pomocí prostorů úložiště Windows | Obnovení není podporováno na stejném virtuálním počítači.<br/><br/> Místo toho obnovte soubory na kompatibilním virtuálním počítači.
Obnovení souborů na virtuálním počítači s Linuxem pomocí polí LVM/raid | Obnovení není podporováno na stejném virtuálním počítači.<br/><br/> Obnovení na kompatibilním virtuálním počítači.
Obnovení souborů se speciálním nastavením sítě | Obnovení není podporováno na stejném virtuálním počítači. <br/><br/> Obnovení na kompatibilním virtuálním počítači.

## <a name="support-for-vm-management"></a>Podpora pro správu virtuálních her

Následující tabulka shrnuje podporu zálohování během úloh správy virtuálních počítačů, jako je například přidání nebo nahrazení disků virtuálních počítačů.

**Obnovení** | **Podporovány**
--- | ---
Obnovení napříč odběrem/oblastí/zónou. | Není podporováno.
Obnovení existujícího virtuálního virtuálního mísy | Použijte možnost nahradit disk.
Obnovení disku s povoleným účtem úložiště pro šifrování služby Azure Storage Service (SSE) | Není podporováno.<br/><br/> Obnovte účet, který nemá povolenou funkci SSE.
Obnovení účtů smíšeného úložiště |Není podporováno.<br/><br/> Na základě typu účtu úložiště budou všechny obnovené disky buď prémiové nebo standardní, a nebudou smíšené.
Obnovení virtuálního virtuálního aplikace přímo do skupiny dostupnosti | U spravovaných disků můžete disk obnovit a použít možnost sady dostupnosti v šabloně.<br/><br/> Pro nespravované disky není podporováno. U nespravovaných disků disk obnovte a pak vytvořte virtuální hovado v sadě dostupnosti.
Obnovení zálohování nespravovaných virtuálních počítače po upgradu na spravovaný virtuální počítače| Podporuje se.<br/><br/> Můžete obnovit disky a potom vytvořit spravovaný virtuální ms.
Obnovení virtuálního počítače do bodu obnovení před migrací virtuálního počítače na spravované disky | Podporuje se.<br/><br/> Obnovení na nespravované disky (výchozí), převést obnovené disky na spravovaný disk a vytvořit virtuální hod se spravovanými disky.
Obnovení virtuálního virtuálního míse, který byl odstraněn. | Podporuje se.<br/><br/> Virtuální ho můžete obnovit z bodu obnovení.
Obnovení virtuálního počítače řadiče domény (DC), který je součástí konfigurace s více řadiči domény prostřednictvím portálu | Podporované, pokud obnovíte disk a vytvoříte virtuální hopomocí pomocí Prostředí PowerShell.
Obnovení virtuálního počítače v jiné virtuální síti |Podporuje se.<br/><br/> Virtuální síť musí být ve stejném předplatném a oblasti.

## <a name="vm-compute-support"></a>Výpočetní podpora virtuálních her

**Compute** | **Podpora**
--- | ---
Velikost virtuálního počítače |Libovolná velikost virtuálního počítače Azure s alespoň 2 jádry procesoru a 1 GB paměti RAM.<br/><br/> [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Zálohování virtuálních aplikací v [sadách dostupnosti](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Podporuje se.<br/><br/> Virtuální ho nelze obnovit v dostupné sadě pomocí možnosti rychle vytvořit virtuální ho. Místo toho při obnovení virtuálního počítače, obnovit disk a použít jej k nasazení virtuálního počítače nebo obnovit disk a použít jej k nahrazení existujícího disku.
Zálohování virtuálních zařízení, které jsou nasazené s [výhodou hybridního použití (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Podporuje se.
Zálohování virtuálních mích, které jsou nasazené ve [škálovací sadě](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Není podporováno.
Zálohování virtuálních počítačů nasazených z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publikováno společností Microsoft, třetí stranou) |Podporuje se.<br/><br/> Virtuální modul musí mít podporovaný operační systém.<br/><br/> Při obnovení souborů na virtuálním počítači, můžete obnovit pouze kompatibilní operační systém (ne starší nebo novější operační systém). Virtuální počítače Azure Marketplace, podporované jako virtuální počítače, neobnovujeme, protože ty potřebují informace o nákupu, ale jenom jako disky.
Zálohování virtuálních zařízení nasazených z vlastní image (třetí strana) |Podporuje se.<br/><br/> Virtuální modul musí mít podporovaný operační systém.<br/><br/> Při obnovení souborů na virtuálním počítači, můžete obnovit pouze kompatibilní operační systém (ne starší nebo novější operační systém).
Zálohování virtuálních počítačů, které se migrují do Azure| Podporuje se.<br/><br/> Chcete-li zálohovat virtuální počítač, musí být agent virtuálního počítače nainstalován v migrovaném počítači.
Zálohování konzistence více virtuálních mís | Azure Backup neposkytuje konzistenci dat a aplikací napříč více virtuálními počítači.
Zálohování s [diagnostickým nastavením](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Nepodporovaný. <br/><br/> Pokud se obnovení virtuálního počítače Azure s diagnostickým nastavením aktivuje pomocí [možnosti Vytvořit nový,](backup-azure-arm-restore-vms.md#create-a-vm) obnovení se nezdaří.
Obnovení virtuálních uživatelů vázaných zónou | Podporované (pro virtuální hod, který je zálohován po lednu 2019 a kde je k dispozici [zóna dostupnosti).](https://azure.microsoft.com/global-infrastructure/availability-zones/)<br/><br/>V současné době podporujeme obnovení do stejné zóny, která je připnutá ve virtuálních náskoncích. Pokud však zóna není k dispozici, obnovení se nezdaří.
Virtuální virtuální mase Gen2 | Podporuje se <br> Azure Backup podporuje zálohování a obnovení [virtuálních počítačích Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Když se tyto virtuální virtuální body obnoví z bodu obnovení, obnoví se jako [virtuální chod Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Podpora úložiště virtuálních her

**Komponenta** | **Podpora**
--- | ---
Datové disky virtuálních počítačů Azure | Záloha virtuálního počítače s 16 nebo méně datových disků.<BR> Pokud se chcete zaregistrovat k privátní verzi Preview virtuálních počítačů s víc než 16 disky (až 32 disků), napište nám na AskAzureBackupTeam@microsoft.com.
Velikost datového disku | Velikost jednotlivých disků může být až 32 TB a maximálně 256 TB v kombinaci pro všechny disky ve virtuálním počítače.
Typ úložiště | Standardní HDD, standardní SSD, Premium SSD.
Spravované disky | Podporuje se.
Šifrované disky | Podporuje se.<br/><br/> Virtuální počítače Azure povolené pomocí Azure Disk Encryption můžou být zálohované (s aplikací Azure AD nebo bez ní).<br/><br/> Šifrované virtuální aplikace nelze obnovit na úrovni souborů nebo složek. Musíte obnovit celý virtuální ms.<br/><br/> Šifrování můžete povolit na virtuálních počítačích, které už jsou chráněné službou Azure Backup.
Disky s povoleným akcelerátorem zápisu | Není podporováno.<br/><br/> Zálohování Azure automaticky vyloučí disky s povoleným akcelerátorem zápisu během zálohování. Vzhledem k tomu, že nejsou zálohovány, nebude možné obnovit tyto disky z bodů obnovení virtuálního počítače.
Zálohování & obnovení virtuálních počítačů nebo disků s odstraněním duplicit | Azure Backup nepodporuje deduplikaci. Další informace naleznete v tomto [článku](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  - Azure Backup není deduplikovat mezi virtuálními počítači v trezoru služby Recovery Services <br/> <br/>  - Pokud jsou virtuální chod ve stavu odstranění duplicit během obnovení, soubory nelze obnovit jako úložiště nerozumí formátu. Však bude možné úspěšně provést úplné obnovení virtuálního movitého virtuálního movitého virtuálního movitého virtuálního
Přidání disku do chráněného virtuálního počítače | Podporuje se.
Změna velikosti disku na chráněném virtuálním počítači | Podporuje se.
Sdílené úložiště| Zálohování virtuálních počítačů pomocí sdíleného svazku clusteru (CSV) nebo souborového serveru s horizontálním navýšením kapacity není podporováno. Zapisovače CSV pravděpodobně selžou během zálohování. Při obnovení nemusí dojít k navrácení disků obsahujících svazky CSV.
[Sdílené disky](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | Není podporováno.

## <a name="vm-network-support"></a>Podpora sítě virtuálních montovin

**Komponenta** | **Podpora**
--- | ---
Počet síťových rozhraní (NIC) | Až do maximálního počtu nic podporovaných pro konkrétní velikost virtuálního počítače Azure.<br/><br/> Síťové karty se vytvoří při vytvoření virtuálního počítače během procesu obnovení.<br/><br/> Počet nic na obnovené ms zrcadlí počet nic na virtuálním počítači, když jste povolili ochranu. Odebrání nic po povolení ochrany nemá vliv na počet.
Externí/interní systém vyrovnávání zatížení |Podporuje se. <br/><br/> [Přečtěte si další informace](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o obnovení virtuálních počítače se speciálním nastavením sítě.
Více vyhrazených ADRES IP |Podporuje se. <br/><br/> [Přečtěte si další informace](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o obnovení virtuálních počítače se speciálním nastavením sítě.
Virtuální počítače s více síťovými adaptéry| Podporuje se. <br/><br/> [Přečtěte si další informace](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o obnovení virtuálních počítače se speciálním nastavením sítě.
Virtuální virtuální společnosti s veřejnými IP adresami| Podporuje se.<br/><br/> Přidružte existující veřejnou IP adresu k nic nebo vytvořte adresu a přidružte ji k nic po dokončení obnovení.
Skupina zabezpečení sítě (NSG) v síti NIC/podsíti. |Podporuje se.
Statická IP adresa | Není podporováno.<br/><br/> Nový virtuální ms, který je vytvořen z bodu obnovení je přiřazena dynamická IP adresa.<br/><br/> U klasických virtuálních počítačů nelze zálohovat virtuální hospodařící s vyhrazenou IP adresou a bez definovaného koncového bodu.
Dynamická IP adresa |Podporuje se.<br/><br/> Pokud nic na zdrojovém virtuálním počítači používá dynamické adresování IP, ve výchozím nastavení ji použije i nic na obnoveném virtuálním počítači.
Azure Traffic Manager| Podporuje se.<br/><br/>Pokud je zálohovaný virtuální virtuální je ve Správci provozu, ručně přidejte obnovený virtuální ms do stejné instance Traffic Manageru.
Azure DNS |Podporuje se.
Vlastní DNS |Podporuje se.
Odchozí připojení přes http proxy | Podporuje se.<br/><br/> Ověřený proxy server není podporován.
Koncové body služby pro virtuální síť| Podporuje se.<br/><br/> Nastavení účtu brány firewall a virtuální sítě by mělo umožňovat přístup ze všech sítí.

## <a name="vm-security-and-encryption-support"></a>Podpora zabezpečení a šifrování virtuálních her

Azure Backup podporuje šifrování pro data v mezipřenosu a v klidovém stavu:

Síťový provoz do Azure:

- Záložní provoz ze serverů do trezoru služby Recovery Services je šifrován pomocí standardu Advanced Encryption Standard 256.
- Záložní data jsou odesílána přes zabezpečené propojení HTTPS.
- Záložní data jsou uložena v trezoru služby Recovery Services v šifrované podobě.
- Pouze vy máte přístupové heslo k odemknutí těchto dat. Společnost Microsoft nemůže kdykoli dešifrovat zálohovaná data.

  > [!WARNING]
  > Po nastavení trezoru máte přístup pouze k šifrovacímu klíči. Společnost Microsoft nikdy neudržuje kopii a nemá přístup ke klíči. Pokud je klíč chybně umístěn, společnost Microsoft nemůže obnovit záložní data.

Zabezpečení dat:

- Při zálohování virtuálních počítačů Azure je potřeba nastavit šifrování *v rámci* virtuálního počítače.
- Azure Backup podporuje Azure Disk Encryption, který používá Nástroj BitLocker na virtuálních počítačích s Windows a na virtuálních počítačích s **Linuxem.**
- Na straně back-endu Azure Backup používá [šifrování služby Azure Storage](../storage/common/storage-service-encryption.md), které chrání neaktivní uložená data.

**Počítač** | **V tranzitu** | **V klidu**
--- | --- | ---
Místní počítače se systémem Windows bez dpm/mabs | ![Ano][green] | ![Ano][green]
Virtuální počítače Azure | ![Ano][green] | ![Ano][green]
Místní/azure virtuální počítače s dpm | ![Ano][green] | ![Ano][green]
Místní/Azure virtuální počítače s MABS | ![Ano][green] | ![Ano][green]

## <a name="vm-compression-support"></a>Podpora komprese virtuálních visutonů

Zálohování podporuje kompresi záložního provozu, jak je shrnuto v následující tabulce. Je třeba počítat s následujícím:

- Pro virtuální počítače Azure rozšíření virtuálních zařízení čte data přímo z účtu úložiště Azure přes síť úložiště. Není nutné tento provoz komprimovat.
- Pokud používáte DPM nebo MABS, můžete ušetřit šířku pásma komprimací dat před zálohováním na DPM/MABS.

**Počítač** | **Komprese do MABS/DPM (TCP)** | **Komprese do trezoru (HTTPS)**
--- | --- | ---
Místní počítače se systémem Windows bez dpm/mabs | Není k dispozici | ![Ano][green]
Virtuální počítače Azure | Není k dispozici | Není k dispozici
Místní/azure virtuální počítače s dpm | ![Ano][green] | ![Ano][green]
Místní/Azure virtuální počítače s MABS | ![Ano][green] | ![Ano][green]

## <a name="next-steps"></a>Další kroky

- [Záloha virtuálních počítačích Azure](backup-azure-arm-vms-prepare.md).
- [Zálohujte počítače se systémem Windows přímo](tutorial-backup-windows-server-to-azure.md)bez záložního serveru.
- [Nastavte MABS](backup-azure-microsoft-azure-backup.md) pro zálohování do Azure a pak zálohovat úlohy mabs.
- [Nastavte dpm](backup-azure-dpm-introduction.md) pro zálohování do Azure a pak zálohujte úlohy na DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
