---
title: Migrace virtuálních počítačů do úložiště Azure Premium | Dokumenty společnosti Microsoft
description: Migrujte stávající virtuální počítače do úložiště Azure Premium. Úložiště Premium storage nabízí vysoce výkonnou podporu disku s nízkou latencí pro úlohy náročné na vstupně-výstupní služby spuštěné na virtuálních počítačích Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 7cb5a335af7093bc217578d57340b03b8b9c08b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748341"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrace do úložiště Azure Premium (nespravované disky)

> [!NOTE]
> Tento článek popisuje, jak migrovat virtuální hod, který používá nespravované standardní disky na virtuální počítače, který používá nespravované disky premium. Doporučujeme používat spravované disky Azure pro nové virtuální počítače a převést předchozí nespravované disky na spravované disky. Spravované disky zpracovávají základní účty úložiště, takže nemusíte. Další informace naleznete v našem [přehledu spravovaných disků](../../virtual-machines/windows/managed-disks-overview.md).
>

Azure Premium Storage poskytuje vysoce výkonnou diskovou podporu s nízkou latencí pro virtuální počítače s úlohami náročnými na vstupně-výstupní jednotky. Rychlost a výkon těchto disků můžete využít migrací disků virtuálních počítačů vaší aplikace do úložiště Azure Premium.

Účelem této příručky je pomoci novým uživatelům azure premium storage lépe připravit na hladký přechod z jejich aktuálního systému na úložiště Premium. Průvodce řeší tři klíčové součásti v tomto procesu:

