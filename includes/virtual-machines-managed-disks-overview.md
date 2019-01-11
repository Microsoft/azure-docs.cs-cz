---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 403f1cee04da17086a55adfbaed28388afd24d29
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211821"
---
# <a name="azure-managed-disks-overview"></a>Přehled služby Azure Managed Disks

Služba Azure Managed Disks zjednodušuje správu disků virtuálních počítačů Azure IaaS pomocí správy [účty úložiště](../articles/storage/common/storage-introduction.md) přidružené k diskům virtuálních počítačů. Stačí jenom zadat typ ([standardní HDD](../articles/virtual-machines/windows/standard-storage.md), [SSD na úrovni Standard](../articles/virtual-machines/windows/disks-standard-ssd.md), nebo [Premium SSD](../articles/virtual-machines/windows/premium-storage.md)) a velikost disku, je nutné, a Azure vytvoří a spravuje disk za vás.

## <a name="benefits-of-managed-disks"></a>Výhody spravovaných disků

Pojďme se podívat na některé z výhod, získáte s použitím spravovaných disků, počínaje tomto videu Channel 9 [lepší odolnost virtuálních počítačů Azure se službou Managed Disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>Snadné a škálovatelné nasazení virtuálních počítačů

Managed Disks se stará o úložiště za vás na pozadí. Dřív jste museli vytvářet účty úložiště pro uložení disků (soubory VHD) pro virtuální počítače Azure. Během vertikálního navyšování kapacity, museli jste Ujistěte se, že jste vytvořili další účty úložiště, abyste neměli překročit limit vstupně-výstupních operací pro úložiště s žádným z disků. Se službou Managed Disks zpracování úložiště jste už omezení limity účtu úložiště (jako je například 20 000 IOPS účtu). Také již budete muset zkopírovat vlastní Image (soubory VHD) k několika účtům úložiště. Můžete spravovat v centrálním umístění – jeden účet úložiště podle oblasti Azure – a jejich používání při vytváření stovek virtuálních počítačů v rámci předplatného.

Spravované disky vám umožní vytvořit virtuální počítač s až 50 000 **disky** typu v předplatné a oblast, která vám umožní vytvořit tisíce **virtuálních počítačů** v rámci jednoho předplatného. Tato funkce také dále zvyšuje škálovatelnost [Virtual Machine Scale Sets](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) , neboť umožňuje vytvořit až na tisíce virtuálních počítačů ve škálovací sadu virtuálních počítačů pomocí imagí Marketplace.

### <a name="better-reliability-for-availability-sets"></a>Vyšší spolehlivost pro skupiny dostupnosti

Spravované disky zajišťuje vyšší spolehlivost pro skupiny dostupnosti tak zajistit, aby disky [virtuálních počítačů ve skupině dostupnosti](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) jsou dostatečně izolované od sebe navzájem k Vyhýbejte se jediným bodů selhání. Disky se automaticky umísťují do odlišných uložišť jednotek škálování (razítka). Pokud razítkem nezdaří z důvodu selhání hardwaru nebo softwaru, selžou pouze instance Virtuálních počítačů na těchto jednotkách. Například Řekněme, že máte aplikaci spuštěné na pět virtuálních počítačů a virtuální počítače jsou ve skupině dostupnosti. Disky pro tyto virtuální počítače nebudou všechny ukládat ve stejném razítka, tak pokud jedno razítko ocitne mimo provoz ostatních instancí aplikace i nadále spouštět.

### <a name="highly-durable-and-available"></a>Vysoká odolnost a dostupnost

Disky Azure jsou navržené pro 99,999% dostupnost. Rozhraní REST klidní s vědomím, že máte tři repliky dat, která umožňuje vysoké odolnosti. Pokud se u jedné nebo dokonce u dvou replik objeví problémy, zbývající repliky pomohou zajistit trvalost vašich dat a vysokou odolnost proti chybám. Tato architektura pomohla Azure soustavně zajišťovat odolnost pro disky IaaS na podnikové úrovni, se špičkovou NULOVOU roční chybovostí.

### <a name="granular-access-control"></a>Podrobné řízení přístupu

Můžete použít [Azure Role-Based řízení přístupu (RBAC)](../articles/role-based-access-control/overview.md) k přiřazení konkrétních oprávnění pro spravovaného disku do jednoho nebo více uživatelů. Spravované disky zpřístupňuje nejrůznějších operací, včetně čtení, zápis (vytvoření/aktualizace), odstranit a načtením [sdílený přístupový podpis (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) disku. Můžete udělit přístup jenom k operacím uživatel potřebuje pro výkon své práce. Například pokud nechcete, aby uživatel ke zkopírování spravovaného disku do účtu úložiště, můžete rozhodnete udělit přístup k exportu akce pro tento spravovaný disk. Podobně pokud nechcete, aby osoba, která použít identifikátor URI SAS ke zkopírování spravovaného disku, můžete neudělujte oprávnění, které chcete spravovaného disku.

### <a name="azure-backup-service-support"></a>Podpora služby Azure Backup

Vytváření úlohy zálohování s časovou synchronizací zálohy, snadné obnovení virtuálního počítače a zásady uchovávání záloh pomocí služby Azure Backup se službou Managed Disks. Spravované disky podporují pouze místně redundantní úložiště (LRS) jako možnost replikace. Tři kopie dat uchovávány v rámci jedné oblasti. Místní zotavení po havárii, je nutné zálohovat disky virtuálních počítačů v jiné oblasti pomocí [služba Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) a účtu úložiště GRS jako trezor služby backup. Momentálně se Azure Backup podporuje disky o velikosti až 4 TB disky, najdete v článku [rychlé obnovení](../articles/backup/backup-instant-restore-capability.md) pro podporu disků 4 TB. Další informace najdete v tématu [služby pomocí Azure Backup pro virtuální počítače se spravovanými disky](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="pricing-and-billing"></a>Ceny a fakturace

Při použití Managed Disks, platí následující aspekty fakturace:

* Typ úložiště

* Velikost disku

* Počet transakcí

* Přenosy odchozích dat

* Spravované snímky disků (kopii celého disku)

Pojďme se na ně podívat tyto možnosti.

**Typ úložiště:** Spravované disky úrovně 3 výkonu nabídky: [Standardní HDD](../articles/virtual-machines/windows/standard-storage.md), [SSD na úrovni Standard](../articles/virtual-machines/windows/disks-standard-ssd.md), a [Premium](../articles/virtual-machines/windows/premium-storage.md). Fakturace spravovaného disku závisí na jaký typ úložiště zvolíte pro disk.

**Velikost disku**: Fakturace za spravované disky závisí na velikosti zřízeného disku. Zřízená velikost (zaokrouhluje nahoru) Azure mapuje na nejbližší možnost Managed Disks uvedená v následujících tabulkách. Každý spravovaný disk mapuje na jednu z podporovaných velikostí zřízené a účtuje se odpovídajícím způsobem. Například pokud vytvoříte standardní spravovaný disk a zadejte zřízená velikost 200 GB, účtuje se vám ceníku typ s.15 disku.

Tady jsou k dispozici pro spravovaný disk úrovně premium velikosti disků, velikosti označený hvězdičkou jsou aktuálně ve verzi preview:

| **Premium SSD spravované <br>typ disku** | **P4** | **P6** | **P10** | **P15** | **P20** | **P30** | **P40** | **P50** | **P60*** | **P70*** | **P80*** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Velikost disku        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1 024 GiB (1 TiB) | 2 048 GiB (2 TiB) | 4 095 GiB (4 TiB) | 8 192 GiB (8 TiB) | 16384 giB (16 TB) | 32 767 giB (TB) |

Tady je že k dispozici pro SSD na úrovni standard velikost disku spravovaný disk, velikosti označený hvězdičkou jsou aktuálně ve verzi preview:

| **SSD na úrovni Standard Managed <br>typ disku** | **E10** | **E15** | **E20** | **E30** | **E40** | **E50** | **E60*** | **E70*** | **E80*** |
|------------------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Velikost disku        | 128 GiB | 256 GiB | 512 GiB | 1 024 GiB (1 TiB) | 2 048 GiB (2 TiB) | 4 095 GiB (4 TiB) | 8 192 GiB (8 TiB) | 16384 giB (16 TB) | 32 767 giB (TB) |

Tady je že k dispozici pro standardní HDD velikosti disků spravovaných disků, velikosti označený hvězdičkou jsou aktuálně ve verzi preview:

| **Standardní HDD spravované <br>typ disku** | **S4** | **S6** | **S10** | **S.15** | **S20** | **S30** | **S40** | **S50** | **S60*** | **S70*** | **S80*** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Velikost disku        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1 024 GiB (1 TiB) | 2 048 GiB (2 TiB) | 4 095 GiB (4 TiB) | 8 192 GiB (8 TiB) | 16384 giB (16 TB) | 32 767 giB (TB) |

**Počet transakcí**: Můžete se účtuje počet transakcí, které provedete na standardní spravovaný disk.

Standardní disky SSD pomocí jednotky v/v velikost 256KB. Pokud přenosu dat je menší než 256 KB, bude považován za 1 jednotkovou vstupně-výstupních operací. Větší velikosti vstupně-výstupní operace se počítají jako více vstupně-výstupních operací velikost 256 KB. Například 1100 KB vstupně-výstupní operace se počítá jako pět jednotky vstupně-výstupních operací.

Se neúčtují žádné poplatky za transakce pro spravovaný disk úrovně premium.

**Přenosy odchozích dat**: [Přenosy odchozích dat](https://azure.microsoft.com/pricing/details/data-transfers/) (data odcházející z Datacenter Azure) jsou zpoplatněné využití šířky pásma.

Podrobné informace o cenách pro službu Managed Disks najdete v tématu [spravované disky ceny](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Snímky spravovaného disku

Spravovaný snímek je jen pro čtení úplná kopie spravovaného disku uložená jako standardní spravovaný disk ve výchozím nastavení. Snímky můžete zálohovat spravovaných disků v libovolném bodě v čase. Tyto snímky existují nezávisle na zdrojový disk a slouží k vytvoření nové služby Managed Disks. Účtují se podle použité velikosti. Například pokud vytvoříte snímku spravovaného disku s zřízená kapacita 64 GiB a skutečné používaných dat velikosti 10 GB, snímku budou účtovat pouze používaných dat velikosti 10 GB.  

[Přírůstkových snímků](../articles/virtual-machines/windows/incremental-snapshots.md) nejsou aktuálně podporovány pro Managed Disks.

Další informace o tom, jak vytvářet snímky se službou Managed Disks, naleznete na následujících odkazech:

* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků ve Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků v Linuxu](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

## <a name="images"></a>Image

Spravované disky podporují také vytváření spravovanou vlastní image. Image můžete vytvořit z vašeho vlastního virtuálního pevného disku v účtu úložiště nebo přímo z generalizovaného virtuálního počítače (sys připravit). Tento proces zachytí do jediného image všechny spravované disky přidružené k virtuálnímu počítači, včetně disků operačního systému i data. Tuto spravovanou vlastní image umožňuje vytváření stovek virtuálních počítačů pomocí vlastní image bez nutnosti kopírovat nebo spravovat všechny účty úložiště.

Informace o vytváření bitové kopie naleznete v následujících článcích:

* [Jak zachytit spravované image zobecněného virtuálního počítače v Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Generalizace a zachytit virtuální počítač s Linuxem pomocí Azure CLI](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>Imagí a snímky

Často uvidíte slovo "image" používalo s virtuálními počítači a nyní uvidíte "snímky" také. Je důležité porozumět rozdílu mezi těmito podmínkami. Se službou Managed Disks můžete využít image generalizovaného virtuálního počítače, které bylo zrušeno. Tato image bude zahrnovat všechny disky připojené k virtuálnímu počítači. Tento obrázek můžete použít k vytvoření nového virtuálního počítače a bude zahrnovat všechny disky.

Snímek je kopie disku v bodě v čase, který se používá. Vztahuje se pouze na jeden disk. Pokud máte virtuální počítač, který má jenom jeden disk (OS), může trvat snímku nebo obrázek se a vytvoření virtuálního počítače ze snímku nebo image.

Co když virtuální počítač má pět disků a jsou rozdělené? Může pořídit snímek jednotlivých disků, ale neexistuje žádná o situaci v rámci virtuálního počítače stav disků – snímky pouze vědět, že jeden disk. V tomto případě snímky by potřeba koordinovat mezi sebou a, která se momentálně nepodporuje.

## <a name="managed-disks-and-encryption"></a>Spravované disky a šifrování

Existují dva typy šifrování fattica v souvislosti s aplikací spravované disky. První z nich je šifrování služby Storage (SSE), které se provádí pomocí služby úložiště. Druhá je Azure Disk Encryption, která můžete povolit na operační systém a datové disky pro virtuální počítače.

### <a name="storage-service-encryption-sse"></a>Šifrování služby Storage (SSE)

[Azure Storage Service Encryption](../articles/storage/common/storage-service-encryption.md) poskytuje šifrování v klidovém stavu a zabezpečit vaše data, aby splňovala závazky vaší organizace zabezpečení a dodržování předpisů z hlediska. SSE je ve výchozím nastavení povolená pro všechny spravované disky, snímky a Image ve všech oblastech, kde jsou k dispozici spravované disky. Od 10. června 2017, všechny nové spravované disky, snímky a Image a nová data zapsaná na existující spravované disky jsou automaticky šifrována – v klidovém stavu pomocí klíčů spravovaných microsoftem ve výchozím nastavení. Přejděte [stránku s nejčastějšími dotazy disky spravované](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) další podrobnosti.

### <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Azure Disk Encryption umožňuje šifrovat disky operačního systému a dat používané ve virtuální počítač IaaS. Toto šifrování zahrnuje spravované disky. Pro Windows jsou šifrované jednotky pomocí standardní technologie šifrování nástrojem BitLocker. Pro Linux disky jsou šifrované pomocí technologii DM-Crypt. Proces šifrování je integrovaná s Azure Key Vault, aby bylo možné řídit a spravovat klíče pro šifrování disků. Další informace najdete v tématu [Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem](../articles/security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>Další postup

Další informace o službě Managed Disks najdete v následujících článcích.

### <a name="get-started-with-managed-disks"></a>Začínáme se spravovanými disky

* [Vytvoření virtuálního počítače pomocí Resource Manageru a PowerShellu](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure CLI](../articles/virtual-machines/linux/quick-create-cli.md)

* [Připojení spravovaného datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Přidání spravovaného disku k virtuálnímu počítači s Linuxem](../articles/virtual-machines/linux/add-disk.md)

* [Spravované disky skripty prostředí PowerShell](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Použití spravovaných disků v šablonách Azure Resource Manageru](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Porovnejte možnosti úložiště Managed Disks

* [SSD disky úrovně Premium](../articles/virtual-machines/windows/premium-storage.md)

* [Standardní SSD a HDD disky](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>Provozní pokyny

* [Migrace z AWS a dalších platforem na Managed Disks v Azure](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Převod virtuálních počítačů Azure na managed disks v Azure](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
