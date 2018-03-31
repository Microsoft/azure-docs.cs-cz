# <a name="azure-managed-disks-overview"></a>Přehled Azure spravované disky

Disky systému Azure spravované zjednodušuje Správa disku pro virtuální počítače Azure IaaS pomocí správy [účty úložiště](../articles/storage/common/storage-introduction.md) přidružené disky virtuálních počítačů. Budete muset zadat typ ([Premium](../articles/virtual-machines/windows/premium-storage.md) nebo [standardní](../articles/virtual-machines/windows/standard-storage.md)) a velikost disku je nutné, a Azure vytváří a spravuje disku za vás.

## <a name="benefits-of-managed-disks"></a>Výhody spravovaných disků

Podívejme se na některé z výhod, získáte pomocí spravovaného disky, počínaje toto video Channel 9 [lepší odolnost virtuální počítač Azure s spravované disky](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>Jednoduché a škálovatelné nasazení virtuálního počítače.

Spravované obslužné rutiny úložiště disky pro vás na pozadí. Dřív jste museli vytvářet účty úložiště pro uložení disky (soubory VHD) pro virtuální počítače Azure. Při rozšiřování prostředků, jste museli Ujistěte se, že jste vytvořili další účty úložiště, nebylo přesáhl limit IOPS pro úložiště s žádným disky. S spravované disky zpracování úložiště, můžete se už není omezené podle limity účtu úložiště (například 20 000 IOPS / účet). Také již budete muset zkopírovat vlastních bitových kopií (soubory VHD) k několika účtům úložiště. Můžete spravovat v centrálním umístění – jeden účet úložiště podle oblasti Azure – a pomocí nich vytvořte stovky virtuálních počítačů v předplatném.

Spravované disky vám umožní vytvořit až 10 000 virtuálních počítačů **disky** v odběru, který vám umožní vytvořit tisíce **virtuální počítače** v rámci jednoho předplatného. Tato funkce také další zvyšuje škálovatelnost [virtuální počítač škálování sady (VMSS)](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) tím, že se vám umožní vytvořit až na tisíce virtuálních počítačů v VMSS, pomocí image pořízenou prostřednictvím Marketplace.

### <a name="better-reliability-for-availability-sets"></a>Vyšší spolehlivost pro skupiny dostupnosti

Spravované disky zajišťuje vyšší spolehlivost pro skupiny dostupnosti zajištěním, který na discích v [virtuální počítače ve skupině dostupnosti](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) jsou dostatečně od sebe navzájem oddělené k Vyhýbejte se jediným bodů selhání. Dělá to tak, že automaticky disky jednotek škálování jiného úložiště (razítka). Pokud se razítko (stamp) se nezdaří z důvodu selhání hardwaru nebo softwaru, pouze instance virtuálních počítačů s disky na těchto razítka se nezdaří. Předpokládejme například že máte aplikaci běžící na virtuálních počítačích pět a virtuální počítače jsou ve skupině dostupnosti. Disky pro tyto virtuální počítače všechny neuloží do stejné značce, takže pokud jedno razítko přestane fungovat, ostatní instance aplikace dál běžet.

### <a name="highly-durable-and-available"></a>Vysoká odolnost a dostupnost

Disky Azure jsou navržené pro 99,999% dostupnost. REST snadnější zároveň budete vědět, že máte tři repliky vaše data, která umožňuje vysokou odolnost. Pokud se u jedné nebo dokonce u dvou replik objeví problémy, zbývající repliky pomohou zajistit trvalost vašich dat a vysokou odolnost proti chybám. Tato architektura pomohla Azure soustavně zajišťovat odolnost pro disky IaaS na podnikové úrovni, se špičkovou NULOVOU roční chybovostí. 

### <a name="granular-access-control"></a>Řízení přístupu granulární

Můžete použít [řízení řízení přístupu (RBAC)](../articles/active-directory/role-based-access-control-what-is.md) přiřadit konkrétní oprávnění pro spravovaných disků na jeden nebo více uživatelů. Spravované disky zpřístupňuje a různé operace, včetně čtení, zápisu (vytvořit nebo aktualizovat), odstranění a načítání [sdílený přístupový podpis (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) disku. Můžete udělit přístup k pouze operace, které uživatel potřebuje provést jeho úlohy. Například pokud nechcete, aby uživatel ke zkopírování spravovaných disků na účet úložiště, můžete není k udělení přístupu k exportu akce pro tohoto spravovaného disku. Podobně pokud nechcete, aby uživatel použít identifikátor URI SAS ke zkopírování se spravovaným diskem, můžete neudělujte oprávnění, které chcete spravovaného disku.

### <a name="azure-backup-service-support"></a>Podporu služby Azure Backup
Pomocí služby zálohování Azure s spravované disky vytvářet úlohy zálohování se zálohy založené na čase, snadno obnovení virtuálních počítačů a zásady uchovávání záloh. Spravované disky podporují pouze místně redundantní úložiště (LRS) jako možnost replikace; To znamená, že udržuje tři kopie dat v jedné oblasti. Místní zotavení po havárii, je nutné zálohovat vaše disky virtuálních počítačů v jiné oblasti pomocí [služba Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) a účet úložiště GRS jako úložiště záloh. Azure Backup podporuje datový disk aktuálně velikostí až 1TB pro zálohování. Další informace o to v [služby pomocí zálohování Azure pro virtuální počítače s spravované disky](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="pricing-and-billing"></a>Ceny a fakturace

Při použití spravovaných disků, platí následující fakturace aspekty:
* Typ úložiště

* Velikost disku

* Počet transakcí

* Přenosy odchozích dat

* Spravovaný Disk snímků (kopie celého disku)

Podívejme bližší pohled na tyto.

**Typ úložiště:** spravované disky nabízí 2 úrovně výkonu: [Premium](../articles/virtual-machines/windows/premium-storage.md) (založené na jednotku SSD) a [standardní](../articles/virtual-machines/windows/standard-storage.md) (založené na HDD). Fakturace spravovaného disku závisí na typu úložiště, kterou jste vybrali pro disk.


**Kapacita disku**: fakturace pro spravované disky závisí na velikost zřízeného disku. Azure mapuje velikost zřízeného (zaokrouhlený nahoru) na nejbližší možnost spravovat disky uvedeného v následujících tabulkách. Spravovaných disků na každý se mapuje na jednu z podporovaných zřízené velikosti a se fakturuje odpovídajícím způsobem. Například pokud vytvoříte standardní se spravovaným diskem a zadejte velikost zřízeného 200 GB, můžete se účtují podle cenové typ S20 disku.

Zde jsou k dispozici pro premium se spravovaným diskem velikosti disků:

| **Premium spravované <br>typ disku** | **P4** | **P6** |**P10** | **P15** | **P20** | **P30** | **P40** | **P50** | 
|------------------|---------|---------|---------|---------|---------|----------------|----------------|----------------|  
| Velikost disku        | 32 GiB   | 64 GiB   | 128 GiB  | 256 GiB  | 512 GiB  | 1024 GiB (1 TiB) | 2048 GiB (2 TiB) | 4095 GiB (4 TiB) | 


Zde jsou k dispozici pro standardní se spravovaným diskem velikosti disků:

| **Standardní spravované <br>typ disku** | **S4** | **S6** | **S10** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|----------------|----------------|----------------| 
| Velikost disku        | 32 GiB   | 64 GiB   | 128 GiB | 512 GiB | 1024 GiB (1 TiB) | 2048 GiB (2 TiB) | 4095 GiB (4 TiB) | 


**Počet transakcí**: fakturuje se počet transakcí, které můžete provádět na standardní spravovaného disku. Neexistuje žádné náklady pro transakce pro premium se spravovaným diskem.

**Odchozí přenosy dat**: [odchozí přenosy dat](https://azure.microsoft.com/pricing/details/data-transfers/) (data přejdete mimo datových center Azure) jsou zpoplatněné podle využití šířky pásma.

Podrobné informace o cenách pro spravované disky, najdete v části [spravované ceny disků](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Spravovaný Disk úrovně snímky

Spravované snímek je jen pro čtení úplnou kopii spravovaným diskem, který je uložený jako standardní spravovaných disků ve výchozím nastavení. Snímky můžete zálohovat vaše spravované disky v libovolném bodě v čase. Tyto snímky existují nezávisle na zdrojový disk a slouží k vytvoření nových disků spravované. Budou se účtují podle použitá velikost. Například pokud vytvoříte snímek se spravovaným diskem s zřízená kapacita 64 GiB a skutečná data použité velikosti 10 GiB, bude účtován snímku pouze pro velikost dat používaných 10 GiB.  

[Přírůstkové snímky](../articles/virtual-machines/windows/incremental-snapshots.md) nejsou aktuálně podporovány pro spravované disky, ale bude podporovaná v budoucnu.

Další informace o tom, jak vytvářet snímky s spravované disky, podrobnosti naleznete v těchto prostředků:

* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků ve Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků v Linuxu](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)


## <a name="images"></a>Image

Spravované disky také podporují vytváření spravované vlastní image. Můžete vytvořit bitovou kopii z vašeho vlastního virtuálního pevného disku v účtu úložiště, nebo přímo z zobecněný virtuální počítač (sys prepped). To zaznamená do jediného image všechny spravované disků přidružených virtuálních počítačů, včetně disků operačního systému i data. To umožňuje vytváření stovky virtuálních počítačů pomocí vlastní image bez nutnosti kopírování nebo spravovat všechny účty úložiště.

Informace o vytváření bitových kopií najdete v následujících článcích:
* [Jak zachytit bitovou kopii spravované zobecněný virtuálního počítače v Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Postup generalize a zachytit virtuální počítač s Linuxem pomocí Azure CLI 2.0](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>Bitové kopie a snímky

Často uvidíte slovo "image" používalo s virtuálními počítači a nyní se zobrazí "snímky" také. Je důležité si uvědomit rozdíl mezi tyto. S spravované disky můžete využít image zobecněný virtuální počítač, který bylo zrušeno. Tento image bude obsahovat všechny disky připojené k virtuálnímu počítači. Tuto bitovou kopii můžete použít k vytvoření nového virtuálního počítače a bude zahrnovat všechny disky.

Snímek je kopii disku v bodě v čase, kdy je odebrán. Platí jenom pro jeden disk. Pokud máte virtuální počítač, který má jenom jeden disk (OS), můžete provést snímek nebo bitovou kopii je a vytvoření virtuálního počítače ze snímku nebo bitovou kopii.

Co když virtuální počítač má pět disků a že jsou rozdělené? Můžete zachytit snímek jednotlivých disků, ale neexistuje žádné informace o tom, v rámci virtuálního počítače stav disky – snímky pouze vědět o jeden disk. V takovém případě by musí být vzájemně koordinovat snímky a který se aktuálně nepodporuje.

## <a name="managed-disks-and-encryption"></a>Spravované disky a šifrování

Existují dva typy šifrování, které popisují v souvislosti s aplikací spravovaných disky. První z nich je šifrování služby úložiště (SSE), která se provádí pomocí služby úložiště. Druhá je Azure Disk Encryption, která můžete povolit na discích operačního systému a dat pro virtuální počítače.

### <a name="storage-service-encryption-sse"></a>Šifrování služby úložiště (SSE)

[Azure šifrování služby úložiště](../articles/storage/common/storage-service-encryption.md) poskytuje šifrování na rest a ochranu dat, aby splňovaly vaše organizace závazky zabezpečení a dodržování předpisů. SSE je ve výchozím nastavení povolena pro všechny spravované disky, snímky a obrázků ve všech oblastech, kde je k dispozici spravované disky. Od 10. června 2017, všechny nové spravované disky, snímky nebo obrázky a nová data k existující spravovaná diskům, jsou automaticky šifrovat na rest klíče spravované microsoftem ve výchozím nastavení. Můžete se rozhodnout, přineste si vlastní klíč k šifrování pro soubory a objektů BLOB služby Azure. Šifrování pro tabulky a fronty bude vždy používat Microsoft spravované klíče.
Pamatujte, že po povolení šifrování služby úložiště, bude šifrovat jenom nová data, a případné existující soubory v rámci tohoto účtu úložiště bude získat zpětně zašifrovaný šifrování procesem na pozadí. Přejděte [stránka spravované nejčastější dotazy disky](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) další podrobnosti.


### <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Azure Disk Encryption umožňuje šifrování operačního systému a datové disky, které jsou použité podle se IaaS virtuální počítač. To zahrnuje spravovaných disky. Pro systém Windows jsou šifrované jednotky pomocí standardních technologií šifrování nástroje BitLocker. Pro systémy Linux jsou disky šifrované pomocí technologie DM-Crypt. Toto jsou integrované s Azure Key Vault umožňují řídit a spravovat disku šifrovací klíče. Další informace najdete v tématu [Azure Disk Encryption pro systém Windows a virtuálních počítačů IaaS Linux](../articles/security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>Další postup

Další informace o spravovaných disků naleznete v následujících článcích.

### <a name="get-started-with-managed-disks"></a>Začínáme se spravovanými disky

* [Vytvoření virtuálního počítače pomocí Resource Manageru a PowerShellu](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure CLI 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

* [Připojit spravované datový disk pro virtuální počítač s Windows pomocí prostředí PowerShell](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Přidání spravovaného disku k virtuálnímu počítači s Linuxem](../articles/virtual-machines/linux/add-disk.md)

* [Spravované disky prostředí PowerShell ukázkové skripty](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Používat spravované disky v šablonách Azure Resource Manager](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Porovnání možností úložiště spravované disky

* [Storage úrovně Premium a disků](../articles/virtual-machines/windows/premium-storage.md)

* [Standardní úložiště a disků](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>Provozní pokyny

* [Migrace z AWS a jiné platformy na spravované disky v Azure](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Převod virtuálních počítačích Azure spravovaných disků v Azure](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