* [Plánování migrace do úložiště Premium](#plan-the-migration-to-premium-storage)
* [Příprava a kopírování virtuálních pevných disků (VD) do úložiště Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Vytvoření virtuálního počítače Azure pomocí úložiště Premium Storage](#create-azure-virtual-machine-using-premium-storage)

Můžete buď migrovat virtuální počítače z jiných platforem do Azure Premium Storage, nebo migrovat stávající virtuální počítače Azure ze standardního úložiště do úložiště Premium. Tato příručka popisuje kroky pro oba dva scénáře. Postupujte podle kroků uvedených v příslušné části v závislosti na scénáři.

> [!NOTE]
> Přehled funkcí a ceny prémiových disků SSD najdete v [části: Vyberte typ disku pro virtuální počítače IaaS](../../virtual-machines/windows/disks-types.md#premium-ssd). Doporučujeme migrovat jakýkoli disk virtuálního počítače, který vyžaduje vysoké viopy, do úložiště Azure Premium pro nejlepší výkon pro vaši aplikaci. Pokud disk nevyžaduje vysoké viposlužby, můžete omezit náklady jeho údržbou ve standardním úložišti, které ukládá data disku virtuálního počítače na pevné disky (HDD) namísto disků SSD.
>

Dokončení procesu migrace v plném rozsahu může vyžadovat další akce před i po krocích uvedených v této příručce. Mezi příklady patří konfigurace virtuálních sítí nebo koncových bodů nebo provádění změn kódu v rámci samotné aplikace, které mohou vyžadovat určité prostoje ve vaší aplikaci. Tyto akce jsou jedinečné pro každou aplikaci a měli byste je dokončit spolu s kroky uvedenými v této příručce, aby byl úplný přechod na úložiště Premium co nejplynulejší.

## <a name="plan-for-the-migration-to-premium-storage"></a><a name="plan-the-migration-to-premium-storage"></a>Plánování přechodu do úložiště Premium
Tato část zajišťuje, že jste připraveni postupovat podle kroků migrace v tomto článku a pomůže vám učinit nejlepší rozhodnutí o typech virtuálních počítačů a disků.

### <a name="prerequisites"></a>Požadavky
* Budete potřebovat předplatné Azure. Pokud ho nemáte, můžete si vytvořit měsíční [bezplatné zkušební](https://azure.microsoft.com/pricing/free-trial/) předplatné nebo navštívit [Azure Pricing](https://azure.microsoft.com/pricing/) a pro další možnosti.
* Ke spuštění rutin prostředí PowerShell budete potřebovat modul Microsoft Azure PowerShell. Bod instalace a pokyny pro instalaci jsou popsané v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).
* Když plánujete používat virtuální počítače Azure spuštěné na úložišti Premium, musíte použít virtuální počítače s podporou úložiště Premium Storage. Disky úložiště Standard i Premium můžete používat s virtuálními zařízeními s podporou úložiště Premium. Disky úložiště Premium budou v budoucnu dostupné s dalšími typy virtuálních počítačů. Další informace o všech dostupných typech a velikostech disků virtuálních počítačů Azure najdete v [tématu Velikosti virtuálních počítačů](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [velikosti pro cloudové služby](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Požadavky
Virtuální počítač Azure podporuje připojení několika disků úložiště Premium, takže vaše aplikace můžou mít až 256 TB úložiště na jeden virtuální počítač. S úložištěm Premium storage můžou vaše aplikace dosáhnout 80 000 VOPS (vstupní ch odpočitacích operací za sekundu) na virtuální počítač a 2000 MB za sekundu propustnost disku na virtuální počítač s extrémně nízkou latencí pro operace čtení. Máte možnosti v různých virtuálních počítačů a disků. Tato část vám pomůže najít možnost, která nejlépe vyhovuje vaší pracovní zátěži.

#### <a name="vm-sizes"></a>Velikost virtuálních počítačů
Specifikace velikosti virtuálního počítače Azure jsou uvedeny v [části Velikosti pro virtuální počítače](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Zkontrolujte charakteristiky výkonu virtuálních počítačů, které fungují s úložištěm Premium, a zvolte nejvhodnější velikost virtuálních počítačů, která nejlépe vyhovuje vaší pracovní zátěži. Ujistěte se, že je k dispozici dostatečná šířka pásma na vašem virtuálním počítači řídit provoz na disku.

#### <a name="disk-sizes"></a>Velikosti disků
Existuje pět typů disků, které lze použít s virtuálním počítačem a každý má konkrétní iOP a omezení propustnost. Vezměte v úvahu tato omezení při výběru typu disku pro váš virtuální počítač na základě potřeb vaší aplikace z hlediska kapacity, výkonu, škálovatelnosti a zatížení ve špičce.

| Typ prémiových disků  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Velikost disku           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| Vstupně-výstupní operace za sekundu / disk       | 500   | 2300  | 5000           | 7 500           | 7 500           | 
| Propustnost / disk | 100 MB za sekundu | 150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu |

V závislosti na zatížení určete, jestli jsou pro váš virtuální počítač potřeba další datové disky. K virtuálnímu počítači můžete připojit několik trvalých datových disků. V případě potřeby můžete promítnout přes disky a zvýšit tak kapacitu a výkon svazku. (Podívejte se, co je disk prokládání [zde](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Pokud prokládáte datové disky úložiště Premium pomocí [prostorů úložiště][4], měli byste je nakonfigurovat jedním sloupcem pro každý použitý disk. V opačném případě může být celkový výkon proklápěného svazku nižší, než bylo očekáváno z důvodu nerovnoměrného rozložení provozu na discích. Pro virtuální počítače s Linuxem můžete použít nástroj *mdadm* k dosažení stejného. Podrobnosti naleznete v článku [Konfigurace softwarového raidu na Linuxu.](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

#### <a name="storage-account-scalability-targets"></a>Cíle škálovatelnosti účtu úložiště

Účty úložiště Premium mají následující cíle škálovatelnosti. Pokud vaše požadavky na aplikace překračují cíle škálovatelnosti jednoho účtu úložiště, vytvořte aplikaci tak, aby používala více účtů úložiště, a rozdělte data mezi tyto účty úložiště.

| Celková kapacita účtu | Celková šířka pásma pro místně redundantní účet úložiště |
|:--- |:--- |
| Kapacita disku: 35 TB<br />Kapacita snímku: 10 TB |Až 50 gigabitů za sekundu pro příchozí + odchozí |

Další informace o specifikacích úložiště Premium najdete v tématu [Cíle škálovatelnosti pro účty úložiště objektů blob stránky premium](../blobs/scalability-targets-premium-page-blobs.md).

#### <a name="disk-caching-policy"></a>Zásady ukládání disku do mezipaměti
Ve výchozím nastavení je zásada ukládání disku do mezipaměti *jen pro čtení* pro všechny datové disky Premium a pro čtení a *zápis* pro disk operačního systému Premium připojený k virtuálnímu počítače. Toto nastavení konfigurace se doporučuje k dosažení optimálního výkonu pro vos vaší aplikace. Pro datové disky náročné na zápis nebo jen pro zápis (například soubory protokolu serveru SQL Server) zakažte ukládání disků do mezipaměti, abyste mohli dosáhnout lepšího výkonu aplikací. Nastavení mezipaměti pro existující datové disky lze aktualizovat pomocí [portálu Azure](https://portal.azure.com) nebo parametru *-HostCaching* rutiny *Set-AzureDataDisk.*

#### <a name="location"></a>Umístění
Vyberte umístění, kde je azure premium storage k dispozici. Aktuální informace o dostupných umístěních najdete [v tématu Služby Azure podle oblastí.](https://azure.microsoft.com/regions/#services) Virtuální počítače umístěné ve stejné oblasti jako účet úložiště, který ukládá disky pro virtuální počítače bude dávat mnohem lepší výkon, než když jsou v samostatných oblastech.

#### <a name="other-azure-vm-configuration-settings"></a>Další nastavení konfigurace virtuálního počítače Azure
Při vytváření virtuálního počítače Azure budete vyzváni ke konfiguraci určitých nastavení virtuálních počítačových společností. Nezapomeňte, že několik nastavení jsou pevné po dobu životnosti virtuálního počítače, zatímco můžete upravit nebo přidat další později. Zkontrolujte tato nastavení konfigurace virtuálního počítače Azure a ujistěte se, že jsou nakonfigurované odpovídajícím způsobem tak, aby odpovídaly vašim požadavkům na úlohy.

### <a name="optimization"></a>Optimalizace
[Azure Premium Storage: Návrh pro vysoký výkon](../../virtual-machines/windows/premium-storage-performance.md) poskytuje pokyny pro vytváření vysoce výkonných aplikací pomocí azure premium storage. Můžete postupovat podle pokynů v kombinaci s osvědčenými postupy výkonu, které se vztahují na technologie používané vaší aplikací.

## <a name="prepare-and-copy-virtual-hard-disks-vhds-to-premium-storage"></a><a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Příprava a kopírování virtuálních pevných disků (VD) do úložiště Premium
V následující části jsou uvedeny pokyny pro přípravu virtuálních dispon ů z virtuálního počítače a kopírování virtuálních dispon do Azure Storage.

* [Scénář 1: "Migruji existující virtuální počítače Azure do úložiště Azure Premium."](#scenario1)
* [Scénář 2: "Migruji virtuální počítače z jiných platforem do úložiště Azure Premium."](#scenario2)

### <a name="prerequisites"></a>Požadavky
Chcete-li připravit virtuální disponály na migraci, budete potřebovat:

* Předplatné Azure, účet úložiště a kontejner v tomto účtu úložiště, do kterého můžete zkopírovat virtuální pevný disk. Všimněte si, že účet cílového úložiště může být účet standardu nebo úložiště Premium v závislosti na vašem požadavku.
* Nástroj pro generalizaci virtuálního pevného disku, pokud z něj plánujete vytvořit více instancí virtuálního počítače. Například sysprep pro Windows nebo virt-sysprep pro Ubuntu.
* Nástroj pro nahrání souboru VHD do účtu úložiště. Viz [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md) nebo použijte průzkumník úložiště [Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Tato příručka popisuje kopírování virtuálního pevného disku pomocí nástroje AzCopy.

> [!NOTE]
> Pokud zvolíte možnost synchronní kopírování s AzCopy, pro optimální výkon, zkopírujte virtuální pevný disk spuštěním jednoho z těchto nástrojů z virtuálního počítače Azure, který je ve stejné oblasti jako cílový účet úložiště. Pokud kopírujete virtuální pevný disk z virtuálního počítače Azure v jiné oblasti, může být váš výkon pomalejší.
>
> Při kopírování velkého množství dat přes omezenou šířku pásma zvažte [použití služby Import a export Azure k přenosu dat do úložiště objektů Blob](../storage-import-export-service.md); To vám umožní přenést data odesláním pevných disků do datového centra Azure. Službu Import a export Azure můžete použít ke kopírování dat jenom do standardního účtu úložiště. Jakmile jsou data ve vašem účtu úložiště standardu, můžete k přenosu dat do účtu úložiště premium použít rozhraní [BLOB API](https://msdn.microsoft.com/library/azure/dd894037.aspx) nebo AzCopy.
>
> Všimněte si, že Microsoft Azure podporuje pouze soubory VHD s pevnou velikostí. Soubory VHDX nebo dynamické virtuální pevné disky nejsou podporovány. Pokud máte dynamický virtuální pevný disk, můžete jej převést na pevnou velikost pomocí rutiny [Převést VHD.](https://technet.microsoft.com/library/hh848454.aspx)
>
>

### <a name="scenario-1-i-am-migrating-existing-azure-vms-to-azure-premium-storage"></a><a name="scenario1"></a>Scénář 1: "Migruji existující virtuální počítače Azure do úložiště Azure Premium."
Pokud migrujete existující virtuální počítače Azure, zastavte virtuální počítač, připravte virtuální pevné disky podle požadovaného typu virtuálního pevného disku a pak zkopírujte virtuální pevný disk pomocí AzCopy nebo PowerShellu.

Virtuální ho virtuálního ms musí být úplně dole, aby migroval y čistý stav. Bude existovat prostoje, dokud migrace neskončí.

#### <a name="step-1-prepare-vhds-for-migration"></a>Krok 1. Příprava virtuálních discích na migraci
Pokud migrujete existující virtuální počítače Azure do úložiště Premium, může být váš virtuální pevný disk:

* Obrázek zobecněný operační systém
* Jedinečný disk operačního systému
* Datový disk

Níže procházíme tyto 3 scénáře pro přípravu virtuálního pevného disku.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Vytvoření více instancí virtuálních počítačích pomocí generalizovaného virtuálního disku operačního systému
Pokud nahráváte virtuální pevný disk, který se použije k vytvoření více obecných instancí virtuálních počítačích Azure, musíte nejprve zobecnit virtuální pevný disk pomocí nástroje sysprep. To platí pro virtuální pevný disk, který je místní nebo v cloudu. Sysprep odebere všechny informace specifické pro počítač z virtuálního pevného disku.

> [!IMPORTANT]
> Pořízení snímku nebo zálohování virtuálního počítače před jeho generalizací. Spuštění sysprep zastaví a navrátit instanci virtuálního soudu. Postupujte podle následujících kroků k vytvoření programu sysprep virtuálního pevného disku operačního systému Windows. Všimněte si, že spuštění příkazu Sysprep bude vyžadovat vypnutí virtuálního počítače. Další informace o sysprepu naleznete v [tématu Sysprep Overview](https://technet.microsoft.com/library/hh825209.aspx) nebo [Sysprep Technical Reference](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Otevřete okno příkazového řádku jako správce.
2. Chcete-li otevřít sysprep, zadejte následující příkaz:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. V nástroji pro přípravu systému vyberte Možnost zadat prostředí mimo systém (OOBE), zaškrtněte políčko Generalizovat, vyberte **Vypnout**a pak klepněte na **OK**, jak je znázorněno na obrázku níže. Program Sysprep zobecní operační systém a vypne systém.

    ![][1]

Pro virtuální ms Ubuntu použijte virt-sysprep k dosažení stejného. Viz [virt-sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) pro více informací. Viz také některé z open source [Linux Server Zřizování software](https://www.cyberciti.biz/tips/server-provisioning-software.html) pro jiné operační systémy Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Vytvoření jedné instance virtuálního virtuálního virtuálního serveru pomocí jedinečného virtuálního pevného disku operačního systému
Pokud máte aplikaci spuštěnou na virtuálním počítači, která vyžaduje data specifická pro počítač, nezobecnit virtuální pevný disk. Negeneralizovaný virtuální pevný disk slouží k vytvoření jedinečné instance virtuálního počítače Azure. Pokud máte například řadič domény ve vašem virtuálním pevném disku, provedení sysprepu způsobí, že bude jako řadič domény neúčinný. Zkontrolujte aplikace spuštěné na vašem virtuálním počítači a dopad spuštění sysprep na ně před generalizací virtuálního pevného disku.

##### <a name="register-data-disk-vhd"></a>Registrace datového disku VHD
Pokud máte datové disky v Azure, které mají být migrovány, musíte se ujistit, že virtuální počítače, které používají tyto datové disky jsou vypnuté.

Podle následujících kroků zkopírujte virtuální pevný disk do úložiště Azure Premium a zaregistrujte ho jako zřízený datový disk.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Vytvoření cíle pro váš Virtuální pevný disk
Vytvořte si účet úložiště pro údržbu virtuálních discích. Při plánování, kam uložit virtuální počítač, zvažte následující body:

* Cílový účet úložiště Premium.
* Umístění účtu úložiště musí být stejné jako virtuální počítače Azure s podporou úložiště Premium Storage, které vytvoříte v konečné fázi. Můžete zkopírovat do nového účtu úložiště nebo chcete na základě vašich potřeb použít stejný účet úložiště.
* Zkopírujte a uložte klíč účtu úložiště cílového účtu úložiště pro další fázi.

U datových disků můžete některé datové disky uchovávat ve standardním účtu úložiště (například disky, které mají chladnější úložiště), ale důrazně doporučujeme přesunout všechna data pro produkční úlohy, abyste mohli používat úložiště premium.

#### <a name="step-3-copy-vhd-with-azcopy-or-powershell"></a><a name="copy-vhd-with-azcopy-or-powershell"></a>Krok 3: Kopírování virtuálního pevného disku pomocí azcopy nebo prostředí PowerShell
Budete muset najít cestu kontejneru a klíč účtu úložiště ke zpracování jedné z těchto dvou možností. Cesta kontejneru a klíč účtu úložiště najdete v **úložišti Azure Portal** > **Storage**. Adresa URL kontejneru bude\/jako "https: /myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Možnost 1: Kopírování virtuálního pevného disku pomocí azcopy (asynchronní kopie)

Pomocí AzCopy můžete snadno nahrát Virtuální pevný disk přes internet. V závislosti na velikosti VHDto, to může nějakou dobu trvat. Nezapomeňte zkontrolovat limity příchozího přenosu dat/odchozího přenosu dat při použití této možnosti. Podrobnosti najdete [v tématu Škálovatelnost a výkonnostní cíle pro standardní účty úložiště.](scalability-targets-standard-account.md)

1. Stáhněte si a nainstalujte AzCopy zde: [Nejnovější verze AzCopy](https://aka.ms/downloadazcopy)
2. Otevřete Azure PowerShell a přejděte do složky, kde je nainstalován AzCopy.
3. Pomocí následujícího příkazu zkopírujte soubor VHD ze zdroje do "Cíl".

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Příklad:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```
 
   Zde jsou popisy parametrů použitých v příkazu AzCopy:

   * **/Source:** _ &lt;&gt;zdroj:_ Umístění adresy URL složky nebo kontejneru úložiště, která obsahuje virtuální pevný disk.
   * **/SourceKey:** _ &lt;&gt;zdroj-účet-klíč :_ Klíč účtu úložiště zdrojového účtu úložiště.
   * **/Dest:** _ &lt;&gt;cíl :_ Adresa URL kontejneru úložiště pro kopírování virtuálního pevného disku.
   * **/DestKey:** _ &lt;dest-account-key&gt;:_ Klíč účtu úložiště cílového účtu úložiště.
   * **/Pattern:** _ &lt;název&gt;souboru :_ Zadejte název souboru virtuálního pevného disku ke kopírování.

Podrobnosti o používání nástroje AzCopy viz [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Možnost 2: Kopírování virtuálního pevného disku pomocí prostředí PowerShell (synchronizovaná kopie)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Soubor VHD můžete také zkopírovat pomocí rutiny PowerShell Start-AzStorageBlobCopy. Ke kopírování virtuálního disku pomocí následujícího příkazu v Prostředí Azure PowerShell. Nahraďte hodnoty v <> odpovídajícími hodnotami z účtu zdrojového a cílového úložiště. Chcete-li použít tento příkaz, musíte mít kontejner s názvem vhds v účtu cílového úložiště. Pokud kontejner neexistuje, vytvořte ho před spuštěním příkazu.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Příklad:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario-2-i-am-migrating-vms-from-other-platforms-to-azure-premium-storage"></a><a name="scenario2"></a>Scénář 2: "Migruji virtuální počítače z jiných platforem do úložiště Azure Premium."
Pokud migrujete virtuální pevný disk z cloudového úložiště jiného než Azure do Azure, musíte nejprve exportovat virtuální pevný disk do místního adresáře. Měj se po ruce úplnou zdrojovou cestu k místnímu adresáři, kde je virtuální pevný disk uložený, a pak ho pomocí AzCopy nahrajte do Služby Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Krok 1. Export virtuálního pevného disku do místního adresáře
##### <a name="copy-a-vhd-from-aws"></a>Kopírování virtuálního pevného disku z AWS
1. Pokud používáte AWS, exportujte instanci EC2 do virtuálního pevného disku v kontejneru Amazon S3. Postupujte podle kroků popsaných v dokumentaci Amazon pro export Amazon EC2 instance nainstalovat amazonec 2 příkazového řádku rozhraní (CLI) nástroj a spusťte příkaz vytvořit instance export-úkol exportovat ec2 instance do souboru VHD. Při spuštění příkazu **create-instance-export-task** použijte **virtuální pevný disk** pro proměnnou DISK&#95;IMAGE&#95;FORMAT. Exportovaný soubor VHD je uložen v kbelíku Amazon S3, který určíte během tohoto procesu.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Stáhněte si soubor VHD z kbelíku S3. Vyberte soubor VHD a potom **položku Akce** > **stáhnout**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Kopírování virtuálního pevného disku z jiného cloudu mimo Azure
Pokud migrujete virtuální pevný disk z cloudového úložiště jiného než Azure do Azure, musíte nejprve exportovat virtuální pevný disk do místního adresáře. Zkopírujte úplnou zdrojovou cestu k místnímu adresáři, ve kterém je uložen virtuální pevný disk.

##### <a name="copy-a-vhd-from-on-premises"></a>Kopírování virtuálního pevného disku z místního prostředí
Pokud migrujete virtuální pevný disk z místního prostředí, budete potřebovat úplnou zdrojovou cestu, ve které je uložen virtuální pevný disk. Zdrojovou cestou může být umístění serveru nebo sdílená složka.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Vytvoření cíle pro váš Virtuální pevný disk
Vytvořte si účet úložiště pro údržbu virtuálních discích. Při plánování, kam uložit virtuální počítač, zvažte následující body:

* Cílový účet úložiště může být standardní nebo prémiové úložiště v závislosti na požadavcích aplikace.
* Oblast účtu úložiště musí být stejná jako virtuální počítače Azure s podporou úložiště Premium Storage, které vytvoříte v konečné fázi. Můžete zkopírovat do nového účtu úložiště nebo chcete na základě vašich potřeb použít stejný účet úložiště.
* Zkopírujte a uložte klíč účtu úložiště cílového účtu úložiště pro další fázi.

Důrazně doporučujeme přesunout všechna data pro produkční úlohy používat prémiové úložiště.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Krok 3. Nahrání virtuálního pevného disku do úložiště Azure
Teď, když máte virtuální pevný disk v místním adresáři, můžete použít AzCopy nebo AzurePowerShell k nahrání souboru .vhd do Azure Storage. Obě možnosti jsou k dispozici zde:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Možnost 1: Použití doplňku AzureShell azurevhd k nahrání souboru .vhd

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Příkladem \<uri> může být **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_**. Příkladem \<> FileInfo může být **_"C:\path\to\upload.vhd"_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Možnost 2: Použití AzCopy k nahrání souboru .vhd

Pomocí AzCopy můžete snadno nahrát Virtuální pevný disk přes internet. V závislosti na velikosti VHDto, to může nějakou dobu trvat. Nezapomeňte zkontrolovat limity příchozího přenosu dat/odchozího přenosu dat při použití této možnosti. Podrobnosti najdete [v tématu Škálovatelnost a výkonnostní cíle pro standardní účty úložiště.](scalability-targets-standard-account.md)

1. Stáhněte si a nainstalujte AzCopy zde: [Nejnovější verze AzCopy](https://aka.ms/downloadazcopy)
2. Otevřete Azure PowerShell a přejděte do složky, kde je nainstalován AzCopy.
3. Pomocí následujícího příkazu zkopírujte soubor VHD ze zdroje do "Cíl".

   ```azcopy
      AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

   Příklad:

   ```azcopy
      AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
   ```

   Zde jsou popisy parametrů použitých v příkazu AzCopy:

   * **/Source:** _ &lt;&gt;zdroj:_ Umístění adresy URL složky nebo kontejneru úložiště, která obsahuje virtuální pevný disk.
   * **/SourceKey:** _ &lt;&gt;zdroj-účet-klíč :_ Klíč účtu úložiště zdrojového účtu úložiště.
   * **/Dest:** _ &lt;&gt;cíl :_ Adresa URL kontejneru úložiště pro kopírování virtuálního pevného disku.
   * **/DestKey:** _ &lt;dest-account-key&gt;:_ Klíč účtu úložiště cílového účtu úložiště.
   * **/BlobType: stránka:** Určuje, že cílem je objekt blob stránky.
   * **/Pattern:** _ &lt;název&gt;souboru :_ Zadejte název souboru virtuálního pevného disku ke kopírování.

Podrobnosti o používání nástroje AzCopy viz [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Další možnosti nahrávání virtuálního pevného disku
Virtuální pevný disk můžete také nahrát do svého účtu úložiště jedním z následujících způsobů:

* [Rozhraní BLOB kopírování azure úložiště](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Azure Storage Explorer nahrávání objektů BLOB](https://azurestorageexplorer.codeplex.com/)
* [Odkaz na rozhraní REST rozhraní REST služby importu a exportu](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Pokud je odhadovaná doba nahrávání delší než 7 dní, doporučujeme použít službu Import/Export Service. Pomocí [dataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) můžete odhadnout čas z velikosti dat a přenosové jednotky.
>
> Import nebo export lze použít ke kopírování do standardního účtu úložiště. Budete muset kopírovat ze standardního úložiště do účtu úložiště premium pomocí nástroje, jako je AzCopy.
>
>

## <a name="create-azure-vms-using-premium-storage"></a><a name="create-azure-virtual-machine-using-premium-storage"></a>Vytváření virtuálních počítačů Azure pomocí úložiště Premium Storage
Po nahrání nebo zkopírování virtuálního pevného disku do požadovaného účtu úložiště, postupujte podle pokynů v této části zaregistrovat VHD jako bitovou kopii operačního systému nebo disk operačního systému v závislosti na scénáři a pak z něj vytvořte instanci virtuálního počítače. Virtuální disk datového disku lze připojit k virtuálnímu počítače po jeho vytvoření.
Ukázkový skript migrace je k dispozici na konci této části. Tento jednoduchý skript neodpovídá všem scénářům. Možná budete muset aktualizovat skript tak, aby odpovídal vašemu konkrétnímu scénáři. Chcete-li zjistit, zda se tento skript vztahuje na váš scénář, naleznete níže [ukázkový skript pro migraci](#a-sample-migration-script).

### <a name="checklist"></a>Kontrolní seznam
1. Počkejte, až bude kopírování všech disků VHD dokončeno.
2. Ujistěte se, že je úložiště Premium k dispozici v oblasti, do které migrujete.
3. Rozhodněte se o nové řadě virtuálních virtuálních mís. Mělo by se jedná o úložiště Premium storage a velikost by měla být v závislosti na dostupnosti v oblasti a na základě vašich potřeb.
4. Rozhodněte přesnou velikost virtuálního počítače, který budete používat. Velikost virtuálního počítače musí být dostatečně velká, aby podporovala počet datových disků, které máte. Například Pokud máte 4 datové disky, virtuální počítače musí mít 2 nebo více jader. Zvažte také výpočetní výkon, paměť a potřeby šířky pásma sítě.
5. Vytvořte účet úložiště Premium v cílové oblasti. Toto je účet, který budete používat pro nový virtuální účet.
6. Měj po ruce aktuální podrobnosti o virtuálním počítače, včetně seznamu disků a odpovídajících objektů BLOB virtuálního pevného disku.

Připravte aplikaci na prostoje. Chcete-li provést čistou migraci, musíte zastavit veškeré zpracování v aktuálním systému. Teprve pak jej můžete dostat do konzistentního stavu, který můžete migrovat na novou platformu. Doba prostojů bude záviset na množství dat na disku, které mají být migrovány.

> [!NOTE]
> Pokud vytváříte virtuální počítač Azure Resource Manager ze specializovaného disku Virtuálního pevného disku, přečtěte si [tuto šablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) pro nasazení virtuálního počítače Správce prostředků pomocí existujícího disku.
>
>

### <a name="register-your-vhd"></a>Zaregistrujte svůj Virtuální pevný disk
Chcete-li vytvořit virtuální ho dispozičního modulu z virtuálního pevného disku operačního systému nebo připojit datový disk k novému virtuálnímu počítače, musíte je nejprve zaregistrovat. Postupujte podle následujících kroků v závislosti na scénáři virtuálního pevného disku.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalizované virtuální disky operačního systému pro vytvoření více instancí virtuálních počítačích Azure
Po nahrání generalizované image operačního systému do účtu úložiště zaregistrujte jako **image virtuálního počítače Azure,** abyste z něj mohli vytvořit jednu nebo více instancí virtuálních počítače. Pomocí následujících rutin Prostředí PowerShell zaregistrujte virtuální pevný disk jako image operačního systému Azure. Zadejte úplnou adresu URL kontejneru, do které byl virtuální pevný disk zkopírován.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Zkopírujte a uložte název této nové image virtuálního počítače Azure. Ve výše uvedeném příkladu je *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Jedinečný virtuální pevný disk operačního systému pro vytvoření jediné instance virtuálního počítače Azure
Po nahrání jedinečného virtuálního pevného disku operačního systému do účtu úložiště jej zaregistrujte jako **disk operačního systému Azure,** abyste z něj mohli vytvořit instanci virtuálního počítače. Pomocí těchto rutin prostředí PowerShell zaregistrujte virtuální pevný disk jako disk operačního systému Azure. Zadejte úplnou adresu URL kontejneru, do které byl virtuální pevný disk zkopírován.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Zkopírujte a uložte název tohoto nového disku operačního systému Azure. Ve výše uvedeném příkladu je *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Virtuální disk datového disku, který má být připojen k novým instancím virtuálních počítačů Azure
Po nahrání virtuálního pevného disku datového disku do účtu úložiště zaregistrujte jej jako datový disk Azure, aby ho bylo možné připojit k nové instanci virtuálního počítače DS Series Series, DSv2 nebo GS Series Azure.

Pomocí těchto rutin prostředí PowerShell zaregistrujte virtuální pevný disk jako datový disk Azure. Zadejte úplnou adresu URL kontejneru, do které byl virtuální pevný disk zkopírován.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Zkopírujte a uložte název tohoto nového datového disku Azure. Ve výše uvedeném příkladu je *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Vytvoření virtuálního účtu s podporou úložiště premium storage
Po registraci bitové kopie operačního systému nebo disku operačního systému vytvořte nový virtuální modul řady DS, DSv2 nebo GS. Budete používat image operačního systému nebo název disku operačního systému, který jste zaregistrovali. Vyberte typ virtuálního zařízení z úrovně Úložiště Premium. V příkladu níže používáme *Standard_DS2* velikost virtuálního počítače.

> [!NOTE]
> Aktualizujte velikost disku a ujistěte se, že odpovídá vašim požadavkům na kapacitu a výkon a dostupným velikostem disků Azure.
>
>

Follow the step by step PowerShell cmdlets below to create the new VM. Nejprve nastavte společné parametry:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Nejprve vytvořte cloudovou službu, ve které budete hostovat nové virtuální počítače.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Dále v závislosti na scénáři vytvořte instanci virtuálního počítače Azure z image operačního systému nebo disku operačního systému, který jste zaregistrovali.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalizované virtuální disky operačního systému pro vytvoření více instancí virtuálních počítačích Azure
Vytvořte jednu nebo více nových instancí virtuálních počítačů DS Series Azure pomocí **image operačního systému Azure,** kterou jste zaregistrovali. Zadejte tento název image operačního systému v konfiguraci virtuálního počítače při vytváření nového virtuálního počítače, jak je znázorněno níže.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Jedinečný virtuální pevný disk operačního systému pro vytvoření jediné instance virtuálního počítače Azure
Vytvořte novou instanci virtuálního počítače Azure řady DS pomocí **disku operačního systému Azure,** který jste zaregistrovali. Zadejte tento název disku operačního systému v konfiguraci virtuálního počítače při vytváření nového virtuálního počítače, jak je znázorněno níže.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Zadejte další informace o virtuálních počítačích Azure, jako je cloudová služba, oblast, účet úložiště, sada dostupnosti a zásady ukládání do mezipaměti. Všimněte si, že instance virtuálního počítače musí být umístěna společně s přidruženým operačním systémem nebo datovými disky, takže vybraná cloudová služba, oblast a účet úložiště musí být všechny ve stejném umístění jako základní virtuální pevné disky těchto disků.

### <a name="attach-data-disk"></a>Připojení datového disku
A konečně, pokud jste zaregistrovali virtuální disky datového disku, připojte je k novému virtuálnímu počítači Azure s podporou úložiště Premium Storage.

Pomocí následující rutiny prostředí PowerShell připojte datový disk k novému virtuálnímu počítače a zadejte zásadu ukládání do mezipaměti. V příkladu pod zásady ukládání do mezipaměti je nastavena na *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Může být další kroky nezbytné pro podporu aplikace, která není zahrnuta v této příručce.
>
>

### <a name="checking-and-plan-backup"></a>Kontrola a plánování zálohování
Jakmile je nový virtuální počítače v provozu, přístup pomocí stejné přihlašovací id a heslo je jako původní virtuální počítače a ověřte, že vše funguje podle očekávání. Všechna nastavení, včetně prokládaných svazků, budou v novém virtuálním počítače.

Posledním krokem je plánování plánu zálohování a údržby pro nový virtuální počítač na základě potřeb aplikace.

### <a name="a-sample-migration-script"></a><a name="a-sample-migration-script"></a>Ukázkový skript pro migraci
Pokud máte více virtuálních počítačů k migraci, automatizace prostřednictvím skriptů Prostředí PowerShell bude užitečná. Následuje ukázkový skript, který automatizuje migraci virtuálního soudu. Všimněte si, že pod skript je pouze příklad a existuje několik předpokladů o aktuální disky virtuálního počítače. Možná budete muset aktualizovat skript tak, aby odpovídal vašemu konkrétnímu scénáři.

Předpoklady jsou:

* Vytváříte klasické virtuální počítače Azure.
* Zdrojové disky operačního systému a zdrojové datové disky jsou ve stejném účtu úložiště a ve stejném kontejneru. Pokud vaše disky operačního systému a datové disky nejsou na stejném místě, můžete použít AzCopy nebo Azure PowerShell ke kopírování Virtuálních pevných disků přes účty úložiště a kontejnery. Viz předchozí krok: [Kopírování virtuálního pevného disku pomocí azCopy nebo PowerShellu](#copy-vhd-with-azcopy-or-powershell). Úprava tohoto skriptu tak, aby vyhovoval vašemu scénáři, je další volbou, ale doporučujeme používat AzCopy nebo PowerShell, protože je jednodušší a rychlejší.

Skript automatizace je uveden níže. Nahraďte text informacemi a aktualizujte skript tak, aby odpovídal konkrétnímu scénáři.

> [!NOTE]
> Použití existujícího skriptu nezachová síťovou konfiguraci zdrojového virtuálního počítače. Budete muset re-config nastavení sítě na migrované virtuální počítače.
>
>

```powershell
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    https://azure.microsoft.com/documentation/articles/powershell-install-configure/
    https://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in seconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module version" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article https://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the source vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting until the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more customization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a><a name="optimization"></a>Optimalizace
Aktuální konfigurace virtuálního počítače může být přizpůsobena speciálně pro správnou práci se standardními disky. Chcete-li například zvýšit výkon pomocí mnoha disků v proklápěcím svazku. Například namísto použití 4 disků samostatně v úložišti Premium storage můžete optimalizovat náklady tím, že budete mít jeden disk. Optimalizace, jako je tento, je třeba zpracovat případ od případu a vyžadovat vlastní kroky po migraci. Všimněte si také, že tento proces nemusí fungovat dobře pro databáze a aplikace, které závisí na rozložení disku definované v nastavení.

##### <a name="preparation"></a>Příprava
1. Dokončete jednoduchou migraci, jak je popsáno v předchozí části. Optimalizace se bude provádět na nový virtuální počítač po migraci.
2. Definujte nové velikosti disků potřebné pro optimalizovanou konfiguraci.
3. Určete mapování aktuálních disků/svazků na nové specifikace disku.

##### <a name="execution-steps"></a>Kroky spuštění
1. Vytvořte nové disky se správnými velikostmi na virtuálním počítači úložiště Premium.
2. Přihlaste se k virtuálnímu počítače a zkopírujte data z aktuálního svazku na nový disk, který se mapuje na tento svazek. Proveďte to pro všechny aktuální svazky, které je třeba mapovat na nový disk.
3. Dále změňte nastavení aplikace pro přepnutí na nové disky a odpojte staré svazky.

Pro optimalizaci aplikace pro lepší výkon disku, naleznete v části optimalizace výkonu aplikace našeho [navrhování pro vysoký výkon](../../virtual-machines/windows/premium-storage-performance.md) článku.

### <a name="application-migrations"></a>Migrace aplikací
Databáze a další složité aplikace mohou vyžadovat zvláštní kroky definované poskytovatelem aplikace pro migraci. Podívejte se prosím na příslušnou dokumentaci k aplikaci. Například obvykle databáze lze migrovat prostřednictvím zálohování a obnovení.

## <a name="next-steps"></a>Další kroky
Konkrétní scénáře pro migraci virtuálních počítačů najdete v následujících zdrojích:

* [Migrace virtuálních počítačů Azure mezi účty úložiště](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Vytvořte a nahrajte virtuální pevný disk Windows Serveru do Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Vytvoření a nahrání virtuálního pevného disku Linuxu do Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrace virtuálních počítačů z Amazon AWS do Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Další informace o Azure Storage a Virtuálních počítačích Azure najdete v následujících zdrojích informací:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Virtuální počítače Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Výběr typu disku pro virtuální počítače IaaS](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
